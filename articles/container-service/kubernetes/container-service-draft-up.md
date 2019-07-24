---
title: (非推奨) Azure Container Service と Azure Container Registry で Draft を使用する
description: ACS Kubernetes クラスターと Azure Container Registry を作成し、Draft を使用して Azure に最初のアプリケーションを作成します。
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: fb34be09ec08957621517c957b3570cdbcfc0468
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787161"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(非推奨) Azure Container Service と Azure Container Registry で Draft を使用して、アプリケーションを構築し Kubernetes にデプロイする

> [!TIP]
> Azure Kubernetes Service の使用に関するこの記事の更新版については、「[Azure Kubernetes Service (AKS) での Draft の使用](../../aks/kubernetes-draft.md)」を参照してください。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) は、新しいオープンソース ツールです。Docker や Kubernetes について詳しくなくても、またそれらをインストールしなくても、容易にコンテナーベースのアプリケーションの開発と、Kubernetes クラスターへのデプロイを行えるようになります。 Draft のようなツールを使用すると、チームはインフラストラクチャをあまり気にせずに、アプリケーションの構築に集中できます。

Draft は、すべての Docker イメージ レジストリと、ローカルを含むすべての Kubernetes クラスターで使用できます。 このチュートリアルでは、ACS を Kubernetes および ACR と共に使用して、Draft を使用した Kubernetes 内にライブではあるが、セキュリティ保護された開発者パイプラインを作成する方法、および他のユーザーがドメインで参照できるように Azure DNS を使用してその開発者パイプラインを公開する方法を示します。


## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する
[新しい Azure Container Registry の作成](../../container-registry/container-registry-get-started-azure-cli.md)は容易です。手順は次のとおりです。

1. Azure リソース グループを作成して、ご自分の ACR レジストリと Kubernetes クラスターを ACS で管理します。
      ```azurecli
      az group create --name draft --location eastus
      ```

