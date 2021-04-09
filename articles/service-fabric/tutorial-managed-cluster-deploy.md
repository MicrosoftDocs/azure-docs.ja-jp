---
title: Service Fabric マネージド クラスターをデプロイする (プレビュー)
description: このチュートリアルでは、テスト用に Service Fabric マネージド クラスターをデプロイします。
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: fd54e6f8c5272c3a8fe0e0162ca2e57968b24642
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791713"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>チュートリアル:Service Fabric マネージド クラスターをデプロイする (プレビュー)

このチュートリアル シリーズでは、次のことについて説明します。

> [!div class="checklist"]
> * Service Fabric マネージド クラスターをデプロイする方法 
> * [Service Fabric マネージド クラスターをスケールアウトする方法](tutorial-managed-cluster-scale.md)
> * [Service Fabric マネージド クラスターにノードを追加および削除する方法](tutorial-managed-cluster-add-remove-node-type.md)
> * [Service Fabric マネージド クラスターにアプリケーションをデプロイする方法](tutorial-managed-cluster-deploy-app.md)

シリーズのこの部分では、次を行う方法について説明します。

> [!div class="checklist"]
> * Azure アカウントに接続する
> * 新しいリソース グループを作成する
> * Service Fabric マネージド クラスターをデプロイする
> * クラスターにプライマリ ノード タイプを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをまだお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。

* [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールします。

* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (以降) をインストールします。

## <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する

`<your-subscription>` を Azure アカウントのサブスクリプション文字列に置き換え、接続します。

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>新しいリソース グループを作成する

次に、Service Fabric マネージド クラスター用のリソース グループを作成し、`<your-rg>` と `<location>` を目的のグループ名と場所に置き換えます。

> [!NOTE]
> パブリック プレビューで現在サポートされているリージョンは、`centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus`、および `eastus2` です。

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターをデプロイする

### <a name="create-a-service-fabric-managed-cluster"></a>Service Fabric の管理対象クラスターの作成

この手順では、New-AzServiceFabricManagedCluster コマンドを使用して Service Fabric マネージド クラスターを作成します。 次の例では、myResourceGroup という名前のリソース グループに myCluster という名前のクラスターを作成します。 このリソース グループは、前の手順で作成しました (eastus2 リージョン)。

この手順では、以下のパラメーターに、独自の値を指定します。

* **クラスター名**:クラスターの一意の名前 (*mysfcluster* など) を入力します。
* **管理パスワード**: クラスター内の基になる VM で RDP に使用する管理者のパスワードを入力します。
* **クライアント証明書の拇印**:クラスターにアクセスするために使用するクライアント証明書の拇印を指定します。 証明書がない場合は、[証明書の設定と取得](../key-vault/certificates/quick-create-portal.md)に関する手順に従って、自己署名証明書を作成 ます。
* **クラスター SKU**:デプロイする [Service Fabric マネージド クラスターのタイプ](overview-managed-cluster.md#service-fabric-managed-cluster-skus)を指定します。 *Basic* SKU クラスターはテスト デプロイのみを目的としており、ノード タイプの追加や削除は許可されていません。

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターにプライマリ ノード タイプを追加する

この手順では、先ほど作成したクラスターにプライマリ ノード タイプを追加します。 すべての Service Fabric クラスターには少なくとも 1 つのプライマリ ノード タイプが必要です。

この手順では、以下のパラメーターに、独自の値を指定します。

* **ノード タイプ名**:クラスターに追加するノード タイプの一意の名前 ("NT1" など) を入力します。

> [!NOTE]
> 追加するノード タイプがクラスター内の最初のノード タイプまたは唯一のノード タイプである場合は、Primary プロパティを使用する必要があります。

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

このコマンドが完了するまでに数分かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

### <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

デプロイが完了したら、ポータルの [Service Fabric マネージド クラスター リソースの概要] ページで、[Service Fabric Explorer] の値を確認します。 証明書の入力を求められたら、PowerShell コマンドでクライアントの拇印を指定した証明書を使用します。

## <a name="next-steps"></a>次の手順

この手順では、最初の Service Fabric マネージド クラスターを作成してデプロイしました。 クラスターをスケーリングする方法の詳細については、次のページを参照してください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターをスケールアウトする](tutorial-managed-cluster-scale.md)
