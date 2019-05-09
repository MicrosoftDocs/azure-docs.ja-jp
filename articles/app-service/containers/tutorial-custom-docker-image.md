---
title: Web App for Containers のカスタム イメージを作成する - Azure App Service | Microsoft Docs
description: Web App for Containers のカスタム Docker イメージを使用する方法。
keywords: Azure App Service, Web アプリ, Linux, Docker, コンテナー
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8463ffcb9d9983ff435c01f75dd48f68bde31767
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545604"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>チュートリアル:カスタム イメージを作成し、プライベート レジストリから App Service 内で実行する

[App Service](app-service-linux-intro.md) は、PHP 7.0 や Node.js 4.5 などの特定のバージョンをサポートする組み込みの Docker イメージを Linux 上で提供します。 App Service では、Docker コンテナー テクノロジを使用して、組み込みイメージとカスタム イメージの両方をサービスとしてのプラットフォームとしてホストします。 このチュートリアルでは、カスタム イメージを作成し、App Service 内で実行する方法について学習します。 このパターンは、組み込みイメージに選択した言語が含まれない場合や、アプリケーションで組み込みイメージで提供されない特定の構成が必要となる場合に便利です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * カスタム イメージをプライベート コンテナー レジストリにデプロイする
> * App Service 内でカスタム イメージを実行する
> * 環境変数を構成する
> * イメージを更新して再デプロイする
> * 診断ログにアクセスする
> * SSH を使用してコンテナーに接続する

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製し、サンプル コードを含むディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Docker ファイルからイメージを作成する

Git リポジトリで、_Dockerfile_ を確認してください。 このファイルには、アプリケーションの実行に必要な Python 環境が記述されています。 さらに、イメージはコンテナーとホスト間のセキュアな通信用に [SSH](https://www.ssh.com/ssh/protocol/) サーバーを設定します。

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

`docker build` コマンドを使用して Docker イメージをビルドします。

```bash
docker build --tag mydockerimage .
```

Docker コンテナーを実行して、ビルドの動作をテストします。 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) コマンドを発行し、イメージの名前とタグを渡します。 必ず `-p` 引数を使用してポートを指定してください。

```bash
docker run -p 8000:8000 mydockerimage
```

`http://localhost:8000` を参照して、Web アプリとコンテナーが正しく機能していることを確認します。

![ローカルでの Web アプリのテスト](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

先ほど作成したイメージを使用するアプリを作成するには、Azure CLI コマンドを実行してリソース グループを作成し、そのイメージをプッシュし、それを実行する App Service プラン Web アプリを作成します。

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

Cloud Shell で、[`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) コマンドを使用して Azure Container Registry を作成します。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Azure Container Registry にサインインする

イメージをレジストリにプッシュするには、プライベート レジストリで認証する必要があります。 Cloud Shell で、[`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) コマンドを使用して、作成したレジストリから資格情報を取得します。

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

出力には、2 つのパスワードがユーザー名と共に含まれています。

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
  "username": "<registry-username>"
}
```

ローカルのターミナル ウィンドウから、次の例に示すように `docker login` コマンドを使用して Azure Container Registry にサインインします。 *\<azure-container-registry-name>* と *\<registry-username>* を、自分のレジストリの値に置き換えます。 入力を求められたら、前の手順のいずれかのパスワードを入力します。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

ログインが成功したことを確認します。

### <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

Azure Container Registry のコンテナー イメージをタグ付けします。 例: 
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

コマンド `docker push` を使用してイメージをプッシュします。 レジストリの名前、イメージの名前、およびタグでイメージをタグ付けします。

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Cloud Shell に戻り、プッシュが成功したことを確認します。

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

次の出力が表示されます。

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Create App Service plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Web アプリの作成

Cloud Shell で [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) コマンドを使用して、`myAppServicePlan` App Service プランに [Web アプリ](app-service-linux-intro.md)を作成します。 _\<app-name>_ を一意のアプリ名に置き換え、_\<azure-container-registry-name>_ を自分のレジストリ名に置き換えます。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Web アプリにレジストリの資格情報を構成する

App Service でプライベート イメージをプルするには、レジストリとイメージに関する情報が必要です。 Cloud Shell で、[`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) コマンドを使用してそれらを提供します。 *\<app-name>*、*\<azure-container-registry-name>*、_\<registry-username>_、および _\<password>_ を置き換えます。

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Docker Hub 以外のレジストリを使用する場合、`--docker-registry-server-url` は、`https://` の後にレジストリの完全修飾ドメイン名が続く形式にする必要があります。
>

