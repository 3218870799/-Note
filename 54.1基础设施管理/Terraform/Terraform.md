基础架构即代码，支持市面上所有的云服务，解决云上硬件资源分配管理的问题；



优点：

并发创建，速度快；

扩容/缩容 很方便 改一个数字就行；

state文件记录资源状态；



与Ansible结合；

# 二：安装与配置

安装：

下载zip 文件 [https://www.terraform.io/downloads.html](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.terraform.io%2Fdownloads.html)

解压后直接就能用。把文件放到合适的路径，比如 /usr/local/bin



配置：

有两个部分，

provider 和 resource。provider 告知与哪一个云平台打交道，这里是Azure；如果使用AWS，这里就写成 provider "aws"。第二部分是资源，说明要生成哪些资源，例子中是resource group，还可以继续往下写，比如网卡，存储，虚拟机等。

```properties
# Configure the provider
provider "azurerm" {
    version = "=1.20.0"
}

# Create a new resource group
resource "azurerm_resource_group" "rg" {
    name     = "royTR"
    location = "eastasia"
}
```



1：创建资源

在初始化项目的时候，Terraform 会解析目录下的*.tf文件并加载相关的 provider插件

```shell
# 初始化
terraform init

# 
terraform apply

# 查看状态
terraform state show

```

2：更改资源

```shell
# 修改配置文件
```

3：销毁基础设施

```shell
terraform destroy
```

