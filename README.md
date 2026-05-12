# Enterprise GitOps & CI/CD Orchestration on OpenShift

Este repositorio centraliza la orquestación, automatización y los manifiestos de infraestructura para una aplicación Full-Stack distribuida. El objetivo principal es demostrar un flujo de entrega continua (CI/CD) profesional utilizando herramientas nativas de Kubernetes en un entorno de **Red Hat OpenShift**.

---

## 🏗️ Arquitectura del Sistema

La solución se basa en una arquitectura de microservicios desacoplada, donde la seguridad, la conectividad y el aislamiento se gestionan a nivel de infraestructura:

![Arquitectura Técnica del Proyecto](./assets/arquitectura.png)

### Ecosistema de Aplicaciones
El código fuente de los componentes se mantiene en repositorios independientes para facilitar el escalado y la mantenibilidad:

* **Frontend Service:** [demo-web](https://github.com/ssh-jmartinez/demo-web.git) - Host estático basado en Nginx Unprivileged que integra las reglas de Reverse Proxy.
* **Backend Service:** [demo-backend](https://github.com/ssh-jmartinez/demo-backend.git) - API REST en Node.js encargada del procesamiento de datos.
* **Database:** Instancia persistente de PostgreSQL gestionada mediante **Persistent Volume Claims (PVC)**.

---

## 🛡️ Seguridad Proactiva y Hardening de Red

A diferencia de despliegues convencionales, este proyecto implementa un modelo de **Seguridad de Confianza Cero (Zero Trust)** mediante **Kubernetes Network Policies**. La seguridad no reside solo en el código, sino en la infraestructura que lo rodea:

### 1. Micro-segmentación de Red
Se ha implementado un aislamiento granular para reducir la superficie de ataque:
* **Default Deny All:** Se bloquea todo el tráfico entrante al namespace por defecto. Ningún servicio es accesible a menos que se defina una regla explícita.
* **Aislamiento de Base de Datos:** La base de datos PostgreSQL solo acepta tráfico proveniente del Pod de Backend. Ni el Frontend ni otros servicios externos pueden establecer conexión directa con la capa de datos.
* **Protección del Ingress:** El tráfico externo está restringido; solo se permite la entrada desde el Router oficial de OpenShift hacia los puertos específicos del Frontend.

### 2. Gestión Segura de Secretos (Sealed Secrets)
Se utilizó **Bitnami Sealed Secrets** para gestionar credenciales de forma profesional.
* **Por qué:** Permite cifrar secretos de forma asimétrica para que solo el controlador del clúster pueda descifrarlos, posibilitando el almacenamiento seguro de credenciales en este repositorio Git sin riesgos de exposición.

---

## ⚙️ Estrategia de Entrega Basada en GitOps

Se optó por un modelo de **Operaciones Basadas en Git (GitOps)** para garantizar que el clúster sea siempre un reflejo fiel del código fuente:

### 1. Conciliación de Estado con Argo CD
Toda la configuración reside en este repositorio utilizando el patrón **App-of-Apps**. Argo CD detecta cualquier desviación manual ("drift") y sincroniza el entorno automáticamente a su estado deseado.

### 2. Punto de Entrada Unificado (Reverse Proxy)
El Frontend actúa como un Proxy Inverso para el Backend. Esto simplifica el DNS, elimina la complejidad de configurar políticas de **CORS** en el código y permite exponer una única **Route** segura hacia el exterior.

### 3. CI/CD Nativo con Tekton
Los procesos de construcción se ejecutan dentro del clúster mediante **Tekton Pipelines** y **Buildah**, garantizando construcciones seguras, eficientes y sin necesidad de demonios de Docker externos.

---

## 🚀 Estructura del Repositorio

Este repositorio separa la lógica de automatización de los recursos de Kubernetes para una gestión clara y escalable:

* **`/k8s-manifests`**:
    * **`/frontend` & `/backend`**: Manifiestos core de las aplicaciones (Deployments, Services).
    * **`/database`**: Configuración de persistencia, valores de DB y secretos sellados.
    * **`/security`**: Políticas de red (**NetworkPolicies**) para el blindaje del tráfico interno.
* **`/argocd-apps`**: Orquestación de aplicaciones mediante Argo CD y gestión jerárquica de recursos.
* **`/pipelines`**: Definiciones de Tasks y Pipelines de Tekton para la automatización del CI.

---

## 💻 Tecnologías Core
* **Orquestación:** Red Hat OpenShift
* **GitOps:** Argo CD
* **Seguridad de Red:** Kubernetes Network Policies (Zero Trust)
* **CI Pipelines:** Tekton (Buildah)
* **Gestión de Secretos:** Bitnami Sealed Secrets
* **Stack:** Nginx (Unprivileged), Node.js, PostgreSQL

---

## 🛠️ Despliegue (One-Click Deployment)

Gracias al patrón **App-of-Apps**, toda la infraestructura (Frontend, Backend, DB y capas de Seguridad) se autoprovisiona de forma atómica:

```bash
argocd app create -f argocd-apps/root-app.yaml
```

---

## 🚀 Estructura del Repositorio de Orquestación

Este repositorio está organizado para separar la lógica de automatización de los recursos de Kubernetes:

* **`/k8s-manifests`**: Contiene los Deployments, Services, Routes y definiciones de **SealedSecrets**.
* **`/argocd-apps`**: Definiciones de las aplicaciones de Argo CD (Root App y Templates) para la gestión del ciclo de vida jerárquico.
* **`/pipelines`**: Definiciones de Tasks y Pipelines de Tekton para el flujo de CI.

---

## 💻 Tecnologías Core
* **Orquestación:** Red Hat OpenShift
* **GitOps:** Argo CD
* **CI Pipelines:** Tekton
* **Container Build:** Buildah
* **Seguridad:** Bitnami Sealed Secrets
* **Stack:** Nginx, Node.js, PostgreSQL