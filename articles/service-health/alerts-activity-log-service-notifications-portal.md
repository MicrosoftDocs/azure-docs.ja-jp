---
title: Azure portal を使用して Azure サービスの通知でアクティビティ ログ アラートを受け取る
description: Azure portal を使用してサービスの正常性通知を行うためのアクティビティ ログ アラートを Azure portal を使用して設定する方法について説明します。
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48126d923cb0baa33058c6fd55e48f31d793fade
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570187"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Azure portal を使用してサービスの通知でアクティビティ ログ アラートを作成する
## <a name="overview"></a>概要

この記事では、Azure portal を使用してサービスの正常性通知を行うためのアクティビティ ログ アラートを Azure portal を使用して設定する方法について説明します。  

サービス正常性通知は、[Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に格納されます。 アクティビティ ログに格納されている大量の情報を考慮し、サービス正常性通知に関するアラートを表示および設定しやすくするための個別のユーザー インターフェイスがあります。 

Azure でサービス正常性通知を Azure サブスクリプションに送信するときに、アラートを受け取ることができます。 次の情報に基づくアラートを構成できます。

- サービス正常性通知のクラス (サービスに関する問題、計画メンテナンス、正常性の勧告、セキュリティに関する勧告)。
- 影響を受けたサブスクリプション。
- 影響を受けたサービス。
- 影響を受けたリージョン。

> [!NOTE]
> サービス正常性通知では、リソース正常性イベントのアラートは送信されません。

次の方法でアラートを送信するユーザーを構成することもできます。

- 既存のアクション グループを選択します。
- 新しいアクション グループを作成します (将来のアラートで使用できます)。

アクション グループの詳細については、[アクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)に関するページを参照してください。

Azure Resource Manager テンプレートを使用したサービス正常性通知アラートの構成方法の詳細については、[Resource Manager テンプレート](../azure-monitor/alerts/alerts-activity-log.md)に関するページを参照してください。

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>初めての Azure Service Health アラートの設定に関するビデオを見る

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Azure portal を使用して Service Health アラートを作成する
1. [ポータル](https://portal.azure.com)で、 **[サービス正常性]** を選択します。

    ![[サービス正常性] サービス](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. **[アラート]** セクションで、 **[正常性アラート]** を選択します。

    ![[正常性アラート] タブ](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. **[サービス正常性アラートの追加]** を選択し、フィールドに入力します。

    ![[サービス正常性アラートの作成] コマンド](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. アラートの対象となる **[サブスクリプション]** 、 **[サービス]** 、 **[リージョン]** を選択します。

    [![[アクティビティ ログ アラートの追加] ダイアログ ボックス](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>このサブスクリプションは、アクティビティ ログ アラートの保存に使用されます。 アラート リソースはこのサブスクリプションにデプロイされ、アクティビティ ログのイベントを監視します。

5. アラートを受け取りたい **イベントの種類** を選択します。これは、 *[サービスの問題]* 、 *[計画メンテナンス]* 、 *[正常性の勧告]* 、および *[セキュリティに関する勧告]* のいずれかです。

6. **[アクション グループの選択]** をクリックして、既存のアクション グループを選択するか、新しいアクション グループを作成します。 アクション グループの詳細については、「[Azure portal でのアクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)」を参照してください。


7. **[アラート ルール名]** と **[説明]** を入力してアラートの詳細を定義します。

8. アラートを保存する **[リソース グループ]** を選択します。



数分以内にアラートがアクティブになり、作成時に指定した条件に基づいてトリガーが開始されます。

[既存の問題管理システム用の webhook 通知を構成する](service-health-alert-webhook-guide.md)方法について学習します。 アクティビティ ログ アラートの webhook スキーマの詳細については、「[Azure アクティビティ ログ アラートのための webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md)」を参照してください。


## <a name="next-steps"></a>次のステップ
- [Azure Service Health アラートの設定に関するベスト プラクティス](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)を学習します。
- [Azure Service Health のモバイル プッシュ通知を設定](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)する方法について学習します。
- [既存の問題管理システム用の webhook 通知を構成する](service-health-alert-webhook-guide.md)方法について学習します。
- [サービス正常性の通知](service-notifications.md)について学習します。
- [通知のレート制限](../azure-monitor/alerts/alerts-rate-limiting.md)について学習します。
- [アクティビティ ログ アラート webhook スキーマ](../azure-monitor/alerts/activity-log-alerts-webhook.md)を確認します。
- [アクティビティ ログ アラートの概要](../azure-monitor/alerts/alerts-overview.md)を把握し、アラートを受信する方法について学習します。
- [アクション グループ](../azure-monitor/alerts/action-groups.md)について学習します。
