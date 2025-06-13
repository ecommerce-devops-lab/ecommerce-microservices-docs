# Modular Terraform Architecture

This project has been refactored into a modular Terraform architecture to improve organization, reusability, and code maintainability.

## Module Structure

### 1. `modules/gcp-infrastructure/`

**Purpose**: Manages all Google Cloud Platform infrastructure
**Included resources**:

* Google Cloud APIs (Container, Compute)
* VPC and Subnet
* GKE Cluster
* Node Pool

**Input variables**:

* `project_id`: GCP project ID
* `region`: GCP region
* `zone`: Specific zone
* `cluster_name`: Cluster name
* `gke_num_nodes`: Number of nodes

**Outputs**:

* Cluster information (name, endpoint, certificate)
* Network data (VPC, subnet)
* Node pool data

### 2. `modules/kubernetes/`

**Purpose**: Basic Kubernetes configuration
**Included resources**:

* Kubernetes Namespace
* General configuration ConfigMaps
* Specific ConfigMaps for Cloud Config

**Input variables**:

* `namespace`: Namespace name
* `cluster_dependency`: Cluster dependency

**Outputs**:

* Namespace name
* ConfigMap names

### 3. `modules/monitoring/`

**Purpose**: Monitoring and observability services
**Included resources**:

* Zipkin Deployment
* Zipkin Service

**Input variables**:

* `namespace`: Kubernetes namespace
* `namespace_dependency`: Namespace dependency
* `zipkin_config`: Full Zipkin configuration

**Outputs**:

* Zipkin service name and type

### 4. `modules/microservices/`

**Purpose**: Deployments and Services of application microservices
**Included resources**:

* Generic Deployments for each microservice
* Generic Services for each microservice
* Specific configuration for Cloud Config (volumes)

**Input variables**:

* `namespace`: Kubernetes namespace
* `project_id`: Project ID for images
* `container_registry_hostname`: Registry hostname
* `image_tag`: Image tag
* `config_map_name`: Main ConfigMap name
* `cloud_config_map_name`: Cloud Config ConfigMap name
* `microservices`: Full microservice configuration map
* `dependencies`: Dependencies from other modules

**Outputs**:

* List of created services
* Service types per microservice
* List of created deployments

## Dependency Flow

```
GCP Infrastructure → Kubernetes Base → Monitoring → Microservices
```

1. **GCP Infrastructure**: Base infrastructure is created first (VPC, GKE)
2. **Kubernetes Base**: Namespace and ConfigMaps are configured
3. **Monitoring**: Zipkin is deployed for traceability
4. **Microservices**: All microservices are deployed

## Advantages of Modular Architecture

### ✅ **Reusability**

* Modules can be reused across different environments (dev, staging, prod)
* Each module is independent and configurable

### ✅ **Maintainability**

* Code organized by responsibility
* Isolated changes per module
* Easy individual testing

### ✅ **Scalability**

* Easy to add new microservices
* Possibility to version modules
* Flexible infrastructure composition

### ✅ **Readability**

* Clear and logical structure
* Separation of concerns
* Specific documentation per module

## Usage

### Full Deployment

```bash
terraform init
terraform plan
terraform apply
```

### Phased Deployment

```bash
# Infrastructure only
terraform apply -target=module.gcp_infrastructure

# Add Kubernetes base
terraform apply -target=module.kubernetes_base

# Add monitoring
terraform apply -target=module.monitoring

# Finally microservices
terraform apply -target=module.microservices
```

### Specific Modifications

```bash
# Only microservices changes
terraform apply -target=module.microservices

# Only monitoring changes
terraform apply -target=module.monitoring
```

## Migration from Previous Architecture

The refactoring maintains the same functionality but organized into modules:

* **Before**: Everything in `main.tf` and `kubernetes.tf`
* **After**: Split across 4 specialized modules

Main variable and output files are preserved to maintain compatibility with existing scripts and CI/CD.

## Next Steps

1. **Module Versioning**: Implement tags to version modules
2. **Remote Modules**: Publish modules in a private registry
3. **Testing**: Implement automated tests for each module
4. **Environments**: Create environment-specific configurations