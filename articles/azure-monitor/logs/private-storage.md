---
title: Azure Monitor Log Analytics でのカスタマー マネージド ストレージ アカウントの使用
description: 独自のストレージ アカウントを Log Analytics のシナリオに使用します
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 69b5927c73dac14c76b94a4ee5bbb21449f8ec98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047454"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics でのカスタマー マネージド ストレージ アカウントの使用

Log Analytics は、さまざまなシナリオで Azure Storage に依存します。 この使用は、通常、自動的に管理されます。 ただし、場合によっては、独自のストレージ アカウント (カスタマー マネージド ストレージ アカウントとも呼ばれます) を提供して管理することが必要になります。 このドキュメントでは、WAD/LAD ログ、Private Link、およびカスタマー マネージド キー (CMK) 暗号化に対するカスタマー マネージド ストレージの使用について説明します。 

> [!NOTE]
> 書式と内容が変更される可能性があるため、Log Analytics によってカスタマー マネージド ストレージにアップロードされたコンテンツには依存しないことをお勧めします。

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Diagnostics 拡張機能ログ (WAD/LAD) の取り込み
Azure Diagnostics 拡張機能エージェント (Windows エージェントの場合は WAD、Linux エージェントの場合は LAD とも呼ばれます) により、さまざまなオペレーティング システム ログが収集されて、カスタマー マネージド ストレージ アカウントに格納されます。 その後、これらのログを Log Analytics に取り込み、それらを確認したり分析したりすることができます。
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>ストレージ アカウントから Azure Diagnostics 拡張機能ログを収集する方法
[Azure portal](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) を使用するか、[Storage Insights API](/rest/api/loganalytics/storage%20insights/createorupdate) を呼び出して、ストレージ アカウントをストレージ データ ソースとして Log Analytics ワークスペースに接続します。

サポートされるデータ型:
* syslog
* Windows イベント
* Service Fabric
* ETW イベント
* IIS ログ

## <a name="using-private-links"></a>プライベート リンクの使用
Azure Monitor リソースへの接続にプライベート リンクが使用されている場合は、カスタム ログまたは IIS ログを取り込むためにカスタマー マネージド ストレージ アカウントが使用されます。 これらのデータ型のインジェスト プロセスでは、最初に中間 Azure ストレージ アカウントにログをアップロードし、その後でのみ、ワークスペースに取り込みます。 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>プライベート リンクを介したカスタマー マネージド ストレージ アカウントの使用
#### <a name="workspace-requirements"></a>ワークスペースの要件
プライベート リンクを介して Azure Monitor に接続する場合、Log Analytics エージェントからは、プライベート リンクを介してアクセスできるワークスペースにのみログを送信できます。 この要件は、次の作業を行う必要があることを意味します。
* Azure Monitor Private Link Scope (AMPLS) オブジェクトを構成する
* ワークスペースに接続する
* プライベート リンクを介して、AMPLS をネットワークに接続する。 

AMPLS の構成手順の詳細については、「[Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](./private-link-security.md)」を参照してください。 

