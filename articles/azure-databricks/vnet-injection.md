---
title: 仮想ネットワーク内での Azure Databricks のデプロイ (プレビュー)
description: この記事では、仮想ネットワークに Azure Databricks をデプロイする (VNet インジェクションとも呼ばれる) 方法について説明します。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: c29d2e1df0979481c0c8a1e1f2cd4d22b013212a
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58229346"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>仮想ネットワーク内での Azure Databricks のデプロイ (プレビュー)

Azure Databricks の既定のデプロイは、Azure 上のフルマネ－ジド サービスとなります。 仮想ネットワーク (VNet) を含むすべてのデータ プレーン リソースが、ロック済みのリソース グループにデプロイされます。 ただし、ネットワークのカスタマイズが必要な場合は、Azure Databricks リソースを独自の仮想ネットワーク内にデプロイできます (VNet インジェクションとも呼ばれます)。この場合、以下のことが可能になります。

* サービス エンドポイントを使用して、より安全な方法で Azure Databricks を (Azure Storage などの) 他の Azure サービスに接続する。
* Azure Databricks で使用するオンプレミスのデータ ソースに接続し、ユーザー定義ルートを活用する。
* Azure Databricks をネットワーク仮想アプライアンスに接続してすべての送信トラフィックを検査し、許可および拒否の規則に従ってアクションを実行する。
* カスタム DNS を使用するように Azure Databricks を構成する。
* ネットワーク セキュリティ グループ (NSG) 規則を構成し、エグレス トラフィック制約を指定する。
* 既存の仮想ネットワーク内に Azure Databricks クラスターをデプロイする。

