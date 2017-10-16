---
title: "Azure Web App for Containers のカスタム Docker イメージを使用する | Microsoft Docs"
description: "Azure Web App for Containers のカスタム Docker イメージを使用する方法。"
keywords: "Azure App Service, Web アプリ, Linux, Docker, コンテナー"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 10795d59b019d7c683abfeea611d5909b88891ec
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>Azure Web App for Containers のカスタム Docker イメージを使用する

[Web App for Containers](app-service-linux-intro.md) は、PHP 7.0 や Node.js 4.5 などの特定のバージョンをサポートする組み込みの Docker イメージを Linux 上で提供します。 Web App for Containers では、Docker コンテナー テクノロジを活用して、組み込みイメージとカスタム イメージの両方をサービスとしてのプラットフォームとしてホストします。 このチュートリアルでは、Web App for Containers で使用するカスタム Docker イメージの作成方法について説明します。これは、お使いの言語に対して組み込みイメージがない場合、または組み込みイメージで提供されていない特定の構成をアプリケーションで必要としている場合の一般的なパターンです。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* [Git](https://git-scm.com/downloads)
* 有効な [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Docker Hub アカウント](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製し、サンプル コードを含むディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>Docker ファイルからイメージを作成する

次の Docker ファイルでは、アプリケーションの実行に必要な Python 環境を記述しています。 さらに、イメージはコンテナーとホスト間のセキュアな通信用に [SSH](https://www.ssh.com/ssh/protocol/) サーバーを設定します。

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Docker イメージを作成するには、`docker build` コマンドを実行し、`mydockerimage` という名前を付け、タグ `v1.0.0` を付けます。 `<docker-id>` を Docker Hub アカウント ID で置換します。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

コマンドによって次のような出力が生成されます。

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

Docker コンテナーを実行して、ビルドの動作をテストします。 [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを発行し、イメージの名前とタグを渡します。 `-p` 引数を使用してポートを指定する必要もあります。

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

Web アプリをローカルに参照して、Web アプリとコンテナーが正しく機能していることを確認します。

![ローカルでの Web アプリのテスト](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>Docker イメージを Docker Hub にプッシュする

レジストリは、イメージをホストし、サービス イメージとコンテナー サービスを提供するアプリケーションです。 イメージを共有するには、レジストリにプッシュする必要があります。 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> プライベート Docker レジストリにプッシュする場合は、 「[Docker イメージをプライベート レジストリにプッシュする](#push-a-docker-image-to-private-registry-optional)」の省略可能な手順をご覧ください。

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub は Docker イメージのレジストリであり、パブリックまたはプライベートの独自のリポジトリをホストすることができます。 カスタム Docker イメージをパブリック Docker Hub にプッシュするには、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用し、完全なイメージ名とタグを指定します。 完全なイメージ名とタグは、次の例のようになります。

```bash
<docker-id>/image-name:tag
```

Docker Hub にログインしていない場合は、イメージをプッシュする前に [docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用してログインします。

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

「ログインに成功しました」のメッセージで、ログインしていることを確認します。 ログインしたら、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュできます。

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

コマンドの出力を調べて、プッシュが成功したことを確認します。

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>Web App for Containers を作成する

Azure Web Apps を使用して、クラウドにネイティブの Linux アプリケーションをホストすることができます。 Web App for Containers を作成するには、グループ、続いてサービス プラン、最後に Web アプリ自体を作成する Azure CLI コマンドを実行する必要があります。 まず、[az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) コマンドを実行し、場所と一意の名前を渡します。

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

次の例のような出力が表示されます。

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

グループの名前を使用すると、[az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) コマンドを使用してアプリのサービス プランを作成するのに役立ちます。 一意の名前を指定し、`--is-linux` フラグを設定する必要もあります。

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

サービス プランを作成すると、次の例のような結果が生成されます。

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

これで、リソース グループとサービス プランが作成され、[az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) コマンドを実行して Web アプリを作成することができます。 ランタイム スタックは Python 3.4 で、Web アプリは前の手順のリソース グループとサービス プラン グループを使用します。

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

Web アプリを作成するコマンドでは、次に示す出力が生成されます。

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

ほとんどの Web アプリには、構成する必要があるアプリケーション設定があります。 他のユーザーによって作成された既存の Docker イメージを使用する場合、イメージにはアプリケーション用のポート 80 以外のポートが必要な場合があります。 `WEBSITES_PORT` を設定するには、次のコード サンプルで示すように [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings) コマンドを実行します。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> アプリケーション設定は、大文字と小文字が区別されます。
>

これを参照することにより、Web アプリの動作を確認します。 ポート番号を忘れないでください。

![Web アプリ ポート構成をテストする](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>Docker Hub から Docker コンテナーを使用するように Web アプリを構成する

[az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) コマンドでは、カスタム Docker イメージを使用することができます。

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> プライベート Docker レジストリからデプロイする場合は、 「[プライベート レジストリから Docker コンテナーを使用するように Web アプリを構成する](#configure-web-app-to-use-docker-container-from-a-private-registry-optional)」の省略可能な手順をご覧ください。

<!-- # [Docker Hub](#tab/docker-hub)-->

パブリック Docker レジストリを使用するように Web アプリを構成するには、アプリ名、リソース グループ名、イメージの名前と URL を [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) コマンドに渡します。

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

正常な構成変更では、コンテナーに関する一般的な情報が返されます。

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>Azure でアプリケーションをテストする

テストする前に、[az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) を使用して Web アプリを再起動し、構成の変更を反映する必要があります。

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

restart コマンドは Web アプリをすばやく再起動するため、端末にフィードバックは表示されません。 Web アプリが実行されたら、`http://<username>.azurewebsites.net` でその URL を参照して Web アプリをテストします。 アプリに新しいウェルカム メッセージが表示されることを確認します。

![Azure で Web アプリをテストする](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Web アプリを変更して再デプロイする

`app.py` という名前の Python ファイルが `using-custom-docker-image` ディレクトリに存在します。 このファイルには、`Hello World!` メッセージを表示するコード行が含まれています。 行を変更して、メッセージ `Hello World of Web Apps running in Docker Containers!` が表示されるようにします。

```python
return "Hello World of Web Apps running in Docker Containers!"
```

Python ファイルを変更し、保存した後で、新しい Docker イメージをリビルドし、プッシュする必要があります。 次に、変更を反映するために Web アプリを再起動します。 このチュートリアルで前に使用したのと同じコマンドを使用します。 「[Docker ファイルからイメージをビルドする](#build-the-image-from-the-docker-file)」と「[push the Docker image (Docker イメージをプッシュする)](#push-docker-image)」のセクションをご覧ください。 「[Azure でアプリケーションをテストする](#tTest-the-application-in-azure)」の手順に従って、Web アプリをテストします

## <a name="connect-to-web-app-for-containers-using-ssh"></a>SSH を使用して Web App for Containers に接続する

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 カスタム Docker イメージで SSH をサポートするには、Dockerfile に組み込む必要があります。 Docker ファイル自体で SSH を有効にします。 SSH 命令は既にサンプルの dockerfile に追加されているので、独自のカスタム イメージでこれらの手順に従うことができます。

* `apt-get` を呼び出してからルート アカウントのパスワードを `"Docker!"` に設定する [RUN](https://docs.docker.com/engine/reference/builder/#run) 命令。

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > この構成では、コンテナーへの外部接続は許可されません。 SSH は Kudu/SCM サイトを通してのみ利用できます。 Kudu/SCM サイトは、資格情報を発行することで認証されます。

* Docker エンジンに [sshd_config](http://man.openbsd.org/sshd_config) ファイルを "*/etc/ssh/*" ディレクトリにコピーするよう指示する [COPY](https://docs.docker.com/engine/reference/builder/#copy) 命令。 構成ファイルは、Azure-App-Service GitHub リポジトリに格納されている [sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config) ファイルに基づいている必要があります。

    > [!NOTE]
    > *sshd_config* ファイルには次の項目を指定する必要があります。 
    > * `Ciphers` には、`aes128-cbc,3des-cbc,aes256-cbc` の項目を少なくとも 1 つ含める必要があります。
    > * `MACs` には、`hmac-sha1,hmac-sha1-96` の項目を少なくとも 1 つ含める必要があります。

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* コンテナーでポート 2222 を公開する [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 命令。 ルート パスワードはわかっていますが、ポート 2222 はインターネットからアクセスすることはできません。 それはプライベート仮想ネットワークのブリッジ ネットワーク内でコンテナーのみがアクセスできる内部ポートです。 その後、コマンドが SSH 構成の詳細をコピーし、`ssh` サービスを起動します。

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

サンプル コードの `init_container.sh` ファイルには、実行時にコンテナーを初期化する方法についての命令が含まれています。 Dockerfile は、[COPY](https://docs.docker.com/engine/reference/builder/#copy)、[RUN](https://docs.docker.com/engine/reference/builder/#run)、[CMD](https://docs.docker.com/engine/reference/builder/#cmd) の各命令を使用して `init_container.sh` スクリプトを正しく起動します。

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

特定のアプリケーションがコンテナーで実行されていることを確認できます。 コンテナーを検査し、実行中のプロセスを確認するには、まずブラウザーを開いて `https://<app name>.scm.azurewebsites.net/webssh/host` に移動します。 その後、対話型コンソールを表示するページにリダイレクトされます。 プロンプトで `top` コマンドを発行します。

```bash
top
```

`top` コマンドは、コンテナー内のすべての実行中のプロセスを公開します。

```bash
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

ご利用ありがとうございます。 Azure Web App for Containers のカスタム Docker イメージが構成されました。

## <a name="push-a-docker-image-to-private-registry-optional"></a>Docker イメージをプライベート レジストリにプッシュする (省略可能)

Azure Container Registry は、プライベート イメージをホスティングするために Azure から管理される Docker サービスです。 デプロイには、[Docker Swarm](https://docs.docker.com/engine/swarm/)、[Kubernetes](https://kubernetes.io/)、Azure アプリ サービス コンテナーなどの任意の種類を使用できます。 Azure Container Registry の使用はプライベート レジストリの使用と同様であるため、独自のプライベート レジストリを使用する必要がある場合、このタスクを完了する手順も同様です。

[az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) コマンドを使用して Azure Container Registry を作成します。 名前、リソース グループ、SKU として `Basic` を渡します。 利用可能な SKU は `Classic`、`Basic`、`Standard`、`Premium` です。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

コンテナーを作成すると、次の出力が生成されます。

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

レジストリにイメージをプッシュするには、レジストリがプッシュを受け入れるように資格情報を指定する必要があります。 これらの資格情報は、[az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) コマンドを使用して取得することができます。 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
```

このコマンドでは、ユーザー名に使用できる 2 つのパスワードが表示されます。

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

これで必要な資格情報が設定されたので、`docker login` コマンドを使用して Azure Container Registry にログインします。 ログインするには、レジストリの URL が必要です。 `http://{azure-container-registry-name>.azurecr.io` の形式を使用します。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

ログインが成功したことを確認します。 `docker push` コマンドを使用し、レジストリの完全な URL に続いてイメージの名前とタグでイメージにタグを付けることにより、イメージをプッシュします。

```bash
docker push http://<azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

ACR リポジトリを一覧表示して、プッシュによってコンテナーがレジストリに正常に追加されたことを確認します。 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

レジストリ内のイメージを一覧表示することで、`mydockerimage` がレジストリに存在することを確認します。

```json
[
  "mydockerimage"
]
```

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>プライベート レジストリから Docker コンテナーを使用するように Web アプリを構成する (省略可能)

Azure Container Registry に格納されているコンテナーを実行するように、Linux 上の Web アプリを構成できます。 Azure Container Registry の使用はプライベート レジストリの使用と同様であるため、独自のプライベート レジストリを使用する必要がある場合、このタスクを完了する手順も同様です。

[az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) コマンドでは、コンテナー レジストリのパスワードが表示されます。 次の手順で Web アプリの構成に使用できるように、ユーザー名とパスワードの 1 つをコピーします。

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

[az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) コマンドを実行します。 このコマンドは、Web アプリにカスタムの Docker イメージを割り当てます。 URL は `https://{your-registry-username}.azurecr.io` の形式である必要があります。 さらに、Web アプリでは、コンテナー レジストリにアクセスするために、前の手順で取得したユーザー名とパスワードが必要です。

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> レジストリの URL 内の `https` が必要です。
>

コマンドは、次の JSON 文字列のような出力を表示し、構成が正常に変更されたことを示します。

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次のステップ

[Azure App Service Web App for Containers の FAQ](app-service-linux-faq.md)

