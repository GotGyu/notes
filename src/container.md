# 容器

## 概念

1. **简介**
   - 一种轻量级的虚拟化技术，通过**操作系统级的虚拟化**，将应用程序及其依赖环境打包在一起，确保应用程序可以在任何环境中一致运行
   - 容器使用宿主操作系统的内核，通过命名空间（Namespace）和控制组（Cgroups）技术实现资源的隔离和限制
     - namespace 确保每个容器拥有独立的系统视图，如进程树、网络接口和文件系统
     - cgroups 限制和分配容器的资源使用，如CPU、内存和I/O。
   - 容器化技术通常依赖于镜像（Image）来实现应用程序的打包。一个镜像包含了应用程序运行所需的所有依赖项，包括代码、库、工具和配置文件
2. **应用场景**
   - 微服务架构：容器非常适合微服务架构，每个微服务可以运行在独立的容器中，具有高扩展性和灵活性。通过容器编排工具，可以实现自动化的部署、扩展和管理
   - 持续集成/持续部署（CI/CD）：容器的快速启动和一致性特性，使其成为CI/CD流程中的理想选择。开发人员可以使用容器创建一致的开发和测试环境，确保代码在各个环境中的一致性
   - 跨平台部署：容器化应用可以在不同的环境中一致运行，无论是开发、测试还是生产环境，使得跨平台部署变得更加简单和可靠
3. **优点**
   - **轻量级**：容器不需要虚拟化整个操作系统，资源利用率高，启动速度快
   - 一致性：由于容器镜像包含了所有依赖项，确保应用程序在不同环境中（开发、测试、生产）运行的一致性
   - 易于部署和扩展：容器可以很容易地复制、分发和部署，特别适合微服务架构和持续集成/持续部署（CI/CD）流程
   - **支持快速扩展**：由于容器的轻量级特性，可以快速启动和销毁，支持应用程序的弹性扩展
4. **缺点**
   - **隔离性不如虚拟机**：容器共享宿主操作系统的内核，隔离性较虚拟机弱，可能存在安全风险
   - 依赖宿主操作系统：容器必须与宿主操作系统的内核兼容，限制了操作系统的多样性
   - 管理复杂性：容器的快速启动和销毁虽然带来了灵活性，但也增加了管理的复杂性，尤其是在大规模容器集群中

## 运行时

### `runc`

轻量级的容器运行时，用于**创建和管理容器进程**，是 Docker, Kubernetes, containerd 等容器管理系统的底层组件，负责实际运行容器。由 OCI 维护，符合 OCI 运行时规范。

1. **作用**

- 基于 namespace 和 cgroups 创建隔离环境
- 执行容器进程：`docker run` 等命令会调用 `runc` 来启动实际的容器进程
- 与更高级的容器管理工具集成：containerd， CRI-O 这些容器运行时会进一步调用 `runc`

2. **内存安全**

   `runc` 不是内存安全的，因为它运行的容器与宿主机共享同一个Linux内核，会导致许多问题

- 容器运行时与内核共享内存
  - `runc` 使用 Namespaces 进行资源隔离，但这些机制主要**限制进程可见性**，而不是完全隔离内存访问
  - 内核的数据结构（如 `task_struct`、`cred` 等）在整个系统范围内共享，攻击者可以通过某些漏洞读取或篡改宿主机关键数据（CVE-2022-0185）
- 内核态的内存访问难以受限
  - 在**用户态**，可以通过检查**页表映射**来控制进程能访问的内存范围（类似 VM 的内存保护）
  - **而内核态的内存访问难以限制**，因为内核有很多共享的全局数据，如 `init_task`,`modprobe_path`，这些数据可以被 `runc` 运行的容器间接访问，并通过某些漏洞修改（e.g. 容器中的恶意进程可以利用 `ptrace` 或 eBPF JIT 漏洞 访问宿主机内核的数据），而且内核是动态变化的，未来版本可能引入新的全局数据，会使攻击面不断增加
- 容器共享 `runc` 运行时的代码
  - `runc` 在宿主机上执行，并在用户态与内核态频繁交互
  - 如果 `runc` 进程或其依赖的 `glibc` / `libseccomp` 组件被劫持，攻击者可以利用 **`runc` 本身** 作为宿主机的攻击入口（CVE-2019-5736）

## 安全性

### 典型容器安全漏洞

大多数不会自动触发，如果容器配置不当（如授予过多权限），攻击者可以通过POC进行利用。为了防范这些问题，应尽可能**使用最小权限运行容器**，避免 `--privileged` 模式，并使用 Seccomp、AppArmor、SELinux 等机制进行隔离

