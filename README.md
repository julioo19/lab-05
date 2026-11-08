# LAB-05: Terraform en Google Cloud

## Evidencia

| Lab | Puntaje | Captura | Carpeta |
|-----|---------|---------|---------|
| GSP233 | XX/100 | GSP233/gsp233.png | GSP233/lab-05-gsp233/ |
| GSP497 | XX/100 | GSP497/gsp497.png | GSP497/lab-05-gsp497/ |
| GSP345 | XX/100 | GSP345/gsp345.png | GSP345/lab-05-gsp345/ |

Evidencia adicional: `GSP233/gsp233-nginx.png`, `GSP233/gsp233-curl-nginx.png`, `GSP233/gsp233-gateway.png` y `GSP497/gsp497-monitor.png`.

## Bitácora

### GSP233 (Kubernetes Load Balancer con Terraform)
**Qué hice.** Instalé Terraform en Cloud Shell, corrí `terraform init` y `apply` sobre el clúster GKE con el servicio nginx y verifiqué que respondiera `Welcome to nginx!` en la IP externa del Service (IP: `X.X.X.X`). Como el `apply` ya había creado todo, saqué el `plan-inicial.txt` desde una copia de los `.tf` sin estado, con los 7 recursos marcados con `+`. La dependencia del `kubernetes_service` con el clúster se ve en `dependencias.txt`, porque el provider `kubernetes` toma `host` y credenciales de `google_container_cluster.default`.
**Qué me costó.** El primer `apply` falló con `project: required field is not set`, porque Cloud Shell no tenía el proyecto configurado. Lo resolví con `gcloud config set project` y `GOOGLE_CLOUD_PROJECT`. Además el editor marcaba un error falso en `resources { limits ... }`, que era solo el esquema del editor y no de Terraform.
**Qué cambiaría.** Guardaría el plan antes del primer `apply`, en vez de reconstruirlo después.

### GSP497 (Cloud Operations for GKE)
**Qué hice.** Creé el Metrics Scope en Monitoring, corrí `make create` (5 recursos), validé con `make validate` y revisé la política `Prometheus mem alloc` en Alerting. Tomé la captura de las métricas de Prometheus y luego corrí `make teardown`, guardando la salida en `destroy.txt`, que termina en `Destroy complete! Resources: 5 destroyed.` y coincide con los 5 recursos de `plan-inicial.txt`.
**Qué me costó.** `make create` hace `init` y `apply` de una vez, así que el plan inicial lo obtuve después con una copia sin estado. También tuve que reinstalar Terraform por el binario faltante en esa sesión.
**Qué cambiaría.** Haría la captura de Monitoring con más margen de tiempo antes del teardown.

### GSP345 (Challenge Lab: infraestructura con Terraform)
**Qué hice.** Armé la estructura de módulos (`instances`, `storage`), importé `tf-instance-1` y `tf-instance-2` (plan con `update in-place`, sin `replace`), creé el bucket y migré el estado al backend `gcs` con prefijo `terraform/state`. Después cambié el tipo de máquina a `e2-standard-2`, agregué y destruí `tf-instance-007067`, usé el módulo `network` del Registry (v10.0.0) con `tf-vpc-157001` y `subnet-01/02`, y creé la regla `tf-firewall` para TCP 80.
**Qué me costó.** Que el `import` coincidiera sin recrear las instancias (imagen `debian-12`, tipo de máquina y red reales) y entender por qué hay que volver a correr `init` al agregar un módulo o un backend.
**Qué cambiaría.** Revisaría cada enunciado antes de escribir el código, porque la versión del provider, la imagen y los nombres cambian por lab.

## Uso de IA

**Qué le pedí.** Guía paso a paso para los tres labs, comandos para guardar los planes (`plan-<n>.txt`, `destroy.txt`), explicaciones de conceptos que me pueden preguntar en la defensa (import, backend, `init`, `-no-color`) y ayuda con errores (proyecto no configurado, instalación de Terraform, push a GitHub rechazado por archivos de más de 100 MB en `.terraform/`).
**Qué me dio.** Bloques de código para cada tarea, la forma de generar un plan inicial desde una copia sin estado, y el `.gitignore` y la rama huérfana para limpiar el historial de Git.
**Qué corregí.** Los valores que la IA propuso de memoria los reemplacé por los del enunciado de cada lab: provider `~> 6.0` (no `5.0.0`), imagen `debian-12` (no `debian-11`) y módulo de red `10.0.0` (no `~> 6.0.0`). También revisé cada `plan` antes de hacer `apply` y confirmé que no hubiera `must be replaced` ni destrucciones inesperadas.
