---
id: technical-overview
title: Technical Overview
description: Technical overview of Crossplane architecture and components
---

## Purpose

Crossplane is a control plane framework built on Kubernetes that enables platform engineers to build self-service platforms for infrastructure and applications. It allows you to define custom APIs for provisioning without implementing complex Kubernetes controllers.

Crossplane uses a function-based composition model where declarative pipelines define how infrastructure and applications are orchestrated. Each function processes a custom resource and outputs the desired state of managed resources, extending Crossplane beyond infrastructure into full-stack control.

Crossplane is now a [CNCF Graduated project](https://www.cncf.io/projects/crossplane/) and is production-ready with broad adoption across enterprises and platform teams.

## Benefits

- **For Platform Engineers**: Define custom APIs for infrastructure and applications without writing controllers. Composition functions reduce complexity while maintaining flexibility.
- **For DevOps/SRE Teams**: Manage multi-cloud infrastructure and applications declaratively through Kubernetes. Leverage familiar workflows like `kubectl`, GitOps, and policy frameworks to define, compose, and automate everything from clusters to workloads.
- **For Application Teams**: Self-serve platforms, infrastructure, and application environments through well-defined APIs. Provision complete stacks via organizational blueprints, with every change versioned and tracked in Git.
- **For Organizations**: For Organizations: Unify infrastructure and application management under a single control plane. Apply consistent policies, improve governance, and automate provisioning across environments and clouds

## Core Architecture

Crossplane extends Kubernetes with three primary abstractions:

### 1. Composition Functions

Composition functions are the core building blocks. They receive an input (a composite resource request) and output the desired infrastructure state. Functions can be:

- **Go-based** - Performance-optimized for complex logic
- **Python-based** - Easier development with CNCF patches and templating
- **KCL-based** - Write your functions with KCL (KCL Configuration Language)
- **Template-based** - Simple declarative configurations using Go templating 

Functions form a pipeline where output from one feeds into the next, allowing modular, reusable infrastructure logic.

### 2. Composite Resources

Composite Resources (XRs) are custom Kubernetes resources that represent your infrastructure and application abstractions. They:

- Encapsulate infrastructure complexity
- Provide a clean API for teams to provision resources
- Are managed by composition functions
- Support Composite Resources (XRs) for multi-tenant isolation

Example: An `Database` composite resource might abstract away provider-specific configuration and networking setup.

### 3. Managed Resources

Managed Resources are Kubernetes representations of your resources (cloud resources, on-prem resources, or services). Crossplane provides providers for AWS, Azure, GCP, and others.

Key characteristics:

- Each provider publishes their own Managed Resource types
- They use status fields to track real cloud resources
- They support drift detection and auto-healing
- Metadata includes references between resources

## System Components

### Crossplane Package Manager

The package manager allows you to:

- **Publish configurations** - Share composite resource definitions and compositions
- **Distribute functions** - Distribute composition functions for reuse
- **Manage dependencies** - Define and resolve configuration dependencies
- **Version management** - Track and upgrade configurations and functions

### Crossplane Controllers

Crossplane runs several key controllers:

- **Composition Controller** - Watches composite resources and executes composition pipelines
- **Resource Controllers** - Each provider contributes controllers for managing their resources
- **Package Manager Controller** - Manages package installation and lifecycle

### Crossplane CLI

The `crossplane` CLI provides commands for:

- Package management (`crossplane xpkg build`, `crossplane xpkg push`)
- Composition validation and testing
- Migration utilities
- Configuration export

## Composition Pipeline

When a user creates a composite resource, Crossplane executes a composition pipeline:

1. **Receive** - User creates a composite resource (XR) with desired configuration
2. **Validate** - Crossplane validates the XR against the CompositeResourceDefinition (CRD)
3. **Compose** - Executes the composition function pipeline:
   - Each function receives the XR and prior function outputs
   - Functions return desired state for managed resources
   - Outputs can be transformed and passed to next function
4. **Create** - Crossplane creates/updates managed resources with the final desired state
5. **Monitor** - Crossplane continuously monitors resources for drift
6. **Patch** - If drift is detected, Crossplane patches resources back to desired state

## Key Design Principles

- **Declarative** - Infrastructure and Application configuration as code, stored in Git, version controlled
- **Composable** - Functions build on each other, enabling reuse and modularity
- **Extensible** - Custom logic through composition functions in your language of choice
- **Cloud Native** - Runs on Kubernetes, uses Kubernetes patterns and conventions
- **Multi-cloud** - Single control plane can manage resources across AWS, Azure, GCP, etc.
- **GitOps-friendly** - Works with Argo CD, Flux, and other GitOps operators
- **Zero-trust ready** - Integrates with policy engines like Kyverno and OPA

## Integration Points

### With Kubernetes

Crossplane leverages core Kubernetes capabilities:

- **CRDs** (Custom Resource Definitions) - Define composite and managed resources
- **Operators Pattern** - Controllers reconcile desired state
- **RBAC** - Control who can create/manage resources
- **Namespaces** - Isolate resources by team or environment

### With Cloud Providers

Each Crossplane provider integrates with cloud APIs through:

- **Service Accounts/IAM Roles** - Authenticate to cloud providers
- **Webhooks** - Receive lifecycle events
- **APIs** - Create, read, update, delete cloud resources

### With GitOps Systems

Crossplane complements GitOps tooling:

- Composite resources and managed resources stored in Git
- GitOps operators (Argo CD, Flux) deploy and synchronize changes
- Single source of truth for infrastructure state

## Operational Characteristics

### State Management

- **Desired State** - Defined in composite/managed resources
- **Observed State** - Tracked through status fields and conditions
- **Reconciliation** - Controllers continuously work to match observed to desired state

### Observability

- **Status Conditions** - Resources expose conditions (Ready, Synced, etc.)
- **Events** - Kubernetes events for all state transitions
- **Metrics** - Prometheus metrics for monitoring controller behavior
- **Logs** - Structured logging from all controllers

### Security

- **RBAC** - Control resource access via Kubernetes RBAC
- **Network Policies** - Restrict controller communication
- **Secrets Management** - Provider credentials stored as Kubernetes secrets
- **Audit Logging** - Full audit trail through Kubernetes audit log

## Learning Path

To understand Crossplane deeply, follow this progression:

1. **[System Model](./03-system-model.md)** - Understand the core concepts (XRs, MRs, Compositions)
2. **[Key Concepts](./04-key-concepts.md)** - Deep dive into each component
3. **Official Documentation** - See [Crossplane docs](https://docs.crossplane.io/) for complete reference
