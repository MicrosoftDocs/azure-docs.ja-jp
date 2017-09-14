---
title: "Azure アクティビティ ログの概要 | Microsoft Docs"
description: "Azure アクティビティ ログの概要と、Azure アクティビティ ログを使用して Azure サブスクリプション内で発生するイベントを理解する方法について説明します。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8ff9f73fc0732cd2227b7e0cc1091e04d69014eb
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Azure アクティビティ ログでサブスクリプション アクティビティを監視する
**Azure アクティビティ ログ**は、Azure で発生したサブスクリプションレベルのイベントの分析に利用できるサブスクリプション ログです。 たとえば、Azure Resource Manager の運用データから、サービスの正常性イベントまでの範囲のデータが含まれています。 管理者のカテゴリではサブスクリプションのコントロール プレーン イベントが報告されるため、アクティビティ ログは以前は "監査ログ" または "操作ログ" と呼ばれていました。 アクティビティ ログを使用すると、サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック/"RDFE" モデルを使用するリソースに対する操作は含まれません。

![アクティビティ ログとその他の種類のログ ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

図 1: アクティビティ ログとその他の種類のログ

アクティビティ ログは[診断ログ](monitoring-overview-of-diagnostic-logs.md)とは異なります。 アクティビティ ログは、外部から行われるリソースの操作に関するデータを提供します ("コントロール プレーン")。 診断ログは、リソースによって出力され、そのリソースの操作に関する情報を提供します ("データ プレーン")。

Azure Portal、CLI、PowerShell コマンドレット、Azure Monitor REST API を使用して、アクティビティ ログからイベントを取得できます。


> [!WARNING]
> Azure アクティビティ ログは、主に Azure Resource Manager で発生したアクティビティを記録します。 クラシック/RDFE モデルを使用しているリソースは追跡されません。 一部のクラシック リソース タイプでは、Azure Resource Manager にプロキシ リソース プロバイダー (例: Microsoft.ClassicCompute) が存在します。 これらのプロキシ リソース プロバイダーを使用して Azure Resource Manager 経由でクラシック リソース タイプを操作すると、その操作がアクティビティ ログに表示されます。 クラシック ポータルまたは Azure Resource Manager プロキシの外部でクラシック リソース タイプを操作すると、そのアクションは操作ログにのみ記録されます。 この操作ログには、クラシック ポータルでのみアクセスできます。
>
>

アクティビティ ログの概要については、次のビデオを参照してください。
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]
> 
>

## <a name="categories-in-the-activity-log"></a>アクティビティ ログのカテゴリ
アクティビティ ログには、複数のカテゴリのデータが含まれています。 各カテゴリのスキーマの詳細については、[こちらの記事を参照してください](monitoring-activity-log-schema.md)。 チェックの内容は次のとおりです
* **管理** - このカテゴリには、Resource Manager で実行されるすべての作成、更新、削除、およびアクション操作のレコードが含まれています。 このカテゴリで表示されるイベントの種類として、"仮想マシンの作成"、"ネットワーク セキュリティ グループの削除" などがあります。ユーザーまたはアプリケーションが Resource Manager を使用して実行するすべてのアクションは、特定のリソースの種類に対する操作としてモデリングされます。 操作の種類が書き込み、削除、またはアクションの場合、その操作の開始のレコードと成功または失敗のレコードは、いずれも管理カテゴリに記録されます。 管理カテゴリには、サブスクリプション内のロールベースのアクセス制御に対する任意の変更も含まれています。
* **サービス正常性** - このカテゴリには、Azure で発生した任意のサービス正常性インシデントのレコードが含まれます。 このカテゴリで表示されるイベントの種類として、"SQL Azure in East US is experiencing downtime" (米国東部の SQL Azure でダウンタイムが発生しています) などがあります。 サービス正常性イベントには、要対応、支援復旧、インシデント、メンテナンス、情報、またはセキュリティという 6 種類があります。イベントの影響を受けるリソースがサブスクリプションにある場合、1 つのイベントのみが表示されます。
* **アラート** - このカテゴリには、Azure アラートの全アクティビティのレコードが含まれています。 このカテゴリで表示されるイベントの種類として、"CPU % on myVM has been over 80 for the past 5 minutes" (過去 5 分間の myVM の CPU % が 80 を超えました) などがあります。 多様な Azure システムにアラートの概念があります。また、何らかのルールを定義し、条件がそのルールと一致するときに通知を受け取ることができます。 サポートされる Azure のアラートの種類が "アクティブになる" たびに、または通知を生成する条件を満たすたびに、アクティブ化のレコードもこのカテゴリのアクティビティ ログにプッシュされます。
* **自動スケール** - このカテゴリには、サブスクリプションで定義したすべての自動スケール設定に基づいて、自動スケール エンジンの操作に関連するすべてのイベントのレコードが含まれます。 このカテゴリで表示されるイベントの種類として、"Autoscale scale up action failed" (自動スケールのスケールアップ アクションに失敗しました) などがあります。 自動スケールを使用すると、自動スケール設定で指定した時刻や負荷 (メトリック) データに基づいて、サポートされるリソースの種類のインスタンス数を自動的にスケールアウトまたはスケールインすることができます。 スケールアップまたはスケールダウンの条件を満たした場合、開始イベントと、成功または失敗イベントがこのカテゴリに記録されます。
* **推奨** - このカテゴリには、Web サイト、SQL サーバーなど、特定のリソースの種類で発生した推奨イベントが含まれます。 推奨イベントは、リソースをより有効に利用する方法について推奨情報を提供します。 推奨情報を発行するリソースがある場合にのみ、この種類のイベントを受け取ります。
* **ポリシー、セキュリティ、およびリソース正常性** - これらのカテゴリにはイベントが含まれていません。将来的に使用するために予約されています。

