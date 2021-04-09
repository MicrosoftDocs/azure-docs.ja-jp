---
title: Azure portal を使用してリソースの正常性アラートを作成する
description: Azure リソースが利用不可になったときに通知するアラートを Azure portal を使用して作成します。
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: e48c400e5be3516b08496db7a4cb6a19e45d6c97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594626"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Azure portal を使用してリソースの正常性アラートを構成する

この記事では、Azure portal を使用してリソースの正常性通知を行うアクティビティ ログ アラートを設定する方法について説明します。

Azure Resource Health では、Azure リソースの現在および過去の正常性状態に関する情報が持続的に通知されます。 Azure Resource Health アラートでは、これらのリソースの正常性状態が変化すると、ほぼリアルタイムで通知できます。 Resource Health アラートをプログラムで作成すると、ユーザーは通知を一括で作成およびカスタマイズできます。

リソース正常性通知は、[Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に保存されます。アクティビティ ログには大量の情報が保存される可能性があるため、リソース正常性通知のアラートの表示と設定を容易にするための別のユーザー インターフェイスがあります。
Azure リソースでリソース正常性通知を Azure サブスクリプションに送信するときに、アラートを受け取ることができます。 次の情報に基づくアラートを構成できます。

* 影響を受けたサブスクリプション。
* 影響を受けたリソースの種類。
* 影響を受けたリソース グループ。
* 影響を受けたリソース。
* 影響を受けたリソースのイベントの状態。
* 影響を受けたリソースの状態。
* 影響を受けたリソースの理由の種類。

次の方法でアラートを送信するユーザーを構成することもできます。

* 既存のアクション グループを選択します。
* 新しいアクション グループを作成します (将来のアラートで使用できます)。

アクション グループの詳細については、[アクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)に関するページを参照してください。

Azure Resource Manager テンプレートを使用したリソース正常性通知アラートの構成方法の詳細については、[Resource Manager テンプレート](./resource-health-alert-arm-template-guide.md)に関するページを参照してください。
Azure portal を使用したリソースの正常性アラート

## <a name="resource-health-alert-using-azure-portal"></a>Azure portal を使用したリソースの正常性アラート

1. Azure [ポータル](https://portal.azure.com/)で、 **[サービス正常性]** を選択します。

    ![サービス正常性の選択](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. **[リソース正常性]** セクションで、 **[サービス正常性]** を選択します。
3. **[リソース正常性アラートの追加]** を選択し、フィールドに入力します。
4. [アラートの対象] で、警告を表示する **[サブスクリプション]** 、 **[リソースの種類]** 、 **[リソース グループ]** および **[リソース]** を選択します。

    ![ターゲットの選択](./media/resource-health-alert-monitor-guide/alert-target.png)

5. アラート条件で、次のように選択します。
    1. アラートを受け取りたい **イベントの状態**。 イベントの重大度レベル: [Active]\(アクティブ\)、[Resolved]\(解決\)、[In Progress]\(進行中\)、[Updated]\(更新\)
    2. アラートを受け取りたい **リソースの状態**。 イベントのリソースの状態:[Available]\(使用可能\)、[Unavailable]\(使用不可\)、[Unknown]\(不明\)、[Degraded]\(デグレード\)
    3. アラートを受け取りたい **理由の種類**。 イベントの原因: [Platform Initiated]\(プラットフォーム開始\)、[User Initiated]\(ユーザー開始\) ![アラート条件選択の正常性の選択](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. [アラートの詳細を定義します] で、以下の詳細を入力します。
    1. **アラート ルール名**:新しいアラート ルールの名前。
    2. **説明**:新しいアラート ルールの説明。
    3. **リソース グループにアラートを保存します**:この新しいルールを保存するリソース グループを選択します。
7. **[アクション グループ]** のドロップダウン メニューから、この新しいアラート ルールに割り当てるアクション グループを指定します。 または、[新しいアクション グループを作成](../azure-monitor/alerts/action-groups.md)して、新しいルールに割り当てます。 新しいグループを作成するには、 **[+ 新しいグループ]** を選択します。
8. ルールを作成後に有効にするには、 **[ルールの作成時に有効にする]** オプションの **[はい]** を選択します。
9. **[アラート ルールの作成]** を選択します。

アクティビティ ログの新しいアラート ルールが作成され、ウィンドウの右上隅に確認メッセージが表示されます。
ルールは、有効化、無効化、編集、または削除することができます。 [アクティビティ ログ ルールを管理する方法](../azure-monitor/alerts/alerts-activity-log.md#view-and-manage-in-the-azure-portal)を確認してください。

## <a name="next-steps"></a>次のステップ

Resource Health に関する詳細情報を参照してください。

* [Azure Resource Health の概要](Resource-health-overview.md)
* [Azure Resource Health で利用できるリソースの種類と正常性チェック](resource-health-checks-resource-types.md)

Service Health アラートを作成します。

* [Service Health のアラートの構成](./alerts-activity-log-service-notifications-portal.md) 
* [Azure アクティビティ ログのイベント スキーマ](../azure-monitor/essentials/activity-log-schema.md)
* [Resource Manager テンプレートを使用して Resource Health アラートを構成する](./resource-health-alert-arm-template-guide.md)
