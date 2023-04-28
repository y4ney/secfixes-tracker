[English](./README.md)
# Alpine 安全修复追踪

这是一个在Alpine中跟踪安全修复的Flask Web应用程序。

通过使用Alpine secdb，我们通常可以推断出哪些CVE已经被修复了，哪些CVE没有被修复。
secfixes报告模式的一些扩展将允许软件包的维护者贡献误报信息。

在不久的将来，当你搜索它时，安全公告讲报告这些修改。

## 安装
- 推荐python版本
  ```bash
  Python 3.9.7
  ```
- 创建虚拟环境，并安装依赖
    ```
    pip3 install -r requirements.txt
    ```
- (非必须) 如果你想要使用像sqlite这样的数据库，你还需要安装驱动
    ```bash
  pip3 install psycopg2
    ```
- 写配置文件：复制实例文件，并按需修改
    ```bash
  vim application.example.cfg
    ```
- (非必须) 设置环境变量

|变量名|说明|
|:----:|:----:|
|SECFIXES_TRACKER_CONFIG|配置文件路径|
|FLASK_APP| 应用程序名称，例如`secfixes_tracker`|

- 初始化数据库： `flask init-db`

## 使用 
|命令|说明|
|:----:|:----:|
|`flask run`|运行网络服务器。这也可以用Gunicorn或类似的东西来完成，但这里没有涵盖。|
|`flask init-db`|初始化数据库。|
|`flask import-apkindex`|导入配置仓库。|
|`flask import-secfixes`|导入配置的secdb提要。|
|`flask import-nvd [feed-name]`|导入NVD提要，如`2021`或`recent`。导入年度提要后，您只需每天导入“recent”数据反馈|





### `flask update-states`
根据secfixes、NVD和apkindex数据反馈。当前内容更新各种`VulnerabilityState`项目，这应该在上述导入之后运行，按小时计算的任务。

## CPE rewriters
该配置允许定义一组自定义重写器。这些重写者应该是

定义为“lambda”函数，以源包名称为输入。他们是

匹配为`cpe_vendor:source_pkgname`或`cpe_vendor:*`作为catch all。


例如:

```
CUSTOM_REWRITERS = {
    'jenkins:*': lambda x: 'jenkins',
}
```
将定义一个与“jenkins”CPE发布的任何软件包相匹配的重写器

供应商和输出“jenkins”（因为所有jenkins组件都在“jenkins”源中）

Alpine的软件包）。

## Cron

您需要运行导入任务，然后运行更新状态任务。仅此而已

那需要做。

## E-mail

电子邮件正在重新设计，以更好地适应跟踪引擎

已实施。一旦电子邮件内容准备好设置说明，请观看此空间。

## Caveats

目前，数据库模式不稳定。你需要重建你的数据库，升级此软件时。一旦我们达到1.0版本，数据库模式将保持稳定。