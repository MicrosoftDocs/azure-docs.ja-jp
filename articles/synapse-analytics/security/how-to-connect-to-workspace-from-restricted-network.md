---
title: 制限されたネットワークから Azure Synapse Analytics Studio のワークスペース リソースに接続する
description: この記事では、制限付きネットワークから Azure Synapse Studio ワークスペース リソースに接続する方法について説明します。
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384522"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>制限されたネットワークからのワークスペース リソースへの接続

あなたは組織の制限付きネットワークを管理している IT 管理者であるとします。 Azure Synapse Analytics Studio と、この制限されたネットワーク内のワークステーションとの間のネットワーク接続を有効にします。 この記事では、その方法について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure Synapse Analytics ワークスペース**:これは、Azure Synapse Analytics から作成できます。 手順 4 ではワークスペース名が必要です。
* **制限付きネットワーク**:IT 管理者は、組織の制限されたネットワークを維持し、ネットワーク ポリシーを構成するアクセス許可を持っています。 手順 3 では、仮想ネットワーク名とサブネットが必要です。


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>手順 1:制限付きネットワークにネットワーク アウトバウンド セキュリティ規則を追加する

4 つのサービス タグを持つ 4 つのネットワーク アウトバウンド セキュリティ規則を追加する必要があります。 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (この種類のルールはオプションです。 データを Microsoft と共有する場合にのみ追加してください)。

次のスクリーンショットは、Azure Resource Manager アウトバウンド規則の詳細を示しています。

