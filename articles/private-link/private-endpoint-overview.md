---
title: Azure プライベート エンドポイントとは
description: Azure プライベート エンドポイントについて学習します
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 80f2e8a8fd41fbafbaf6d30bc1001b86c5dcdd50
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266365"
---
# <a name="what-is-azure-private-endpoint"></a>Azure プライベート エンドポイントとは

Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントでは、自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスは、Azure Storage や SQL などの Azure サービスであっても、独自の [Private Link サービス](private-link-service-overview.md)であってもかまいません。
  
## <a name="private-endpoint-properties"></a>プライベート エンドポイントのプロパティ 
 プライベート エンドポイントでは、次のプロパティを指定します。 


|プロパティ  |Description |
|---------|---------|
|名前    |    リソース グループ内の一意の名前。      |
|Subnet    |  仮想ネットワークからデプロイしてプライベート IP アドレスを割り当てるサブネット。 サブネットの要件については、この記事の「制限事項」セクションを参照してください。         |
|Private Link リソース    |   使用可能な種類の一覧から、リソース ID または別名を使用して接続するプライベート リンク リソース。 このリソースに送信されるすべてのトラフィックに対して、一意のネットワーク識別子が生成されます。       |
|ターゲット サブリソース   |      接続するサブリソース。 それぞれの種類のプライベート リンク リソースには、好みに応じて選択できるさまざまなオプションがあります。    |
|接続の承認方法    |  自動または手動。 ロールベースのアクセス制御 (RBAC) のアクセス許可に基づいて、自分のプライベート エンドポイントを自動的に承認することができます。 RBAC を使用せずにプライベート リンク リソースに接続する場合は、手動による方法を使用して、リソースの所有者が接続を承認できるようにします。        |
|要求メッセージ     |  要求された接続を手動で承認するためのメッセージを指定できます。 このメッセージは、特定の要求を識別するために使用できます。        |
|接続の状態   |   プライベート エンドポイントがアクティブかどうかを指定する読み取り専用のプロパティ。 トラフィックの送信に使用できるのは、承認済み状態のプライベート エンドポイントのみです。 使用可能なその他の状態: <br>- **[Approved]\(承認済み\)** : 接続が自動または手動で承認され、使用する準備が整っています。</br><br>- **[Pending]\(保留中\)** : 接続が手動で作成されており、プライベート リンク リソースの所有者による承認を待っています。</br><br>- **[Rejected]\(拒否済み\)** : プライベート リンク リソースの所有者によって接続が拒否されました。</br><br>- **[Disconnected]\(切断済み\)** : プライベート リンク リソースの所有者によって接続が削除されました。 プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。 </br>|

