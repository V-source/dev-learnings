# Máquinas de Estados

Las máquinas de estados son un patrón de diseño donde un objeto tiene un número finito de estados y solo puede estar en uno a la vez. Transiciones entre estados ocurren según eventos definidos.

## Implementación básica en JS

```javascript
class StateMachine {
  constructor(states, initialState) {
    this.states = states;
    this.currentState = initialState;
  }

  transition(event) {
    const nextState = this.states[this.currentState][event];
    if (nextState) {
      this.currentState = nextState;
      return true;
    }
    return false;
  }
}
```

## Ejemplo práctico - Un pedido

```javascript
const pedido = new StateMachine({
  pendiente: { pagar: 'pagando', cancelar: 'cancelado' },
  pagado: { preparar: 'preparando', reembolsar: 'reembolsado' },
  preparando: { enviar: 'enviado' },
  enviado: { entregar: 'entregado' }
}, 'pendiente');

pedido.transition('pagar'); // -> 'pagando'
```

## Cuándo usarlos

- Flujos de trabajo con pasos definidos
- UI con modos distintos (edición, vista, carga)
- Validación de formularios por pasos
- Protocolos de comunicación

---

**Librerías populares:** XState, Machina.js, State Machine (npm)

## Cuándo SÍ se requieren State Machines (y cuándo no)

### Casos donde SÍ tiene sentido:

1. **Sistemas de pagos/Transacciones financieras**
   - Un pago tiene estados críticos: `pendiente` → `autorizado` → `capturado` → `conciliado`. Saltarse uno es un bug crítico.

2. **Procesos de onboarding/approval workflows**
   - Documentos que requieren aprobación secuencial por múltiples roles (ej: aprobación de préstamo, ticket de soporte escalado).

3. **Máquinas físicas/IoT**
   - Una cafetera, un dispenser, un PLC industrial. El hardware tiene estados físicos finitos que no puedes violar.

4. **Juegos y simuladores**
   - Un personaje tiene estados: `idle` → `walking` → `running` → `jumping`. No puedes estar corriendo y saltando al mismo tiempo (depende, pero en la mayoría de casos sí).

5. **Protocolos de red/Comunicación**
   - Handshakes TCP, MQTT, WebSockets. El protocolo mismo define una máquina de estados.

### Casos donde NO necesitas una State Machine:

- **Gestión de UI simple**: Si solo tienes 2-3 estados y las transiciones son obvias, es overkill.
- **CRUD simple**: Crear, leer, actualizar, delete no es una máquina de estados.
- **Formularios con validación**: A menos que los pasos dependan del estado previo de manera compleja.

### La pregunta clave:
¿Tu sistema tiene **reglas de transición estrictas** que si se violan causa un bug real (no solofechas feas)? Si la respuesta es sí, necesitas una state machine. Si es "para organizar mejor el código", probablemente no.
