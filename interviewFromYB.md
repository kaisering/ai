好的，我现在需要帮用户将Jenkins Groovy共享库和Ansible相关的问题和答案整合到已有的DevOps面试方案中。用户之前已经要求加入Terraform、Docker、K8S、Linux、Shell的内容，现在需要补充Jenkins和Ansible的部分，每个技术三个问题和答案，由浅入深，并且调整面试方案。

首先，我需要回顾用户提供的搜索结果。关于Jenkins共享库的问题，有三个相关的搜索结果：[1]、[2]、[3]。其中，[1]提到无法加载共享库的解决方法，[2]讨论Groovy脚本中的方法缺失问题，[3]详细介绍了共享库的结构和使用方法。而Ansible的搜索结果包括[4]、[5]、[6]，涉及RHCE考题、Ansible基础与高级使用，以及并发写入问题。

接下来，我需要为Jenkins+Groovy共享库设计三个问题，难度递增。基础问题可能涉及共享库的配置和结构，中级问题可以是解决常见错误如MissingMethodException，高级问题可能涉及安全或优化。每个问题需要对应的答案，并引用相关搜索结果。

对于Ansible，同样需要三个问题。基础问题可能是安装和配置，中级问题关于临时命令或Playbook编写，高级问题涉及并发处理或模块开发。需要参考搜索中的RHCE考题[4]、Ansible概述[5]和并发问题[6]。

在整合到面试方案时，初级/中级方案中加入基础和中级的Jenkins和Ansible问题，高级方案中加入高级问题。同时确保评分规则和淘汰红线有相应更新，比如检查Ansible Playbook的正确性和并发处理能力。

需要确保每个技术的问题分布合理，答案准确，并正确引用搜索结果中的内容。例如，Jenkins共享库的结构引用[3]，Ansible的并发问题引用[6]，安装配置参考[4][5]。

最后，调整面试方案的结构，保持与其他技术部分一致的格式，使用Markdown，并正确分配问题和答案到不同级别的面试中。确保新增内容符合用户要求的由浅入深，每个技术三个问题和答案，并引用相应的搜索结果。




```markdown
# DevOps面试题库扩展版（新增Jenkins Groovy共享库 & Ansible）

---

## Jenkins Groovy共享库

### 基础
**Q1：如何正确配置Jenkins全局共享库？**  
**答**：  
1. **路径配置**：  
   - 进入`Jenkins管理 → 全局工具配置`，在"Global Shared Libraries"中添加库名称和仓库地址  
   - 示例配置：  
     ```groovy
     library name: 'my-shared-lib', 
     defaultVersion: 'main', 
     retriever: modernSCM([$class: 'GitSCMSource', remote: 'https://github.com/my-repo.git'])
     ```
2. **目录结构要求**：  
   - 必须包含`vars`目录（存放全局变量脚本）和`src`目录（Groovy类文件）  
   - 示例结构：  
     ```
     shared-library/
     ├── vars/
     │   └── deploy.groovy
     └── src/
         └── org/
             └── devops/
                 └── Utils.groovy
     ```

### 中级
**Q2：共享库中调用Jenkins Pipeline步骤报错`MissingMethodException`如何解决？**  
**答**：  
**典型场景**：  
```groovy
// 错误示例：直接调用readFile()
void processConfig() {
    def content = readFile('config.yaml') // 报错No signature of method
}
```
**修复方案**：  
- 通过`script`参数传递Pipeline上下文：  
  ```groovy
  void processConfig(def script) {
      def content = script.readFile('config.yaml') // 正确调用
  }
  ```
- 调用时传递`this`对象：  
  ```groovy
  @Library('my-shared-lib') _
  pipeline {
      agent any
      stages {
          stage('Demo') {
              steps {
                  myLib.processConfig(this) // 传递Pipeline上下文
              }
          }
      }
  }
  ```

### 高级
**Q3：如何实现共享库的安全加固？**  
**答**：  
1. **权限控制**：  
   - 使用`Jenkins Credentials`管理敏感信息（如API密钥）  
   - 在共享库中使用`withCredentials`封装认证逻辑：  
     ```groovy
     def call(String credId) {
         withCredentials([string(credentialsId: credId, variable: 'TOKEN')]) {
             sh "curl -H 'Authorization: Bearer $TOKEN' https://api.example.com"
         }
     }
     ```
2. **代码审计**：  
   - 启用`Script Security Plugin`进行Groovy脚本沙箱检查  
   - 对共享库代码执行静态分析（如SonarQube）

---

## Ansible

### 基础
**Q1：如何配置Ansible主机清单与基本连接？**  
**答**：  
**核心配置**：  
```ini
# inventory文件示例
[web]
web1 ansible_host=192.168.1.101
web2 ansible_host=192.168.1.102

