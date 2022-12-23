# OOCSS + BEM

![](https://img.shields.io/badge/Alpha_1_wip-5c7219?style=for-the-badge&logo=esbuild&labelColor=1e485a&logoColor=white) 
![](https://img.shields.io/badge/UPDATE-2022/18/12-red?style=for-the-badge) 

Este documento contiene una pequeña guía sobre el uso y desarrollo con CSS orientado
a objetos, conocido como OOCSS y BEM para la estructura del mismo.

Todas las reglas aquí establecidas solamente pretenden establecer un estandar
de desarrollo para facilitar el trabajo diario y en equipo. 

Actualmente esta guía **se encuentra en su versión alpha**, pudiendo contener
errores tipográficos o carecer de más contexto en algunas partes.

## Consideraciones previas

- Usar siempre minúsculas y preferiblemente la versión abreviada para 
todas las unidades hexadecimales. 
- Abreviaturas para propiedades y valores de propiedad donde tenga sentido; 
donde no tiene sentido, usar una propiedad abreviada que te haga declarar 
valores cero, aquí es mejor ser explícito aunque signifique más líneas de CSS;
- Comillas simples para cadenas, url(), valores, etc.;
- Envolver los valores del selector de atributos entre comillas dobles;
- Incluir un espacio después de cada coma en valores separados por comas;
- Los paréntesis no deben rellenarse con espacios;
- Cuando se necesita una marca decimal, incluya siempre el cero;
- Usar siempre dos puntos dobles (::) para pseudo elementos; 
- Usar siempre px como unidades para cosas de tamaño como las siguientes propiedades de CSS:
    - border-radius
    - border
    - box-shadow
    - text-shadow
- NO se permite el uso de estilos en linea ni la directiva !important
- Solo se permiten usar clases para los selectores
    - ✅ .clase {}
	- ❌ #identificador {}
	- ❌ span {}
	- ❌ style="color: red"
- Usar siempre nombres descriptivos o relacionados en función del bloque, 
elemento o modificación.

# **Bloque** 
Que tiene sentido por si mismo y puede contener hijos. Por su propia definición
los bloques DEBEN ser reutilizables y independientes. Los **bloques** más comunes 
son **header, sidebar, aside, main, footer, article, section, ul, ol, div**
 
```erb
...
    <nav class="videogames-nav">
		<a href="#" class="videogames-nav__item">Foo</a>
		<a href="#" class="videogames-nav__item">Foo</a>
    <nav>
...
 
``` 

# **Elemento** 
Siempre DEBE tener un **Bloque** como padre. Un elemento son Las distintas 
partes de un bloque.
 
```erb
<header>
	<nav class="header__nav">
		<a href="#" class="header__nav__item">Foo</a>
		<a href="#" class="header__nav__item">Foo</a>
	</nav>
</header>
```

# **Modificador**
Puede aplicarse a **Bloque** y **Elemento**, es un conjunto de la clase junto al
nombre descriptivo de la modificación.

Aplicado a un **Bloque**:
```erb
<header>
	<nav class="header__nav header__nav--hide">
		<a href="#" class="header__nav__item">Foo</a>
		<a href="#" class="header__nav__item">Foo</a>
	</nav>
</header>
```

Aplicado a un **Elemento**:
```erb
<header>
	<nav class="header__nav">
		<a href="#" class="header__nav__item">Foo</a>
		<a href="#" class="header__nav__item header__nav__item--text-blue">Foo</a>
	</nav>
</header>
```

## Caso practico
⚠ Los ejemplos anteriores **no se deben utilizar** ya qué es solamente tienen 
fines demostrativos. En un desarrollo normal la etiqueta **nav** debería 
contenerse ser un componente individual para asegurar el aislamiento entre **Bloques**:

```erb
...
    <header>
        <nav-component-name></nav-component-name>
    </header>
...
```

**nav-component-name**
```erb 
...
	<nav class="nav">
		<a href="#" class="nav__item">Foo</a>
		<a href="#" class="nav__item">Foo</a>
	</nav>
...
```

## Especificación de nombres
La separación entre bloque y elemento la delimita el uso de dos guiones bajos "__" 
entre ambos:

```erb
...
    <nav class="videogames-nav">
		<a href="#" class="videogames-nav__item">Foo</a>
		<a href="#" class="videogames-nav__item">Foo</a>
    </nav>
...
 
``` 

El uso de los modificadores se aplica agregando dos guiones "--" despues del
nombre del **bloque** o **bloque**_**elemento**:

```css
.bloque__elemento--display-none{}
```

Nunca se pueden usar directamente clases modificadores, por ejemplo:

    ▸ ❌ class="button--primary"
    ▸ ✅ class="button button--primary"

**NESTED CSS (SASS\LESS\PostCSS)**

```scss
.videogames-nav{
    .videogames-nav__item{
        // Some style
    }
} 
```

**CSS**
```css
.videogames-nav{}

.videogames-nav
    .videogames-nav__item{
        /* Some style */
    }
```

En ocasiones los nombres de los **Bloques**, **Elementos** y **Modificadores** 
pueden estar compuestos por varias palabras y en ese caso usaremos los guiones
medios "-" para su separación.

## Anidamiento
En BEM no existe el concepto de sub-elementos. Un **bloque** puede contener 
diferente número de **elementos** pero un **elemento** no puede contener más
sub-elementos.

No existen clases que puedan ser **elemento**__**sub-elemento**:

```css
.bloque__elemento__sub-elemento{}
```

En su lugar se crean un nuevo bloque, recordando que no es necesario que los 
nombres de los elementos esten anidados de la misma manera que el html.

```erb 
 <article class="top-ranked-game">
    <div  class="top-ranked-game__content">
        <img class="top-ranked-game__image"src="#">
        <p class="top-ranked-game__text">Some text...</p>
    </div>
    <span class="top-ranked-game__rank">
        ...
    </span>
</article>
```

## Sass
Una forma de determinar el alcance de los estilos anidado, pero obtener CSS que 
no está anidado, con @at-root:

```scss
.block {
  @at-root #{&}__element {
  }
  @at-root #{&}--modifier {
  }
}
```

Se transformaría en:

```css
.block {
}
.block__element {
}
.block--modifier {
}
```

Abstrayendo así en todos los niveles que se quiera. 

## Problemas comunues
En ocasiones podemos tener la siguiente estructura:
 
```css
/** ❌ Nope! */
.nav .nav__listItem .btn--orange {
  background-color: green;
}
```

Un bloque (como .nav) nunca debe anular los estilos de otro bloque o modificador
(como .btn--orange). De lo contrario, esto haría casi imposible leer el HTML 
y entender lo que hace este componente; en el proceso, estamos obligados debilitamos
la confianza de otros desarrolladores en el código.

▸ Nunca anular modificadores en un bloque no relacionado.
▸ Evitar hacer elementos parentales innecesarios cuando el hijo puede existir felizmente por sí mismo.

## Modificadores encadenables
Los modificadores encadenables se indican con un guión inicial -, 
un espacio de nombres y un descriptor para la modificación.

```erb
<button class="btn -color-green -size-large">
```

Como su nombre lo indica, los modificadores encadenables nos brindan la capacidad 
de configurar un módulo en el HTML con una sintaxis breve y concisa.

La regla de oro es que **los modificadores encadenables nunca deben modificar la 
misma propiedad CSS dos veces para un módulo dado**. 
Esto es para garantizar que los estilos no se deterioren y que el orden en que
se apliquen sea irrelevante.

Ejemplo de la biblioteca UI de Envato Market:
```erb 
<h2 class="t-heading -size-m -color-light">Heading</h2>
<p class="t-body -size-s">Paragraph</p> 
```

## Prefijos recomendados
- `l-`: **Módulos de diseño**

    *Ejemplos:*
    - `.l-cuadrícula`
    - `$l-rejilla-canalón`
    - `_l-grid.scss`

- `c-`: **Componentes**

    *Ejemplos:*
    - `.c-botón`
    - `$c-botón-color de primer plano`
    - `_c-botón.scss`


- `h-`: **Helpers*.

    *Ejemplos:*
    - `.h-texto-pequeño`
    - `$h-texto-pequeño-tamaño`
    - `_h-tamaños-de-texto.scss`

- `t-`: **Temas**

    *Ejemplos:*
    - `.t-oscuro`
    - `$t-color-de-fondo-oscuro`
    - `_t-dark.scss`

- `s-`: **Ambito**

    *Ejemplos:*
    - `.s-subtexto-alineación`
    - `$s-subtexto-alineación-fondo-espaciado`
    - `_s-subtexto-alineación.scss`

Y el resto:

- `js-`: Hooks JS
- `is-`/`has-`: Hooks de estado
- `ss-`: Hooks del lado del servidor
- `track-`: Hooks de seguimiento


## Clases de estado y utilidad
Las clases de 'estado' y 'utilidad' también son una forma de modificador y, debido 
a su naturaleza de alta prioridad, siempre deben aplicarse después de otros 
modificadores para que sus estilos tengan prioridad (con las clases de estado al final).

- is-active
- has-loaded
- is-loading
- is-visible 
- is-hidden 
- is-disabled
- is-enbled
- is-expanded
- is-collapsed

### Expresar
Las clases de estado tienen el prefijo **is-** o **has-** y se usan para indicar 
que un módulo ha sido modificado de alguna manera por la lógica de la aplicación, 
ya sea código del lado del servidor o JavaScript.

```erb
<input class="f-input -type-string is-invalid">
```

### Utilidad (también conocido como ayudantes o helpers)
Las clases de utilidad no son su modificador tradicional, sino que son clases 
de ayuda de un solo uso diseñadas para realizar una tarea repetible simple. 
Con el prefijo **h-** o **u-** se pueden usar de forma independiente 
o encadenados a un módulo.

```erb
<h2 class="t-heading -size-xl h-text-align-center">
```

## JS Hooks
Los JS Hooks tienen el prefijo js-y no tienen ningún estilo. Todo su propósito
es usarse en JavaScript para apuntar a elementos en el DOM.

## Urls de utilidad

[BEM CHEAT SHEET](https://9elements.com/bem-cheat-sheet/) - Listado de plantillas
de ejemplo para los componentes más usuales como breadcrumb, button, cards...
