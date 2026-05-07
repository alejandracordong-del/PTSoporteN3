# Prueba Técnica: Ingeniero de Soporte N3 - Diagnóstico de Microservicios

## Escenario
Se ha reportado una caída crítica en el portal de clientes. El equipo de Nivel 2 escaló el caso indicando que, tras el último despliegue, el servicio no responde correctamente. Tu misión es estabilizar el entorno, identificar las fallas y asegurar la comunicación entre los componentes.

## Arquitectura del Entorno
El sistema consta de tres capas corriendo en contenedores Docker:
1. **nginx-proxy**: Balanceador de carga y punto de entrada (Puerto 8080).
2. **api-service**: Lógica de negocio (Node.js).
3. **database**: Motor de base de datos (PostgreSQL).

---

## Instrucciones para el Candidato

### 1. Preparación
Asegúrate de tener instalado **Docker** y **Docker Compose** y bajar los archivos necesarios de este repositorio.

### 2. Ejecución del Incidente
Inicia el entorno ejecutando el siguiente comando en tu terminal:
```bash
docker-compose up -d
```

### 3. Entrega
1. Debe hacer un Pull request al repositorio para que el propietario del repo pueda ver los cambios.
2. Si desea puede hacer un fork del proyecto.
3. También es válido crear un repo completamente nuevo y subir allí los archivos corregidos. Debe enviar el Link de su repo a la persona que lo ha estado acompañando en el proceso de selección
4. En el archivo readme.md debe subir las notas con la explicación o justificación de los cambios efectuados para que el proyecto pueda correr. Es decir documente cómo ha resuelto el incidente y resalte las partes donde tuvo que hacer las modificaciones.

#### Plus
Agrega un healthcheck para que la API no se conecte a la base de datos sin que este servicio de Postgres esté listo.