# Aprende

¡Hola! Este es tu primer paso para convertirte en un experto en llevar
aplicaciones R a producción. En esta sección encontrarás muchos
artículos tutoriales que te ayudarán a comenzar con el
prodverse.

[Shiny en producción con el prodverse](shiny_in_prod.md) es un
tutorial que te guiará a través del proceso de construir una aplicación Shiny muy
mínima con autenticación de usuario y desplegarla en un servidor con réplicas y balanceo de carga usando **faucet** y
**tapLock**. (La aplicación en el tutorial es muy básica y no
particularmente útil, pero es un buen punto de partida para entender
el prodverse.)

[Escalando Plumber con faucet](scaling_plumber.md) es un tutorial
que te llevará a través del proceso de escalar una API Plumber con
**faucet**. Primero instalarás faucet y lo probarás localmente,
luego usarás Docker para construir una imagen de contenedor para tu API.
La API será lenta para mostrar cómo **faucet** intentará hacerla
más rápida agregando réplicas y balanceo de carga.
