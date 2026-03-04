# DDD (Domain-Driven Design)

DDD es una metodología de diseño de software que enfatiza la colaboración entre expertos del dominio y desarrolladores para crear modelos que resuelvan problemas de negocio complejos.

## Conceptos clave

### 1. Dominio

- El área de conocimiento del negocio
- El problema que resuelves

### 2. Modelo Ubicuo

- Lenguaje compartido entre técnicos y expertos del dominio
- Términos consistentes en código, documentación y conversaciones

### 3. Bloques constructores

| Concepto | Descripción |
|----------|-------------|
| Entity | Tiene identidad única (ej: Usuario #123) |
| Value Object | Inmutable, definido por sus atributos (ej: Dirección) |
| Aggregate | Grupo de entidades con raíz común |
| Repository | Abstrae acceso a datos |
| Service | Operaciones que no pertenecen a una entidad |
| Domain Event | Algo significativo que ocurrió |

### 4. Arquitectura por capas

```
┌─────────────────────────────────┐
│         Presentation            │
├─────────────────────────────────┤
│        Application              │  (Casos de uso)
├─────────────────────────────────┤
│          Domain                │  (Entidades, Value Objects)
├─────────────────────────────────┤
│       Infrastructure            │  (BD, APIs externas)
└─────────────────────────────────┘
```

## Cuándo usarlo

- Sistemas complejos con reglas de negocio elaboradas
- Equipos grandes que necesitan lenguaje común
- Cuando el dominio evoluciona frecuentemente

## Ejemplo simple

```javascript
// Domain
class Carrito {
  constructor() {
    this.items = [];
    this.estado = 'abierto';
  }

  agregar(producto) {
    if (this.estado !== 'abierto') {
      throw new Error('Carrito cerrado');
    }
    this.items.push(producto);
  }

  cerrar() {
    this.estado = 'cerrado';
  }
}
```

## Dónde NO tiene sentido DDD

- CRUD simples
- Apps triviales
- Scripts automatizados

---

**Librería popular:** EffectTS (TypeScript) facilita DDD funcional.