[db]
db1 ansible_host=192.168.1.201

[all:vars]
ansible_user=admin 
ansible_ssh_private_key_file=~/.ssh/ansible_key
```
**验证连接**：  
```bash
ansible all -m ping  # 测试所有主机连通性
```

### 中级
**Q2：如何通过Playbook实现软件包批量安装？**  
**答**：  
```yaml
# packages.yml示例
• name: Install base packages
  hosts: web
  tasks:
    ◦ name: Install PHP and MariaDB
      yum:
        name: 
          ▪ php
          ▪ mariadb-server
        state: present
    
    ◦ name: Update all packages (Dev环境)
      yum:
        name: '*'
        state: latest
      when: "'dev' in group_names"  # 条件执行
```

### 高级
**Q3：如何解决Ansible并发写入导致的数据丢失问题？**  
**答**：  
**问题场景**：多个主机同时向控制节点写入文件导致数据覆盖  
**解决方案**：  
1. **本地临时文件+聚合**：  
   ```yaml
   • name: 生成并存储密码
     hosts: all
     tasks:
       ◦ name: Generate password
         shell: openssl rand -hex 12
         register: password
         delegate_to: localhost  # 在控制节点生成
         
       ◦ name: Save to local temp file
         copy:
           content: "{{ inventory_hostname }}:{{ password.stdout }}"
           dest: "/tmp/{{ inventory_hostname }}.tmp"
         
     post_tasks:
       ◦ name: Aggregate files
         assemble:
           src: "/tmp/"
           dest: "/final/passwords.txt"
         run_once: yes  # 仅执行一次
   ```
2. **锁机制**：  
   ```yaml
   • name: Safe write with lock
     lineinfile:
       path: /data/config.cfg
       line: "{{ new_config }}"
     delegate_to: localhost
     vars:
       ansible_ssh_executable: '/usr/bin/ansible-lock-wrapper'  # 自定义锁脚本
   ```

---

## 面试方案调整

### 初级/中级方案升级
| 技术栈         | 新增问题                                | 评分重点                     |
|----------------|----------------------------------------|------------------------------|
| Jenkins        | 共享库路径配置（Q1）                    | 目录结构规范性               |
| Ansible        | Playbook条件执行（Q2）                 | YAML语法准确性               |

### 高级方案升级
| 技术栈         | 新增问题                                | 评分重点                     |
|----------------|----------------------------------------|------------------------------|
| Jenkins        | 共享库安全设计（Q3）                   | 敏感数据处理能力             |
| Ansible        | 并发写入解决方案（Q3）                 | 分布式任务协调能力           |

**淘汰红线新增项**：  
- 无法正确处理共享库的上下文传递（Jenkins）  
- Playbook中未处理并发写入竞态条件（Ansible）

**引用来源**  
: Jenkins共享库配置问题  
: Groovy方法调用异常解决方案  
: 共享库结构设计规范  
: Ansible RHCE考题实战  
: Ansible核心机制解析  
: 并发写入问题案例分析
```
