---

## 🛠️ Reporte Técnico de Soporte N3: Resolución del Incidente

### 1. Preparación e Instalación del Entorno
Para cumplir con los prerrequisitos del despliegue, se preparó la infraestructura local en Windows mediante:
* **Activación de WSL 2:** Configuración del Subsistema de Windows para Linux y la plataforma de máquina virtual.
* **Docker Desktop:** Instalación del entorno y verificación del estado activo del motor nativo (*Engine Running*).

---

### 2. Diagnóstico y Corrección de Errores (Paso a Paso)

Durante el despliegue de la infraestructura como código, se identificaron y solucionaron **tres fallos críticos** que impedían la disponibilidad del servicio:

#### **Fallo 1: Aborto del Despliegue por Insuficiencia de Memoria (`lappiz-api`)**
* **Síntoma:** Al ejecutar `docker-compose up -d`, el demonio de Docker arrojó el error: `Error response from daemon: Minimum memory limit allowed is 6MB`.
* **Causa Raíz (RCA):** El archivo original definía un límite de recursos de `memory: 5M` para el microservicio de la API. Docker exige un mínimo estricto de 6MB por contenedor para inicializar procesos aislados.
* **Solución:** Se editó el archivo `docker-compose.yml` elevando la restricción a un umbral operativo estándar de **512M**.

#### **Fallo 2: Error de Bucle Local en la Red (`DB_HOST`)**
* **Síntoma:** La API de Node.js no lograba establecer comunicación con la base de datos relacional.
* **Causa Raíz (RCA):** La variable de entorno `DB_HOST` estaba apuntando a `'localhost'`. En entornos dockerizados, `localhost` resuelve al propio contenedor aislado y no a la red compartida del stack.
* **Solución:** Se modificó la variable en el `docker-compose.yml` para apuntar a `'database'`, utilizando el DNS interno de la red de Docker Compose para enlazar con el contenedor de PostgreSQL.

#### **Fallo 3: Enrutamiento Erróneo del Proxy Inverso (`nginx.conf`)**
* **Síntoma:** El navegador no lograba conectar con la aplicación (Error 502/503) a pesar de tener los contenedores en estado *Up*.
* **Causa Raíz (RCA):** El bloque `upstream` en el archivo `nginx.conf` enviaba las peticiones al puerto `8080` (`server api-service:8080`), mientras que la API de Node.js estaba configurada mediante `target_output` para escuchar en el puerto **4500**.
* **Solución:** Se corrigió el archivo de configuración de Nginx cambiando el puerto al valor real (**4500**) y se reinició el contenedor del proxy (`docker-compose restart nginx-proxy`).

---

### 3. Verificación de Éxito
Tras corregir la configuración de la infraestructura, todos los servicios se estabilizaron exitosamente. Al consultar el puerto público asignado (`http://localhost:8080`), el proxy inverso procesa y sirve correctamente la respuesta de la API, confirmando la resolución definitiva del incidente:

> **Resultado:** *Ingeniero de Soporte - Has resuelto el incidente.*

---

#### **Plus Implementado: Orquestación y Healthcheck de Dependencias**
* **Solución Avanzada:** Se implementó una directiva `healthcheck` nativa mediante el comando `pg_isready` en el contenedor de PostgreSQL. 
* **Control de Flujo:** Se condicionó el arranque de `api-service` utilizando `depends_on` con la regla `condition: service_healthy`. Esto garantiza de forma estricta que la API de Node.js no iniciará su proceso de escucha hasta que el motor de la base de datos esté completamente operativo y aceptando conexiones en red.