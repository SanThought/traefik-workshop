# Traefik Workshop Project

Este proyecto es una implementación simple para el taller de Traefik. Integra dos servicios backend (una API Express y un servidor Nginx estático) a través de Traefik. La configuración se realiza mediante archivos de Docker Compose y un archivo estático de configuración de Traefik (`traefik.yml`).

---

## Índice

- [Descripción](#descripción)
- [Estructura del Proyecto](#estructura-del-proyecto)
- [Requisitos Previos](#requisitos-previos)
- [Instrucciones de Instalación](#instrucciones-de-instalación)
- [Ejecución del Proyecto](#ejecución-del-proyecto)
- [Flujo de Tráfico y Middlewares](#flujo-de-tráfico-y-middlewares)
- [Preguntas de Evaluación](#preguntas-de-evaluación)

---

## Descripción

El proyecto demuestra cómo configurar Traefik como un reverse proxy para enrutar el tráfico hacia:

- **API Express**: Un servicio que responde en el puerto 3000 y se protege mediante autenticación básica.
- **Servidor Nginx**: Un servicio estático que responde en el puerto 80.

Traefik se configura para detectar automáticamente los servicios que tengan la etiqueta `traefik.enable=true` en Docker Compose y enrutarlos según las reglas definidas.

---

## Estructura del Proyecto

```
traefik-workshop/
├── traefik.yml # Configuración estática de Traefik
├── docker-compose.yml # Define los servicios: Traefik, API y Nginx
└── api/ # Servicio API en Express
├── Dockerfile # Instrucciones de construcción para la API
├── package.json # Dependencias y scripts de la API
└── server.js # Código fuente del servidor Express
```

---

## Requisitos Previos

- **Docker** y **Docker Compose** instalados en tu máquina.
- Conexión a Internet para descargar las imágenes base.
- (Opcional) Configuración local para resolver dominios:  
  Edita tu archivo `/etc/hosts` e incluye:

127.0.0.1 api.localhost nginx.localhost

---

## Instrucciones de Instalación

1. **Clonar el repositorio o descargar el proyecto.**
  
2. **Navega a la carpeta del proyecto:**
  
  ```bash
  cd traefik-workshop
  ```
  

3. **Construir y levantar los contenedores:**
  
  ```bash
  docker-compose up -d
  ```
  
4. **Verifica que los servicios estén funcionando:**
  
  - Traefik Dashboard: [http://localhost:8080](http://localhost:8080/)
    
  - API: [http://api.localhost](http://api.localhost/) (se requerirá autenticación básica)
    
  - Nginx: [http://nginx.localhost](http://nginx.localhost/)
    

---

## Ejecución del Proyecto

- **Traefik**:  
  Se encarga de detectar los servicios a través de Docker labels y enrutar el tráfico a cada uno.
  
- **API Express**:  
  Desplegada desde el directorio `api/`, responde en el puerto 3000 y está protegida con autenticación básica (credenciales: `admin:password`).
  
- **Servidor Nginx**:  
  Sirve contenido estático y se accede mediante el dominio `nginx.localhost`.
  

Para detener los servicios, ejecuta:

```bash
docker-compose down
```

---

## Flujo de Tráfico y Middlewares

![image](https://github.com/user-attachments/assets/21395868-2de3-436a-9559-abfbd0485ccf)


- **Detección de Servicios:**  
  Traefik monitorea Docker y solo detecta los contenedores que tienen la etiqueta `traefik.enable=true`.
  
- **Enrutamiento:**
  
  - **API:** Las peticiones a `http://api.localhost` se redirigen al contenedor de la API en el puerto 3000.
    
  - **Nginx:** Las peticiones a `http://nginx.localhost` se redirigen al contenedor de Nginx en el puerto 80.
    
- **Middleware de Autenticación:**  
  Se ha configurado un middleware en Traefik para la API que protege el servicio con autenticación básica. Sin este middleware, el servicio sería accesible sin control, pero con él, solo usuarios con las credenciales correctas podrán acceder.
  

---

## Preguntas de Evaluación

**1. ¿Cómo detecta Traefik los servicios configurados en Docker Compose?**  
Traefik escucha el socket Docker y solo toma en cuenta los contenedores con la etiqueta `traefik.enable=true`.

**2. ¿Qué papel juegan los middlewares en la seguridad y gestión del tráfico?**  
Los middlewares actúan como filtros y transformadores del tráfico; en este caso, la autenticación básica protege la API y puede modificar o bloquear peticiones según la configuración.

**3. ¿Cómo se define un router en Traefik, y qué parámetros son esenciales?**  
Un router se define mediante etiquetas en Docker Compose (ej. `traefik.http.routers.api.rule`), indicando condiciones de enrutamiento como `Host` o `PathPrefix`, y se asigna a un entrypoint (por ejemplo, `web`).

**4. ¿Cuál es la diferencia entre un router y un servicio en Traefik?**  
El router decide qué tráfico se redirige a qué contenedor, mientras que el servicio define los detalles de conexión (por ejemplo, el puerto interno) para ese contenedor.

**5. ¿Cómo se pueden agregar reglas de enrutamiento adicionales para diferentes rutas?**  
Agregando nuevas etiquetas a los contenedores, como reglas con `PathPrefix` o combinando condiciones (ej. `Host` y `Path`), lo que permite definir rutas específicas para distintos tipos de tráfico.

---
