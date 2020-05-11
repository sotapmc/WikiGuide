# 开发部署

本文主要介绍如何进行开发部署。若要对本项目进行贡献，则必须先进行开发部署。在进行部署之前，请确保你的电脑安装有如下程序

- Python 3.6
- Node.js

## 基本

一般情况下，按照流程，可以 Fork [本项目](//github.com/sotapmc/Monologue)至你自己的 GitHub 账号下，然后进行 `clone`。在本文中就不多供述了。当你 `clone` 下来以后，可以看到如下目录结构

```tree
Monologue
├─backend
│  └─mono-back
├─node_modules
├─public
└─src
```

这个目录就是**前端目录**，前端目录中包含着后端目录，后端目录即 `backend`。在 `backend` 中有一个 `mono-back` 文件夹，属于后端开发的根文件夹，后端的所有文件均放在这个文件夹里。

## 前端部署

若要部署前端，请确保 `npm` 的版本正确。在这里给出参考：开发此程序时，`npm` 的版本号为 `6.13.4`，Node.js 的版本号为 `12.14.1`。使用命令行前往前端的目录，键入

```bash
npm install
```

安装 `package.json` 内列出的依赖，也就是项目所需要的依赖。安装完毕以后，执行 `npm run serve` 启动前端开发服务器，就可以进行前端的开发了。但是此时只是能进行前端的开发，如果不部署后端，那么前端和后端对接的位置将会出现错误，因此建议继续往下阅读。

## 后端部署

后端部署比较容易，实际上，由于项目自带了相关的依赖，因此通常情况下不需要自己安装依赖

!> 如果提示找不到依赖，则需要手动安装

后端的依赖：

```bash
pip install flask flask-cors flask-restful pymysql pyyaml markdown pymdown-extensions
```

如有需要，可以使用 `pip` 进行安装。

依赖就绪以后，前往后端根目录（前文中提到的 `mono-back` 文件夹），找到 `config.example.yml` 文件，将其内容改为符合当前环境的内容，并重命名为 `config.yml`。随后执行：

```bash
python ./App.py
```

即可启动一个后端服务器。通常情况下，该服务器位于 `localhost:5000`，前端的 `vue.config.js` 里已经有了相关的设置。

## 完成

上述操作全部完成以后，保持前、后端两个开发服务器同时运行，就可以进行正常开发了。

!> 开发服务器的性能低下，不能把开发部署和生产化部署相混淆