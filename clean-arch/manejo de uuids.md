
El manejo de los **UUIDs (o IDs auto-generados)** en Clean Architecture desata debates intensos entre arquitectos. Sin embargo, si nos apegamos al **desacoplamiento radical** y a la **testabilidad**, la respuesta de élite es:

**El UUID se genera en la capa de Aplicación (Caso de Uso) o en el Dominio (Entidad), pero la herramienta técnica para generarlo se inyecta.**

Nunca debe ir en el Controlador (porque el ID es un concepto de negocio, no de HTTP) y preferiblemente no debe ir en el Repositorio/Base de datos (si dejas que la base de datos genere el ID, tu Entidad de Dominio nace "incompleta" en memoria y dependes de la persistencia para tener un objeto válido).

---

### 1. El Mapa de Dependencias de los IDs

Para evitar que tu Dominio o Aplicación importen directamente librerías externas de infraestructura como `uuid` o `crypto`, creamos una **abstracción (interface)**.

```
[ CAPA DE DOMINIO ]                      [ CAPA DE INFRAESTRUCTURA ]
+-------------------------+               +----------------------------------+
| IIdGenerator (Interface)| <--------- | UuidV4Generator (Class)          |
+-------------------------+ (Implementa) +----------------------------------+
         ^
         | (Se inyecta en)
+-------------------------+
| CreateUserUseCase       |
+-------------------------+
[ CAPA DE APLICACIÓN ]

```

---

### 2. Implementación Paso a Paso

#### A. El Contrato (Capa de Dominio)

Definimos que nuestro sistema necesita generar identificadores únicos, pero no nos importa cómo se generan físicamente.

```typescript
// src/domain/services/id-generator.interface.ts
export interface IIdGenerator {
  generate(): string;
}

```

#### B. La Implementación Real (Capa de Infraestructura)

Aquí es donde finalmente podemos importar la librería de TypeScript o usar el API nativo de Node.js/Web.

```typescript
// src/infrastructure/services/uuid-generator.ts
import { IIdGenerator } from "../../domain/services/id-generator.interface";
import { v4 as uuidv4 } from "uuid"; // Detalle tecnológico

export class UuidGenerator implements IIdGenerator {
  generate(): string {
    return uuidv4();
  }
}

```

#### C. El Caso de Uso (Capa de Aplicación)

El caso de uso recibe el generador de IDs por el constructor. De esta forma, la entidad `User` se crea desde el primer milisegundo con su ID definitivo antes de tocar la base de datos (Turso o MongoDB).

```typescript
// src/application/use-cases/create-user.use-case.ts
import { User } from "../../domain/entities/user.entity";
import { IUserRepository } from "../../domain/repositories/user.repository";
import { IIdGenerator } from "../../domain/services/id-generator.interface";

export class CreateUserUseCase {
  constructor(
    private readonly userRepository: IUserRepository,
    private readonly idGenerator: IIdGenerator // Inyectamos el generador
  ) {}

  async execute(input: CreateUserInputDto) {
    const userExists = await this.userRepository.findByEmail(input.email);
    if (userExists) throw new Error("UserAlreadyExistsError");

    // 1. Generamos el ID aquí en la aplicación
    const userId = this.idGenerator.generate();

    // 2. La entidad nace con su ID ya asignado
    const user = new User({
      id: userId,
      name: input.name,
      email: input.email
    });

    // 3. Se guarda en Turso o Mongo sabiendo exactamente qué ID tiene
    await this.userRepository.save(user);

    return { id: user.id, email: user.email };
  }
}

```

---

### 3. ¿Por qué este enfoque es el superior? (Testability by Design)

Si usaras `uuidv4()` directamente importado dentro de la Entidad o del Caso de Uso, tus pruebas unitarias se volverían impredecibles porque cada vez que ejecutas el test, el ID cambia.

Al usar una interfaz inyectada, en tus archivos de pruebas (`.spec.ts` o `.test.ts`) puedes crear un **Mock** (un simulador) que siempre devuelva el mismo ID:

```typescript
// src/application/use-cases/create-user.use-case.spec.ts
import { CreateUserUseCase } from "./create-user.use-case";

describe("CreateUserUseCase", () => {
  it("should create a user with a specific ID", async () => {
    // Un generador falso para testing que no usa librerías externas
    const mockIdGenerator = { generate: () => "id-de-prueba-123" };
    const mockRepo = { save: jest.fn(), findByEmail: jest.fn().mockResolvedValue(null) };

    const useCase = new CreateUserUseCase(mockRepo, mockIdGenerator);
    const result = await useCase.execute({ name: "Alice", email: "alice@mail.com" });

    // Tu test es 100% predecible y matemático
    expect(result.id).toBe("id-de-prueba-123");
  });
});

```

### Resumen de la regla

Generar el ID en el **Caso de Uso** pidiéndole el token a una interfaz `IIdGenerator` es la mejor práctica. Permite que tus entidades tengan identidad propia desde que nacen en memoria y mantiene tu código protegido contra cambios de librerías de terceros.
