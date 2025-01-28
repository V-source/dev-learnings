# primero lo primero

## instalar:
- `@emotion/react`
- `@emotion/styled`

### `@emotion/react`

La biblioteca "@emotion/react" es una herramienta importante en el ecosistema de React y styled-components. Aquí te explico para qué sirve:

### Propósito principal

La biblioteca "@emotion/react" proporciona una capa de abstracción que facilita el uso de Emotion en aplicaciones React. Su propósito principal es simplificar la integración entre styled-components y React.

### Ventajas principales

1. **Simplificación del uso de Emotion**: Permite usar el API de Emotion de forma más sencilla en React, sin necesidad de escribir código JSX anormal.

2. **Compatibilidad con TypeScript**: Ofrece soporte nativo para TypeScript, lo que mejora la experiencia de desarrollo al trabajar con tipos.

3. **Mejor rendimiento**: Optimiza las renderizaciones y reduce el número de elementos DOM creados, lo que puede mejorar el rendimiento de tu aplicación.

4. **Flexibilidad**: Facilita el uso tanto de styled-components como de otros sistemas de estilos basados en Emotion (como @emotion/styled) en la misma aplicación.

### Uso común

```jsx
import { css } from '@emotion/react';

const buttonStyles = css`
  background-color: blue;
  color: white;
  padding: 10px 20px;
`;

function MyButton() {
  return <button css={buttonStyles}>Click me</button>;
}
```

En este ejemplo, `css` es una función proporcionada por "@emotion/react" que permite definir estilos CSS como strings y aplicarlos directamente a los elementos React.
### Integración con styled-components

Cuando usas "@emotion/react" junto con styled-components, puedes aprovechar las mejores partes de ambos sistemas:

```jsx
import styled from '@emotion/styled';
import { motion } from 'framer-motion';

const StyledButton = styled(motion.button)`
  /* Estilos CSS */
`;

export default StyledButton;
```

Aquí, `styled` proviene de "@emotion/styled", mientras que `motion` viene de framer-motion. Esto te permite combinar el poder de styled-components con las capacidades de animación de framer-motion [7].

### Conclusión

La biblioteca "@emotion/react" es un puente importante entre React y el ecosistema de Emotion, simplificando el proceso de creación de componentes estilizados y mejorando el rendimiento de tus aplicaciones React. Es especialmente útil cuando trabajas con styled-components o otras herramientas basadas en Emotion en tu proyecto.