2. [az acr create](/cli/azure/acr#az-acr-create) を使用して ACR イメージ レジストリを作成し、`--admin-enabled` オプションが `true` に設定されていることを確認します。
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Kubernetes を使用して Azure Container Service をデプロイする

これで、[az acs create](/cli/azure/acs#az-acs-create) を使用し、Kubernetes を `--orchestrator-type` の値として、ACS クラスターを作成する準備が整いました。
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Kubernetes は既定の種類の Orchestrator ではないため、必ず `--orchestrator-type kubernetes` スイッチを使用してください。

正常に終了すると、出力は次のようになります。

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

クラスターが作成されたところで、[az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) コマンドを使用して資格情報をインポートできます。 現在、クラスターのローカル構成ファイルがあり、Helm と Draft が処理を行うためにこれが必要です。

## <a name="install-and-configure-draft"></a>Draft をインストールして構成する


1. ご使用の環境用の Draft を https://github.com/Azure/draft/releases からダウンロードし、パスにインストールしてこのコマンドを使用できるようにします。
2. ご使用の環境用の Helm を https://github.com/kubernetes/helm/releases からダウンロードし、[パスにインストールしてこのコマンドを使用できるようにします](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client)。
3. レジストリを使用して、作成される各 Helm チャートのサブドメインを作成するように、Draft を構成します。 Draft を構成するには次が必要です。
   - Azure Container Registry 名 (この例では `draftacsdemo`)
   - レジストリ キーまたはパスワード (`az acr credential show -n <registry name> --output tsv --query "passwords[0].value"` を使用)

   `draft init` を呼び出すと、構成プロセスから上の値を入力するよう求められます。レジストリ URL の URL 形式がレジストリ名 (この例では `draftacsdemo`) および `.azurecr.io` であることに注意してください。 ユーザー名がそのままレジストリ名になります。 このプロセスは、初めて実行すると、次のように表示されます。
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

これで、アプリケーションをデプロイする準備が整いました。


## <a name="build-and-deploy-an-application"></a>アプリケーションを構築してデプロイする

Draft リポジトリには [6 個の単純なサンプル アプリケーション](https://github.com/Azure/draft/tree/master/examples)があります。 このリポジトリを複製して、[Java のサンプル ](https://github.com/Azure/draft/tree/master/examples/example-java) を使用しましょう。 examples/java ディレクトリに移動し、`draft create` と入力してアプリケーションを構築します。 次の例のようになります。
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

出力には Dockerfile と Helm チャートが含まれます。 構築してデプロイするには、`draft up` と入力するだけです。 大量に出力されますが、出力は次の例のようになります。
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>アプリケーションを安全に表示する

コンテナーが ACS で実行されるようになりました。 それを表示するには、`draft connect` コマンドを使用します。これにより、アプリケーションの特定のポートを使用したクラスターの IP へのセキュリティ保護された接続が作成され、それをローカルに表示できるようになります。 成功した場合は、**SUCCESS** インジケータの後の最初の行で、アプリに接続する URL を探します。

> [!NOTE]
> ポッドの準備ができていなかったことを示すメッセージを受信した場合は、少し待ってから再試行するか、または `kubectl get pods -w` でポッドを監視し、準備ができたら再試行することもできます。

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

前の例では、`curl -s http://localhost:46143` と入力すると、応答 `Hello World, I'm Java!` を受信する可能性があります。 CTRL+ または CMD+C (OS 環境によります) を押すと、セキュリティ保護されたトンネルが切断され、反復処理を続行できます。

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Azure DNS でデプロイ ドメインを構成することによるアプリケーションの共有

前の手順で Draft が作成する開発者の反復ループは既に実行しました。 ただし、次のことを行うことによって、インターネット全体でアプリケーションを共有できます。
1. ACS クラスター内でのイングレスのインストール (アプリを表示するパブリック IP アドレスを提供するため)
2. Azure DNS へのカスタム ドメインの委任、および ACS がイングレス コントローラーに割り当てる IP アドレスへのドメインのマッピング

### <a name="use-helm-to-install-the-ingress-controller"></a>イングレス コントローラーをインストールするには、Helm を使用します。
**Helm** を使用して `stable/traefik` (イングレス コントローラー) を検索およびインストールすることによって、ビルドへの受信要求を有効にします。
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
ここで、`ingress` コントローラーにウォッチを設定して、デプロイ時に外部 IP 値をキャプチャするようにします。 この IP アドレスは、次のセクションでデプロイ ドメインにマップされるものです。

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

このケースでは、デプロイ ドメインの外部 IP は `13.64.108.240` です。 ここで、その IP にドメインをマップできます。

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>イングレス IP をカスタム サブドメインにマッピングする

Draft によって、作成される各 Helm チャート (作業対象の各アプリケーション) のリリースが作成されます。 それぞれに対して、**Draft** で使用される名前が生成されます。これは、ユーザーが制御するルート "_デプロイ ドメイン_" 上の "_サブドメイン_" です。 (この例では、デプロイ ドメインとして `squillace.io` を使用します)。このサブドメインの動作を有効にするには、デプロイ ドメインの DNS エントリで `'*.draft'` に対して A レコードを作成する必要があります。こうすることで、生成される各サブドメインが Kubernetes クラスターの受信コントローラーにルーティングされます。 

ドメイン プロバイダーには DNS サーバーを割り当てる独自の方法があります。[ドメイン ネームサーバーを Azure DNS に委任する](../../dns/dns-delegate-domain-azure-dns.md)には、次の手順を実行します。

1. ゾーンのリソース グループを作成します。
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. ドメインの DNS ゾーンを作成します。
   [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) コマンドを使用して、ドメインに関して DNS の制御を Azure DNS に委任するネームサーバーを取得します。
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. 与えられた DNS サーバーを、デプロイ ドメインのドメイン プロバイダーに追加します。これにより、Azure DNS を使用し、必要に応じて自分のドメインを再指定できるようになります。 これを行う方法は、ドメインによって異なります。「[delegate your domain nameservers to Azure DNS (ドメイン ネームサーバーを Azure DNS に委任する)](../../dns/dns-delegate-domain-azure-dns.md)」には、知っておくべきいくつかの詳細事項が含まれています。 
4. ドメインが Azure DNS に委任されたら、デプロイ ドメインの A レコードセット エントリを作成し、前のセクションの手順 2. の `ingress` IP にマッピングします。
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
   ```
   次のような画面が出力されます。
   ```json
   {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. **Draft** を再インストールする

   1. `helm delete --purge draft` を入力して、クラスターから **draftd** を削除します。 
   2. 同じ `draft-init` コマンドを使用して、ただし `--ingress-enabled` オプションで **Draft** を再インストールします。
      ```bash
      draft init --ingress-enabled
      ```
      上で初めて行ったのと同じようにプロンプトに応答します。 ただし、Azure DNS で構成した完全なドメイン パスを使用して答えるべき質問がもう 1 つあります。

6. 受信用の最上位ドメインを入力します (例: draft.example.com): draft.squillace.io
7. 今度は `draft up` を呼び出すと、形式 `<appname>.draft.<domain>.<top-level-domain>` の URL でアプリケーション (または `curl`) を表示できるようになります。 この例の場合は、`http://handy-labradoodle.draft.squillace.io` です。 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>次の手順

これで、ACS Kubernetes クラスターが用意されました。[Azure Container Registry](../../container-registry/container-registry-intro.md) を使用して調査し、このシナリオのさまざまなデプロイを作成できます。 たとえば、特定の ACS デプロイの深い階層のサブドメインの処理を制御する、draft._basedomain.toplevel_ ドメイン DNS レコードセットを作成できます。






