---
title: docker run コマンドのコンテナー実行の例
titleSuffix: Azure Cognitive Services
description: 医療コンテナー用の docker run コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: af8fec56c32b52e2af584e59f08db6cc7129c9c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98947578"
---
## <a name="install-the-container"></a>コンテナーをインストールする

Text Analytics for Health コンテナーをインストールして実行するには、複数の方法があります。 

- [Azure portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) を使用して Text Analytics リソースを作成し、Docker を使用してコンテナーを取得します。
- 次の PowerShell と Azure CLI のスクリプトを使用して、リソースのデプロイとコンテナーの構成を自動化します。

### <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

コンテナー イメージをダウンロードした後に独自の環境でコンテナーを実行するには、そのイメージ ID を検索します。
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

次の `docker run` コマンドを実行します。 次のプレースホルダーを実際の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | 自分の Text Analytics リソースのキー。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Text Analytics API にアクセスするためのエンドポイント。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | コンテナーのイメージ ID。 | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | コンテナーの入力ディレクトリ。 | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

このコマンドは、次の操作を行います。

- 入力ディレクトリがホスト コンピューター上に存在することを前提としています
- コンテナー イメージから Text Analytics for Health コンテナーを実行します
- 6 つの CPU コアと 12 ギガバイト (GB) のメモリを割り当てます
- TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
- コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

### <a name="demo-ui-to-visualize-output"></a>出力を視覚化するためのデモ UI

> [!NOTE]
> デモは、Text Analytics for Health コンテナーでのみ使用できます。

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。  また、コンテナーのエンドポイントに `/demo` を追加するとアクセス可能となるコンテナーに、視覚化ツールを提供しています。 次に例を示します。

```
http://<serverURL>:5000/demo
```

次の cURL 要求の例を使用し、`serverURL` 変数を適切な値に置き換えて、デプロイしたコンテナーにクエリを送信します。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Azure Web App for Containers を使用してコンテナーをインストールする

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) は、クラウドでコンテナーを実行する専用の Azure リソースです。 自動スケーリング、Docker コンテナーと Docker Compose のサポート、HTTPS のサポートなど、すぐに使用できる機能があります。

> [!NOTE]
> Azure Web アプリを使用すると、ドメインが自動的に `<appservice_name>.azurewebsites.net` の形式で取得されます

Azure CLI を使用してこの PowerShell スクリプトを実行し、HTTPS 経由でサブスクリプションとコンテナー イメージを使用して、Web App for Containers を作成します。 スクリプトが完了するまで (約 25 から 30 分) 待ってから、最初の要求を送信します。

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Azure コンテナー インスタンスを使用してコンテナーをインストールする

Azure コンテナー インスタンス (ACI) を使用して、デプロイを容易にすることもできます。 ACI は、サーバーレスなマネージド Azure 環境内で Docker コンテナーをオンデマンドで実行できるリソースです。 

Azure portal を使用して ACI リソースをデプロイする手順については、[Azure Container Instances の使用方法](../../containers/azure-container-instance-recipe.md)に関する記事を参照してください。 また、Azure CLI を使用して次の PowerShell スクリプトを実行し、コンテナー イメージを使用してサブスクリプションに ACI を作成することもできます。  スクリプトが完了するまで (約 25 から 30 分) 待ってから、最初の要求を送信します。  ACI リソースあたりの CPU の最大数に制限があるため、要求あたり 5 個を超える大きいドキュメント (それぞれ約 5000 文字) を送信することが予想される場合は、このオプションを選択しないでください。
可用性の情報については、[ACI リージョンのサポート](../../../container-instances/container-instances-region-availability.md)に関する記事を参照してください。 

> [!NOTE] 
> Azure Container Instances には、組み込みドメインに対する HTTPS のサポートは含まれません。 HTTPS が必要な場合は、証明書の作成とドメインの登録など、手動で構成する必要があります。 この手順については、以下の NGINX を参照してください。

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>ACI の接続をセキュリティで保護する

既定では、コンテナー API で ACI を使用するときに、セキュリティは提供されません。 これは、通常、コンテナーはネットワーク ブリッジによって外部から保護されるポッドの一部として実行されるためです。 ただし、前面にあるコンポーネントを使用してコンテナーを変更し、コンテナー エンドポイントを非公開にしておくこともできます。 次の例では、[NGINX](https://www.nginx.com) をイングレス ゲートウェイとして使用し、HTTPS/SSL およびクライアント証明書認証をサポートします。

> [!NOTE]
> NGINX は、オープンソースで高パフォーマンスの HTTP サーバーとプロキシです。 NGINX コンテナーは、1 つのコンテナーに対する TLS 接続を終端するために使用できます。 さらに複雑な NGINX イングレス ベースの TLS 終端ソリューションも可能です。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX をイングレス ゲートウェイとして設定する

NGINX では、[構成ファイル](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)を使用して、実行時に機能を有効にします。 別のサービスに対する TLS 終端を有効にするには、TLS 接続を終端するための SSL 証明書と、サービスのアドレスを指定するための `proxy_pass` を、指定する必要があります。 以下ではサンプルを提供します。


> [!NOTE]
> `ssl_certificate` では、NGINX コンテナーのローカル ファイル システム内でパスが指定されている必要があります。 `proxy_pass` に指定するアドレスは、NGINX コンテナーのネットワーク内から使用できる必要があります。

NGINX コンテナーにより、`/etc/nginx/conf.d/` にマウントされている `_.conf_` 内のすべてのファイルが HTTP 構成パスに読み込まれます。

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Docker Compose ファイルの例

次の例では、[Docker Compose](https://docs.docker.com/compose/reference/overview) ファイルを作成して、NGINX と Text Analytics for Health のコンテナーをデプロイする方法を示します。

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

この Docker Compose ファイルを開始するには、ファイルのルート レベルでコンソールから次のコマンドを実行します。

```bash
docker-compose up
```

詳細については、[NGINX SSL 終端](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)に関する NGINX のドキュメントを参照してください。