![Azure Resource Manager サービス タグの詳細のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

他の 3 つのルールを作成する場合は、**宛先サービス タグ** の値をリストから **AzureFrontDoor**、**AzureActiveDirectory**、または **AzureMonitor** に置き換えます。

詳細については、[サービス タグの概要](../../virtual-network/service-tags-overview.md)に関するページを参照してください。

## <a name="step-2-create-private-link-hubs"></a>手順 2:プライベート リンク ハブの作成

次に、Azure portal からプライベート リンク ハブを作成します。 ポータルでこれを見つけるには *Azure Synapse Analytics (プライベート リンク ハブ)* を検索し、必要な情報を入力して作成します。 

![Synapse プライベート リンク ハブの作成のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>手順 3:Synapse Studio のプライベート エンドポイントの作成

Azure Synapse Analytics Studio にアクセスするには、Azure portal からプライベート エンドポイントを作成する必要があります。 ポータルでこれを見つけるには、*Private Link* を検索します。 **Private Link センター** で、 **[プライベート エンドポイントの作成]** を選択し、作成するために必要な情報を入力します。 

> [!Note]
> **[リージョン]** の値が、Azure Synapse Analytics ワークスペースと同じであることを確認します。

![[プライベート エンドポイントの作成] の [基本] タブのスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

**[リソース]** タブで、手順 2 で作成したプライベート リンク ハブを選択します。

![[プライベート エンドポイントの作成] の [リソース] タブのスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

**[構成]** タブに移動します。 
* **[仮想ネットワーク]** に、制限された仮想ネットワーク名を選択します。
* **[サブネット]** に、制限付き仮想ネットワークのサブネットを選択します。 
* **[プライベート DNS ゾーンと統合する]** に、 **[はい]** を選択します。

![[プライベート エンドポイントの作成] の [構成] タブのスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

プライベート リンク エンドポイントが作成された後、Azure Synapse Analytics Studio の Web ツールのサインイン ページにアクセスできます。 ただし、ワークスペース内のリソースにはまだアクセスできません。 そのためには、次の手順を完了する必要があります。

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>手順 4:ワークスペース リソースのプライベート エンドポイントの作成

Azure Synapse Analytics Studio ワークスペース リソース内のリソースにアクセスするには、次のものを作成する必要があります。

- **ターゲット サブリソース** の型が **Dev** である少なくとも 1 つのプライベート リンク エンドポイント。
- アクセスするワークスペースのリソースに応じて、その他の 2 つの **Sql** または **SqlOnDemand** の型の省略可能なプライベート リンク エンドポイント。

これらの作成は、前の手順でエンドポイントを作成する方法と似ています。  

**[リソース]** タブで、次の操作を実行します。

* **[リソースの種類]** に、 **[Microsoft.Synapse/workspaces]** を選択します。
* **[リソース]** に、前に作成したワークスペース名を選択します。
* **[対象サブリソース]** に、エンドポイントの種類を選択します。
  * **[Sql]** は、SQL プールで SQL クエリを実行するためのものです。
  * **[SqlOnDemand]** は、SQL に組み込まれているクエリを実行するためのものです。
  * **[Dev]** は、Azure Synapse Analytics Studio ワークスペース内の他のすべてのユーザーにアクセスするためのものです。 少なくともこの種類のプライベート リンク エンドポイントを 1 つ作成する必要があります。

![プライベート エンドポイントの作成、[リソース] タブ、ワークスペースのスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>手順 5:ワークスペースのリンクされたストレージに対するプライベート エンドポイントの作成

Azure Synapse Analytics Studio ワークスペースのストレージ エクスプローラーを使用してリンクされたストレージにアクセスするには、1 つのプライベート エンドポイントを作成する必要があります。 この手順は、手順 3 と似ています。 

**[リソース]** タブで、次の操作を実行します。
* **[リソースの種類]** には、 **[Microsoft.Synapse/storageAccounts]** を選択します。
* **[リソース]** に、前に作成したストレージ アカウント名を選択します。
* **[対象サブリソース]** に、エンドポイントの種類を選択します。
  * **[BLOB]** は Azure Blob Storage 用です。
  * **[DFS]** は Azure Data Lake Storage Gen2 用です。

![プライベート エンドポイントの作成の [リソース] タブのストレージのスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

これで、リンクされたストレージ リソースにアクセスできるようになりました。 仮想ネットワーク内の Azure Synapse Analytics Studio ワークスペースで、ストレージ エクスプローラーを使用して、リンクされたストレージ リソースにアクセスできます。

次のスクリーンショットに示すように、ワークスペースのマネージド仮想ネットワークを有効にすることができます。

![[マネージド仮想ネットワークの有効化] オプションが強調表示されている [Synapse ワークスペースを作成する] のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

ノートブックが特定のストレージ アカウントの元にあるリンクされたストレージ リソースにアクセスできるようにするには、Azure Synapse Analytics Studio でマネージド プライベート エンドポイントを追加します。 [ストレージ アカウント名] には、ノートブックからアクセスする必要のあるものを指定します。 詳細については、「[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)」を参照してください。

このエンドポイントを作成すると、承認状態に **[保留中]** の状態が表示されます。 このストレージ アカウントの所有者からの承認を要求するには、Azure portal 内のこのストレージ アカウントの **[プライベート エンドポイント接続]** タブで行います。 承認されると、このストレージ アカウントで、リンクされたストレージ リソースにノートブックからアクセスできるようになります。

これで、すべてが設定されました。 Azure Synapse Analytics Studio ワークスペース リソースにアクセスできます。

## <a name="appendix-dns-registration-for-private-endpoint"></a>付録: プライベート エンドポイントの DNS 登録

次のスクリーンショットのように、プライベート エンドポイントの作成中に [プライベート DNS ゾーンとの統合] が有効になっていない場合は、プライベート エンドポイントごとに "**プライベート DNS ゾーン**" を作成する必要があります。
![Synapse プライベート DNS ゾーンの作成 1 のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

ポータルで **プライベート DNS ゾーン** を見つけるには、 *[プライベート DNS ゾーン]* で検索します。 **[プライベート DNS ゾーン]** で、作成に必要な以下の情報を入力します。

* **[名前]** には、次のように特定のプライベート エンドポイントのプライベート DNS ゾーン専用の名前を入力します。
  * **`privatelink.azuresynapse.net`** は、Azure Synapse Analytics Studio ゲートウェイにアクセスするためのプライベート エンドポイント用です。 この種類のプライベート エンドポイントの作成については、手順 3 を参照してください。
  * **`privatelink.sql.azuresynapse.net`** は、SQL プールおよび組み込みプールで SQL クエリを実行するためのこの種類のプライベート エンドポイント用です。 エンドポイントの作成については、手順 4 を参照してください。
  * **`privatelink.dev.azuresynapse.net`** は、Azure Synapse Analytics Studio ワークスペース内の他のすべてにアクセスするためのこの種類のプライベート エンドポイント用です。 この種類のプライベート エンドポイントの作成については、手順 4 を参照してください。
  * **`privatelink.dfs.core.windows.net`** は、Azure Data Lake Storage Gen2 にリンクされたワークスペースにアクセスするためのプライベート エンドポイント用です。 この種類のプライベート エンドポイントの作成については、手順 5 を参照してください。
  * **`privatelink.blob.core.windows.net`** は、Azure Blob Storage にリンクされたワークスペースにアクセスするためのプライベート エンドポイント用です。 この種類のプライベート エンドポイントの作成については、手順 5 を参照してください。

![Synapse プライベート DNS ゾーンの作成 2 のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

**プライベート DNS ゾーン** を作成した後、作成したプライベート DNS ゾーンを入力し、 **[仮想ネットワーク リンク]** を選択して仮想ネットワークへのリンクを追加します。 

![Synapse プライベート DNS ゾーンの作成 3 のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

必須フィールドを次のように入力します。
* **[リンク名]** には、リンクの名前を入力します。
* **[仮想ネットワーク]** では、お使いの仮想ネットワークを選択します。

![Synapse プライベート DNS ゾーンの作成 4 のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

仮想ネットワーク リンクが追加されたら、前に作成した **プライベート DNS ゾーン** に DNS レコード セットを追加する必要があります。

* **[名前]** には、別のプライベート エンドポイント専用の名前文字列を入力します。 
  * **web** は、Azure Synapse Analytics Studio にアクセスするためのプライベート エンドポイント用です。
  * "***YourWorkSpaceName***" は、SQL プールで SQL クエリを実行するためのプライベート エンドポイント用で、Azure Synapse Analytics Studio ワークスペース内の他のすべてにアクセスするためのプライベート エンドポイント用でもあります。
  * "***YourWorkSpaceName*-ondemand**" は、組み込みプールで SQL クエリを実行するためのプライベート エンドポイント用です。
* **[種類]** では、DNS レコードの種類 **[A]** のみを選択します。 
* **[IP アドレス]** には、各プライベート エンドポイントの対応する IP アドレスを入力します。 プライベート エンドポイントの概要の **[ネットワーク インターフェイス]** で IP アドレスを取得できます。

![Synapse プライベート DNS ゾーンの作成 5 のスクリーンショット。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>次のステップ

[マネージド ワークスペースの仮想ネットワーク](./synapse-workspace-managed-vnet.md)の詳細を参照してください。

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を参照してください。
