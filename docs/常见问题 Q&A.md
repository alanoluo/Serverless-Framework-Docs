### 项目安装常见问题
1. [安装过于缓慢怎么办？](#install-1)
2. [输入 Serverless 指令不生效？](#install-2)

### 开发环境常见问题

1. [开发环境在中国境外时如何部署？](#1)
2. [部署时显示交互为英文？](#2)
3. [开发环境在中国境外部署缓慢如何处理？](#3)
4. [用户环境无外网权限，必须通过代理才能访问外网，该如何部署？](#4)
5. [使用 Windows Powershell 部署无权限如何处理？](#5)

### 权限配置常见问题
1. [报错“The appid is unavailable for legal reasons” 如何处理？](#cre-1)
2. [报错“you are not authorized to perform operation resource has no permission”如何处理？](#cre-2)
3. [子账号缺少权限该如何处理？](#cre-3)

### 部署流程常见问题
1. [通过 MongoDB 组件部署全栈应用，报错 "EnvId is invalid"是什么原因？](#deploy-1)
2. [通过组件创建 API 网关触发器和云函数，为什么函数控制台不显示触发器信息?](#deploy-2)
3. ["cdn host no icp"是什么报错？](#deploy-3)
4. [serverless.yml 配置信息不生效？](#deploy-4)
5. [部署时参数校验失败？](#deploy-5)




<span id="install-1"></span>
#### 安装过于缓慢怎么办？
为保证安装速度和稳定性，建议您使用 cnpm 来完成安装，通过命令 `npm install -g cnpm --registry=https://registry.npm.taobao.org` 安装 cnpm，然后将所有使用的 npm 命令替换为 cnpm 即可。

<span id="install-2"></span>
#### 输入 Serverless 指令不生效？
初始化项目时，必须保证当前目录下无 `serverless.yml` 文件，否则 Cli 会判断当前目录已经是 serverless 项目，无法初始化应用。

<span id="1"></span>
#### 开发环境在中国境外时如何部署？
由于 Serverless Framework 在部署时会默认检测是否为中国用户，如果开发环境在中国境外，但希望使用中国版体验的 Serverless Framework，可以在 .env 文件中增加配置 `SERVERLESS_PLATFORM_VENDOR=tencent` 即可指定默认提供中国版体验。

<span id="2"></span>
#### 部署时显示交互为英文？
如果您的部署环境配置了代理，可能会出现该问题，请确认您的开发环境是否在中国境内，然后在 .env 文件中增加配置 `SERVERLESS_PLATFORM_VENDOR=tencent` 即可。

<span id="3"></span>
#### 开发环境在中国境外部署缓慢如何处理？
Serverless Framework 部署引擎目前在是在中国境内，因此境外部署时，在上传文件过程中可能会非常缓慢，可以通过在 `.env` 文件中增加配置 `GLOBAL_ACCELERATOR_NA=true` 开启境外加速 。

<span id="4"></span>
#### 用户环境无外网权限，必须通过代理才能访问外网，该如何部署？
在 .env 文件中增加以下配置即可：
```
HTTP_PROXY=http://127.0.0.1:12345  #请将'12345'替换为您的代理端口
HTTPS_PROXY=http://127.0.0.1:12345  #请将'12345'替换为您的代理端口
```

<span id="5"></span>
#### 使用 Windows Powershell 部署无权限如何处理？
Windows powershell 的权限管理比较严格，需要执行 set-executionpolicy remotesigned 命令后即可正常部署。此外，Windows 环境下建议通过 serverless deploy 全拼方式部署。

<span id="cre-1"></span>
#### 报错“The appid is unavailable for legal reasons” 如何处理？
该报错是由于账户欠费，无法创建新的后付费资源所导致的。请您检查账户是否欠费，账户冲正后即可解决。

<span id="cre-2"></span>
#### 报错“you are not authorized to perform operation resource has no permission”如何处理？

该报错是由于账号本身或调用角色 SLS_QcsRole 缺少相关权限导致的，请根据报错信息，确定缺少的策略，再通过 [CAM 访问管理控制台](https://console.cloud.tencent.com/cam)，将缺少的策略赋予账号以及角色 SLS_QcsRole。

报错信息示例：
```
 [request id:xxxxx]you are not authorized to perform operation (scf:CreateFunction) resource (qcs::scf:gz:uin/xxxxxx:function/*) has no permission
```
从报错信息可知，缺少 `scf:CreateFunction` 的权限，因此需要登陆控制台，为对应的账号与角色 SLS_QcsRole 赋予 `SCFFullAccess` 的策略。

<span id="cre-3"></span>
#### 子账号缺少权限该如何处理？
参考[子账号权限配置说明](https://github.com/AprilJC/Serverless-Framework-Docs/blob/main/docs/%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/%E6%9D%83%E9%99%90%E9%85%8D%E7%BD%AE%E8%AF%B4%E6%98%8E.md#%E5%AD%90%E8%B4%A6%E5%8F%B7%E6%9D%83%E9%99%90%E9%85%8D%E7%BD%AE)


<span id="deploy-1"></span>
#### 报错 "EnvId is invalid"是什么原因？
TCB DB 组件目前默认为用户创建一个免费的 TCB 环境，如果您已有免费环境，通过 Serverless Component 再次创建会失败报错，您可删去 db 文件夹，通过配置 demo 目录中 backend --> serverless.yml 中的 MongoId 参数，输入您已有 TCB 环境的 ID，完成项目的部署。

<span id="deploy-2"></span>
#### 通过组件创建 API 网关触发器和云函数，为什么函数控制台不显示触发器信息?

Serverless 组件通过调用 API 网关接口完成网关触发器创建，目前 SCF 控制台不支持显示通过网关接口创建的触发器，您可以在 [API 网关控制台](https://console.cloud.tencent.com/apigateway/index) 完成相关触发器的配置管理。

<span id="deploy-3"></span>
#### "cdn host no icp" 是什么报错？
腾讯云规定，在使用 CDN 加速域名或自定义域名时，域名必须经过 icp 备案，否则无法生效，详情请查看[域名备案](https://cloud.tencent.com/document/product/243/37402)。

<span id="deploy-4"></span>
#### serverless.yml 配置信息不生效？
yml 文件的配置需要严格按照示例格式填写，请确定您的配置文件内容与缩进格式都正确无误。

<span id="deploy-5"></span>
#### 部署时参数校验失败？
为保证应用成功部署，serverless framework 会对 yml 里的参数进行格式校验，请确保您的参数格式符合规定，如果不需要的参数请直接删除，置空也会导致校验失败。