---
template: overrides/home.html
title: prodverse
hide:
  - navigation
  - toc
---

<style>
.container {
    display: flex;
    flex: 1 1 0px;
    width: 100%;
    align-items: center; /* Aligns the image and text vertically */
}

.image {
    width: 50%;
    padding-right: 20px;
    padding-left: 20px;
    /* center the image */
    display: flex;
    justify-content: center;
}

.image img {
    max-height: 400px;
}

.text {
    width: 50%;
    padding-left: 20px;
    padding-right: 20px;
    font-size: 1.5em;
}
</style>

<div class="container">
    <div class="image">
        <img src="/figures/prodverse.png" alt="Descripción de la Imagen">
    </div>
    <div class="text">
        <h1>prodverse</h1>
        <p>
            El prodverse es una caja de herramientas diseñada por <a
            href="https://www.ixpantia.com/es/">ixpantia</a> para llevar tus
            proyectos de ciencia de datos a la producción. El ecosistema está
            construido para ayudarte a ti y a tu equipo a construir, desplegar,
            y mantener productos de datos listos para producción escritos con
            el lenguaje de programación R.
        </p>
    </div>
</div>

---

## ¿Qué es exactamente el prodverse?

El prodverse es una colección de herramientas y paquetes de R que te ayudan a construir, desplegar,
y mantener productos de datos listos para producción escritos con el lenguaje de programación R.

Los objetivos del prodverse son:

  - **Hacer que R sea escalable**: R es un gran lenguaje para la ciencia de datos, pero
    muchas veces faltan las herramientas para escalarlo a muchos usuarios.
    El prodverse busca llenar este vacío.
  - **Preparar R para producción**: R con Shiny y Plumber son herramientas increíbles,
    pero hay muchas cosas que se necesitan para hacerlos listos para producción.
    Autenticación, registro y replicación son solo algunas de las cosas que
    proporciona el prodverse.
  - **Sin bloqueo de proveedor**: El prodverse tiene como objetivo proporcionar una gran experiencia
    en cualquier lugar donde quieras desplegar tus aplicaciones R.

---

## ¿Debería considerar el prodverse sobre otras herramientas?

El prodverse no es para todos. Si estás buscando una herramienta que permita
el despliegue y la compartición de tus aplicaciones R con un solo clic, entonces deberías
considerar [Posit Connect](https://posit.co/products/enterprise/connect/). Posit
Connect es una gran herramienta para compartir tus aplicaciones R con tu equipo de ciencia de datos o tu organización.

Si tu equipo de ciencia de datos no está listo para una plataforma de ciencia de datos completamente desarrollada
como Posit Connect, o necesita una solución más personalizada, entonces el prodverse
podría ser una buena opción para ti.

---

## ¿Necesitas ayuda?

Si necesitas consultoría personalizada o soporte para tu equipo de ciencia de datos
por favor contáctanos en [ixpantia.com](https://www.ixpantia.com/es/contacto).