### <a name="configure-environment-variables"></a>環境変数を構成する

ほとんどの Docker イメージは、80 以外のポートなどのカスタム環境変数を使用します。 イメージが使用するポートを App Service に指示するには、`WEBSITES_PORT` アプリケーション設定を使用します。 [このチュートリアルに含まれる Python サンプル](https://github.com/Azure-Samples/docker-django-webapp-linux)の GitHub ページは、`WEBSITES_PORT` を _8000_ に設定する必要があることを示しています。

アプリ設定を設定するには、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 アプリケーション設定は、大文字と小文字を区別し、スペースで区切られます。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Web アプリをテストする

Web アプリの動作を確認するには、これを参照します (`http://<app-name>.azurewebsites.net`)。

> [!NOTE]
> 初めてアプリにアクセスしたときは、App Service がイメージ全体をプルする必要があるため、時間がかかる場合があります。 ブラウザーがタイムアウトした場合は、単にページを更新します。

![Web アプリ ポート構成をテストする](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Web アプリを変更して再デプロイする

ローカルの Git リポジトリで、app/templates/app/index.html を開きます。 最初の HTML 要素を見つけて、それを次のように変更します。

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Python ファイルを変更し、保存した後で、新しい Docker イメージをリビルドし、プッシュする必要があります。 次に、変更を反映するために Web アプリを再起動します。 このチュートリアルで前に使用したのと同じコマンドを使用します。 「[Docker ファイルからイメージを作成する](#build-the-image-from-the-docker-file)」と「[Azure Container Registry へのイメージのプッシュ](#push-image-to-azure-container-registry)」を参照してください。 「[Web アプリをテストする](#test-the-web-app)」の手順に従って、Web アプリをテストします。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>SSH 接続を有効にする

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 コンテナーへの SSH 接続を有効にするには、それに合わせてカスタム イメージを構成する必要があります。 必要な構成が既にあるサンプル リポジトリを見てみましょう。

* この [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile) の次のコードでは、SSH サーバーをインストールし、さらにサインイン資格情報を設定しています。

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > この構成では、コンテナーへの外部接続は許可されません。 SSH は Kudu/SCM サイトを通してのみ利用できます。 Kudu/SCM サイトは Azure アカウントにより認証されます。

* この [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) では、[sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config file in the repository) を */etc/ssh/* ディレクトリにコピーしています。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* この [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) では、コンテナーでポート 2222 を公開しています。 それはプライベート仮想ネットワークのブリッジ ネットワーク内でコンテナーのみがアクセスできる内部ポートです。 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* この[エントリ スクリプト](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5)では、SSH サーバーを起動しています。

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

```bash
top
```

`top` コマンドは、コンテナー内のすべての実行中のプロセスを公開します。

```
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

お疲れさまでした。 App Service でカスタムの Linux コンテナーを構成できました。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * カスタム イメージをプライベート コンテナー レジストリにデプロイする
> * App Service 内でカスタム イメージを実行する
> * 環境変数を構成する
> * イメージを更新して再デプロイする
> * 診断ログにアクセスする
> * SSH を使用してコンテナーに接続する

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [カスタム コンテナーの構成](configure-custom-container.md)

> [!div class="nextstepaction"]
> [チュートリアル:マルチコンテナーの WordPress アプリ](tutorial-multi-container-app.md)