Azure Databricks リソースを独自の仮想ネットワークにデプロイして、柔軟な CIDR 範囲 を活用することもできます (仮想ネットワークの場合は /16-/24 の間の任意の場所、サブネットの場合は /18-/26 の間の任意の場所)。

  > [!NOTE]
  > 仮想ネットワークを既存のワークスペースに置き換えることはできません。 現在のワークスペースで、必要な数のアクティブ クラスター ノードに対応できない場合は、より大きな仮想ネットワーク内に別のワークスペースを作成します。 古いワークスペースから新しいワークスペースにリソース (Notebook、クラスター構成、ジョブ) をコピーするには、[詳細な移行手順](howto-regional-disaster-recovery.md#detailed-migration-steps)に従ってください。

## <a name="virtual-network-requirements"></a>仮想ネットワークの要件

Azure portal 内で Azure Databricks ワークスペースのデプロイ インターフェイスを使い、必要なサブネット、ネットワーク セキュリティ グループ、およびホワイト リスト登録を設定して既存の仮想ネットワークを自動的に構成することも、Azure Resource Manager テンプレートを使用して仮想ネットワークを構成し、ワークスペースをデプロイすることもできます。

Azure Databricks ワークスペースをデプロイする仮想ネットワークは、以下の要件を満たす必要があります。

### <a name="location"></a>Location

仮想ネットワークは、Azure Databricks ワークスペースと同じ場所内に存在する必要があります。

### <a name="subnets"></a>サブネット

仮想ネットワークには、以下に示す Azure Databricks 専用の 2 つのサブネットを含める必要があります。

   1. クラスター内通信が可能なネットワーク セキュリティ グループが構成されているプライベート サブネット。

   2. Azure Databricks コントロール プレーンとの通信が可能なネットワーク セキュリティ グループが構成されているパブリック サブネット。

### <a name="address-space"></a>アドレス空間

仮想ネットワークの場合は /16 - /24 の間の CIDR ブロック、プライベートおよびパブリック サブネットの場合は /18 - /26 の間の CIDR ブロック。

### <a name="whitelisting"></a>ホワイトリスト登録

サブネットおよび Azure Databricks コントロール プレーン間のすべての送信および受信トラフィックをホワイトリスト登録する必要があります。

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

このセクションでは、Azure portal 内で Azure Databricks ワークスペースを作成して、既存の仮想ネットワーク内にデプロイする方法について説明します。 Azure Databricks は、指定の CIDR 範囲を使用した 2 つの新しいサブネットとネットワーク セキュリティ グループを含む仮想ネットワークを更新し、受信および送信サブネット トラフィックをホワイトリスト登録して、この更新された仮想ネットワークにワークスペースをデプロイします。

## <a name="prerequisites"></a>前提条件

Azure Databricks ワークスペースをデプロイする仮想ネットワークが必要です。 既存の仮想ネットワークを使用することも、新しい仮想ネットワークを作成することもできますが、仮想ネットワークは、作成を計画している Azure Databricks ワークスペースと同じリージョンに存在する必要があります。 仮想ネットワークの場合、/16 - /24 の間の CIDR 範囲が必要です。

  > [!Warning]
  > 小さい仮想ネットワーク (低い CIDR 範囲) を使用するワークスペースは、大きな仮想ネットワークを使用するワークスペースよりも早く IP アドレス (ネットワーク空間) を使い切る可能性があります。 たとえば、/24 の仮想ネットワークと /26 のサブネットを使用するワークスペースは、最大 64 個のノードを同時にアクティブにできますが、/20 の仮想ネットワークと /22 のサブネットを使用するワークスペースは、最大 1024 個のノードに対応できます。

  サブネットは、ワークスペースの構成時に自動的に作成されます。また、サブネットの CIDR 範囲は、ワークスペースの構成時に指定できます。

## <a name="configure-the-virtual-network"></a>仮想ネットワークの構成

1. Azure portal 内で **+ (リソースの作成) > 分析 > Azure Databricks** を選択し、Azure Databricks service\(Azure Databricks サービス\) ダイアログを開きます。

2. ファースト ステップ ガイド の「Step 2:Create an Azure Databricks workspace (手順 2: Azure Databricks ワークスペースの作成)」に記載されている構成手順に従い、[Virtual Network] オプション内の [Deploy Azure Databricks workspace]\(Azure Databricks ワークスペースのデプロイ\) を選択します。

   ![Azure Databricks サービスの作成](./media/vnet-injection/create-databricks-service.png)

3. 使用する仮想ネットワークを選択します。

   ![[Virtual Network] オプション](./media/vnet-injection/select-vnet.png)

4. Azure Databricks 専用の 2 つのサブネットの /18 - /26 間のブロックで CIDR 範囲を指定します。

   * パブリック サブネットの作成時は、Azure Databricks コントロール プレーンとの通信が可能なネットワーク セキュリティ グループが関連付けられます。
   * プライベート サブネットの作成時は、クラスター内通信が可能なネットワーク セキュリティ グループが関連付けられます。

5. **[作成]** をクリックし、Azure Databricks ワークスペースを仮想ネットワークにデプロイします。

## <a name="advanced-resource-manager-configurations"></a>リソース マネージャーの高度な構成

仮想ネットワークの構成をより詳細に制御する場合、たとえば、既存のサブネットを使用したり、既存のネットワーク セキュリティ グループを使用したり、独自のセキュリティ規則を作成したりする場合は、ポータルで仮想ネットワークを構成して、ワークスペースをデプロイする代わりに、以下の Azure Resource Manager テンプレートを使用します。

### <a name="all-in-one"></a>オール イン ワン

仮想ネットワーク、ネットワーク セキュリティ グループ、および Azure Databricks ワークスペースをオール イン ワン で作成するには、[Databricks VNet が挿入されたワークスペース用のオール イン ワン テンプレート](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)を使用します。

このテンプレートを使用するときは、サブネット トラフィックを手動でホワイトリスト登録する必要はありません。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

既存の仮想ネットワークに必要な規則と共にネットワーク セキュリティ グループを作成するには、[Databricks VNet インジェクション用のネットワーク セキュリティ グループ テンプレート](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection)を使用します。

このテンプレートを使用するときは、サブネット トラフィックを手動でホワイトリスト登録する必要はありません。

### <a name="virtual-network"></a>仮想ネットワーク

適切なパブリック サブネットとプライベート サブネットを持つ仮想ネットワークを作成するには、[Databricks VNet インジェクション用の仮想ネットワーク テンプレート](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection)を使用します。

このテンプレートを使用するときに、ネットワーク セキュリティ グループ テンプレートを併用しない場合は、仮想ネットワークで使用するネットワーク セキュリティ グループに、ホワイト リスト登録規則を手動で追加する必要があります。

### <a name="azure-databricks-workspace"></a>Azure Databricks ワークスペース

パブリック サブネットとプライベート サブネットを持ち、適切に構成されたネットワーク セキュリティ グループが既に設定されている既存の仮想ネットワークに Azure Databricks ワークスペースをデプロイするには、[Databricks VNet インジェクション用のワークスペース テンプレート](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection)を使用します。

このテンプレートを使用するときに、ネットワーク セキュリティ グループ テンプレートを併用しない場合は、仮想ネットワークで使用するネットワーク セキュリティ グループに、ホワイト リスト登録規則を手動で追加する必要があります。

## <a name="whitelisting-subnet-traffic"></a>サブネット トラフィックのホワイト リスト登録

[Azure portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) や [Azure Resource Manager テンプレート](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced)を使用せずにネットワーク セキュリティ グループを作成する場合は、サブネット上の以下のトラフィックを手動でホワイトリスト登録する必要があります。

|方向|Protocol|ソース|発信元ポート|変換先|宛先ポート|
|---------|--------|------|-----------|-----------|----------------|
|受信|\*|VirtualNetwork|\*|\*|\*|
|受信|\*|コントロール プレーン NAT IP|\*|\*|22|
|受信|\*|コントロール プレーン NAT IP|\*|\*|5557|
|送信|\*|\*|\*|Webapp IP|\*|
|送信|\*|\*|\*|SQL (サービス タグ)|\*|
|送信|\*|\*|\*|ストレージ (サービス タグ)|\*|
|送信|\*|\*|\*|VirtualNetwork|\*|

以下の IP アドレスを使用してサブネット トラフィックをホワイトリスト登録します。 SQL (metastore) およびストレージ (成果物およびログ ストレージ) の場合は、SQL およびストレージの[サービス タグ](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)を使用する必要があります。

|Azure Databricks リージョン|Service|パブリック IP|
|-----------------------|-------|---------|
|米国東部|コントロール プレーン NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|米国東部 2|コントロール プレーン NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|米国中北部|コントロール プレーン NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|米国中央部|コントロール プレーン NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|米国中南部|コントロール プレーン NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|米国西部|コントロール プレーン NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|米国西部 2|コントロール プレーン NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|カナダ中部|コントロール プレーン NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|カナダ東部|コントロール プレーン NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|英国西部|コントロール プレーン NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|英国南部|コントロール プレーン NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|西ヨーロッパ|コントロール プレーン NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|北ヨーロッパ|コントロール プレーン NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|インド中部|コントロール プレーン NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|インド南部|コントロール プレーン NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|インド西部|コントロール プレーン NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|東南アジア|コントロール プレーン NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|東アジア|コントロール プレーン NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|オーストラリア東部|コントロール プレーン NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|オーストラリア南東部|コントロール プレーン NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|オーストラリア中部|コントロール プレーン NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|オーストラリア中部 2|コントロール プレーン NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|東日本|コントロール プレーン NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|西日本|コントロール プレーン NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="workspace-launch-errors"></a>ワークスペース起動時のエラー

Azure Databricks のサインイン画面で、カスタム仮想ネットワーク内のワークスペースを起動することができず、**[We've encountered an error creating your workspace.Make sure the custom network configuration is correct and try again.]\(ワークスペースの作成中にエラーが発生しました。カスタム ネットワーク構成が正しいことを確認して、やり直してください。\)** というエラーが表示されます。

このエラーは、ネットワーク構成が要件を満たしていない場合に発生します。 ワークスペースを作成したときに、このトピックの手順に従っていたことを確認してください。

### <a name="cluster-creation-errors"></a>クラスター作成時のエラー

**Instances Unreachable:Resources were not reachable via SSH. (インスタンスに到達できませんでした。SSH 経由でリソースに到達できません。)**

考えられる原因: コントロール プレーンから worker へのトラフィックがブロックされています。 受信セキュリティ規則が要件を満たしていることを確認して、修正を行ってください。 オンプレミス ネットワークに接続されている既存の仮想ネットワークをデプロイ先にする場合は、「Connecting your Azure Databricks Workspace to your On-Premises Network (Azure Databricks ワークスペースをオンプレミス ネットワークに接続する)」の説明に従って、設定を確認してください。

**Unexpected Launch Failure:An unexpected error was encountered while setting up the cluster.Retry and contact Azure Databricks if the problem persists.Internal error message:Timeout while placing node. (予期しない起動エラー: クラスターの設定中に予期しないエラーが発生しました。再試行しても問題が解決しない場合は、Azure Databricks にお問い合わせください。内部エラー メッセージ: ノードの配置中にタイムアウトになりました。)**

考えられる原因: worker から Azure Storage エンドポイントへのトラフィックがブロックされています。 送信セキュリティ規則が要件を満たしていることを確認して、修正を行ってください。 また、カスタム DNS サーバーを使用している場合は、仮想ネットワーク内の DNS サーバーの状態も確認してください。

**Cloud Provider Launch Failure:A cloud provider error was encountered while setting up the cluster.See the Azure Databricks guide for more information.Azure error code:AuthorizationFailed/InvalidResourceReference. (クラウド プロバイダーの起動エラー: クラスターの設定中にクラウド プロバイダーのエラーが発生しました。詳細については、『Azure Databricks guide (Azure Databricks ガイド)』を参照してください。Azure エラー コード: AuthorizationFailed/InvalidResourceReference。)**

考えられる原因: 仮想ネットワークまたはサブネットが存在していません。 仮想ネットワークとサブネットが存在することを確認してください。

**Cluster terminated.理由:Spark Startup Failure:Spark was not able to start in time.This issue can be caused by a malfunctioning Hive metastore, invalid Spark configurations, or malfunctioning init scripts.Refer to the Spark driver logs to troubleshoot this issue, and contact Databricks if the problem persists.Internal error message:Spark failed to start:Driver failed to start in time. (クラスターが終了しました。理由: Spark の起動エラー: Spark が時間内に起動できませんでした。この問題の原因としては、Hive metastore の不具合、Spark の構成が無効、または init スクリプトの不具合が考えられます。この問題をトラブルシューティングするには、Spark ドライバー ログを参照してください。問題が解決しない場合は、Databricks にお問い合わせください。内部エラー メッセージ: Spark を起動できません。ドライバーを時間内に起動できません。)**

考えられる原因:コンテナーがホスティング インスタンスや DBFS ストレージ アカウントと通信できません。 DBFS ストレージ アカウントのサブネットにカスタム ルートを追加し、次回のホップをインターネットにして、修正を行ってください。

### <a name="notebook-command-errors"></a>Notebook コマンドのエラー

**Command is hanging (コマンドがハングしています)**

考えられる原因: worker 間の通信がブロックされています。 受信セキュリティ規則が要件を満たしていることを確認して、修正を行ってください。

**Notebook workflow fails with the exception: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException (Notebook ワークフローが失敗し、例外 com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException が発行されました)**

考えられる原因: worker から Azure Databricks Webapp へのトラフィックがブロックされています。 送信セキュリティ規則が要件を満たしていることを確認して、修正を行ってください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Databricks を使ったデータの抽出、変換、読み込み](databricks-extract-load-sql-data-warehouse.md)
