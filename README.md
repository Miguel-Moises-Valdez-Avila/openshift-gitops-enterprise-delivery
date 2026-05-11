# Enterprise GitOps & CI/CD Orchestration on OpenShift

Este repositorio centraliza la orquestación, automatización y los manifiestos de infraestructura para una aplicación Full-Stack distribuida. El objetivo principal es demostrar un flujo de entrega continua (CI/CD) profesional utilizando herramientas nativas de Kubernetes en un entorno de **Red Hat OpenShift**.

---

## 🏗️ Arquitectura del Sistema

La solución se basa en una arquitectura de microservicios desacoplada, donde la seguridad y la conectividad se gestionan a nivel de infraestructura:

![Arquitectura Técnica del Proyecto](./assets/arquitectura.png)

### Ecosistema de Aplicaciones
El código fuente de los componentes se mantiene en repositorios independientes para facilitar el escalado y la mantenibilidad:

* **Frontend Service:** [demo-web](https://github.com/ssh-jmartinez/demo-web.git) - Host estático basado en Nginx Unprivileged que integra las reglas de Reverse Proxy.
* **Backend Service:** [demo-backend](https://github.com/ssh-jmartinez/demo-backend.git) - API REST en Node.js encargada del procesamiento de datos.
* **Database:** Instancia persistente de PostgreSQL gestionada mediante **Persistent Volume Claims (PVC)**.

---

## ⚙️ Estrategia de Entrega Basada en GitOps

En lugar de despliegues manuales o imperativos, se optó por un modelo de **Operaciones Basadas en Git (GitOps)** para garantizar la consistencia del entorno:

### 1. Centralización de la Conectividad (Reverse Proxy)
Se decidió utilizar Nginx no solo como servidor de archivos, sino como un **Punto de Entrada Unificado**. 
* **Por qué:** Al redirigir el tráfico `/api` internamente hacia el backend, eliminamos la necesidad de configurar políticas de CORS en el código y simplificamos el DNS, exponiendo un único punto de entrada (Route) hacia el exterior.

### 2. Conciliación de Estado con Argo CD
Toda la configuración del clúster reside en este repositorio de manifiestos utilizando el patrón **App-of-Apps**.
* **Por qué:** Esto garantiza que el clúster siempre refleje la "fuente de verdad" definida en Git. Si un objeto de Kubernetes se modifica manualmente, Argo CD detecta la desviación ("drift") y lo sincroniza automáticamente.

### 3. Pipelines de Construcción Nativos (Tekton)
El proceso de CI se ejecuta dentro del propio clúster mediante **Tekton Pipelines**.
* **Por qué:** Al usar Buildah para la construcción de imágenes, el proceso es más seguro (daemonless) y se integra perfectamente con el registro de imágenes interno de OpenShift.

### 4. Gestión Segura de Secretos (Sealed Secrets)
Se implementó **Bitnami Sealed Secrets** para gestionar credenciales de base de datos de forma segura.
* **Por qué:** Permite cifrar secretos de forma que solo el controlador dentro del clúster pueda descifrarlos. Esto hace posible almacenar información sensible en este repositorio público sin comprometer la seguridad.

---

## 🚀 Despliegue en un solo paso (One-Click Deployment)

Gracias a la implementación del patrón **App-of-Apps**, la infraestructura es completamente autoprovisionable. No es necesario crear cada componente manualmente; el sistema está diseñado para el despliegue automático del ecosistema completo.

Para desplegar toda la infraestructura (Frontend, Backend, Database, Secretos y Pipelines), solo es necesario ejecutar:

```bash
argocd app create -f argocd-apps/root-app.yaml
```

---

## 🚀 Estructura del Repositorio de Orquestación

Este repositorio está organizado para separar la lógica de automatización de los recursos de Kubernetes:

* **`/k8s-manifests`**: Contiene los Deployments, Services, Routes y definiciones de **SealedSecrets**.
* **`/argocd-apps`**: Definiciones de las aplicaciones de Argo CD (Root App y Templates) para la gestión del ciclo de vida jerárquico.
* **`/pipeline`**: Definiciones de Tasks y Pipelines de Tekton para el flujo de CI.

---

## 💻 Tecnologías Core
* **Orquestación:** Red Hat OpenShift
* **GitOps:** Argo CD
* **CI Pipelines:** Tekton
* **Container Build:** Buildah
* **Seguridad:** Bitnami Sealed Secrets
* **Stack:** Nginx, Node.js, PostgreSQL