<div align="center">

<div align="center">
  <img src="https://raw.githubusercontent.com/jlengelbrecht/home-ops/main/docs/src/assets/homelab0.jpg" width="144px" height="144px"/>
  <img src="https://raw.githubusercontent.com/jlengelbrecht/home-ops/main/docs/src/assets/arrow.png" width="144px" height="144px"/>
  <img src="https://raw.githubusercontent.com/jlengelbrecht/home-ops/main/docs/src/assets/home-ops.png" width="144px" height="144px"/>
</div>

### <img src="https://fonts.gstatic.com/s/e/notoemoji/latest/1f680/512.gif" alt="🚀" width="16" height="16"> My K8S Operations Repository <img src="https://fonts.gstatic.com/s/e/notoemoji/latest/1f680/512.gif" alt="🚀" width="16" height="16">

_deployed on Talos... managed with Flux, Renovate, Deepsource, and GitHub Actions_ <img src="https://fonts.gstatic.com/s/e/notoemoji/latest/1f916/512.gif" alt="🤖" width="16" height="16">

</div>

<div align="center">

[![Discord](https://img.shields.io/discord/1123378591003066392?style=for-the-badge&label&logo=discord&logoColor=white&color=blue)](https://discord.gg/qzTUfjutJ9)&nbsp;&nbsp;
[![Talos](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.devbu.io%2Ftalos_version&style=for-the-badge&logo=talos&logoColor=white&color=blue&label=%20)](https://talos.dev)&nbsp;&nbsp;
[![Kubernetes](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.devbu.io%2Fkubernetes_version&style=for-the-badge&logo=kubernetes&logoColor=white&color=blue&label=%20)](https://kubernetes.io)&nbsp;&nbsp;
[![Renovate](https://img.shields.io/github/actions/workflow/status/jlengelbrecht/home-ops/renovate.yaml?branch=main&label=&logo=renovatebot&style=for-the-badge&color=blue)](https://github.com/jlengelbrecht/prox-ops/actions/workflows/renovate.yaml)

</div>

## 🏗️ Infrastructure

### Proxmox 🖥️

| Server      | CPU                                          | RAM   | Storage | Network         | GPU       |
| ----------- | -------------------------------------------- | ----- | ------- | --------------- | --------- |
| Dell R630   | 2x Intel(R) Xeon(R) CPU E5-2680 v3 @ 2.50GHz | 190GB | 10TB    | 2x 10G - 2 x 1G |
| Dell R630   | 2x Intel(R) Xeon(R) CPU E5-2680 v3 @ 2.50GHz | 190GB | 10TB    | 2x 10G - 2 x 1G |
| Dell R730XD | 2x Intel(R) Xeon(R) CPU E5-2699 v3 @ 2.30GHz | 125GB | 40TB    | 2x 10G - 2 x 1G | Titan Xp  |
| Dell R730XD | 2x Intel(R) Xeon(R) CPU E5-2699 v3 @ 2.30GHz | 250GB | 100TB   | 2x 10G - 2 x 1G | RTX A2000 |

| Cluster      | CPU | RAM  | Storage | Network | Type          |
| ------------ | --- | ---- | ------- | ------- | ------------- |
| Master-1     | 4   | 8GB  | 120GB   | 10G     | Control Plane |
| Master-2     | 4   | 8GB  | 120GB   | 10G     | Control Plane |
| Master-3     | 4   | 8GB  | 120GB   | 10G     | Control Plane |
| Worker-1     | 8   | 32GB | 120GB   | 10G     | Worker        |
| Worker-2     | 8   | 32GB | 120GB   | 10G     | Worker        |
| Worker-3     | 8   | 32GB | 120GB   | 10G     | Worker        |
| Worker-4     | 8   | 32GB | 120GB   | 10G     | Worker        |
| Worker-5     | 8   | 32GB | 120GB   | 10G     | Worker        |
| Worker-6     | 8   | 32GB | 120GB   | 10G     | Worker        |
| GPU-Worker-1 | 8   | 32GB | 120GB   | 10G     | GPU Worker    |
| GPU-Worker-2 | 8   | 32GB | 120GB   | 10G     | GPU Worker    |

## 💥 Reset

There might be a situation where you want to destroy your Kubernetes cluster. The following command will reset your nodes back to maintenance mode, append `--force` to completely format your the Talos installation. Either way the nodes should reboot after the command has run.

```sh
task talos:reset # --force
```

## 🛠️ Talos and Kubernetes Maintenance

#### ⚙️ Updating Talos node configuration

📍 _Ensure you have updated `talconfig.yaml` and any patches with your updated configuration._

```sh
# (Re)generate the Talos config
task talos:generate-config
# Apply the config to the node
task talos:apply-node HOSTNAME=? MODE=?
# e.g. task talos:apply-config HOSTNAME=k8s-0 MODE=auto
```

#### ⬆️ Updating Talos and Kubernetes versions

📍 _Ensure the `talosVersion` and `kubernetesVersion` in `talhelper.yaml` are up-to-date with the version you wish to upgrade to._

```sh
# Upgrade node to a newer Talos version
task talos:upgrade-node HOSTNAME=?
# e.g. task talos:upgrade HOSTNAME=k8s-0
```

```sh
# Upgrade cluster to a newer Kubernetes version
task talos:upgrade-k8s
# e.g. task talos:upgrade-k8s
```

## 🤖 Renovate

[Renovate](https://www.mend.io/renovate) is a tool that automates dependency management. It is designed to scan your repository around the clock and open PRs for out-of-date dependencies it finds. Common dependencies it can discover are Helm charts, container images, GitHub Actions, Ansible roles... even Flux itself! Merging a PR will cause Flux to apply the update to your cluster.

To enable Renovate, click the 'Configure' button over at their [Github app page](https://github.com/apps/renovate) and select your repository. Renovate creates a "Dependency Dashboard" as an issue in your repository, giving an overview of the status of all updates. The dashboard has interactive checkboxes that let you do things like advance scheduling or reattempt update PRs you closed without merging.

The base Renovate configuration in your repository can be viewed at [.github/renovate.json5](./.github/renovate.json5). By default it is scheduled to be active with PRs every weekend, but you can [change the schedule to anything you want](https://docs.renovatebot.com/presets-schedule), or remove it if you want Renovate to open PRs right away.

## 🐛 Debugging

Below is a general guide on trying to debug an issue with an resource or application. For example, if a workload/resource is not showing up or a pod has started but in a `CrashLoopBackOff` or `Pending` state.

1. Start by checking all Flux Kustomizations & Git Repository & OCI Repository and verify they are healthy.

   ```sh
   flux get sources oci -A
   flux get sources git -A
   flux get ks -A
   ```

2. Then check all the Flux Helm Releases and verify they are healthy.

   ```sh
   flux get hr -A
   ```

3. Then check the if the pod is present.

   ```sh
   kubectl -n <namespace> get pods -o wide
   ```

4. Then check the logs of the pod if its there.

   ```sh
   kubectl -n <namespace> logs <pod-name> -f
   # or
   stern -n <namespace> <fuzzy-name>
   ```

5. If a resource exists try to describe it to see what problems it might have.

   ```sh
   kubectl -n <namespace> describe <resource> <name>
   ```

6. Check the namespace events

   ```sh
   kubectl -n <namespace> get events --sort-by='.metadata.creationTimestamp'
   ```

Resolving problems that you have could take some tweaking of your YAML manifests in order to get things working, other times it could be a external factor like permissions on NFS. If you are unable to figure out your problem see the help section below.
