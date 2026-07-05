# Enterprise GitOps & CI/CD Orchestration on Red Hat OpenShift

[![Platform](https://img.shields.io/badge/Platform-Red%20Hat%20OpenShift-red?style=flat-square&logo=redhatopenshift)](https://www.redhat.com/en/technologies/cloud-computing/openshift)
[![GitOps](https://img.shields.io/badge/GitOps-Argo%20CD-orange?style=flat-square&logo=argocd)](https://argoproj.github.io/cd/)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-Tekton%20Pipelines-blue?style=flat-square&logo=tekton)](https://tekton.dev/)

**Maintained and Engineered by:** Miguel Moisés Valdez Ávila  
*Role Perspective:* DevOps & Cloud Infrastructure Specialist

---

## 🚀 Project Overview

This repository centralizes the enterprise-grade orchestration, automation, and declarative infrastructure manifests required to deploy a secure, resilient Full-Stack application. Built entirely upon Cloud-Native principles, the architecture leverages a strict **GitOps** operational model to eliminate configuration drift and achieve fully automated continuous delivery.

The core mission of this project is to showcase production-ready patterns for modern containerized workflows using enterprise Linux standards and Kubernetes frameworks.

---

## 🏗️ Core Architecture & GitOps Flow

The infrastructure is driven by a decentralized pipeline topology designed for multi-tenant isolation and maximum security compliance:

1. **Declarative State**: All Kubernetes and OpenShift native manifests are version-controlled in this repository.
2. **Continuous Integration (CI)**: Managed via **Tekton Pipelines**, utilizing daemonless builder technology (**Buildah**) to compile container images without exposing privileged Docker sockets.
3. **Continuous Delivery (CD)**: Orchestrated by **Argo CD**, which monitors this repository for semantic changes and automatically synchronizes the live cluster state.
4. **Advanced Deployment**: Implements Progressive Delivery using **Argo Rollouts** to execute automated **Canary Deployments** and traffic splitting.

---

## 🛠️ Key Technical Features

<table border="1" cellpadding="8" style="border-collapse: collapse; width: 100%; text-align: left;">
  <thead>
    <tr style="background-color: #24292e; color: white;">
      <th>Component / Layer</th>
      <th>Cloud-Native Architecture Decision</th>
      <th>Enterprise Rationale & Advantage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Container Building</strong></td>
      <td>Buildah (Rootless / Daemonless)</td>
      <td>Mitigates CVE risks by bypassing the Docker daemon requirement, enforcing strict compliance standards.</td>
    </tr>
    <tr>
      <td><strong>Traffic Routing</strong></td>
      <td>OpenShift Routes (Edge TLS Termination)</td>
      <td>Optimizes performance by handling encryption/decryption at the router level before traffic reaches Pod services.</td>
    </tr>
    <tr>
      <td><strong>Progressive Delivery</strong></td>
      <td>Argo Rollouts (Canary Patterns)</td>
      <td>Enables automated, real-time traffic splitting between Stable and Canary builds, minimizing blast radius during upgrades.</td>
    </tr>
    <tr>
      <td><strong>Configuration Mgmt</strong></td>
      <td>Kustomize Integration</td>
      <td>Maintains dry (Don't Repeat Yourself) manifests by utilizing native base/overlay composition without heavy templating engine overhead.</td>
    </tr>
  </tbody>
</table>

<br>

---

## 📁 Repository Structure

```text
├── argocd-apps/           # Parent Application-of-Applications definitions for Argo CD
├── assets/                # Architectural diagrams and static documentation resources
├── k8s-manifests/         # Core Kubernetes & OpenShift declarations
│   └── 03-advanced-delivery/
│       ├── frontend/      # Stable/Canary Services, Routes, and Rollout specs
│       └── backend/       # Core API deployment layers and data structures
└── pipelines/             # Tekton task bundles, persistent volume claims, and pipeline runs