#### 容器逃逸

攻击者突破容器边界，获取宿主机的访问权限

- 典型方法
  - 覆盖 `runc` 二进制（CVE-2019-5736）
    - `runc` 是 Docker、Kubernetes 等容器运行时使用的核心组件
    - 攻击者可通过 `exec` 进入容器后，利用 `runc` 运行新进程时的机制，**覆盖 `runc` 可执行文件**，从而执行任意代码
    - 需要容器内部有足够权限（如 `CAP_SYS_ADMIN`）
  - 利用 Linux 内核漏洞
    - 许多内核漏洞（如 `dirty cow`、`overlayfs` 漏洞）可以让攻击者在容器内提权并逃逸到宿主机
    - 需要容器具备 `CAP_SYS_ADMIN` 或 `CAP_DAC_OVERRIDE` 等权限（某些漏洞不需要），且宿主机要运行易受攻击的Linux内核版本

#### 文件挂载漏洞

容器中的挂载点（如 `bind mount` 或 `volume`）可能被滥用，导致攻击者访问或修改宿主机上的敏感文件

- 典型方法

  - 挂载 `/proc`、`/sys` 等系统目录
    - `/proc/self/exe` 可用于访问和修改正在运行的进程
    - `/sys/kernel/debug/` 可用于修改内核参数
    - 需要 `--privileged` 或 `CAP_SYS_ADMIN` 权限，且容器对 `procfs` 或 `sysfs` 目录有读写权限

  - 挂载宿主机的 `docker.sock`

    - 如果攻击者可以访问 `/var/run/docker.sock`，就可以控制 Docker 进程，创建特权容器，最终获取宿主机权限

    - 需要 `-v /var/run/docker.sock:/var/run/docker.sock` 绑定到容器内，且容器有足够权限执行 Docker 命令

#### 提权为root

攻击者在容器内部获取 `root` 权限后，可能进一步利用容器的错误配置或内核漏洞提权到宿主机

- 典型方法：
  - 不安全的 `--privileged` 容器
    - 运行 `--privileged` 容器时，它拥有所有 `CAP_*` 权限，并可以访问 `/dev` 设备，甚至直接修改 `host` 的内核参数
    - 需要容器以 `--privileged` 方式运行
  - 滥用 `CAP_SYS_ADMIN`
    - 许多逃逸漏洞（如 `mount` 滥用、`overlayfs` 漏洞）需要 `CAP_SYS_ADMIN` 权限
    - 需要容器分配了 `CAP_SYS_ADMIN`，但默认容器不具备
  - 利用 `setuid` 程序
    - 容器中某些二进制文件（如 `sudo`、`pkexec`）可能存在提权漏洞，允许普通用户提升为 `root`
    - 需要容器中存在易受攻击的 `setuid` 程序

## 和虚拟机的区别

