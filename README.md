# 🚔 Robo del Siglo

> Un juego arcade para dos jugadores hecho en **Wollok**: un ladrón intenta vaciar un banco y un policía intenta frenarlo a los tiros.

![Robo del Siglo en pleno juego](docs/captura-juego.png)

## Sobre el proyecto

Es el trabajo práctico de **Paradigmas de Programación** (UTN FRBA, 2026), hecho en equipo de cuatro y entregado en tres etapas.

El objetivo de esta parte de la materia era practicar **programación orientada a objetos**: polimorfismo, herencia, delegación, bajo acoplamiento y tests. El juego es la excusa para aplicarlo.

Los dos jugadores comparten el teclado. El **ladrón** recorre el banco juntando monedas, bolsas de dinero y diamantes, y los lleva a su camión para sumar puntos. El **policía** busca armas por el mapa, las recarga con municiones y trata de dispararle antes de que se salga con la suya. El ladrón tiene tres vidas; cuando las pierde, la partida termina y se muestra el puntaje final. No hay victoria: es un arcade y el desafío es superar el puntaje anterior. Las reglas y los controles se explican dentro del juego.

## Diseño

- **Objetos nulos en lugar de `null`.** Un policía sin arma, un ladrón con las manos vacías o "ninguna pantalla" responden a los mismos mensajes que sus pares y simplemente no hacen nada. Así se evitan los condicionales que preguntan "¿hay algo?".
- **Herencia y polimorfismo.** Los dos personajes comparten una clase base abstracta, las armas también, y todo lo que se ve en el tablero (dinero, armas, municiones, camión y proyectiles) tiene un comportamiento por defecto que cada uno redefine solo si lo necesita.
- **Doble despacho para las colisiones.** Cuando alguien llega a una celda, lo que está ahí le avisa al personaje de qué se trata y el personaje decide qué hacer, sin preguntar por el tipo de cada cosa.
- **Colisiones solo cuando algo se mueve.** En vez de revisar choques en cada cuadro del juego, se revisa únicamente la celda a la que llega un personaje, o la celda a la que avanza un proyectil.
- **Paredes como una matriz de ceros y unos.** El dibujo de las paredes y los muebles viene en la imagen de fondo y la lógica es una matriz donde `1` es una celda bloqueada y `0` una libre. Pasar de unos 140 objetos de pared a esa matriz, junto con las colisiones al moverse, hizo que el servidor del juego mande más del doble de cuadros por segundo en mis mediciones.
- **Responsabilidades separadas.** El flujo de la partida, los elementos que aparecen en el mapa, el teclado, las pantallas y el dinero están a cargo de objetos distintos.
- **Reaparición de armas y municiones.** Vuelven a aparecer a los 30 segundos de ser tomadas, y solo si la partida sigue siendo la misma.
- **Sonido.** Música de fondo y un efecto distinto por acción. Como en Wollok Game un sonido se reproduce una sola vez, cada efecto alterna entre dos copias para poder repetirse.
- **Tests automatizados.** 77 tests, organizados por entrega, que cubren movimiento, paredes, dinero, armas, reaparición y el flujo entre pantallas.

## Cómo correrlo

Necesitás [Node.js](https://nodejs.org) (el proyecto se prueba con la versión 20).

```bash
npm install -g wollok-ts-cli
wollok run main.juego      # inicia el juego: la consola muestra la dirección donde abrirlo en el navegador
wollok test                # corre los tests
```

También se puede usar la extensión de Wollok para Visual Studio Code: abrís la carpeta del proyecto y lanzás el juego desde ahí.

## Mi parte

Dentro del equipo me encargué de:

- **La base del juego:** la plantilla del proyecto, el ladrón y el policía con su movimiento, y el camión.
- **El dinero:** monedas, bolsas y diamantes, cómo los recoge el ladrón y los entrega en el camión, los efectos que tienen sobre su velocidad y la reposición del dinero en el banco.
- **Las armas:** la pistola, el disparo y el proyectil, la escopeta con más daño y más lenta, y que el ladrón pierda vidas según el daño que recibe. También hice que las armas reaparezcan después de ser tomadas.
- **Las colisiones** entre el ladrón, el policía y el camión.
- **El mapa:** la plantilla, el posicionamiento del dinero y varios de los sprites, como el del proyectil y el diseño del HUD.
- **El rendimiento:** pasé las colisiones a revisarse solo cuando algo se mueve y las paredes y el decorado a una matriz de celdas bloqueadas.
- **El diseño:** los objetos nulos, el reparto de responsabilidades del flujo de la partida y la base común de todo lo que se muestra en el tablero.
- **El sonido:** la música de fondo y los efectos.
- **Los tests** de la segunda y la tercera entrega.

> Parte de estos cambios (refactors, tests y mediciones de rendimiento) fueron desarrolados con la ayuda de Claude Code como asistente.

## El equipo

| Integrante | Aportes principales |
|---|---|
| **Valentino Mazza** | Base del juego, dinero, armas, colisiones, rendimiento, sonido y tests (ver arriba) |
| **Kevin Bernabe** | Colisión inicial con las paredes, puntos y vidas en pantalla, primer modelado de la pistola, pantallas de inicio, reglas y game over, el control del flujo de la partida y la generalización del dinero en una sola clase configurable |
| **Francisco Ghioldi Crespo** | Refactor de las direcciones y separación de los personajes |
| **Valentin Penna** | Diagramas de clases, reaparición de las municiones |

## ¿Qué es Wollok?

[Wollok](https://www.wollok.org) es un lenguaje de programación y entorno de desarrollo pensado para enseñar **programación orientada a objetos**. Es open source, lo desarrolla Fundación Uqbar (un grupo de docentes de universidades nacionales de Argentina) y se usa en materias iniciales de programación de universidades argentinas. Se distribuye bajo licencia LGPLv3.

Algunas ideas que lo distinguen:

- **Objetos desde el primer día.** Lo que existe una sola vez en el juego, como el ladrón, el policía o el banco, se define directamente como un objeto, sin necesidad de armar antes una clase. Las clases aparecen cuando hay muchas instancias parecidas, como el dinero o los proyectiles.
- **Polimorfismo sin interfaces.** Si dos objetos entienden el mismo mensaje, se pueden usar indistintamente. No hace falta declarar un tipo en común.
- **Bloques de código y herencia**, que se van incorporando de a poco a medida que el programa crece.
- **Tests unitarios integrados**, que se corren desde el mismo entorno donde se programa.

### Wollok Game

Wollok incluye **Wollok Game**, una librería para hacer juegos 2D sobre un tablero de celdas. Cada elemento del juego es un objeto que sabe en qué celda está y qué imagen mostrar; la librería se ocupa de dibujarlo, leer el teclado, reproducir sonidos y programar acciones en el tiempo.
