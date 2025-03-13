面试方案一
一、面试步骤及题目
1. 开场与自我介绍（5 分钟）
候选人需重点阐述在 Terraform、Docker、K8S 等技术栈中的深度实践经验。
2. 技术深度提问（20 分钟）
Terraform（由浅入深）
基础：Terraform 配置文件中provider和resource的作用是什么？如何定义一个 AWS EC2 实例？
答案：
provider定义云厂商（如 AWS），resource声明基础设施资源。
示例代码：
hcl
provider "aws" {  
  region = "us-west-2"  
}  
resource "aws_instance" "example" {  
  ami           = "ami-0c55b159cbfafe1f0"  
  instance_type = "t2.micro"  
}  

进阶：Terraform 状态文件（terraform.tfstate）的作用是什么？如何管理多个环境的状态？
答案：
状态文件记录资源 ID 和元数据，用于跟踪变更。
通过terraform workspace管理多环境（如dev、prod），或使用远程状态存储（如 AWS S3 + DynamoDB 锁）。
高级：如何在 Terraform 中实现多云资源管理？结合provider和data资源说明。
答案：
定义多个provider块（如 AWS 和 Azure），通过data资源引用其他云的资源。
示例：
hcl
provider "aws" { alias = "east" region = "us-east-1" }  
provider "azure" { alias = "central" location = "centralus" }  
data "aws_vpc" "existing" { provider = aws.east }  

Docker（由浅入深）
基础：Dockerfile 中COPY和ADD指令的区别是什么？如何构建一个 Java 应用镜像？
答案：
COPY仅复制文件，ADD支持解压 tar 和远程 URL。
Dockerfile 示例：
dockerfile
FROM openjdk:17  
COPY target/app.jar /app.jar  
CMD ["java", "-jar", "/app.jar"]  

进阶：如何优化 Docker 镜像大小？多阶段构建的原理是什么？
答案：
优化方法：删除不必要文件、使用alpine基础镜像、减少层操作。
多阶段构建：通过多个FROM指令，仅保留最终运行所需文件。
dockerfile
FROM maven:3.8.6 AS build  
COPY pom.xml .  
RUN mvn package  

FROM openjdk:17-jre  
COPY --from=build target/app.jar /app.jar  

高级：Docker 存储驱动（如 overlay2、btrfs）的区别是什么？如何排查容器网络连通性问题？
答案：
存储驱动影响镜像层合并和性能，overlay2 轻量级，btrfs 支持快照。
排查网络：使用docker inspect查看容器网络配置，docker exec进入容器执行ping/traceroute。
Kubernetes（由浅入深）
基础：Kubernetes 中Pod和Deployment的关系是什么？如何暴露 Pod 为服务？
答案：
Pod是最小调度单元，Deployment管理 Pod 的生命周期。
通过Service暴露 Pod，示例：
yaml
apiVersion: v1  
kind: Service  
metadata: name: my-service  
spec:  
  selector: app: my-app  
  ports: - port: 80 targetPort: 8080  

