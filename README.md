# Post-contenido — Unidad 7: Gestión de Tareas con Spring Boot

## Descripción
Repositorio del laboratorio de la Unidad 7 de Programación Web — Séptimo
Semestre. Un único proyecto Spring Boot con dos capas sobre el mismo
TareaService: una vista Thymeleaf (@Controller, parte 1) y una API REST
(@RestController, parte 2).

## Parte 1 — Vista Thymeleaf con @Controller
TareaController expone /tareas con filtrado por @RequestParam (prioridad,
completada), formularios validados con @Valid + BindingResult, y las
acciones completar/eliminar implementadas como POST (no GET) para no
introducir efectos secundarios en peticiones de solo lectura.

## Parte 2 — API REST con @RestController
TareaApiController expone /api/tareas con los verbos GET, POST, PUT,
PATCH y DELETE, inyectando por constructor la MISMA instancia de
TareaService que usa la Parte 1. ApiErrorHandler traduce los errores de
@Valid en JSON estructurado (400 Bad Request), en lugar de la pantalla
de error HTML por defecto de Spring Boot.

## Decisiones de diseño
- Inyección por constructor (no @Autowired en campo) en ambos
  controladores: mejora la testabilidad y hace explícita la dependencia.
- @FutureOrPresent en lugar de @Future en fechaLimite: permite tareas
  con vencimiento el mismo día de su creación.
- POST (no GET) para completar/eliminar en TareaController: una petición
  GET debe ser segura y no debe modificar estado del servidor.
- PATCH (no PUT) para /api/tareas/{id}/completar: representa una
  actualización parcial de un único campo, no el reemplazo del recurso.
- Manejo de validación separado por capa: BindingResult para la vista
  HTML, @RestControllerAdvice para la API JSON — cada una responde en el
  formato que le corresponde.
- Persistencia en memoria (Map en TareaService) en lugar de JPA/Hibernate:
  la persistencia real se introduce formalmente en la Unidad 8.

## Tabla de endpoints de la API

| Método | URL                          | Código éxito  | Código error | Descripción                                          |
|--------|------------------------------|---------------|--------------|-------------------------------------------------------|
| GET    | /api/tareas                  | 200 OK        | —            | Lista de tareas, filtrable con ?prioridad= y/o ?completada= |
| GET    | /api/tareas/{id}             | 200 OK        | 404          | Retorna la tarea con el ID indicado                   |
| POST   | /api/tareas                  | 201 Created   | 400          | Crea una tarea con el JSON del body                   |
| PUT    | /api/tareas/{id}             | 200 OK        | 404 / 400    | Reemplaza todos los campos de la tarea existente      |
| PATCH  | /api/tareas/{id}/completar   | 200 OK        | 404          | Actualización parcial: marca completada = true        |
| DELETE | /api/tareas/{id}             | 204 No Content| 404          | Elimina la tarea con el ID indicado                   |

## Cómo compilar y ejecutar
1. Clonar el repositorio: `git clone https://github.com/Ashh2222/Arayon-post1-u7-.git`
2. Abrir la carpeta como proyecto Maven en IntelliJ IDEA
3. Ejecutar `TareasApplication` (o `mvn spring-boot:run`)
4. Vista web: http://localhost:8080/tareas
   API REST: http://localhost:8080/api/tareas (probar con Postman o curl)

## Capturas de pantalla

### Parte 1 — Vista Thymeleaf
![Lista de tareas con filtro aplicado](captures/filtro.png)
![Formulario con título y fecha inválidos](captures/nombre_fecha_invalidas.png)
![Formulario en modo edición con datos prellenados](captures/editar.png)
![Lista con una tarea marcada como completada](captures/completadas.png)

### Parte 2 — API REST (Postman)
![GET con filtro - 200 OK](captures/GET1.png)
![POST tarea válida - 201 Created](captures/POST1.png)
![POST tarea inválida - 400 Bad Request](captures/POST2.png)
![PATCH completar tarea - 200 OK](captures/PATCH1.png)
![DELETE tarea - 204 No Content](captures/DELETE1.png)
![GET tarea eliminada - 404 Not Found](captures/GET2.png)