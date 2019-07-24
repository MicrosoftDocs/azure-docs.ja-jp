---
title: Web App for Containers でマルチコンテナー アプリを作成する - Azure App Service
description: Azure 上で Docker Compose と Kubernetes の構成ファイル、WordPress および MySQL アプリと共に複数のコンテナーを使用する方法について説明します。
keywords: azure app service, Web アプリ, linux, docker, compose, マルチコンテナー, マルチ コンテナー, コンテナー用の Web アプリ, 複数のコンテナー, コンテナー, kubernetes, wordpress, azure db for mysql, コンテナーを使用した運用データベース
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: cd7edb576264ac8bb8a076bbb4b2970579056f13
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547633"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>チュートリアル:Web App for Containers でマルチコンテナー (プレビュー) アプリを作成する

[Web App for Containers](app-service-linux-intro.md) には、Docker イメージを柔軟に使用できる機能があります。 このチュートリアルでは、WordPress と MySQL を使用してマルチコンテナー アプリを作成する方法について説明します。 このチュートリアルは Cloud Shell で行いますが、[Azure CLI](/cli/azure/install-azure-cli) コマンド ライン ツール (2.0.32 以降) を使用して、これらのコマンドをローカルで実行することもできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Web App for Containers で使用できるように Docker Compose の構成を変換する
> * Web App for Containers で使用できるように Kubernetes の構成を変換する
> * Azure にマルチコンテナー アプリを展開する
> * アプリケーションの設定を追加する
> * コンテナーに永続的ストレージを使用する
> * Azure Database for MySQL に接続する
> * エラーをトラブルシューティングする

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルには、[Docker Compose](https://docs.docker.com/compose/) または [Kubernetes](https://kubernetes.io/) の使用経験が必要となります。

## <a name="download-the-sample"></a>サンプルのダウンロード

このチュートリアルでは、[Docker](https://docs.docker.com/compose/wordpress/#define-the-project) の作成ファイルを使用しますが、Azure Database for MySQL、永続的なストレージ、Redis を含むように変更します。 構成ファイルは [Azure サンプル](https://github.com/Azure-Samples/multicontainerwordpress)にあります。

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

サポートされる構成オプションについては、「[Docker Compose options](configure-custom-container.md#docker-compose-options)」(Docker Compose オプション) を参照してください。

Cloud Shell で、チュートリアルのディレクトリを作成し、そのディレクトリに移動します。

```bash
mkdir tutorial

cd tutorial
```

次に、次のコマンドを実行して、サンプル アプリのリポジトリをチュートリアルのディレクトリに複製します。 その後、`multicontainerwordpress` ディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Cloud Shell で [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所*米国中南部*に作成します。 **Standard** レベルの Linux 上の App Service がサポートされているすべての場所を表示するには、[`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) コマンドを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

通常は、現在地付近の地域にリソース グループおよびリソースを作成します。

コマンドが完了すると、リソース グループのプロパティが JSON 出力に表示されます。

## <a name="create-an-azure-app-service-plan"></a>Azure App Service プランの作成

Cloud Shell で [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) コマンドを使用して、リソース グループに App Service プランを作成します。

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

次の例では、**Standard** 価格レベル (`--sku S1`) を使用して、Linux コンテナー (`--is-linux`) に `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service プランが作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>WordPress および MySQL コンテナーを使用した Docker Compose

## <a name="create-a-docker-compose-app"></a>Docker Compose アプリを作成する

Cloud Shell で [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) コマンドを使って、`myAppServicePlan` App Service プランにマルチコンテナーの [Web アプリ](app-service-linux-intro.md)を作成します。 _\<app-name>_ は忘れずに固有のアプリ名に置き換えてください。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Web アプリが作成されると、Cloud Shell に、次の例のような出力が表示されます。

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>アプリの参照

展開されたアプリ (`http://<app-name>.azurewebsites.net`) を参照します。 アプリの読み込みには数分かかることがあります。 エラーが発生した場合は、数分待ってからブラウザーを更新してください。 問題が発生して解決したい場合は、[コンテナーのログ](#find-docker-container-logs)を確認してください。

![Web App for Containers のサンプル マルチコンテナー アプリ][1]

**おめでとうございます**。Web App for Containers にマルチコンテナー アプリを作成しました。 次に、Azure Database for MySQL を使用するようにアプリを構成します。 この時点で WordPress をインストールしないでください。

## <a name="connect-to-production-database"></a>運用データベースに接続する

運用環境でデータベース コンテナーを使用することはお勧めしません。 ローカル コンテナーはスケーラブルではありません。 代わりに、スケーラブルな Azure Database for MySQL を使用します。

### <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成

[`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) コマンドを使用して Azure Database for MySQL サーバーを作成します。

次のコマンドで、_&lt;mysql-server-name>_ プレースホルダーを MySQL サーバー名に置き換えます (有効な文字は `a-z`、`0-9`、および `-` です)。 この名前は、MySQL サーバーのホスト名 (`<mysql-server-name>.database.windows.net`) の一部であるため、グローバルに一意である必要があります。

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

サーバーの作成が完了するまでに数分かかる場合があります。 MySQL サーバーが作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "southcentralus",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

[`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) コマンドを使用して、MySQL サーバーでクライアント接続を許可するためのファイアウォール規則を作成します。 開始 IP と終了 IP の両方が 0.0.0.0 に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> [アプリで使用する送信 IP アドレスのみを使用する](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)ことで、ファイアウォール規則による制限をさらに厳しくすることができます。
>

### <a name="create-the-wordpress-database"></a>WordPress データベースを作成する

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

データベースが作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>WordPress でデータベース変数を構成する

WordPress アプリをこの新しい MySQL サーバーに接続するには、`MYSQL_SSL_CA` によって定義された SSL CA パスなど、いくつかの WordPress 固有の環境変数を構成します。 [DigiCert](https://www.digicert.com/) の [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) は、以下の[カスタム イメージ](https://docs.microsoft.com/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations)にあります。

これらの変更を行うには、Cloud Shell で [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 アプリケーション設定は、大文字と小文字を区別し、スペースで区切られます。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

アプリ設定が作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql-server-name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

環境変数の詳細については、「[Configure environment variables](configure-custom-container.md#configure-environment-variables)」(環境変数を構成する) を参照してください。

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>MySQL SSL およびその他の構成にカスタム イメージを使用する

既定では、SSL は Azure Database for MySQL に使用されます。 SSL と MySQL を使用するには、WordPress に追加の構成が必要です。 WordPress の "公式イメージ" に追加の構成は用意されていませんが、便宜的に[カスタム イメージ](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/)が用意されています。 実際には、イメージに必要な変更を加えます。

カスタム イメージは、[Docker Hub の WordPress](https://hub.docker.com/_/wordpress/) の "公式イメージ" に基づいています。 Azure Database for MySQL のこのカスタム イメージでは、以下の変更が行われました。

* [SSL 用の Baltimore Cyber Trust Root Certificate ファイルを MySQL に追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [WordPress wp-config.php の MySQL SSL 証明機関の証明書のアプリ設定を使用する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [MySQL SSL に必要な MYSQL_CLIENT_FLAGS の WordPress 定義を追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Redis では、以下の変更が行われました (後のセクションで使用されます)。

* [Redis v4.0.2 の PHP 拡張機能を追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [ファイル抽出に必要な unzip を追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Redis Object Cache 1.3.8 WordPress プラグインを追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [WordPress wp-config.php の Redis ホスト名のアプリ設定を使用する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

カスタム イメージを使用するには、docker-compose-wordpress.yml ファイルを更新します。 Cloud Shell で、「`nano docker-compose-wordpress.yml`」と入力して nano テキスト エディターを開きます。 `image: microsoft/multicontainerwordpress` を使用するように `image: wordpress` を変更します。 データベース コンテナーは不要になります。 構成ファイルから `db`、`environment`、`depends_on`、および `volumes` セクションを削除します。 ファイルは次のコードのようになります。

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

変更内容を保存し、nano を終了します。 コマンド `^O` を使用して保存し、`^X` を使用して終了します。

### <a name="update-app-with-new-configuration"></a>新しい構成でアプリを更新する

Cloud Shell で、[az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) コマンドを使用して、マルチコンテナー [Web アプリ](app-service-linux-intro.md)を再構成します。 _\<app-name>_ は忘れずに前に作成した Web アプリの名前に置き換えてください。

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

アプリが再構成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>アプリの参照

展開されたアプリ (`http://<app-name>.azurewebsites.net`) を参照します。 アプリには Azure Database for MySQL が使用されるようになります。

![Web App for Containers のサンプル マルチコンテナー アプリ][1]

## <a name="add-persistent-storage"></a>永続的ストレージを追加する

マルチコンテナーは Web App for Containers で実行されています。 ただし、すぐに WordPress をインストールして後でアプリを再起動すると、WordPress のインストールがなくなっていることがわかります。 これは、現在、Docker Compose の構成がコンテナー内のストレージの場所を指しているために発生します。 コンテナーにインストールされたファイルは、アプリの再起動後に残りません。 このセクションでは、WordPress コンテナーに[永続的なストレージを追加](configure-custom-container.md#use-persistent-shared-storage)します。

### <a name="configure-environment-variables"></a>環境変数を構成する

永続的なストレージを使用するには、App Service 内でこの設定を有効にします。 この変更を行うには、Cloud Shell で [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 アプリケーション設定は、大文字と小文字を区別し、スペースで区切られます。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

アプリ設定が作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>構成ファイルを変更する

Cloud Shell で、「`nano docker-compose-wordpress.yml`」と入力して nano テキスト エディターを開きます。

`volumes` オプションは、ファイル システムをコンテナー内のディレクトリにマップします。 `${WEBAPP_STORAGE_HOME}` は、アプリの永続的なストレージにマップされる App Service の環境変数です。 ボリューム オプションでこの環境変数を使用すると、WordPress ファイルはコンテナーではなく永続的なストレージにインストールされます。 ファイルを次のように変更します。

`wordpress` セクションで `volumes` オプションを追加すると、次のコードのようになります。

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>新しい構成でアプリを更新する

Cloud Shell で、[az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) コマンドを使用して、マルチコンテナー [Web アプリ](app-service-linux-intro.md)を再構成します。 _\<app-name>_ は忘れずに固有のアプリ名に置き換えてください。

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

コマンドを実行すると、次のような出力が表示されます。

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>アプリの参照

展開されたアプリ (`http://<app-name>.azurewebsites.net`) を参照します。

WordPress コンテナーは、Azure Database for MySQL と永続的なストレージを使用しています。

## <a name="add-redis-container"></a>Redis コンテナーを追加する

 WordPress の "公式イメージ" には Redis の依存関係は含まれていません。 Redis を WordPress と共に使用するために必要なこれらの依存関係と追加の構成が、この[カスタム イメージ](https://github.com/Azure-Samples/multicontainerwordpress)に用意されています。 実際には、イメージに必要な変更を加えます。

カスタム イメージは、[Docker Hub の WordPress](https://hub.docker.com/_/wordpress/) の "公式イメージ" に基づいています。 Redis のこのカスタム イメージでは、以下の変更が行われました。

* [Redis v4.0.2 の PHP 拡張機能を追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [ファイル抽出に必要な unzip を追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Redis Object Cache 1.3.8 WordPress プラグインを追加する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [WordPress wp-config.php の Redis ホスト名のアプリ設定を使用する。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

構成ファイルの一番下に redis コンテナーを追加すると、次の例のようになります。

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>環境変数を構成する

Redis を使用するには、App Service 内でこの設定 `WP_REDIS_HOST` を有効にします。 これは、WordPress が Redis ホストと通信するために*必要な設定*です。 この変更を行うには、Cloud Shell で [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 アプリケーション設定は、大文字と小文字を区別し、スペースで区切られます。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

アプリ設定が作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>新しい構成でアプリを更新する

Cloud Shell で、[az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) コマンドを使用して、マルチコンテナー [Web アプリ](app-service-linux-intro.md)を再構成します。 _\<app-name>_ は忘れずに固有のアプリ名に置き換えてください。

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

コマンドを実行すると、次のような出力が表示されます。

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>アプリの参照

展開されたアプリ (`http://<app-name>.azurewebsites.net`) を参照します。

手順を完了し、WordPress をインストールします。

### <a name="connect-wordpress-to-redis"></a>WordPress を Redis に接続する

WordPress 管理者にログインします。左側のナビゲーションで **[プラグイン]** を選択し、**[Installed Plugins]\(インストールされているプラグイン\)** を選択します。

![WordPress プラグインを選択する][2]

すべてのプラグインがここに表示されます

プラグイン ページで **[Redis Object Cache]\(Redis オブジェクト キャッシュ\)** を見つけて **[アクティブ化]** をクリックします。

![Redis をアクティブ化する][3]

**[設定]** をクリックします。

![[設定] をクリックする][4]

**[Enable Object Cache]\(オブジェクト キャッシュを有効にする\)** ボタンをクリックします。

![[Enable Object Cache]\(オブジェクト キャッシュを有効にする\) ボタンをクリックする][5]

WordPress が Redis サーバーに接続されます。 同じページに接続の**状態**が表示されます。

![WordPress が Redis サーバーに接続されます。 同じページに接続の**状態**が表示されます。][6]

**おめでとうございます**。WordPress を Redis に接続しました。 運用環境対応のアプリが、**Azure Database for MySQL、永続的なストレージ、Redis** を使用するようになりました。 App Service プランを複数のインスタンスにスケールアウトできるようになりました。

## <a name="use-a-kubernetes-configuration-optional"></a>Kubernetes 構成を使用する (省略可能)

このセクションでは、Kubernetes 構成を使用して複数のコンテナーを展開する方法について説明します。 前の手順に従って、[リソース グループ](#create-a-resource-group)と [App Service プラン](#create-an-azure-app-service-plan)を作成します。 手順の大部分は作成セクションの手順と似ているので、構成ファイルは結合されています。

### <a name="kubernetes-configuration-file"></a>Kubernetes 構成ファイル

このチュートリアルのこの部分には *kubernetes-wordpress.yml* を使用します。 以下、参考までに記載します。

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

サポートされる構成オプションについては、｢[Kubernetes configuration options](configure-custom-container.md#kubernetes-configuration-options)」(Kubernetes 構成オプション) を参照してください。

### <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成

[`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) コマンドを使用して、Azure Database for MySQL でサーバーを作成します。

次のコマンドで、_&lt;mysql-server-name>_ プレースホルダーを MySQL サーバー名に置き換えます (有効な文字は `a-z`、`0-9`、および `-` です)。 この名前は、MySQL サーバーのホスト名 (`<mysql-server-name>.database.windows.net`) の一部であるため、グローバルに一意である必要があります。

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

MySQL サーバーが作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "southcentralus",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

[`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) コマンドを使用して、MySQL サーバーでクライアント接続を許可するためのファイアウォール規則を作成します。 開始 IP と終了 IP の両方が 0.0.0.0 に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> [アプリで使用する送信 IP アドレスのみを使用する](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)ことで、ファイアウォール規則による制限をさらに厳しくすることができます。
>

### <a name="create-the-wordpress-database"></a>WordPress データベースを作成する

[Azure Database for MySQL サーバー](#create-an-azure-database-for-mysql-server)がまだない場合は作成します。

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

データベースが作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="create-a-multi-container-app-kubernetes"></a>マルチコンテナー アプリを作成する (Kubernetes)

Cloud Shell で [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) コマンドを使って、`myResourceGroup` リソース グループおよび `myAppServicePlan` App Service プランにマルチコンテナーの [Web アプリ](app-service-linux-intro.md)を作成します。 _\<app-name>_ は忘れずに固有のアプリ名に置き換えてください。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

Web アプリが作成されると、Cloud Shell に、次の例のような出力が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-database-variables-in-wordpress"></a>WordPress でデータベース変数を構成する

WordPress アプリをこの新しい MySQL サーバーに接続するには、いくつかの WordPress 固有の環境変数を構成します。 この変更を行うには、Cloud Shell で [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 アプリケーション設定は、大文字と小文字を区別し、スペースで区切られます。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

アプリ設定が作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql-server-name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>永続的ストレージを追加する

マルチコンテナーは Web App for Containers で実行されています。 ファイルは保持されないので、再起動時にデータは消去されます。 このセクションでは、WordPress コンテナーに[永続的なストレージを追加](configure-custom-container.md#use-persistent-shared-storage)します。

### <a name="configure-environment-variables"></a>環境変数を構成する

永続的なストレージを使用するには、App Service 内でこの設定を有効にします。 この変更を行うには、Cloud Shell で [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 アプリケーション設定は、大文字と小文字を区別し、スペースで区切られます。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

アプリ設定が作成されると、Cloud Shell に、次の例のような情報が表示されます。

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="browse-to-the-app"></a>アプリの参照

展開されたアプリ (`http://<app-name>.azurewebsites.net`) を参照します。

このアプリは、Web App for Containers で複数のコンテナーを実行しています。

![Web App for Containers のサンプル マルチコンテナー アプリ][1]

**おめでとうございます**。Web App for Containers にマルチコンテナー アプリを作成しました。

Redis を使用するには、[WordPress を Redis に接続する](#connect-wordpress-to-redis)手順に従ってください。

## <a name="find-docker-container-logs"></a>Docker コンテナー ログを検索する

複数のコンテナーを使用して問題が発生した場合は、`https://<app-name>.scm.azurewebsites.net/api/logs/docker` を参照してコンテナー ログにアクセスできます。

次の例のような出力が表示されます。

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app-name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

各コンテナーのログと親プロセスの追加ログが表示されます。 ログを表示するには、それぞれの `href` 値をブラウザーにコピーします。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
> * Web App for Containers で使用できるように Docker Compose の構成を変換する
> * Web App for Containers で使用できるように Kubernetes の構成を変換する
> * Azure にマルチコンテナー アプリを展開する
> * アプリケーションの設定を追加する
> * コンテナーに永続的ストレージを使用する
> * Azure Database for MySQL に接続する
> * エラーをトラブルシューティングする

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [カスタム コンテナーの構成](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
