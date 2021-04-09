---
title: リソースによる Azure Sentinel データへのアクセスを管理する | Microsoft Docs
description: この記事では、ユーザーがアクセスできるリソースによる Azure Sentinel データへのアクセスを管理する方法について説明します。 リソースによるアクセスを管理することにより、Azure Sentinel エクスペリエンス全体を使用せずに、特定のデータにのみアクセスできるようにすることができます。 この方法は、リソースコンテキスト RBAC とも呼ばれています。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: 26124f8f650e1006244b4871e26962d417d90fd4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054250"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>リソースによる Azure Sentinel データへのアクセスを管理する

通常、Azure Sentinel ワークスペースにアクセスできるユーザーは、セキュリティ コンテンツを含め、すべてのワークスペース データにもアクセスできます。 管理者は、[Azure ロール](roles.md) を使用することにより、チームのアクセス要件に応じて、Azure Sentinel の特定の機能に対するアクセスを構成できます。

ただし、Azure Sentinel ワークスペース内の特定のデータしかアクセスする必要がなく、Azure Sentinel 環境全体にアクセスする必要がないユーザーもいます。 たとえば、セキュリティ オペレーション以外 (非 SOC) のチームに、そのチームが所有するサーバーの Windows イベント データへのアクセスを提供することが必要な場合があります。

このような場合は、Azure Sentinel ワークスペースまたは特定の Azure Sentinel 機能へのアクセスを提供する代わりに、ユーザーに許可されるリソースに基づいてロールベースのアクセス制御 (RBAC) を構成します。 この方法は、**リソースコンテキスト RBAC** とも呼ばれています。

ユーザーが、Azure Sentinel ワークスペースではなくアクセス可能なリソースを介して Azure Sentinel データにアクセスできる場合、ユーザーは次の方法を使用してログとブックを表示できます。

- **リソース自体を使用する** (Azure 仮想マシンなど): この方法を使用して、特定のリソースのみのログおよびブックを表示します。

- **Azure Monitor を使用する**: この方法は、複数のリソースまたはリソース グループにまたがるクエリを作成する必要がある場合に使用します。 Azure Monitor でログおよびブックに移動して、スコープを 1 つ以上の特定のリソース グループまたはリソースに定義します。

Azure Monitor でリソースコンテキスト RBAC を有効にします。 詳細については、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](/azure/azure-monitor/logs/manage-access)」を参照してください。

