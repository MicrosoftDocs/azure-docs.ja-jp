---
title: Azure Cosmos DB で Azure Kubernetes を使用する方法
description: Azure Cosmos DB (プレビュー) を使用して Azure での Kubernetes クラスターのブートストラップする方法について説明します
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 2c6af53aeec5d40f603d65595d93527107c0d80a
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427708"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Azure Cosmos DB (プレビュー) で Azure Kubernetes を使用する方法

Azure Cosmos DB の etcd API では、Azure Cosmos DB を Azure Kubernetes のバックエンド ストアとして使用できます。 Azure Cosmos DB は etcd ワイヤ プロトコルを実装します。これにより、マスター ノードの API サーバーは、ローカルにインストールされた etcd にアクセスする場合と同様に Azure Cosmos DB を使用できるようになります。 Azure Cosmos DB の etcd API は現在プレビュー段階にあります。 Kubernetes のバッキング ストアとして Azure Cosmos etcd API を使用する場合、次の利点があります。 

* etcd を手動で構成して管理する必要はありません。
* Cosmos で保証される etcd の高可用性 (単一のリージョンで 99.99%、複数のリージョンで 99.999%)。
* etcd の柔軟なスケーラビリティ。
* 既定でのセキュリティ保護とエンタープライズ対応。
* 業界をリードする包括的な SLA。

Azure Cosmos DB での etcd API の詳細については、[概要](etcd-api-introduction.md)の記事をご覧ください。 この記事では、[Azure Kubernetes エンジン](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) を使用して、ローカルでインストールされ構成されている etcd の代わりに [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) を使用する、Azure 上の Kubernetes クラスターをブートストラップする方法について説明します。 

## <a name="prerequisites"></a>前提条件

1. [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) の最新バージョンをインストールします。 お使いのオペレーティング システムに固有の Azure CLI をダウンロードしてインストールできます。

1. Azure Kubernetes エンジンの[最新バージョン](https://github.com/Azure/aks-engine/releases)をインストールします。 さまざまなオペレーティング システムのインストール手順は、[Azure Kubernetes エンジン](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine)のページに記載されています。 リンク先のドキュメントの **AKS エンジンのインストール**に関するセクションの手順だけが必要です。ダウンロード後、zip ファイルを抽出します。

   Azure Kubernetes エンジン (**aks-engine**) は、Azure 上の Kubernetes クラスター用の Azure Resource Manager テンプレートを生成します。 aks-engine への入力は、オーケストレーター、フィーチャー、エージェントなど、目的のクラスターを記述したクラスター定義ファイルです。 入力ファイルの構造は、Azure Kubernetes Service のパブリック API に似ています。

1. Azure Cosmos DB の etcd API は現在プレビュー段階にあります。 [https://aka.ms/cosmosetcdapi-signup](https://aka.ms/cosmosetcdapi-signup ) でサインアップしてプレビュー バージョンを使用します。 フォームを送信した後、サブスクリプションは、Azure Cosmos etcd API を使用できるようにホワイトリストに登録されます。 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Azure Cosmos DB でクラスターをデプロイする

1. コマンド プロンプト ウィンドウを開き、次のコマンドで Azure にサインインします。

   ```azurecli-interactive
   az login 
   ```

1. 複数のサブスクリプションがある場合は、Azure Cosmos DB etcd API のホワイトリストに登録されているサブスクリプションに切り替えます。 次のコマンドを使用して、必要なサブスクリプションに切り替えることができます。

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. 次に、Azure Kubernetes クラスターが必要とするリソースをデプロイする新しいリソース グループを作成します。 必ず "centralus" リージョンにリソース グループを作成します。 リソース グループが "centralus" リージョン内にあることは必須ではありませんが、Azure Cosmos etcd API は現在、"centralus" リージョンへのデプロイのみに使用できます。 したがって、Kubernetes クラスターを Cosmos etcd インスタンスと併置することをお勧めします。

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. 次に、同じリソース グループの一部であるリソースと通信できるように、Azure Kubernetes クラスターのサービス プリンシパルを作成します。 Azure CLI、PowerShell または Azure portal を使用してサービス プリンシパルを作成することができます。この例では CLI を使用して作成します。

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   このコマンドは、たとえば次のようなサービス プリンシパルの詳細を出力します。
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   **appId** と **[パスワード]** フィールドを書き留めます。次の手順でこれらのパラメーターを使用するためです。 

1. コマンド プロンプトから、Azure Kubernetes エンジン実行可能ファイルが配置されているフォルダーに移動します。 たとえば、次のようにコマンド プロンプトでフォルダーに移動することができます。

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. 任意のテキスト エディターを開き、Azure Cosmos DB etcd API を使用して、Azure Kubernetes クラスターをデプロイする Resource Manager テンプレートを定義します。 次の JSON 定義をテキスト エディターにコピーし、`apiModel.json` としてファイルに保存します。

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   JSON/クラスター定義ファイルで、注意すべきキー パラメーターは **"cosmosEtcd": true** です。 このパラメーターは、"masterProfile" プロパティ内にあり、通常の etcd ではなく、Azure Cosmos etcd API を使用するようにデプロイに指示します。 

1. 次のコマンドで、Azure Cosmos DB を使用する Azure Kubernetes クラスターをデプロイします。

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes エンジンは、Azure Kubernetes の目的の図形、サイズ、および構成について説明するクラスター定義を使用します。 クラスター定義を通じて有効にできる複数の機能があります。 この例では、次のパラメーターを使用します。

   * **subscription-id:** Azure Cosmos DB etcd API が有効になっている Azure サブスクリプション ID。
   * **client-id:** サービス プリンシパルの appId。 `appId` は、手順 4 で出力として返されました。
   * **Client-secret:** サービス プリンシパルのパスワードまたはランダムに生成されたパスワード。 この値は、手順 4 の 'password' パラメーターで出力として返されました。 
   * **dnsPrefix:** リージョン固有の DNS 名。 この値は、ホスト名 (値の例は - myprod1, staging) の一部を形成します。
   * **location:** クラスターのデプロイ先の場所。現在、"centralus" だけがサポートされています。

   > [!Note]
   > Azure Cosmos etcd API は現在、"centralus" リージョンでのデプロイにのみ利用できます。 
 
   * **api-model:** テンプレート ファイルへの完全修飾パス。
   * **force-overwrite:** このオプションは、出力ディレクトリ内の既存のファイルを自動的に上書きするために使用されます。
 
   次のコマンドはデプロイの例を示します。

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>デプロイを検証する

テンプレート デプロイが完了するまで、数分間かかります。 デプロイが正常に完了すると、コマンド プロンプトで次の出力が表示されます。

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

リソース グループには、仮想マシン、Azure Cosmos アカウント (etcd API)、仮想ネットワーク、可用性セット、Kubernetes クラスターで必要なその他のリソースなどのリソースが含まれるようになりました。 

Azure Cosmos アカウントの名前は、k8s が付加された、指定した DNS プレフィックスに一致します。 Azure Cosmos アカウントは、**EtcdDB** という名前のデータベースと **EtcdData** という名前のコンテナーで自動的にプロビジョニングされます。 コンテナーにはすべての etcd 関連データが格納されます。 コンテナーは、特定数の要求ユニットでプロビジョニングされ、ワークロードに基づいて、[スループットを拡大縮小 (増減)](scaling-throughput.md)できます。 

## <a name="next-steps"></a>次の手順

* [Azure Cosmos のデータベース、コンテナー、およびアイテムの操作](databases-containers-items.md)方法について学習する
* [プロビジョニング スループット コストの最適化](optimize-cost-throughput.md)方法について学習する

