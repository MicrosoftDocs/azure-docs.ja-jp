---
title: Azure Monitor アラートの共通アラート スキーマ
description: 共通アラート スキーマの理解、使用すべき理由と有効化の方法
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 39ea469b4109f4715db0d0a73ad069121058a46f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038259"
---
# <a name="common-alert-schema"></a>共通アラート スキーマ

この記事では、共通アラート スキーマの概要、それを使用する利点、および有効化の方法について説明します。

## <a name="what-is-the-common-alert-schema"></a>アラート スキーマとは

共通アラート スキーマは、今日の Azure でのアラート通知の使用エクスペリエンスを標準化します。 これまで、今日の Azure の 3 種類のアラート (メトリック、ログ、アクティビティ ログ) は、独自の電子メール テンプレートや Webhook スキーマ などを持ちました。共通アラート スキーマにより、一貫性のあるスキーマでアラート通知を受信できます。

すべてのアラート インスタンスでは、**影響を受けたリソース** および **アラートの原因** が記述されており、これらのインスタンスは次のセクションの共通スキーマで記述されています。
* **要点**: すべてのアラートの種類において共通の一連の **標準化されたフィールド**。これらのフィールドでは、アラート対象の **リソース** と、追加の共通アラート メタデータ (重大度や説明など) が記述されています。 
* **アラート コンテキスト**: **アラートの原因** が記述されているフィールドと、**アラートの種類に基づいて** 異なるフィールドのセット。 たとえば、メトリック アラートの場合はメトリック名やメトリック値などのフィールドがアラート コンテキストにあり、アクティビティ ログ アラートの場合はそのアラートを生成したイベントに関する情報があります。 

ユーザーが使用する一般的な統合シナリオでは、なんらかのピボット (リソース グループなど) に基づいた関心のあるチームに対してアラート インスタンスのルーティングが必要になります。その後、担当のチームがアラートの処理を開始します。 共通アラート スキーマを使用すると、必須フィールドを利用することでアラートの種類にわたって標準化されたルーティング ロジックを持つことができ、関心のあるチームがさらに調査を行うために、コンテキスト フィールドをそのままにできます。

これにより、統合を少なくでき、管理と保守のプロセスを _はるかに_ 単純なタスクにできます。 さらに、今後のアラート ペイロードのエンリッチメント (たとえば、カスタマイズや診断のエンリッチメントなど) は共通スキーマで行われます。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>共通アラート スキーマによって強化された機能

共通アラート スキーマは、主にアラート通知で示されます。 強化された機能を次に示します。

| アクション | 機能強化|
|:---|:---|
| sms | すべてのアラートの種類における一貫性のある SMS テンプレート。 |
| Email | 一貫性のある詳細な電子メール テンプレート。これにより、一目で問題を診断できます。 ポータルおよび影響を受けるリソース上のアラート インスタンスへの埋め込みディープ リンクにより、即座に修復プロセスに移動できます。 |
| Webhook、Logic App、Azure Function、Automation Runbook | すべてのアラートの種類における一貫性のある JSON 構造により、異なるアラートの種類にわたって簡単に統合を構築できます。 |

また、新しいスキーマにより、Azure portal と Azure mobile app の両方で、より優れたアラート使用エクスペリエンスを可能にします。 

[Webhooks、Logic Apps、Azure Functions、Automation Runbooks のスキーマ定義について説明します。](./alerts-common-schema-definitions.md)

> [!NOTE]
> 次のアクションでは、共通アラート スキーマはサポートされません。ITSM Connector。

## <a name="how-do-i-enable-the-common-alert-schema"></a>共通アラート スキーマを有効にする方法

ポータルおよび REST API の両方で、アクション グループを介して共通アラート スキーマにオプトインまたはオプトアウトできます。 新しいスキーマへ切り替えるトグルは、アクション レベルで存在します。 たとえば、電子メール アクションと Webhook アクションには個別にオプトインする必要があります。

> [!NOTE]
> 1. 次のアラートの種類では、共通スキーマがデフォルトでサポートされています (オプトインは不要です)。
>     * スマート検出アラート
> 1. 現在、次のアラートの種類では共通スキーマはサポートされていません。
>     * [VM 分析情報](../vm/vminsights-overview.md)によって生成されたアラート
>     * [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md) によって生成されたアラート

### <a name="through-the-azure-portal"></a>Azure portal を使用

![共通アラートスキーマのオプトイン](media/alerts-common-schema/portal-opt-in.png)

1. アクション グループで既存または新規のアクションを開きます。 
1. 次に示すように、共通アラート スキーマを有効化するためのトグルで [はい] を選択します。

### <a name="through-the-action-groups-rest-api"></a>Action Groups REST API を使用

[Action Groups API](/rest/api/monitor/actiongroups) を使用して共通アラート スキーマにオプトインすることもできます。 [作成または更新](/rest/api/monitor/actiongroups/createorupdate)の REST API 呼び出しの実行中に、電子メール、Webhook、Logic Apps、Azure Functions、Automation Runbook のすべてのアクションに対して、フラグ "useCommonAlertSchema" を 'true' (オプトイン) または 'false' (オプトアウト) に設定できます。

たとえば、[作成または更新](/rest/api/monitor/actiongroups/createorupdate) REST API に対して次の要求本文を実行すると、次の操作が行われます。

* 電子メール アクション "John Doe's email" に対して共通アラート スキーマを有効にします
* 電子メール アクション "Jane Smith's email" に対して共通アラート スキーマを無効にします
* Webhook アクション "Sample webhook" に対して共通アラート スキーマを有効にします

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>次のステップ

- [Webhooks、Logic Apps、Azure Functions、Automation Runbooks の共通アラート スキーマ定義。](./alerts-common-schema-definitions.md)
- [共通アラートのスキーマを利用してすべてのアラートを処理するロジック アプリを作成する方法について説明します。](./alerts-common-schema-integrations.md)