# Arquitectura Modular de Terraform

Este proyecto ha sido refactorizado a una arquitectura modular de Terraform para mejorar la organización, reutilización y mantenibilidad del código.

## Estructura de Módulos

### 1. `modules/gcp-infrastructure/`

**Propósito**: Gestiona toda la infraestructura de Google Cloud Platform
**Recursos incluidos**:

- APIs de Google Cloud (Container, Compute)
- VPC y Subred
- Clúster GKE
- Node Pool

**Variables de entrada**:

- `project_id`: ID del proyecto de GCP
- `region`: Región de GCP
- `zone`: Zona específica
- `cluster_name`: Nombre del clúster
- `gke_num_nodes`: Número de nodos

**Outputs**:

- Información del clúster (nombre, endpoint, certificado)
- Datos de red (VPC, subred)
- Datos del node pool

### 2. `modules/kubernetes/`

**Propósito**: Configuración base de Kubernetes
**Recursos incluidos**:

- Namespace de Kubernetes
- ConfigMaps de configuración general
- ConfigMaps específicos para Cloud Config

**Variables de entrada**:

- `namespace`: Nombre del namespace
- `cluster_dependency`: Dependencia del clúster

**Outputs**:

- Nombre del namespace
- Nombres de los ConfigMaps

### 3. `modules/monitoring/`

**Propósito**: Servicios de monitoring y observabilidad
**Recursos incluidos**:

- Deployment de Zipkin
- Service de Zipkin

**Variables de entrada**:

- `namespace`: Namespace de Kubernetes
- `namespace_dependency`: Dependencia del namespace
- `zipkin_config`: Configuración completa de Zipkin

**Outputs**:

- Nombre y tipo del servicio de Zipkin

### 4. `modules/microservices/`

**Propósito**: Deployments y Services de los microservicios de la aplicación
**Recursos incluidos**:

- Deployments genéricos para cada microservicio
- Services genéricos para cada microservicio
- Configuración específica para Cloud Config (volumes)

**Variables de entrada**:

- `namespace`: Namespace de Kubernetes
- `project_id`: ID del proyecto para las imágenes
- `container_registry_hostname`: Hostname del registry
- `image_tag`: Tag de las imágenes
- `config_map_name`: Nombre del ConfigMap principal
- `cloud_config_map_name`: Nombre del ConfigMap de Cloud Config
- `microservices`: Mapa completo de configuración de microservicios
- `dependencies`: Dependencias de otros módulos

**Outputs**:

- Lista de servicios creados
- Tipos de servicio de cada microservicio
- Lista de deployments creados

## Flujo de Dependencias

```
GCP Infrastructure → Kubernetes Base → Monitoring → Microservices
```

1. **GCP Infrastructure**: Se crea primero la infraestructura base (VPC, GKE)
2. **Kubernetes Base**: Se configura el namespace y ConfigMaps
3. **Monitoring**: Se despliega Zipkin para trazabilidad
4. **Microservices**: Se despliegan todos los microservicios

## Ventajas de la Arquitectura Modular

### ✅ **Reutilización**

- Los módulos pueden reutilizarse en diferentes entornos (dev, staging, prod)
- Cada módulo es independiente y configurable

### ✅ **Mantenibilidad**

- Código organizado por responsabilidades
- Cambios aislados por módulo
- Facilidad para testing individual

### ✅ **Escalabilidad**

- Fácil agregar nuevos microservicios
- Posibilidad de versionar módulos
- Composición flexible de infraestructura

### ✅ **Legibilidad**

- Estructura clara y lógica
- Separación de concerns
- Documentación específica por módulo

## Uso

### Despliegue Completo

```bash
terraform init
terraform plan
terraform apply
```

### Despliegue por Fases

```bash
# Solo infraestructura
terraform apply -target=module.gcp_infrastructure

# Agregar Kubernetes base
terraform apply -target=module.kubernetes_base

# Agregar monitoring
terraform apply -target=module.monitoring

# Finalmente microservicios
terraform apply -target=module.microservices
```

### Modificaciones Específicas

```bash
# Solo cambios en microservicios
terraform apply -target=module.microservices

# Solo cambios en monitoring
terraform apply -target=module.monitoring
```

## Migración desde la Arquitectura Anterior

La refactorización mantiene la misma funcionalidad pero organizada en módulos:

- **Antes**: Todo en `main.tf` y `kubernetes.tf`
- **Después**: Distribuido en 4 módulos especializados

Los archivos de variables y outputs principales se mantienen para preservar la compatibilidad con scripts y CI/CD existentes.

## Próximos Pasos

1. **Versionado de Módulos**: Implementar tags para versionar módulos
2. **Módulos Remotos**: Publicar módulos en un registry privado
3. **Testing**: Implementar tests automatizados para cada módulo
4. **Environments**: Crear configuraciones específicas por entorno
