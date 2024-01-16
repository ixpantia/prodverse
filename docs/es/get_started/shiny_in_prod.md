# Shiny en producción con el prodverse

¡Bienvenido! Estás a un paso de aprender cómo desplegar una
aplicación Shiny pública con el prodverse. Esta aplicación será
pública, lo que significa que cualquiera con la URL podrá
acceder a ella, sin embargo, estará protegida por autenticación de Google. Esto significa que solo las personas que agregues como
usuarios de prueba podrán acceder a la aplicación.

Desplegar una aplicación privada generalmente implica configuración
específica de la organización, reglas de firewall y otras medidas de seguridad. Si
tu organización utiliza Google como su proveedor de identidad, puedes
usar esta guía para desplegar una aplicación privada con mínima configuración adicional.

## Prerrequisitos

- **Una cuenta de Google Cloud Platform (GCP)**: Necesitarás una
  cuenta de GCP para crear un proyecto y habilitar las APIs que
  utiliza el prodverse. Puedes crear una cuenta gratuita
  [aquí](https://cloud.google.com/free/).
- **Un proyecto de Google Cloud Platform**: Necesitarás un proyecto de GCP
  para desplegar tu aplicación. Puedes crear un proyecto
  [aquí](https://console.cloud.google.com/projectcreate).
- **Un nombre de dominio**: Necesitarás un nombre de dominio para desplegar
  tu aplicación. Esto puede ser un subdominio de un dominio que tengas,
  por ejemplo, `example.com` o `myapp.example.com`.
- (Opcional) **Una VM de Linux conectada a internet**: Podrías
  querer una máquina virtual para ejecutar el prodverse. Esto es
  opcional ya que no lo necesitas completamente, puedes ejecutar la
  aplicación de ejemplo en tu máquina local, Linux, Mac o Windows.
  Sin embargo, si quieres compartir tu aplicación con otros, se recomienda
  usar una VM. Esta guía asumirá que tienes una VM de Linux con Docker instalado.

## Paso 1: Crear un proyecto de GCP

Si ya tienes un proyecto de GCP, puedes saltarte este paso.

 1. Ve al siguiente enlace:
    [https://console.cloud.google.com/projectcreate](https://console.cloud.google.com/projectcreate)
 2. Nombra tu proyecto
 3. Haz clic en `Crear`

![Crear un proyecto de GCP](/figures/get_started/create_gcp_project.png)

## Paso 2: Crear una pantalla de consentimiento OAuth

Si ya tienes una pantalla de consentimiento OAuth, puedes saltarte este paso.

 1. Ve al siguiente enlace:
    [https://console.cloud.google.com/apis/credentials/consent](https://console.cloud.google.com/apis/credentials/consent)
 1. Selecciona `Externo` y haz clic en `Crear`: Si estás en una organización,
    podrías querer seleccionar `Interno` en su lugar. Si estás utilizando una
    cuenta personal de GCP, no podrás seleccionar `Interno`.
    `Interno` te permitirá compartir la aplicación con personas en tu
    organización sin tener que agregarlas como usuarios de prueba.
    ![Crear una pantalla de consentimiento OAuth](/figures/get_started/create_gcp_external_consent_screen.png)
 1. Nombra tu pantalla de consentimiento OAuth y completa los campos requeridos.
 1. Haz clic en `Guardar y Continuar`
 1. Agrega los siguientes alcances no sensibles:
    ![Agregar alcances a la pantalla de consentimiento OAuth](/figures/get_started/non_sensitive_scopes.png)
 1. Haz clic en `Guardar y Continuar`
 1. Agrega tus usuarios de prueba (Solo si seleccionaste `Externo` en el paso 2) 
 1. Haz clic en `Guardar y Continuar`
 1. Valida que la información sea correcta y haz clic en `Volver al Dashboard`

## Paso 3: Crear un ID de cliente OAuth

Si ya tienes un ID de cliente OAuth, puedes saltarte este paso, sin embargo,
necesitarás asegurarte de que el ID de cliente siga los campos como se muestra
en este paso.

![Crear ID de Cliente OAuth](/figures/get_started/create_oauth_client_id_gcp.png)

Después de hacer clic en `Crear ID de cliente OAuth`, se te presentará un
formulario que necesitarás completar. Los siguientes campos son requeridos:

 - Tipo de aplicación: `Aplicación web`
 - Nombre: Como quieras nombrar tu aplicación
 - Orígenes de JavaScript autorizados: `http://<tu-dominio>` o `https://<tu-dominio>` (si tienes SSL)
 - URI de redirección autorizadas: `http://<tu-dominio>/login` o `https://<tu-dominio>/login` (si tienes SSL)

Para este ejemplo el dominio será `example.com`, por lo tanto, los campos se
deberán completar de la siguiente manera:

 - Tipo de aplicación: `Aplicación web`
 - Nombre: `Mi Aplicación Shiny`
 - Orígenes de JavaScript autorizados: `http://example.com`
 - URI de redirección autorizadas: `http://example.com/login`

Asegúrate de reemplazar `example.com` con tu dominio.

Una vez que hayas completado el formulario, haz clic en `Crear` y guarda
el `ID de cliente` y el `Secreto de cliente` para más adelante.

![ID de Cliente OAuth creado](/figures/get_started/gcp_client_id_and_secret.png)

## Paso 4: Nuestra aplicación Shiny

Para este ejemplo, crearemos una aplicación Shiny muy simple que
mostrará un gráfico con una distribución aleatoria. La aplicación
usará un deslizador para cambiar el número de observaciones en la
distribución. Esta aplicación también usará [`tapLock`](https://ixpantia.github.io/tapLock/)
para añadir autenticación a la aplicación. Además, la usaremos
para mostrar mensajes de bienvenida agradables al usuario.

Antes de construir la aplicación necesitaremos instalar `tapLock`
y `shiny`:

```r
install.packages(c("shiny", "tapLock"))
```

Además, crea un archivo `.Renviron` para propósitos de prueba. Si estás
usando Docker o cualquier otra solución para tus variables de entorno,
adelante y haz eso en su lugar.

```
# .Renviron
GOOGLE_CLIENT_ID=<tu-id-de-cliente>
GOOGLE_CLIENT_SECRET=<tu-secreto-de-cliente>
APP_URL=<tu-url-de-la-aplicación>
```

Ahora esta será nuestra aplicación:

```r
# app.R
library(shiny)
library(tapLock)

ui <- fluidPage(
  sidebarLayout(
    sidebarPanel(
      # Un mensaje de bienvenida al usuario
      h3(textOutput("welcome")),
      sliderInput("n", "Número de observaciones", 1, 100, 50)
    ),
    mainPanel(
      # Un histograma de la distribución generada
      plotOutput("plot")
    )
  )
)

server <- function(input, output, session) {

  output$plot <- renderPlot({
    hist(rnorm(input$n))
  })

  output$welcome <- renderText({
    paste(
      "Bienvenido",
      # Aquí extraemos el campo "name" del token de autenticación
      get_token_field(token(), "name"),
      "a la aplicación!"
    )
  })

}

# Obtenemos el id de cliente y el secreto del cliente de las variables de entorno
client_id <- Sys.getenv("GOOGLE_CLIENT_ID")
client_secret <- Sys.getenv("GOOGLE_CLIENT_SECRET")
app_url <- Sys.getenv("APP_URL")

# Creamos una nueva configuración de autenticación para Google
auth_config <- new_google_config(
  client_id = client_id,
  client_secret = client_secret,
  app_url = app_url
)

# En lugar de usar shinyApp(), usamos tapLock::sso_shiny_app()
sso_shiny_app(auth_config, ui, server)
```
## Paso 5: Desplegar la aplicación

Tenemos una aplicación, ahora necesitamos desplegarla. El método exacto para
desplegar la aplicación dependerá de tu entorno, sistema operativo y otros factores. Para este ejemplo usaremos Docker,
sin embargo, puedes usar servicios de Systemd, servicios de Windows, o
cualquier otro método que prefieras. Docker es simplemente una forma conveniente
de desplegar la aplicación ya que cualquiera que use Linux, Mac o Windows
puede utilizarlo.

Usaremos [`faucet`](https://ixpantia.github.io/faucet/), un servidor web muy ligero que gestiona aplicaciones Shiny y APIs Plumber
con replicación integrada y balanceo de carga. También es muy fácil
de usar y configurar.

Primero, crearemos un `Dockerfile`:

```dockerfile
# Las etiquetas de faucet vienen en la forma
# de faucet:<versión>-r<versión-de-r>
# En este ejemplo usaremos etiqueta `r4.3`
# la cual trae la versión más reciente de
# faucet y R 4.0.3
FROM ixpantia/faucet:r4.3

# Instalamos los paquetes requeridos
RUN Rscript -e "install.packages(c('shiny', 'tapLock'))"

# Copiamos el archivo app.R a la imagen
# en el directorio de trabajo actual
COPY --chown=faucet:faucet ./app.R app.R

USER faucet
```

> **Nota**: A partir de ahora, asumiremos que estás trabajando en tu
> máquina virtual, o al menos donde pretendes desplegar la aplicación.
> Puedes continuar probando la aplicación localmente si has configurado
> el ID de cliente OAuth para usar `localhost` como el origen y
> URI de redirección. Expondremos el puerto 80 cuando ejecutemos el
> contenedor, si quieres cambiar esto, tendrás que
> configurar el ID de cliente OAuth para usar el puerto correcto.

Ahora construiremos la imagen:

```bash
sudo docker build -t my-shiny-app .
```

Antes de ejecutar el contenedor, recuerda que necesitamos establecer las
variables de entorno. Para este ejemplo, crearemos un
archivo llamado `.env` en cualquier lugar del servidor, asumiremos
que ejecutas el siguiente comando en el mismo directorio que el
archivo `.env`. Si no, cambia la ruta a la ruta absoluta
del archivo `.env`.

```
sudo docker run --restart unless-stopped --env-file .env -p 80:3838 my-shiny-app
```

Aquí estamos ejecutando el contenedor con las variables de entorno
disponibles en el archivo `.env`, y estamos exponiendo el puerto 80 en
el host al puerto 3838 en el contenedor. Si quieres usar
otro puerto, cambia el primer número al puerto que quieras
usar.

Ahora deberías poder acceder a tu aplicación en `http://<tu-dominio>`.
Si quieres usar SSL, querrás usar un proxy inverso como
Nginx o Apache. Consulta
[Shiny Server: Running with a Proxy](https://support.posit.co/hc/en-us/articles/213733868-Running-Shiny-Server-with-a-Proxy)
para ejemplos sobre cómo hacer esto. También puede leer
[faucet: Con Proxy Inverso](https://ixpantia.github.io/faucet/es/reverse_proxy/)
en la documentación de faucet.

## Paso 6: Ajustando nuestro despliegue

Nuestro despliegue actualmente utiliza la configuración base de faucet,
que en su mayoría es excelente. Sin embargo, es posible que quieras personalizar
aspectos como el número de trabajadores (el número de instancias concurrentes de Shiny), si estás utilizando un proxy inverso, probablemente
quieras usar los encabezados `X-Forwarded-For` o `X-Real-IP` para obtener la dirección IP real del usuario, y otros aspectos del despliegue.

### Número de trabajadores

Por defecto, faucet utilizará el número de núcleos de CPU como el
número de trabajadores. Esto suele ser un buen valor predeterminado, sin embargo,
puedes querer cambiar esto dependiendo de tu aplicación. Por
ejemplo, si tu aplicación es muy intensiva en CPU, es posible que quieras
usar menos trabajadores para evitar sobrecargar la CPU. Si tu
aplicación es muy intensiva en I/O, es posible que quieras usar más trabajadores
para evitar bloquear a los trabajadores.

Para cambiar el número de trabajadores, puedes usar la variable de entorno `FAUCET_WORKERS`. Por ejemplo, si quieres usar 4 trabajadores,
puedes cambiar el archivo `.env` de la siguiente manera:

```
GOOGLE_CLIENT_ID=<tu-id-de-cliente>
GOOGLE_CLIENT_SECRET=<tu-secreto-de-cliente>
APP_URL=<tu-url-de-la-aplicación>
FAUCET_WORKERS=4
```

### Usando un proxy inverso

Cuando usas un proxy inverso, querrás usar los
encabezados `X-Forwarded-For` o `X-Real-IP` para obtener la verdadera
dirección IP del usuario. Esto se debe a que la dirección IP
del usuario será la dirección IP del proxy inverso,
no la dirección IP del usuario. Esto es importante si
quieres registrar correctamente y equilibrar la carga de tu aplicación.

Para usar los encabezados `X-Forwarded-For` o `X-Real-IP`, puedes
usar la variable de entorno `FAUCET_IP_FROM`.

Por ejemplo, si estás usando Nginx, puedes agregar lo
siguiente a tu bloque `location`:

```
proxy_set_header    X-Real-IP $remote_addr;
proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
```

Ahora puedes cambiar el archivo `.env` de la siguiente manera:

```
GOOGLE_CLIENT_ID=<tu-id-de-cliente>
GOOGLE_CLIENT_SECRET=<tu-secreto-de-cliente>
APP_URL=<tu-url-de-la-aplicación>
FAUCET_IP_FROM=x-real-ip
```

## Paso 7: Permitiendo que nuestra aplicación se ejecute en segundo plano

Ahora que tenemos nuestra aplicación en funcionamiento, queremos asegurarnos de que
se mantenga en funcionamiento incluso si cerramos nuestro terminal. Para esto
ejecutaremos el contenedor en modo desacoplado y usaremos `docker compose` para gestionar el contenedor.

Primero, crearemos un archivo `docker-compose.yml`:

```yaml
version: "3.9"
services:
  my-shiny-app:
    image: my-shiny-app
    restart: unless-stopped
    env_file: .env
    ports:
      - "80:3838"
```

Ahora podemos ejecutar el contenedor en modo desacoplado:

```bash
sudo docker compose up -d
```

Ahora puedes cerrar tu terminal y el contenedor
seguirá ejecutándose.

## Conclusión

Ahora tienes una aplicación Shiny pública protegida
por autenticación de Google. Puedes compartir la URL con
cualquiera que quieras y podrán acceder a la aplicación
siempre y cuando sean agregados como usuarios de prueba en la pantalla de consentimiento OAuth.

O si tu aplicación es interna, puedes compartir la URL con
cualquiera en tu organización y podrán
acceder a la aplicación siempre y cuando sean parte de tu
organización.

Podrías mejorar este despliegue añadiendo SSL, utilizando
un proxy inverso, ajustando el número de trabajadores,
y otros aspectos del despliegue. Sin embargo, este
es un buen punto de partida para una aplicación Shiny pública.