进阶：Kubernetes 调度策略有哪些？如何通过NodeSelector和Taint/Toleration实现节点亲和性？
答案：
调度策略：默认调度、NodeSelector、NodeAffinity、Taint/Toleration。
NodeSelector通过标签匹配节点，Taint标记节点不可调度，Toleration允许 Pod 调度到带 Taint 的节点。
高级：Kubernetes Service Mesh（如 Istio）如何实现服务间认证？流量镜像的作用是什么？
答案：
通过 Istio 的ServiceEntry和DestinationRule配置 mTLS 认证。
流量镜像：将生产流量复制到测试环境，验证新版本兼容性。
3. 场景分析（10 分钟）
题目：在 Kubernetes 集群中部署一个高可用的微服务，如何利用 StatefulSet、Headless Service 和 PersistentVolumeClaim 保证数据一致性？
答案：
StatefulSet：管理有状态服务，确保 Pod 有序部署和唯一标识。
Headless Service：为 Pod 分配固定 DNS，便于服务发现。
PersistentVolumeClaim：绑定持久化存储，保证数据持久化。
4. 候选人反问（5 分钟）
二、打分规则与建议
技术领域	基础问题（1-2 分）	进阶问题（3-4 分）	高级问题（5 分）
Terraform	能写出简单资源配置	理解状态管理和多环境隔离	掌握多云配置和数据源引用
Docker	熟悉基础指令和 Dockerfile 语法	掌握镜像优化和多阶段构建	深入理解存储驱动和网络排错
K8S	熟悉 Pod/Deployment/Service 概念	掌握调度策略和节点亲和性配置	能设计 Service Mesh 和流量管理方案
建议：
关注候选人对底层原理的理解（如 Terraform 执行计划生成、Docker 镜像分层）。
考察候选人是否有大规模集群实践经验（如管理 500 + 节点的 K8S 集群）。
面试方案二
一、面试步骤及题目
1. 开场与自我介绍（5 分钟）
候选人需突出在 Linux 系统调优、Shell 脚本自动化方面的经验。
2. 技术深度提问（20 分钟）
Linux（由浅入深）
基础：如何查找占用 CPU 最高的进程？top和htop的区别是什么？
答案：
使用top或htop，按P键按 CPU 排序。
htop交互性更强，支持鼠标操作和进程树展示。
进阶：如何排查 Linux 服务器内存泄漏问题？结合free、vmstat、valgrind说明。
答案：
free查看内存使用总量，vmstat监控内存交换，valgrind分析进程内存泄漏。
高级：Linux 内核参数vm.swappiness的作用是什么？如何优化 Swap 分区使用？
答案：
vm.swappiness控制内存与 Swap 的交换倾向（0-100），建议设置为 10 减少 Swap 使用。
优化方法：增加物理内存、使用 SSD 作为 Swap 设备。
Shell 脚本（由浅入深）
基础：如何统计文件中包含特定字符串的行数？写出 Shell 命令。
答案：
grep -c "keyword" filename.txt 或 awk '/keyword/{count++} END{print count}' filename.txt。
进阶：Shell 脚本中如何实现参数校验？结合getopts或case语句举例。
答案：
使用getopts处理短选项：
bash
while getopts ":a:b" opt; do  
    case $opt in  
        a) echo "Option a: $OPTARG" ;;  
        b) echo "Option b" ;;  
    esac  
done  

高级：如何编写一个可重入的 Shell 脚本？防止并发执行的方法有哪些？
答案：
使用flock命令加文件锁：
bash
exec 200>/tmp/myscript.lock  
flock -xn 200 || exit 1  
# 脚本逻辑  
flock -u 200  

Kubernetes（由浅入深）
基础：Kubernetes 中ReplicaSet和Deployment的区别是什么？
答案：
ReplicaSet确保 Pod 副本数，Deployment支持滚动更新和回滚。
进阶：如何监控 Kubernetes 集群资源使用情况？结合kubectl top和 Prometheus 说明。
答案：
kubectl top查看节点 / Pod 资源使用，Prometheus 通过kube-state-metrics采集指标。
高级：Kubernetes 联邦（Cluster Federation）的作用是什么？如何实现跨集群应用部署？
答案：
联邦管理多个 K8S 集群，通过FederatedDeployment同步应用到各集群。
3. 场景分析（10 分钟）
题目：如何使用 Terraform 和 Ansible 实现基础设施即代码（IaC）？请描述完整流程。
答案：
Terraform 定义基础设施（EC2、VPC），输出资源信息（如 IP 地址）。
Ansible 通过 Terraform 输出的 IP 地址，部署应用和配置（如 Nginx 服务）。
4. 候选人反问（5 分钟）
二、打分规则与建议
技术领域	基础问题（1-2 分）	进阶问题（3-4 分）	高级问题（5 分）
Linux	熟悉常用监控命令	掌握内存 / CPU 分析工具	理解内核参数调优和性能优化
Shell 脚本	能编写简单脚本	掌握参数处理和流程控制	实现脚本并发控制和异常处理
K8S	熟悉基础资源对象	掌握监控和调度策略	理解多集群管理和联邦部署
建议：
对 Shell 脚本问题，要求候选人写出可运行的代码片段，并解释逻辑。
在 Linux 性能优化问题中，考察候选人是否有实际故障排查案例（如线上 OOM 问题处理）。