プライベート エンドポイントに関する重要な詳細情報を次に示します。 
- プライベート エンドポイントは、[VPN](https://azure.microsoft.com/services/vpn-gateway/) と [Express Route](https://azure.microsoft.com/services/expressroute/) のどちらかのほか、Private Link を使用したサービスを使って、同じ VNet、各リージョンでピアリングされた VNet、グローバルにピアリングされた VNet、およびオンプレミスのコンシューマー間の接続を可能にします。
 
- プライベート エンドポイントを作成すると、リソースのライフサイクルにわたってネットワーク インターフェイスも作成されます。 このインターフェイスには、Private Link サービスにマップされる、サブネットからのプライベート IP アドレスが割り当てられます。
 
- プライベート エンドポイントは、仮想ネットワークと同じリージョンにデプロイする必要があります。 
 
- プライベート リンク リソースは、仮想ネットワークおよびプライベート エンドポイントとは別のリージョンにデプロイできます。
 
- 同じプライベート リンク リソースを使用して、複数のプライベート エンドポイントを作成できます。 共通の DNS サーバー構成を使用する単一のネットワークでは、特定のプライベート リンク リソースに単一のプライベート エンドポイントを使用することで、エントリの重複や DNS 解決の競合を回避することをお勧めします。 
 
- 同じ仮想ネットワーク内の同じサブネットまたは異なるサブネットに複数のプライベート エンドポイントを作成できます。 サブスクリプションに作成できるプライベート エンドポイントの数には制限があります。 詳細については、 [Azure の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)に関する記事を参照してください。


 
## <a name="private-link-resource"></a>プライベート リンク リソース 
プライベート リンク リソースは、特定のプライベート エンドポイントの宛先ターゲットです。 使用可能なプライベート リンク リソースの種類の一覧を次に示します。 
 
|プライベート リンク リソース名  |リソースの種類   |サブリソース  |
|---------|---------|---------|
|**Private Link サービス** (独自のサービス)   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure SQL Database** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure SQL Data Warehouse** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  BLOB (blob、blob_secondary)<BR> Table (table、table_secondary)<BR> Queue (queue、queue_secondary)<BR> File (file、file_secondary)<BR> Web (web、web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (blob、blob_secondary)       |
 
 
## <a name="network-security-of-private-endpoints"></a>プライベート エンドポイントのネットワーク セキュリティ 
Azure サービスでプライベート エンドポイントを使用する場合、トラフィックは特定のプライベート リンク リソースに対してセキュリティで保護されます。 プラットフォームによってアクセス制御が実行され、指定されたプライベート リンク リソースのみに到達するネットワーク接続が検証されます。 同じ Azure サービス内の追加のリソースにアクセスするには、追加のプライベート エンドポイントが必要です。 
 
パブリック エンドポイントにアクセスしてサポートされている Azure サービスに接続できないように、対象のワークロードを完全にロックダウンすることができます。 この制御により、同じ Azure サービス上でホストされている他のリソースへのアクセスを防止する組み込みの流出保護を提供することで、自分のリソースにネットワーク セキュリティ レイヤーが追加されます。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>承認ワークフローを使用したプライベート リンク リソースへのアクセス 
次の接続の承認方法を使用して、プライベート リンク リソースに接続できます。
- **自動的**な承認: 特定のプライベート リンク リソースに対するアクセス許可を自ら所有しているか、付与されている場合。 必要なアクセス許可は、次の形式のプライベート リンク リソースの種類に基づきます: Microsoft.\<プロバイダー>/<リソースの種類>/privateEndpointConnectionApproval/action
- **手動**による要求: 必要な許可がなく、アクセスを要求したい場合。 承認ワークフローが開始されます。 プライベート エンドポイントとそれ以降のプライベート エンドポイント接続は、"Pending (保留中)" 状態で作成されます。 プライベート リンク リソースの所有者は、接続を承認する役割を担います。 次の承認ワークフロー ダイアグラムに示すように、プライベート エンドポイントは、承認されると、トラフィックを正常に送信できるようになります。  

![ワークフローの承認](media/private-endpoint-overview/private-link-paas-workflow.png)
 
プライベート リンク リソースの所有者は、プライベート エンドポイント接続で次のアクションを実行できます。 
- すべてのプライベート エンドポイント接続の詳細を確認する。 
- プライベート エンドポイント接続を承認する。 対応するプライベート エンドポイントは、プライベート リンク リソースにトラフィックを送信できるようになります。 
- プライベート エンドポイント接続を拒否する。 対応するプライベート エンドポイントが更新され、状態が反映されます。
- 任意の状態のプライベート エンドポイント接続を削除する。 対応するプライベート エンドポイントは、このアクションを反映して切断済み状態に更新されます。プライベート エンドポイントの所有者は、この時点でのみリソースを削除できます。 
 
> [!NOTE]
> 承認済み状態のプライベート エンドポイントのみが、指定されたプライベート リンク リソースにトラフィックを送信できます。 

### <a name="connecting-using-alias"></a>別名を使用した接続
別名は、サービスの所有者が標準のロード バランサーの背後にプライベート リンク サービスを作成したときに生成される一意のモニカーです。 サービスの所有者は、この別名をオフラインでコンシューマーと共有できます。 コンシューマーは、リソース URI と別名のいずれかを使用して、プライベート リンク サービスへの接続を要求できます。 別名を使用して接続する場合は、手動の接続承認方法を使用してプライベート エンドポイントを作成する必要があります。 手動の接続承認方法を使用するには、プライベート エンドポイント作成フロー中に手動要求パラメーターを true に設定します。 詳細については、「[New-AzPrivateEndpoint](https://docs.microsoft.com/en-us/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0)」および「[az network private-endpoint create](https://docs.microsoft.com/en-us/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)」を参照してください。 

## <a name="dns-configuration"></a>DNS の構成 
接続文字列の一部として完全修飾ドメイン名 (FQDN) を使用してプライベート リンク リソースに接続する場合は、割り当てられたプライベート IP アドレスに解決されるように DNS 設定を正しく構成することが重要です。 既存の Azure サービスには、パブリック エンドポイント経由で接続するときに使用する DNS 構成が既に存在している場合があります。 プライベート エンドポイントを使用して接続するには、これをオーバーライドする必要があります。 
 
プライベート エンドポイントに関連付けられたネットワーク インターフェイスには、特定のプライベート リンク リソースに割り当てられた FQDN やプライベート IP アドレスなど、DNS を構成するために必要な情報の完全なセットが含まれています。 
 
次のオプションを使用して、プライベート エンドポイントの DNS 設定を構成できます。 
- **ホスト ファイルを使用する (テストにのみ推奨)** 。 仮想マシン上のホスト ファイルを使用して、DNS をオーバーライドすることができます。  
- **プライベート DNS ゾーンを使用する**。 プライベート DNS ゾーンを使用して、特定のプライベート エンドポイントの DNS 解決をオーバーライドすることができます。 プライベート DNS ゾーンを自分の仮想ネットワークにリンクして、特定のドメインを解決することができます。
- **カスタム DNS サーバーを使用する**。 独自の DNS サーバーを使用して、特定のプライベート リンク リソースの DNS 解決をオーバーライドすることができます。 自分の DNS サーバーが仮想ネットワーク上にホストされている場合は、プライベート DNS ゾーンを使用する DNS 転送規則を作成して、すべてのプライベート リンク リソースの構成を簡略化することができます。
 
> [!IMPORTANT]
> パブリック エンドポイントを解決する目的でアクティブに使用されているゾーンをオーバーライドすることはお勧めしません。 リソースへの接続は、パブリック DNS への DNS 転送なしでは正しく解決できません。 問題を回避するには、別のドメイン名を作成するか、次の各サービスの推奨される名前に従ってください。 
 
Azure サービスについては、次の表に示すように、推奨されるゾーン名を使用します。

|Private Link リソースの種類   |サブリソース  |ゾーン名  |
|---------|---------|---------|
|SQL DB または DW (Microsoft.Sql/servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Storage アカウント (Microsoft.Storage/storageAccounts)    |  BLOB (blob、blob_secondary)        |    privatelink.blob.core.windows.net      |
|Storage アカウント (Microsoft.Storage/storageAccounts)    |    Table (table、table_secondary)      |   privatelink.table.core.windows.net       |
|Storage アカウント (Microsoft.Storage/storageAccounts)    |    Queue (queue、queue_secondary)     |   privatelink.queue.core.windows.net       |
|Storage アカウント (Microsoft.Storage/storageAccounts)   |    File (file、file_secondary)      |    privatelink.file.core.windows.net      |
|Storage アカウント (Microsoft.Storage/storageAccounts)     |  Web (web、web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake ファイル システム Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake ファイル システム Gen2 (dfs、dfs_secondary)        |     privatelink.dfs.core.windows.net     |
||||
 

提案されたドメイン名に解決をリダイレクトするために、Azure によってパブリック DNS に正規名の DNS レコード (CNAME) が作成されます。 この解決は、プライベート エンドポイントのプライベート IP アドレスでオーバーライドすることができます。 
 
アプリケーションで接続 URL を変更する必要はありません。 パブリック DNS を使用して解決を試みると、DNS サーバーはプライベート エンドポイントに解決するようになります。 このプロセスはアプリケーションに影響しません。 
 
## <a name="limitations"></a>制限事項
 
次の表に、プライベート エンドポイントを使用する場合の既知の制限事項を一覧で示します。 


|制限事項 |説明 |対応策  |
|---------|---------|---------|
|ネットワーク セキュリティ グループ (NSG) 規則は、プライベート エンドポイントには適用されない    |NSG は、プライベート エンドポイントではサポートされません。 プライベート エンドポイントを含むサブネットに NSG を関連付けることはできますが、プライベート エンドポイントによって処理されるトラフィックに対して規則は有効ではありません。 サブネットにプライベート エンドポイントをデプロイするには、[ネットワーク ポリシーの適用を無効にする](disable-private-endpoint-network-policy.md)必要があります。 NSG は、同じサブネット上にホストされている他のワークロードにも適用されます。   | ソース クライアントにおけるアウトバウンド トラフィックに対して NSG 規則を使用して、トラフィックを制御します。        |
|プライベート エンドポイントは、サービス エンドポイントまたは専用ワークロードに対応するサブネットには作成できない    |プライベート エンドポイントは、専用ワークロードに委任されるサービス エンドポイントまたはサブネットに対応するサブネット上でデプロイできません|  プライベート エンドポイントをデプロイするサブネットを別個に作成します。        |
|プライベート エンドポイントは、同じリージョン内の (顧客が所有する) プライベート リンク サービスにしかマップできない    |   別のリージョンから (独自の) プライベート リンク サービスに接続することはできません       |  プレビュー期間中は、対象の Private Link サービスを同じリージョン内にデプロイする必要があります。        |
|専用ワークロードはプライベート エンドポイントにアクセスできない    |   自分の仮想ネットワークにデプロイされた次のサービスは、プライベート エンドポイントを使用してプライベート リンク リソースにアクセスすることはできません。<br>App Service プラン</br>Azure Container Instances</br>Azure NetApp Files</br>Azure の専用 HSM<br>       |   プレビュー期間中は軽減策はありません。       |
|  ポータルで、別名を使用したプライベート エンドポイントの作成がサポートされていない  |   ポータルでは、リソース URI を使用したプライベート エンドポイントの作成のみを行うことができます      | リソース URI を使用してプライベート エンドポイント接続を要求します        |

## <a name="next-steps"></a>次の手順
- [ポータルを使用して SQL Database サーバー用のプライベート エンドポイントを作成する](create-private-endpoint-portal.md)
- [PowerShell を使用して SQL Database サーバー用のプライベート エンドポイントを作成する](create-private-endpoint-powershell.md)
- [CLI を使用して SQL Database サーバー用のプライベート エンドポイントを作成する](create-private-endpoint-cli.md)
- [ポータルを使用してストレージ アカウント用のプライベート エンドポイントを作成する](create-private-endpoint-storage-portal.md)
- [Azure PowerShell を使用して独自の Private Link サービスを作成する](create-private-link-service-powershell.md)