## <a name="event-schema-per-category"></a>カテゴリごとのイベント スキーマ
[カテゴリごとのアクティビティ ログ イベント スキーマの詳細については、この記事を参照してください。](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>アクティビティ ログで実行できること
アクティビティ ログでは次のことを実行できます。

![Azure アクティビティ ログ](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* **Azure Portal** でアクティビティ ログを照会して表示する。
* [アクティビティ ログ イベントに対するアラートを設定する](monitoring-activity-log-alerts.md)
* サード パーティのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、アクティビティ ログを [**Event Hubs** にストリーミング](monitoring-stream-activity-logs-event-hubs.md)する。
* [**PowerBI コンテンツ パック**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)を使用して、アクティビティ ログを PowerBI で分析する。
* [アーカイブや手動での検査に使用するためにアクティビティ ログを**ストレージ アカウント**に保存する。](monitoring-archive-activity-log.md) **ログ プロファイル**を使用して、リテンション期間 (日数) を指定できます。
* PowerShell コマンドレット、CLI、または REST API を使用してアクティビティ ログを照会する。

## <a name="query-the-activity-log-in-the-azure-portal"></a>Azure Portal でアクティビティ ログに対してクエリを実行する
Azure Portal のさまざまな場所でアクティビティ ログを表示できます。
* **[アクティビティ ログ] ブレード**。左側のナビゲーション ウィンドウの [その他のサービス] で [アクティビティ ログ] を検索してアクセスすることができます。
* **[監視] ブレード**。左側のナビゲーション ウィンドウに既定で表示されます。 アクティビティ ログはこの Azure Monitor ブレードのセクションの 1 つです。
* 任意のリソースの**リソース ブレード**。たとえば、仮想マシンの構成ブレードです。 このようなリソース ブレードのほとんどで、セクションの 1 つとしてアクティビティ ログが表示されます。アクティビティ ログをクリックすると、そのリソースに関連するアクティビティ ログのイベントが自動的に絞り込まれます。

Azure Portal では、次のフィールドでアクティビティ ログを絞り込むことができます。
* [期間] - イベントの開始時刻と終了時刻。
* [カテゴリ] - 前述のイベント カテゴリ。
* [サブスクリプション] - 1 つまたは複数の Azure サブスクリプション名。
* [リソース グループ] - サブスクリプション内の 1 つまたは複数のリソース グループ。
* [リソース (名前)] - 特定のリソース名。
* [リソースの種類] - リソースの種類 (Microsoft.Compute/virtualmachines など)。
* [操作名] - Azure Resource Manager 操作の名前 (Microsoft.SQL/servers/Write など)。
* [重大度] - イベントの重大度レベル (情報、警告、エラー、重大)。
* [イベント開始者] - "呼び出し元"。つまり、操作を実行したユーザー。
* [Open search]\(検索を開く\) - すべてのイベントのフィールド全体で、指定した文字列を検索するテキスト検索ボックスが開きます。

フィルターのセットを定義して、他のセッションでも使用できるクエリとして保存することができます。今後、同じクエリを実行する必要がある場合に、保存したフィルターを適用することができます。 また、Azure ダッシュボードにクエリをピン留めして、特定のイベントを常に監視することもできます。

[適用] をクリックすると、クエリが実行され、一致するイベントがすべて表示されます。 一覧のイベントをクリックすると、そのイベントの概要と、そのイベントのすべての JSON が未加工で表示されます。

さらに強力な機能もあります。**[ログ検索]** アイコンをクリックすると、[Log Analytics Activity Log Analytics ソリューション](../log-analytics/log-analytics-activity.md)でアクティビティ ログ データを表示することができます。 [アクティビティ ログ] ブレードには基本的なログのフィルター/閲覧機能がありますが、Log Analytics を使用すると、より強力な方法でデータをピボット、クエリ、視覚化することができます。

## <a name="export-the-activity-log-with-a-log-profile"></a>ログ プロファイルを使用してアクティビティ ログをエクスポートする
**ログ プロファイル** は、アクティビティ ログをエクスポートする方法を制御します。 ログ プロファイルを使用して、以下を構成できます。

* アクティビティ ログの送信先 (ストレージ アカウントまたは Event Hubs)
* 送信するイベント カテゴリ (Write、Delete、Action)。 "*ログ プロファイルでの "カテゴリ" の意味は、アクティビティ ログ イベントでの意味とは異なります。ログ プロファイルでの "カテゴリ" は、操作の種類 (Write、Delete、Action) を指します。アクティビティ ログ イベントでの "category" プロパティは、イベントのソースまたは種類 (Administration、ServiceHealth、Alert など) を指します。*"
* エクスポートするリージョン (場所)。 アクティビティ ログのイベントの多くはグローバル イベントなので、"global" を含めてください。
* アクティビティ ログをストレージ アカウントに保持する期間。
    - リテンション期間が 0 日の場合、ログは永続的に保持されます。 リテンション期間が 0 日の場合、ログは永続的に保持されます。
    - リテンション期間ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合 (たとえば、Event Hubs または OMS オプションだけが選択されている場合)、保持ポリシーは無効になります。
    - 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。

ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたはイベント ハブ名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な RBAC アクセスを持っている必要があります。

ここに挙げた設定は、ポータルの [アクティビティ ログ] ブレードの [エクスポート] オプションで構成できます。 さらに、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell コマンドレット、または CLI を使えば、プログラムを使って構成することもできます。 1 つのサブスクリプションで使用できるログ プロファイルは 1 つだけです。

### <a name="configure-log-profiles-using-the-azure-portal"></a>Azure Portal を使用したログ プロファイルの構成
Azure Portal の [エクスポート] オプションを使用して、アクティビティ ログを Event Hubs にストリーミングしたり、ストレージ アカウントに保存したりできます。

1. ポータルの左側のメニューを使用して、 **[アクティビティ ログ]** ブレードに移動します。

    ![ポータルの [アクティビティ ログ] に移動](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. ブレードの上部にある **[エクスポート]** ボタンをクリックします。

    ![ポータルの [エクスポート] ボタン](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. ブレードが表示されたら、以下を選択できます。  
  * イベントをエクスポートするリージョン
  * イベントの保存先となるストレージ アカウント
  * ストレージでイベントを保持する日数。 日数を 0 にした場合には、ログが永久に保持されます。
  * イベントのストリーミング用にイベント ハブを作成する Service Bus 名前空間。

     ![[Export Activity Log (アクティビティ ログのエクスポート)] ブレード](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. **[保存]** をクリックして設定を保存します。 設定はサブスクリプションにすぐに適用されます。

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Azure PowerShell コマンドレットを使用したログ プロファイルの構成
#### <a name="get-existing-log-profile"></a>既存のログ プロファイルの取得
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>ログ プロファイルの追加
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| プロパティ | 必須 | Description |
| --- | --- | --- |
| 名前 |はい |ログ プロファイルの名前。 |
| StorageAccountId |なし |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| serviceBusRuleId |なし |Event Hubs を作成する Service Bus 名前空間の Service Bus 規則 ID。 これは、`{service bus resource ID}/authorizationrules/{key name}` の形式の文字列です。 |
| 場所 |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 |
| RetentionInDays |はい |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が 0 の場合、ログは無期限に (いつまでも) 保存されます。 |
| カテゴリ |なし |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、Write、Delete、Action です。 |

#### <a name="remove-a-log-profile"></a>ログ プロファイルの削除
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Azure クロスプラットフォーム CLI を使用したログ プロファイルの構成
#### <a name="get-existing-log-profile"></a>既存のログ プロファイルの取得
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name` プロパティには、ログ プロファイルの名前を指定する必要があります。

#### <a name="add-a-log-profile"></a>ログ プロファイルの追加
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| name |はい |ログ プロファイルの名前。 |
| storageId |なし |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| serviceBusRuleId |なし |Event Hubs を作成する Service Bus 名前空間の Service Bus 規則 ID。 これは、`{service bus resource ID}/authorizationrules/{key name}` の形式の文字列です。 |
| 場所 |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 |
| RetentionInDays |はい |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が 0 の場合、ログは無期限に (いつまでも) 保存されます。 |
| categories |なし |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、Write、Delete、Action です。 |

#### <a name="remove-a-log-profile"></a>ログ プロファイルの削除
```
azure insights logprofile delete --name my_log_profile
```

## <a name="next-steps"></a>次のステップ
* [アクティビティ ログ (以前の監査ログ) の詳細を確認する](../azure-resource-manager/resource-group-audit.md)
* [Azure アクティビティ ログを Event Hubs にストリーミングする](monitoring-stream-activity-logs-event-hubs.md)

