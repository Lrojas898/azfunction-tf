
# Proceso de Deployment de Azure Function con Terraform - Luis Rojas

## Introducción

Este README explica el proceso paso a paso para configurar y desplegar una Azure Function utilizando Terraform en la suscripción de Luis Rojas. El proceso se basa en la actividad realizada en clase de Ingeniería de Software V en la Universidad ICESI. Se incluyen los comandos principales y descripciones de cada etapa, enfocándonos exclusivamente en la suscripción de Luis Rojas.

Se asume que se tiene instalado Azure CLI y Terraform en el entorno local. La función se despliega como una Azure Function App con un trigger HTTP.

## Requisitos Previos

- Cuenta de Azure con suscripción activa (en este caso, "Pay-As-You-Go").
- Azure CLI instalado y configurado.
- Terraform instalado.
- Archivos de configuración de Terraform (.tf) preparados para definir recursos como el resource group, storage account, function app, etc.

## Pasos del Proceso

### 1. Configuración de la Suscripción y Terraform Plan

En esta etapa, se inicia sesión en Azure, se selecciona la suscripción y se inicializa Terraform para planificar los cambios.

Comandos ejecutados:

```bash
az login
# (Se abre el navegador para autenticación)

az account set --subscription "Pay-As-You-Go"
# (Selecciona la suscripción específica)

terraform init
# (Inicializa el directorio de Terraform, descarga providers y módulos)

terraform plan
# (Genera un plan de ejecución mostrando los recursos que se crearán/modificarán)
```

El output del `terraform plan` muestra un resumen de los recursos a crear, como el resource group, storage account y la function app.

### 2. Terraform Plan con Nombre de la Función

Se ejecuta un plan específico pasando el nombre de la función como variable para personalizar el deployment.

Comando ejecutado:

```bash
terraform plan -var "app_name=terraform-function-app-lrojas"
```

El output detalla los recursos que se añadirán, como:
- azurerm_resource_group
- azurerm_storage_account
- azurerm_application_insights
- azurerm_function_app
- Entre otros (aproximadamente 8 recursos nuevos).

No se aplican cambios aún; solo se visualiza el plan.

### 3. Confirmación de Apply

Se confirma y aplica el deployment. Terraform pregunta por confirmación antes de proceder.

Comando ejecutado:

```bash
terraform apply -var "app_name=terraform-function-app-lrojas"
```

Durante la ejecución:
- Se muestra el plan nuevamente.
- Pregunta: "¿Do you want to perform these actions?" (Se responde con "yes").

Terraform comienza a crear los recursos en Azure.

### 4. Culminación Satisfactoria Apply

El apply se completa exitosamente. Terraform crea todos los recursos definidos.

Output típico:
- Recursos creados (ej.: 8 added, 0 changed, 0 destroyed).
- Mensaje: "Apply complete!"

En este punto, la Azure Function está desplegada y lista en el portal de Azure.

### 5. Prueba en HTTP

Se prueba la función desplegada accediendo a su endpoint HTTP.

Comando o acción ejecutada:

```bash
curl "[https://terraform-function-app-lrojas.azurewebsites.net/api/HttpTrigger1?name=Luis](https://lrojasfirstfunction.azurewebsites.net/api/lrojasfirstfunction)"
# (Respuesta esperada: "Hello, Luis" o similar, dependiendo de la implementación de la función)
```

Alternativamente, se puede probar en un navegador visitando la URL con el parámetro `name`. Esto verifica que la función responde correctamente a solicitudes HTTP.

## Notas Adicionales

- **Variables de Terraform**: Se utiliza `-var "app_name=..."` para personalizar nombres y evitar conflictos.
- **Limpieza**: Para destruir los recursos, usa `terraform destroy -var "app_name=terraform-function-app-lrojas"`.
- **Errores Comunes**: Asegúrate de que la suscripción tenga permisos suficientes y que no haya conflictos de nombres en Azure.
- **Referencias**: Consulta la documentación oficial de [Terraform para Azure Functions](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/function_app) para más detalles sobre los recursos.

Este proceso demuestra el uso de Infrastructure as Code (IaC) con Terraform para automatizar deployments en Azure. Si necesitas ajustes o más detalles, revisa los archivos .tf originales.


















### **Infraestructura como código**

- **Utilizar archivos de definición**: Todas las herramientas de infraestructura como código tienen un formato propio para definir la infraestructura.
- **Autodocumentación de procesos y sistemas**: Al utilizar el enfoque de infraestructura como código, podemos reutilizar el código. Es importante que este esté documentado adecuadamente para que otros usuarios comprendan el propósito y funcionamiento del módulo.
- **Versionar todo**: Esto nos permite rastrear los cambios realizados. Si se comete un error, podemos retroceder a una versión estable.
- **Preferir cambios pequeños**: Realizar cambios pequeños para evitar grandes impactos.
- **Mantener los servicios continuamente disponibles**: Garantizar la disponibilidad continua es clave en la infraestructura.

### **Beneficios de la infraestructura como código**

