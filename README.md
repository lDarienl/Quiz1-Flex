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
````

Esto genera el archivo `lex.yy.c`.

### 2) Compilar

```bash
cc -o complejos lex.yy.c -lfl
```

### 3) Ejecutar con un archivo `.txt`

```bash
./complejos quizprueba1.txt
./complejos quizprueba2.txt
./complejos quizprueba3.txt
```

---

## Ejemplos esperados

* `quizprueba1.txt` (ej: `1.3e-12 + 2.34I`) → **ACEPTA**
* `quizprueba2.txt` (ej: `1.13e10`) → **NO ACEPTA**
* `quizprueba3.txt` (ej: `0.13J`) → **ACEPTA**

---

## Explicación del código (fbquiz1.l)

Un archivo de Flex tiene 3 secciones separadas por `%%`:

1. **Definiciones**
2. **Reglas léxicas**
3. **Código C (main, helpers, etc.)**

---

### 1) Definiciones y macros

Dentro de `%{ ... %}` se escribe código C que Flex copia al archivo generado:

* `acepta`: bandera que se activa si se reconoce una forma válida
* `error`: bandera que se activa si aparece cualquier carácter inválido

Se definen dos macros:

#### REAL

Reconoce números reales con:

* signo opcional `+` o `-`
* parte decimal tipo `12`, `12.`, `12.34`, `.5`
* notación científica opcional: `e/E` con signo opcional (`1.3e-12`, `2E10`)

#### IMAG

Reconoce la unidad imaginaria:

* `i`, `I`, `j`, `J`

---

### 2) Reglas léxicas

Las reglas están entre `%% ... %%`. Flex lee la entrada y busca coincidencias.

#### Regla: forma `a +/- b i`

```lex
{REAL}[ \t]*[+-][ \t]*{REAL}[ \t]*{IMAG}
```

Significa:

* un REAL
* espacios opcionales
* un `+` o `-` obligatorio
* espacios opcionales
* otro REAL
* espacios opcionales
* y finalmente `i/I/j/J`

Ejemplos que acepta:

* `1.3e-12 + 2.34I`
* `1.3e-12+2.34I`
* `-5 - 3j`

Cuando hace match, se ejecuta:

```c
if (!error) acepta = 1;
```

---

#### Regla: forma `a i` (eimaginario)

```lex
{REAL}[ \t]*{IMAG}
```

Ejemplos:

* `0.13J`
* `2i`
* `-3.5I`

---

#### Ignorar espacios y saltos de línea

```lex
[ \t\n\r]+
```

---

#### Detectar cualquier carácter inválido

```lex
.
```

El punto `.` significa “cualquier carácter”.
Si aparece algo fuera de los patrones válidos, se marca:

* `error = 1`
* `acepta = 0`

---

#### Fin del archivo

```lex
<<EOF>>
```

Cuando termina la entrada, el scanner finaliza.

---

### 3) ¿Cómo lee el archivo? (`yyin`)

En `main` se abre el archivo recibido por parámetro:

```c
FILE *f = fopen(argv[1], "r");
```

Luego se asigna a `yyin`:

```c
yyin = f;
yylex();
```

* `yyin` es la entrada que Flex usa para leer (por defecto sería `stdin`)
* `yylex()` ejecuta el scanner sobre el archivo completo

Finalmente se cierra el archivo:

```c
fclose(f);
```

y se imprime:

* `ACEPTA` si se reconoció una forma válida y no hubo errores
* `NO ACEPTA` en cualquier otro caso

---

## Notas

* El programa valida **estructura léxica** (forma del número complejo).
* Si en el archivo aparece cualquier otro texto además del número complejo, se marca error y se imprime `NO ACEPTA`.

