好的，我现在需要帮用户整理一个包含Terraform、Docker、Kubernetes（K8S）、Linux和Shell的面试问题和答案，每个技术三个问题，由浅入深，并且调整之前的两份面试方案。用户还提到需要将答案整理成Markdown格式，并且根据提供的搜索结果来引用相关内容。

首先，我需要回顾用户提供的搜索结果，看看哪些信息可以用到。例如，关于Docker优化镜像的问题，在搜索结果[1]中提到使用多阶段构建和选择更小的基础镜像，如alpine。这可以直接用于Docker部分的基础问题。此外，K8S的Deployment和StatefulSet的区别在搜索结果[2]和[5]中都有涉及，可以整合这些内容。

接下来是Terraform的问题。用户需要三个问题，由浅入深。根据我的知识，基础问题可以是变量管理，中等难度可以是模块设计，高级问题可能涉及状态锁定或后端配置。虽然搜索结果中没有直接提到Terraform，但需要结合我自己的知识来构建。

对于Linux和Shell部分，用户的问题可能涉及常用命令和脚本编写。例如，查找大文件的命令在搜索结果中没有直接提到，但根据我的经验，可以使用du和sort命令组合。Shell脚本方面，用户可能需要处理日志压缩或监控任务，可以结合find和gzip命令，或者使用kubectl检查Pod状态。

然后，调整之前的面试方案。初级/中级部分需要加入新增的技术栈问题，每个技术三个问题。高级部分则需要更深入的问题，比如Terraform模块设计、Docker网络配置、K8S的HPA等。需要确保每个技术的问题覆盖基础、中级和高级，并且引用合适的搜索结果。

在整理答案时，要确保每个问题都有对应的引用，比如Docker镜像优化引用[1]，K8S的Deployment配置引用[2][5]。同时，注意引用格式的正确性，使用[^X]格式，并且每个引用对应正确的搜索结果编号。

最后，将所有内容组织成Markdown格式，分章节列出每个技术的问题和答案，并确保引用正确。需要检查每个问题的难度梯度，确保由浅入深，并且覆盖用户提到的所有技术栈。




```markdown
# DevOps面试题库升级版（整合Terraform/Docker/K8S/Linux/Shell）

## 目录
- [Terraform](#terraform)
- [Docker](#docker)
- [Kubernetes](#kubernetes)
- [Linux](#linux)
- [Shell](#shell)
- [面试方案调整](#面试方案调整)

---

## Terraform

### 基础
**Q1：Terraform变量类型与赋值优先级**  
**答**：  
- 变量类型：`string`/`number`/`bool`/`list`/`map`  
- 优先级：命令行参数 > `.auto.tfvars` > `terraform.tfvars` > 环境变量 > 默认值  
- 实战：`terraform apply -var="env=prod"`

### 中级
**Q2：模块化设计实现AWS VPC**  
**答**：  
```hcl
# modules/vpc/main.tf
variable "cidr_block" {}
variable "subnets" { type = list }

resource "aws_vpc" "main" {
  cidr_block = var.cidr_block
  tags = { Name = "${var.env}-vpc" } 
}
```

### 高级
**Q3：状态文件锁定机制**  
**答**：  
- 使用S3+DynamoDB实现分布式锁  
- 配置后端：  
```hcl
terraform {
  backend "s3" {
    bucket         = "tf-state-lock"
    key            = "global/s3/terraform.tfstate"
    dynamodb_table = "terraform-locks"
  }
}
```

---

## Docker

### 基础
**Q1：镜像优化三大原则**  
**答**：  
1. 多阶段构建（减少构建依赖）  
2. Alpine基础镜像（5MB极简镜像）  
3. 合并RUN指令（减少镜像层数）

### 中级
**Q2：跨主机网络配置**  
**答**：  
```bash
# 创建overlay网络
docker network create -d overlay --attachable my_net
# 服务间通信验证
docker service create --network my_net --name web nginx
```

### 高级
**Q3：容器安全加固**  
**答**：  
- 启用用户命名空间（`--userns=host`）  
- 限制能力：`--cap-drop=ALL --cap-add=NET_BIND_SERVICE`  
- 只读文件系统：`--read-only`

---

## Kubernetes

### 基础
**Q1：Deployment核心配置**  
**答**：  
```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    spec:
      containers:
      ◦ name: nginx
        image: nginx:1.21
        ports:
        ▪ containerPort: 80
```

### 中级
**Q2：Service流量分发**  
**答**：  
- ClusterIP：内部服务发现  
- NodePort：外部访问基础方案（30000-32767端口）  
- LoadBalancer：云厂商集成方案

### 高级
**Q3：自定义指标扩缩容**  
**答**：  
1. 部署Prometheus Adapter  
2. 创建HPA配置：  
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  metrics:
  • type: Pods
    pods:
      metric: 
        name: http_requests_per_second
      target: 
        type: AverageValue 
        averageValue: 500
```

---

## Linux

### 基础
**Q1：磁盘空间排查**  
**答**：  
```bash
# 查找TOP10大文件
du -ah / | sort -rh | head -n10
# 清理日志：find /var/log -name "*.log" -mtime +7 -delete
```

### 中级
**Q2：系统性能分析**  
**答**：  
- CPU：`top`/`perf`  
- 内存：`free -h`/`vmstat 2`  
- IO：`iostat -x 1`/`iotop`

### 高级
**Q3：内核参数调优**  
**答**：  
```bash
# 调整文件句柄限制
echo "fs.file-max=1000000" >> /etc/sysctl.conf
# TIME_WAIT优化
net.ipv4.tcp_tw_reuse = 1
```

---

## Shell

### 基础
**Q1：日志压缩脚本**  
**答**：  
```bash
#!/bin/bash
find /var/log -name "*.log" -mtime +7 -exec gzip --keep {} \;
```

### 中级
**Q2：服务状态监控**  
**答**：  
```bash
#!/bin/bash
if ! systemctl is-active nginx >/dev/null; then
  systemctl restart nginx
  echo "Nginx restarted" | mail -s "Alert" admin@example.com
fi
```

### 高级
**Q3：K8S自动化运维**  
**答**：  
```bash
#!/bin/bash
failed_pods=$(kubectl get pods -o json | jq '.items[] | select(.status.phase != "Running")')
echo $failed_pods | xargs -I {} kubectl delete pod {}
```

---

## 面试方案调整

### 初级/中级方案升级
| 技术栈   | 新增问题                               | 评分重点                     |
|----------|----------------------------------------|------------------------------|
| Terraform| 多环境变量管理（Q1）                   | 变量作用域理解               |
| Docker   | 多阶段构建实战（Q2）                   | 镜像层优化能力               |
| K8S      | Deployment与Service联动（Q3）         | YAML语法准确性               |

### 高级方案升级
| 技术栈   | 新增问题                               | 评分重点                     |
|----------|----------------------------------------|------------------------------|
| Terraform| 模块化架构设计（Q2）                   | 可复用性验证                 |
| K8S      | 自定义指标扩缩容（Q3）                 | 监控系统集成能力             |
| Shell    | K8S异常Pod自愈脚本（Q3）               | 自动化运维思维               |

---

**引用来源**  
: K8s面试题总结（十一）  
: 2025高薪必备：K8S面试题101问  
: k8s面试中最常见的50个问题  
: kubernetes常见面试题  
: k8s运维面试题【转】  
```