- **Creación rápida y bajo demanda**: Con un único archivo de definición de infraestructura que almacena todas nuestras configuraciones, podemos crear múltiples veces la infraestructura sin necesidad de rehacer todo desde el principio.
- **Automatización**: Una vez creado el archivo de definición, podemos usar herramientas de **continuous integration** para automatizar la infraestructura.
- **Visibilidad y trazabilidad**: El versionamiento de la infraestructura como código permite una mayor visibilidad y trazabilidad, ya que todos los cambios quedan registrados.
- **Ambientes homogéneos**: Podemos crear varios ambientes a partir del mismo archivo de definición, cambiando únicamente algunos parámetros.

---

### **Mejores prácticas**

- **Modularidad**: Es recomendable dividir la infraestructura en módulos reutilizables para facilitar su mantenimiento y escalabilidad.
- **Mantener las configuraciones centralizadas**: Utilizar variables y archivos de configuración para gestionar parámetros y evitar valores "hardcoded".
- **Manejo seguro del estado**: Almacenar el archivo `terraform.tfstate` de manera remota (por ejemplo, en un bucket S3 con bloqueo de versión) para evitar problemas en equipos distribuidos.
- **Revisiones de código y pull requests**: Antes de aplicar cambios importantes en la infraestructura, hacer revisiones mediante pull requests para asegurar que los cambios han sido revisados por otros.

### **Ambientes**

Terraform permite la creación de múltiples ambientes (dev, stage, prod) con diferentes configuraciones. Puedes gestionar estos ambientes utilizando archivos `.tfvars` específicos para cada entorno.

- **Ambiente de desarrollo (dev)**: Se recomienda utilizar recursos más pequeños y económicos en este ambiente para reducir costos.
- **Ambiente de producción (prod)**: Aquí es importante configurar instancias y recursos con redundancia y alta disponibilidad.
  
Ejemplo de estructura para gestionar ambientes:

```bash
├── main.tf
├── variables.tf
├── dev.tfvars
├── prod.tfvars
```

Al aplicar los cambios para un ambiente en específico, puedes ejecutar:

```bash
terraform apply --var-file="dev.tfvars"
```

### **Automatización con CI/CD**

Integrar Terraform en un flujo de CI/CD es una excelente práctica para automatizar la gestión de la infraestructura. Puedes utilizar herramientas como Jenkins, GitLab CI, o GitHub Actions para automatizar el proceso de despliegue y validación.

Ejemplo de un pipeline básico en GitLab CI:

```yaml
stages:
  - validate
  - plan
  - apply

validate:
  script:
    - terraform init
    - terraform validate

plan:
  script:
    - terraform plan

apply:
  script:
    - terraform apply --auto-approve
```

Este pipeline primero inicializa el entorno, luego valida la configuración, y finalmente aplica los cambios automáticamente.

### **Seguridad**

- **Manejo seguro de credenciales**: Nunca almacenar credenciales en el código fuente. Utilizar herramientas como **AWS Secrets Manager** o **HashiCorp Vault** para gestionar los secretos de manera segura.
- **Control de acceso basado en roles (IAM)**: Asignar roles y permisos específicos a los recursos de Terraform mediante políticas de IAM para restringir el acceso según sea necesario.
- **Cifrado de datos**: Utilizar cifrado en reposo y en tránsito para proteger los datos sensibles, como el uso de **KMS (Key Management Service)** de AWS.
- **Seguridad en el estado**: Si almacenas el archivo `terraform.tfstate` en un bucket S3, asegúrate de habilitar el cifrado y el control de versiones para evitar modificaciones no autorizadas.

---

### **Manejo de variables en Terraform**

Para hacer escalable y reutilizable el archivo de definición de infraestructura, se recomienda no usar valores "hardcoded". Terraform permite crear variables de los siguientes tipos:

- **string**
- **number**
- **boolean**
- **map**
- **list**

Si no se declara un tipo, el valor por defecto será `string`. Sin embargo, es una buena práctica especificar el tipo de la variable.

Ejemplo de definición de variables:

```terraform
variable "ami_id" {
  type        = string
  description = "ID de la AMI"
}

variable "instance_type" {
  type        = string
  description = "Tipo de instancia"
}

variable "tags" {
  type        = map
  description = "Etiquetas para la instancia"
}
```

### **Asignar valores a las variables**

Los valores de las variables se pueden asignar de tres maneras:

1. Utilizando variables de entorno.
2. Pasándolos como argumentos en la línea de comandos.
3. Mediante un archivo `.tfvars` con formato `key = value`.

Ejemplo de archivo `.tfvars`:

```terraform
ami_id        = "ami-0ca0c67309196175e"
instance_type = "t2.micro"
tags = {
  Name       = "devops-tf"
  Environment = "Dev"
}
```

Para usar este archivo con variables:

```bash
terraform apply --var-file="dev.tfvars"
```

### **Destruir la infraestructura**

Para eliminar la infraestructura creada, se puede utilizar:

```bash
terraform destroy --var-file="dev.tfvars" -auto-approve
```