#### <a name="storage-account-requirements"></a>ストレージ アカウントの要件
プライベート リンクに正常に接続するには、ストレージ アカウントが次のようになっている必要があります。
* VNet またはピアリングされたネットワーク上に存在し、プライベート リンクを介して VNet に接続されていること。
* リンク先のワークスペースと同じリージョンに存在していること。
* Azure Monitor によるストレージ アカウントへのアクセスが許可されていること。 選択したネットワークのみにストレージ アカウントへのアクセスを許可することにした場合は、次の例外を選択する必要があります。"信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します"。
![ストレージ アカウント信頼 MS サービスの画像](./media/private-storage/storage-trust.png)
* ワークスペースで他のネットワークからのトラフィックも処理する場合は、関連するネットワークまたはインターネットからの受信トラフィックを許可するように、ストレージ アカウントを構成する必要があります。
* エージェントとストレージ アカウント間の TLS バージョンを調整する - TLS 1.2 以上を使用してデータを Log Analytics に送信することをお勧めします。 [プラットフォーム固有のガイダンス](https://docs.microsoft.com/azure/azure-monitor/logs/data-security#sending-data-securely-using-tls-12)を確認し、必要に応じて、[TLS 1.2 を使用するようにエージェントを構成](https://docs.microsoft.com/azure/azure-monitor/agents/agent-windows#configure-agent-to-use-tls-12)します。 何らかの理由でそれが不可能な場合は、TLS 1.0 を受け入れるようにストレージ アカウントを構成します。

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>CMK データ暗号化のためのカスタマー マネージド ストレージ アカウントの使用
ストレージ アカウント内の保存データはすべて、Azure Storage によって暗号化されます。 既定では、Microsoft のマネージド キー (MMK) を使用してデータを暗号化します。しかし、Azure Storage では、Azure Key Vault の CMK を使用してストレージ データを暗号化することもできます。 独自のキーを Azure Key Vault にインポートするか、または Azure Key Vault API を使用してキーを生成することができます。
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>カスタマー マネージド ストレージ アカウントを必要とする CMK のシナリオ
* CMK によるログ アラート クエリの暗号化
* CMK による保存されたクエリの暗号化

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>カスタマー マネージド ストレージ アカウントに CMK を適用する方法
##### <a name="storage-account-requirements"></a>ストレージ アカウントの要件
ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 Azure Storage ストレージの暗号化およびキー管理の詳細については、「[保存データに対する Azure Storage 暗号化](../../storage/common/storage-service-encryption.md)」を参照してください。

##### <a name="apply-cmk-to-your-storage-accounts"></a>ストレージ アカウントに CMK を適用する
Azure Key Vault で CMK を使用するように Azure ストレージ アカウントを構成するには、[Azure portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)、[PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)、または [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) を使用します。 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>ストレージ アカウントを Log Analytics ワークスペースにリンクする
### <a name="using-the-azure-portal"></a>Azure ポータルの使用
Azure portal で、ワークスペースのメニューを開き、 *[リンクされたストレージ アカウント]* を選択します。 ブレードが開き、前述のユース ケース (Private Link 経由で取り込み、保存されたクエリまたはアラートに CMK を適用) でリンクされたストレージ アカウントが表示されます。
![[リンクされたストレージ アカウント] ブレードの画像](./media/private-storage/all-linked-storage-accounts.png) 表の項目を選択すると、そのストレージ アカウントの詳細が示されます。ここでは、この種類のリンクされたストレージ アカウントを設定または更新できます。 
![[ストレージ アカウントのリンク] ブレードの画像](./media/private-storage/link-a-storage-account-blade.png) 必要に応じて、異なるユース ケースに同じアカウントを使用できます。

### <a name="using-the-azure-cli-or-rest-api"></a>Azure CLI または REST API の使用
[Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) または [REST API](/rest/api/loganalytics/linkedstorageaccounts) を使用して、ストレージ アカウントをワークスペースにリンクすることもできます。

適用可能な dataSourceType 値は次のとおりです。
* CustomLogs – カスタム ログと IIS ログの取り込みにストレージ アカウントを使用します
* Query - ストレージ アカウントを使用して保存されたクエリを格納します (CMK 暗号化に必要)
* Alerts - ストレージ アカウントを使用してログベースのアラートを格納します (CMK 暗号化に必要)


## <a name="managing-linked-storage-accounts"></a>リンクされたストレージ アカウントの管理

### <a name="create-or-modify-a-link"></a>リンクを作成または変更する
ストレージ アカウントをワークスペースにリンクすると、Log Analytics ではサービスによって所有されているストレージ アカウントの代わりにそのアカウントが使用されるようになります。 そのための方法は次のとおりです。 
* 複数のストレージ アカウントを登録して、ログの負荷を分散する
* 複数のワークスペースに同じストレージ アカウントを再利用する

### <a name="unlink-a-storage-account"></a>ストレージ アカウントのリンクを解除する
ストレージ アカウントの使用を停止するには、ワークスペースからストレージのリンクを解除します。 ワークスペースからすべてのストレージ アカウントのリンクを解除すると、Log Analytics ではサービスで管理されたストレージ アカウントの利用が試みられます。 ネットワークでインターネットへのアクセスが制限されている場合、これらのストレージが使用できない可能性があり、ストレージに依存するシナリオはすべて失敗します。

### <a name="replace-a-storage-account"></a>ストレージ アカウントを置換する
インジェストに使用されるストレージ アカウントを置き換えるには
1.  **新しいストレージ アカウントへのリンクを作成します。** ログ エージェントによって更新された構成が取得され、さらに、新しいストレージへのデータの送信を開始します。 このプロセスには数分かかることがあります。
2.  **次に、古いストレージ アカウントのリンクを解除して、エージェントから削除されたアカウントへの書き込みを停止します。** データがこのアカウントからすべて取り込まれるまで、取り込みプロセスでデータを読み取り続けます。 すべてのログが取り込まれるまでは、ストレージ アカウントを削除しないでください。

### <a name="maintaining-storage-accounts"></a>ストレージアカウントのメンテナンス
#### <a name="manage-log-retention"></a>ログの保持期間を管理する
独自のストレージ アカウントを使用する場合、保持期間はユーザーが設定します。 Log Analytics により、プライベート ストレージに格納されているログは削除されません。 代わりに、ユーザー設定に従って負荷を処理するポリシーを設定する必要があります。

#### <a name="consider-load"></a>負荷を考慮する
ストレージ アカウントでは、要求の調整を開始する前に、読み取りおよび書き込み要求の特定の負荷を処理することができます (詳細については、[BLOB ストレージのスケーラビリティおよびパフォーマンス ターゲット](../../storage/common/scalability-targets-standard-account.md)に関するページを参照してください)。 調整が行われると、ログの取り込みに要する時間に影響します。 ストレージ アカウントが過負荷になっている場合は、追加のストレージ アカウントを登録し、負荷をアカウント間で分散します。 ストレージ アカウントの容量とパフォーマンスを監視するには、[Azure portal でその分析情報]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview)を確認します。

### <a name="related-charges"></a>関連料金
ストレージ アカウントは、格納されているデータの量、ストレージの種類、冗長性の種類に応じて課金されます。 詳細については、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs)」と「[Table Storage の料金](https://azure.microsoft.com/pricing/details/storage/tables)」に関するページを参照してください。


## <a name="next-steps"></a>次の手順

- [Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](private-link-security.md)方法を確認する
- [Azure Monitor のカスタマー マネージド キー](../logs/customer-managed-keys.md)について確認する
