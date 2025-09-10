---
author: rwestMSFT
ms.author: randolphwest
ms.date: 09/05/2025
ms.service: sql
ms.topic: include
ms.custom:
  - linux-related-content
---
Starting with [!INCLUDE [sssql25-md](../../includes/sssql25-md.md)] and [!INCLUDE [sssql22-md](../../includes/sssql22-md.md)] CU 20, [!INCLUDE [ssnoversion-md](../../includes/ssnoversion-md.md)] detects and honors control group (cgroup) v2 constraints, improving performance stability and resource isolation across Docker, Kubernetes, and OpenShift environments. Control groups enable fine-grained control in the Linux kernel over system resources such as CPU and memory.

With cgroup v2 support, [!INCLUDE [ssnoversion-md](../../includes/ssnoversion-md.md)] mitigates out of memory (OOM) errors previously observed in containerized deployments, particularly on Kubernetes clusters (for example, AKS v1.25+), where memory limits defined in container specifications weren't enforced.

### Check cgroup version

```bash
stat -fc %T /sys/fs/cgroup
```

The results are as follows:

| Result | Description |
| --- | --- |
| `cgroup2fs` | You're using cgroup v2 |
| `cgroup` | You're using cgroup v1 |

### Switch to cgroup v2

The easiest path is choosing a distribution that supports cgroup v2 out of the box.

If you need to switch manually, add the following line to your GRUB configuration:

```text
systemd.unified_cgroup_hierarchy=1
```

Then, run the following command to update GRUB:

```bash
sudo update-grub
```

For more information, see the following resources:

- [Quickstart: Deploy a SQL Server Linux container to Kubernetes using Helm charts](../sql-server-linux-containers-deploy-helm-charts-kubernetes.md)
- [Linux Kernel cgroup v2 documentation](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html)
- [Control Group v2](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html)
