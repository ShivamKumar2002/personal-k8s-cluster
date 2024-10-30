# Personal Kubernetes Cluster GitOps Repository

This repository contains the declarative configuration for my personal Kubernetes cluster, managed using GitOps principles with [ArgoCD](https://argo-cd.readthedocs.io/en/stable/). The setup leverages ArgoCD's ability to automatically sync and deploy applications based on the state defined in this repository.


## Important Note

This GitOps repository is tailored to my personal Kubernetes cluster setup and preferences. While it can serve as a valuable reference or starting point, it's not intended for direct use in other environments. If you find this structure and approach helpful:

- Consider forking this repository and adapting it to your specific needs.
- Use it as inspiration for your own GitOps setup, incorporating the parts that align with your requirements.
- Remember to review and modify all configurations, before applying them to your own cluster.


## Getting Started

- Fork this repository or create a new one based on this structure.
- Customize the configurations to match your cluster setup and requirements.
- Ensure you have a Kubernetes cluster set up and running.
- Install ArgoCD on your cluster using the files in the `cluster/argocd/` directory.
- Configure ArgoCD to use your customized repository as its source of truth.
- Make sure to update the relevant Application resources in the `cluster/bootstrap/` directory to point to your repository.
- Apply the bootstrap applications from the `cluster/bootstrap/` directory to start managing your cluster with ArgoCD.


## Repository Structure

The repository is organized as follows:

```
.
|--- README.md
|--- cluster/
    |--- main.yaml
    |
    |--- apps/
    |   |--- category1/
    |   |   |--- app1/
    |   |   |   |--- kustomization.yaml
    |   |   |--- app2/
    |   |       |--- kustomization.yaml
    |   |       |--- values.yaml
    |   |--- category2/
    |       |--- app3/
    |           |--- values.yaml
    |
    |--- argocd/
    |   |--- kustomization.yaml
    |   |--- namespace.yaml
    |   |--- values.yaml
    |
    |--- bootstrap/
        |--- category1/
        |   |--- app1.yaml
        |   |--- app2.yaml
        |--- category2/
            |--- app3.yaml
```

### cluster/

This directory contains all the Kubernetes manifests and configuration files for the cluster.

### cluster/main.yaml

The `cluster/main.yaml` file serves as the primary entry point for ArgoCD in this GitOps setup. It defines an ArgoCD Application resource that targets the `cluster/bootstrap/` directory. This Application is configured to recursively apply all Application resources found within the bootstrap directory. By using the `directory.recurse: true` option, it ensures that all nested Application definitions are processed. The `main.yaml` file also specifies important sync policies, such as automated pruning and self-healing, which help maintain the desired cluster state. Additionally, it sets up server-side apply and namespace creation options, facilitating a smooth deployment process. This centralized approach allows for efficient management of the entire cluster configuration through a single ArgoCD Application.

#### cluster/apps/

The `cluster/apps/` directory contains contains folders for individual applications or services. Each application folder may have:

- A `kustomization.yaml` file for Kustomize-based deployments.
- Kubernetes manifest files for direct application.
- Helm values files (`values.yaml`) for Helm-based deployments.
- Any additional files and configurations required for that specific application or service.

This structure allows for flexible deployment strategies, including direct manifest application, Kustomize overlays, and Helm chart deployments.

#### cluster/argocd/

The `cluster/argocd/` directory contains the necessary files for deploying ArgoCD itself on the cluster. This includes the core ArgoCD components and any additional configurations or customizations specific to this setup.

#### cluster/bootstrap/

The `bootstrap/` directory contains YAML files defining ArgoCD `Application` (`argoproj.io/v1alpha1/Application`) resources. These Application resources define how ArgoCD manages the specific app/service in the cluster.


## How ArgoCD Works in This Setup

This repository uses the [App of Apps](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#app-of-apps) pattern with ArgoCD to manage the cluster state. Here's how it works:

- The main entry point is defined in `cluster/main.yaml` file, which is an ArgoCD Application that points to the `cluster/bootstrap/` directory.

- ArgoCD applies the `cluster/main.yaml` Application, which then recursively applies all Application resources found in the `cluster/bootstrap/` directory.

- Each Application in `cluster/bootstrap/` typically points to a corresponding folder under `cluster/apps/`.

- ArgoCD then processes each of these Applications:
   - It applies any Kubernetes manifest files found in the specified `cluster/apps/` subdirectory.
   - It also applies any Manifests or Kustomizations defined within each Application's spec.

This setup provides several benefits:
- Clear separation between application definitions (`cluster/apps/`) and deployment instructions (`cluster/bootstrap/`).
- Scalable and maintainable GitOps structure.
- Easy addition or removal of entire applications by modifying the `cluster/bootstrap/` directory.
- Centralized management of all cluster applications through a single ArgoCD Application (`cluster/main.yaml`).

To make changes to the cluster:

1. Modify the relevant files in `cluster/apps/` or `cluster/bootstrap/`.
2. Commit and push your changes.
3. ArgoCD will automatically detect the changes and apply them to the cluster, ensuring the desired state matches the repository state.

This approach allows for efficient management of multiple applications in a Kubernetes cluster while adhering to GitOps principles.


## Best Practices

- Always use declarative configuration.
- Utilize Kustomize overlays for managing environment-specific variations.
- Keep sensitive information out of the repository. Use [External Secrets Operator](https://external-secrets.io/latest/) or a similar solution for managing secrets.
- Regularly update applications and ArgoCD to the latest stable versions.


## Contributing

Contributions are welcome! If you'd like to fix an issue, please follow these steps:

- Fork the repository.
- Create a new branch.
- Add and test your fix.
- Commit and push your changes.
- Submit a pull request to this repository.

## Support

For any questions or issues, please open an issue in this repository.

## Inspirations

- [clearlybaffled/homelab](https://github.com/clearlybaffled/homelab)
- [onedr0p/home-ops](https://github.com/onedr0p/home-ops)