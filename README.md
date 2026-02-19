# Quiz 1 — Identificador de números complejos (SOLO FLEX)

Este proyecto implementa un analizador léxico en **Flex** que **verifica si el contenido de un archivo `.txt` corresponde a un número complejo** en formato:

- **a + bi** ó **a - bi**
- ó forma **a·i** (ej: `0.13J`)

Donde:
- `a` y `b` son números reales (permiten decimales y notación científica `e/E`)
- la unidad imaginaria puede ser: `i`, `I`, `j`, `J`
- La salida del programa es **ACEPTA** o **NO ACEPTA** por pantalla.

---

## Archivos

- `fbquiz1.l` → código fuente del scanner en Flex
- `quizprueba1.txt`, `quizprueba2.txt`, `quizprueba3.txt` → archivos de prueba

---

## Compilación y ejecución

### 1) Generar el scanner en C
```bash
flex fbquiz1.l
