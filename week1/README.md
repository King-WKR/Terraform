# Terraform

### 一、环境

- provider:腾讯云 v1.81.87
- windows10
- terraform v1.7.5

### 二、任务目标

- 创建VPC
- 创建tencentcloud_cos_bucket

### 三、任务过程

#### 3.1 使用准备：环境变量鉴权

1. 在腾讯云的访问管理中，创建新用户Sakura，并分配权限AdministratorAccess![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\1.png)
2. 在用户列表中选中该用户，并且新建API密钥，保存CSV文件![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\2.png)
3. 在系统变量中新建TENCENTCLOUD_SECRET_ID，TENCENTCLOUD_SECRET_KEY并输入csv文件中保存的ID和KEY![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\3.png)

#### 3.2 创建VPC

##### 3.2.1 确保创建VPC之前，上海的私有网络为空![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\6.png)

##### 3.2.2 terraform代码

```HCL
terraform {
  required_providers {
    tencentcloud = {
      source = "tencentcloudstack/tencentcloud"
      # 通过version指定版本
      # version = ">=1.60.18"
    }
  }
}

#注释掉了id和key，使用的是本地环境变量保存敏感信息
provider "tencentcloud" {
  region = "ap-shanghai"
  # secret_id = "my-secret-id"
  # secret_key = "my-secret-key"
}
```

##### 3.2.2 运行过程

1. terraform init初始化Terraform，下载所需要的模块，已经做了init加速处理，所以运行只需10秒左右![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\4.png)
2. terraform plan查看当前的配置文件和状态文件来计算出需要对基础设施进行的变更。它会列出将要添加、修改或删除的资源，并显示这些变更的详细信息，例如资源名称、类型、属性变化等。![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\5.png)
3. terraform apply申请所写的VPC资源![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\7.png)

4. 检查是否申请成功![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\8.png)

> 私有网络创建成功

#### 3.3 创建tencentcloud_cos_bucket

##### 3.3.1 获取appid

> 右上角点击账号，选择账号信息获取APPID

##### <img src="C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\9.png" style="zoom: 33%;" />

##### <img src="C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\10.png" style="zoom: 50%;" />

##### 3.3.2 terraform代码

```HCL
resource "tencentcloud_cos_bucket" "mycos" {
  bucket = "examplebucket-11111111111"     #存储桶名称，存储桶名称格式为 BucketName-APPID
  acl    = "private"  #ACL 权限为私有
}
```

##### 3.3.3 执行结果

> 创建一个空桶

![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\11.png)

##### 3.3.4 terraform也支持桶的创建对象资源

```HCL
resource "tencentcloud_cos_bucket_object" "myobject" {
  bucket = "examplebucket-1302032337"  # 存储桶名称，格式为 BucketName-APPID
  key    = "picture.jpg"   # 对象键
  source = "C:\\Users\\t.wang.kangrui\\Pictures\\Screenshots\\ka.png"  # 待上传文件路径，需要包含路径和文件名
}
```

这里上传了一个照片，可以在控制台中查到已经上传成功。

![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\13.png)

![](C:\Users\t.wang.kangrui\Desktop\terraformstudy\report1\12.png)

##### 使用terraform destroy可以销毁桶。

### 四、结论

​通过以上操作，我已经成功地使用 Terraform 创建了腾讯云的基础云资源，包括创建了一个虚拟私有云VPC以及一个腾讯云对象存储桶COS Bucket。
在这个过程中，我学会了如何配置 Terraform 环境、使用 Terraform 的声明式语法来描述云资源、执行 Terraform 命令来创建和管理这些资源。
通过 Terraform 的plan和apply，我可以清晰地查看将要应用的变更，以及应用这些变更来创建或更新云资源。同时，我也学会了如何在 Terraform 中使用本地环境变量来保存敏感信息，如密钥和密钥 ID，以确保安全性。
最后，我还学习了如何使用 Terraform 来上传文件到 COS 桶中，并且能够在腾讯云控制台中验证上传的文件。通过执行destroy命令，在不需要资源时安全地清理和销毁这些资源，以避免不必要的费用开销。
综上所述，通过本次操作，我已经掌握了使用 Terraform 创建和管理腾讯云资源的基本方法，并且有了深入学习的基础。
