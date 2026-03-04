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