![img](https://ucc.alicdn.com/z3pojg2spmpe4/developer-article1633138/20241103/e70781d41a074b7795759964623d8596.jpeg?x-oss-process=image/resize,w_1400/format,webp)

| 类别       | 容器                                                         | 虚拟机                                                       |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 架构       | 应用级/OS级的虚拟化技术，**共享宿主OS的内核**，但各自拥有独立的用户空间 | 基础架构级/硬件级的虚拟化技术，**有独立的OS内核**、文件系统、内存分配 |
| 性能       | 通过直接利用宿主OS的资源，降低了资源消耗和延迟，提升整体性能 | hypervisor在分配和管理资源时会引入额外的开销管理虚拟化层，导致资源利用率相对较低 |
| 资源利用率 | 直接利用宿主的资源，计算资源的利用效率高                     | 为每个实例分配独立的资源，资源利用率较低                     |
| 延迟       | 能直接访问宿主硬件，延迟低                                   | 引入虚拟化层，需要通过hypervisor访问物理硬件，延迟高         |
| 启动时间   | 短，接近即时                                                 | 需要加载和启动完整的OS，时间较长                             |
| 隔离性     | 使用namespace和cgroup，隔离性弱，尤其在多租户环境中          | 硬件级的隔离，虚拟机之间完全独立，隔离性强                   |
| 可移植性   | 高度可移植性，可以在不同系统上保持一致的行为                 | 可移植性较低，依赖于特定的硬件和OS配置                       |
| 安全性     | 安全性依赖于宿主OS的安全性，通常低于VM                       | 安全性较高，即使一个虚拟机遭到攻击，其他虚拟机和宿主系统仍然保持安全 |
| 灵活性     | 在资源分配和扩展方面具有更大的灵活性，可以轻松地进行动态扩展和缩减 | 灵活性较低，因为需要固定的资源分配，不适合处理动态负载变化   |
| 密度       | 可以在一台宿主机上以更高的密度部署                           | 在同样的硬件配置下，能够运行的虚拟机数量往往少于容器         |
| 效率       | 在存储和内存利用率方面更加高效，可以在更少的存储和内存资源下运行多个实例，特别适合大规模部署 | 需要为每个实例分配额外的存储和内存资源，使得虚拟机的效率相对较低 |
| 部署复杂性 | 相对简单。通过容器镜像，应用程序及其依赖项可以被打包成一个独立的单元，便于分发和部署。容器编排工具进一步简化了容器的管理，使得大规模部署更加容易 | 相对复杂，需要配置和管理独立的操作系统。每个虚拟机实例都需要进行操作系统安装、配置和优化，这使得虚拟机的部署和维护更加费时费力 |
| 使用场景   | 非常适合微服务架构、持续集成/持续部署（CI/CD）流程、应用程序的快速扩展和管理分布式系统。在这些场景中，容器的轻量级、快速启动和高密度特性发挥了重要作用 | 通常用于运行遗留应用程序、测试不同的操作系统、创建隔离的操作系统环境以及开发沙箱。虚拟机强大的隔离性和多操作系统支持，使其在需要高安全性和多样化操作系统的场景中具有优势 |



# 虚拟机

## 概念

1. **简介**

   - 通过软件仿真生成的独立计算机环境，类似物理计算机，运行在宿主OS之上
   - 由Hypervisor（虚拟机监控器）软件层管理，它负责在物理硬件和虚拟机之间进行资源分配和调度，运行在物理硬件之上
     - Hypervisor有两种：
       1. 裸金属型：直接运行在物理硬件之上，没有宿主操作系统。常见的例子包括VMware ESXi、Microsoft Hyper-V和Xen
       2. 托管型：运行在宿主操作系统之上，依赖宿主操作系统来管理硬件资源。常见的例子包括VMware Workstation、Oracle VirtualBox和Parallels Desktop

   - 通过虚拟化技术，将物理计算机的资源（CPU, 内存，存储，网络接口）分割成多个独立的虚拟资源

   - 每个虚拟机可以安装自己的操作系统，运行独立的应用程序

2. **应用场景**

   - 多租户云服务：在云服务提供商的基础设施中，虚拟机常用于为不同的客户提供独立的计算环境。这种环境确保每个客户的数据和操作系统彼此隔离，提高了安全性和隐私性

   - 开发与测试环境：开发人员可以使用虚拟机模拟不同的操作系统和硬件配置，测试应用程序的兼容性和性能

   - 灾难恢复与备份：虚拟机的快照功能可以用于系统备份和灾难恢复，确保在系统故障时能够快速恢复到正常状态

3. **优点**

   - **资源隔离性强**：每个虚拟机都有自己独立的操作系统和应用程序，彼此完全隔离

   - 操作系统多样性：虚拟机允许在同一台物理服务器上运行不同的操作系统

   - 成熟的管理工具：虚拟机技术已经发展多年，拥有成熟的管理工具和生态系统，支持企业级应用的部署和管理

4. **缺点**

   - **性能开销大**：由于虚拟机需要仿真完整的硬件环境，并运行一个完整的操作系统，资源利用率相对较低，性能开销较大

   - **启动时间长**：虚拟机的启动需要加载完整的操作系统，启动时间通常较长

   - **占用资源多**：每个虚拟机都需要独立的操作系统，占用较多的内存和存储资源



## 安全性

### 内存安全

1. **内存安全的原因**

​	VM 之所以内存安全，是因为 虚拟化技术对内存的访问接口极其受限：

- 内存访问受虚拟化页表（EPT/NPT）严格控制：
  - 在 VM 中，所有的内存访问都必须通过二级页表（Extended Page Table, EPT / Nested Page Table, NPT）进行转换，主机（Hypervisor）可以完全掌控虚拟机的内存访问范围
  - 只要确保页表映射的合法性，VM 无法访问宿主机或其他 VM 的内存
- 所有 CPU 特权操作都需要 Hypervisor 参与：
  - VM 运行在用户态或客体内核态，而 `runc` 运行的容器直接共享宿主机内核，没有类似的隔离保护
  - 例如，VM 不能直接修改 EPT，而容器中的进程可以影响宿主机内核的共享数据结构
- Hypervisor 隔离机制严格：
  - Hypervisor 通过 IOMMU、vCPU 限制、特权指令拦截等机制进一步限制 Guest OS 对硬件资源的访问，确保 VM 不能越权操作宿主机

# kubernetes

## 简介

- **是什么**

  一个开源的容器编排引擎，可以用来管理容器化的应用，包括容器的自动化的部署、扩容、缩容、升级、回滚等等

- **为什么要用**

  之前一般都是使用Docker来管理容器化的应用，但是Docker只是一个单机的容器管理工具，只能管理单个节点上的容器，当应用程序需要运行在多个节点上的时候，就需要使用容器编排引擎来管理这些节点，比如Docker Swarm、Mesos、k8s等等。k8s基本上已经成为了容器编排引擎的事实标准。

## 资源对象/组件

1. pod（容器组）：最小的可部署单元，用于承载和管理容器的抽象层，一个Pod可以包含一个或多个紧密关联的容器，它们共享相同的网络命名空间、IP地址和存储卷，并在同一个宿主机上运行
2. node：是一个运行着k8s节点软件的物理机器或虚拟机，每个node负责运行pod中的容器，是k8s集群中的工作节点，实际执行应用程序工作负载并提供运行环境
3. kubelet：是k8s系统中的核心组件，运行在node上，负责管理和维护每个node上的pod，并确保它们按预期运行。并与k8s控制平面进行通信， 以确保node上的Pod与集群中的其他组件保持同步
4. kube-proxy(k-proxy)：是k8s中的网络核心组件，实现了服务暴露和转发等网络功能。支持用户空间模式（基本不再使用）、ipvs、iptables三种代理模式
5. master：集群控制节点，每个k8s集群里都必须要有一个master节点来负责整个集群的管理和控制。基本上k8s的所有控制命令都发给它，它来负责具体的执行过程，后面执行的所有命令基本都是在master节点上运行的
6. api-server(api)：k8s中最重要的组件，因为它是实现声明式api的关键，其核心功能是提供了k8s各类资源对象（pod, rc, service等）的增删改查以及HTTP REST接口
7. controller-manager(c-m运行控制器)：处理集群中常规任务的后台线程，保证集群中各种资源的实际状态（status）和用户定义的期望状态（spec）一致
8. etcd：键值数据库，可用于服务发现以及配置中心，采用raft一致性算法，是保存k8s所有集群数据的后台数据库，在整个云原生中发挥着极其重要的作用
9. kuve-scheduler(sched)：核心组件之一，主要负责整个集群资源的调度功能，根据特定的调度算法和策略，将pod调度到最优的工作节点上面去，从而更加合理、充分地利用集群的资源
10. deployment(deploy)：一种Pod管理方式，可以指挥k8s如何创建和更新应用实例，创建deplotmeny后，master会将应用程序调度到集群中的各个节点中，一般用来部署无状态应用
11. statefulset(sts)：用来管理有状态应用的工作负载API对象，用来管理某pod集合的部署和扩缩，并为这些pod提供持久存储和持久标识符
12. replicaset(rs)：维护一组在任何时候都处于运行状态的 Pod 副本的稳定集合
13. daemonset(ds)：确保全部 （或者某些）node上运行一个 Pod 的副本。 当有node加入集群时， 也会为他们新增一个Pod，当有node从集群移除时， 这些Pod也会被回收。删除 DaemonSet 将会删除它创建的所有 Pod
14. service(svc)：将运行在一个或一组pod上的网络应用程序公开为网络服务的方法
15. endpoint(ep)：一个资源对象，存在etcd中，用来记录一个svc对应的所有pod的访问地址
16. ingress(ing)：对集群中服务的外部访问进行管理的 API 对象， 典型的访问方式是 HTTP。 可以通过Ingress资源来配置不同的转发规则， 从而达到根据不同的规则设置访问集群内不同的Service所对应的后端Pod
17. configmap(cm)：一种 API 对象， 用来将非机密性的数据保存到键值对中。 使用时， Pod 可以将其用作环境变量、 命令行参数或者存储卷中的配置文件。ConfigMap 将你的环境配置信息和容器镜像解耦， 便于应用配置的修改
18. secret：一种包含少量敏感信息例如密码、 令牌或密钥的对象。 这样的信息可能会被放在 Pod 规约中或者镜像中
19. namespace(ns)：提供一种机制， 将同一集群中的资源划分为相互隔离的组， 以便进行分类、 筛选和管理。同一命名空间内的资源名称要唯一， 但跨命名空间时没有这个要求
20. volume(vol)：用于 Pod 内部的数据存储， Pod 容器内部数据是可以共享的， 其生命周期与所属 Pod 生命周期相同。
21. ...还有很多先不写了

## 常用命令

1. 基础使用

   ```shell
   # 查看帮助
   kubectl --help
   
   # 查看API版本
   kubectl api-versions
   
   # 查看集群信息
   kubectl cluster-info
   ```

2. 资源的创建和运行

   ```shell
   # 创建并运行一个指定的镜像
   kubectl run NAME --image=image [params...]
   # e.g. 创建并运行一个名字为nginx的Pod
   kubectl run nginx --image=nginx
   
   # 根据YAML配置文件或者标准输入创建资源
   kubectl create RESOURCE
   # e.g.
   # 根据nginx.yaml配置文件创建资源
   kubectl create -f nginx.yaml
   # 根据URL创建资源
   kubectl create -f https://k8s.io/examples/application/deployment.yaml
   # 根据目录下的所有配置文件创建资源
   kubectl create -f ./dir
   
   # 通过文件名或标准输入配置资源
   kubectl apply -f (-k DIRECTORY | -f FILENAME | stdin)
   # e.g.
   # 根据nginx.yaml配置文件创建资源
   kubectl apply -f nginx.yaml
   ```

3. 查看资源信息

   ```shell
   # 查看集群中某一类型的资源
   kubectl get RESOURCE
   # 其中，RESOURCE可以是以下类型：
   kubectl get pods / po         # 查看Pod
   kubectl get svc               # 查看Service
   kubectl get deploy            # 查看Deployment
   kubectl get rs                # 查看ReplicaSet
   kubectl get cm                # 查看ConfigMap
   kubectl get secret            # 查看Secret
   kubectl get ing               # 查看Ingress
   kubectl get pv                # 查看PersistentVolume
   kubectl get pvc               # 查看PersistentVolumeClaim
   kubectl get ns                # 查看Namespace
   kubectl get node              # 查看Node
   kubectl get all               # 查看所有资源
   
   # 后面还可以加上 -o wide 参数来查看更多信息
   kubectl get pods -o wide
   
   # 查看某一类型资源的详细信息
   kubectl describe RESOURCE NAME
   # e.g. 查看名字为nginx的Pod的详细信息
   kubectl describe pod nginx
   ```

4. 资源的修改、删除和清理

   ```shell
   # 更新某个资源的标签
   kubectl label RESOURCE NAME KEY_1=VALUE_1 ... KEY_N=VALUE_N
   # e.g. 更新名字为nginx的Pod的标签
   kubectl label pod nginx app=nginx
   
   # 删除某个资源
   kubectl delete RESOURCE NAME
   # e.g. 删除名字为nginx的Pod
   kubectl delete pod nginx
   
   # 删除某个资源的所有实例
   kubectl delete RESOURCE --all
   # e.g. 删除所有Pod
   kubectl delete pod --all
   
   # 根据YAML配置文件删除资源
   kubectl delete -f FILENAME
   # e.g. 根据nginx.yaml配置文件删除资源
   kubectl delete -f nginx.yaml
   
   # 设置某个资源的副本数
   kubectl scale --replicas=COUNT RESOURCE NAME
   # e.g. 设置名字为nginx的Deployment的副本数为3
   kubectl scale --replicas=3 deployment/nginx
   
   # 根据配置文件或者标准输入替换某个资源
   kubectl replace -f FILENAME
   # e.g. 根据nginx.yaml配置文件替换名字为nginx的Deployment
   kubectl replace -f nginx.yaml
   ```

5. 调试和交互

   ```shell
   # 进入某个Pod的容器中
   kubectl exec [-it] POD [-c CONTAINER] -- COMMAND [args...]
   # e.g. 进入名字为nginx的Pod的容器中，并执行/bin/bash命令
   kubectl exec -it nginx -- /bin/bash
   
   # 查看某个Pod的日志
   kubectl logs [-f] [-p] [-c CONTAINER] POD [-n NAMESPACE]
   # e.g. 查看名字为nginx的Pod的日志
   kubectl logs nginx
   
   # 将某个Pod的端口转发到本地
   kubectl port-forward POD [LOCAL_PORT:]REMOTE_PORT [...[LOCAL_PORT_N:]REMOTE_PORT_N]
   # e.g. 将名字为nginx的Pod的80端口转发到本地的8080端口
   kubectl port-forward nginx 8080:80
   
   # 连接到现有的某个Pod（将某个Pod的标准输入输出转发到本地）
   kubectl attach POD -c CONTAINER
   # e.g. 将名字为nginx的Pod的标准输入输出转发到本地
   kubectl attach nginx
   
   # 运行某个Pod的命令
   kubectl run NAME --image=image -- COMMAND [args...]
   # e.g. 运行名字为nginx的Pod
   kubectl run nginx --image=nginx -- /bin/bash
   ```

   