> [!NOTE]
> データが、Syslog、CEF、または AAD データなどの Azure リソースではない場合、またはカスタム コレクターによって収集されたデータである場合は、データを識別してアクセスを有効にするために使用されるリソース ID を手動で構成する必要があります。
>
> 詳細については、「[リソースコンテキスト RBAC を明示的に構成する](#explicitly-configure-resource-context-rbac)」を参照してください。
>
## <a name="scenarios-for-resource-context-rbac"></a>リソースコンテキスト RBAC のシナリオ

次の表は、リソースコンテキスト RBAC が最も役立つシナリオを示しています。 SOC チームと非 SOC チームの間のアクセス要件の違いに注意してください。

| 要件の種類 |SOC チーム  |非 SOC チーム  |
|---------|---------|---------|
|**アクセス許可**     | ワークスペース全体        |   特定のリソースのみ      |
|**データ アクセス**     |  ワークスペース内のすべてのデータ       | チームがアクセスを許可されているリソースのデータのみ        |
|**エクスペリエンス**     |  完全な Azure Sentinel エクスペリエンス (ユーザーに割り当てられている[機能のアクセス許可](roles.md)によって制限される場合がある)       |  ログ クエリとブックのみ       |
|     |         |         |

チームのアクセス要件が、上記の表で説明した非 SOC チームと同様である場合は、選択肢としてリソースコンテキスト RBAC が適している可能性があります。

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>ソースコンテキスト RBAC を実装するための代替方法

組織で必要なアクセス許可によっては、ソースコンテキスト RBAC を使用しても完全に解決できない場合があります。

次の一覧では、データ アクセスの他のソリューションの方が要件に適しているシナリオについて説明します。

|シナリオ  |解決策  |
|---------|---------|
|**子会社に、完全な Azure Sentinel エクスペリエンスを必要とする SOC チームがある**。     |  この場合は、マルチワークスペース アーキテクチャを使用して、データのアクセス許可を分離します。 <br><br>詳細については、次を参照してください。 <br>- [ワークスペースおよびテナント全体での Azure Sentinel の拡張](extend-sentinel-across-workspaces-tenants.md)<br>    - [多くのワークスペースのインシデントを一度に操作する](multiple-workspace-view.md)          |
|**特定の種類のイベントへのアクセスを提供する必要がある**。     |  たとえば、Windows 管理者に対して、すべてのシステムで Windows セキュリティ イベントにアクセスできるようにします。 <br><br>このような場合は、[テーブルレベルの RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) を使用して、各テーブルのアクセス許可を定義します。       |
| **リソースに基づくのではなく、アクセスをより詳細なレベルに制限するか、またはイベント内のフィールドのサブセットのみに制限する**   |   たとえば、ユーザーの子会社に基づいて Office 365 ログへのアクセスを制限することができます。 <br><br>この場合、[Power BI のダッシュボードおよびレポート](/azure/azure-monitor/platform/powerbi)で組み込みの統合を使用して、データへのアクセスを提供します。      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>ソースコンテキスト RBAC を明示的に構成する

ソースコンテキスト RBAC を構成する必要があるが、データが Azure リソースではない場合、次の手順を実行します。

たとえば、Azure リソースではない Azure Sentinel ワークスペース内のデータには、Syslog、CEF、または AAD データか、カスタム コレクターによって収集されたデータが含まれます。

**ソースコンテキスト RBAC を明示的に構成する場合**:

1. Azure Monitor で、[リソースコンテキスト RBAC を有効にしている](/azure/azure-monitor/platform/manage-access)ことを確認します。 

1. Azure Sentinel 環境全体を使用せずに、ご使用のリソースにアクセスする必要があるユーザーのチームごとに、[リソース グループを作成](/azure/azure-resource-manager/management/manage-resource-groups-portal)します。

    各チーム メンバーに[ログ閲覧者のアクセス許可](/azure/azure-monitor/platform/manage-access#resource-permissions)を割り当てます。

1. 作成したリソース チーム グループにリソースを割り当て、関連するリソース ID でイベントにタグを付けます。

    Azure リソースによって Azure Sentinel にデータが送信されると、ログ レコードには、データ リソースのリソース ID で自動的にタグが付けられます。

    > [!TIP]
    > 目的に合わせて作成された特定のリソース グループで、アクセスを許可するリソースをグループ化することをお勧めします。
    >
    > グループ化できない場合は、チームが、アクセスさせたいリソースに対して直接アクセスできるログ閲覧者のアクセス許可を持っていることを確認してください。
    >

    リソース ID の詳細については、次のセクションを参照してください。

    - [ログの転送を使用するリソース ID](#resource-ids-with-log-forwarding)
    - [Logstash コレクションを使用するリソース ID](#resource-ids-with-logstash-collection)
    - [Log Analytics API コレクションを使用するリソース ID](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>ログの転送を使用するリソース ID

[Common Event Format (CEF)](connect-common-event-format.md) または [Syslog](connect-syslog.md) を使用してイベントを収集する場合、複数のソース システムからイベントを収集するために、ログの転送が使用されます。

たとえば、CEF または Syslog を転送する VM が、Syslog イベントを送信するソースをリッスンし、それらを Azure Sentinel に転送する場合、転送されるすべてのイベントに、ログ転送 VM のリソース ID が割り当てられます。

複数のチームがある場合は、個別のチームごとに、イベントを処理する個別のログ転送 VM があることを確認してください。

たとえば、VM を分離すると、チーム A に属する Syslog イベントは、コレクター VM A を使用して収集されます。

> [!TIP]
> - ログ フォワーダーとして、オンプレミスの VM または別のクラウド VM (AWS など) を使用する場合は、[Azure Arc](/azure/azure-arc/servers/overview) を実装して、その VM が確実にリソース ID を持つようにしてください。
> - ログ転送 VM 環境をスケーリングするには、CEF および Sylog ログを収集するための [VM スケール セット](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)を作成することを検討してください。


### <a name="resource-ids-with-logstash-collection"></a>Logstash コレクションを使用するリソース ID

Azure Sentinel [Logstash 出力プラグイン](connect-logstash.md)を使用してデータを収集する場合、**azure_resource_id** フィールドを使用して、出力にリソース ID を含めるようにカスタム コレクターを構成します。

リソースコンテキスト RBAC を使用しており、API によって収集されたイベントを特定のユーザーが使用できるようにする必要がある場合、[ユーザー用に作成した](#explicitly-configure-resource-context-rbac)リソース グループのリソース ID を使用します。

たとえば、次のコードは、サンプルの Logstash 構成ファイルを示しています。

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> 異なるイベントに適用されるタグを区別するために、複数の `output` セクションを追加できます。
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Log Analytics API コレクションを使用するリソース ID

[Log Analytics データ コレクター API](/azure/azure-monitor/platform/data-collector-api) を使用して収集する場合、HTTP [*x-ms-AzureResourceId*](/azure/azure-monitor/platform/data-collector-api#request-headers) 要求ヘッダーを使用して、イベントにリソース ID を割り当てます。

リソースコンテキスト RBAC を使用しており、API によって収集されたイベントを特定のユーザーが使用できるようにする必要がある場合、[ユーザー用に作成した](#explicitly-configure-resource-context-rbac)リソース グループのリソース ID を使用します。


## <a name="next-steps"></a>次のステップ

詳細については、「[Azure Sentinel のアクセス許可](roles.md)」を参照してください。
