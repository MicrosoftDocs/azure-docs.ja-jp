---
title: Azure サービス正常性通知とは
description: サービス正常性通知を使用すると、Microsoft Azure によって発行されるサービスの正常性に関するメッセージを表示できます。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ab4aa2f37d5a883c83b8ee09b5de6551ebf13d2e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995150"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal を使用したサービス正常性通知の表示

サービス正常性通知は、Azure によって発行され、サブスクリプションのリソースに関する情報を含みます。 これらの通知はアクティビティ ログのイベントのサブクラスであり、アクティビティ ログでも確認できます。 サービス正常性通知には、クラスに応じて、情報のみの通知とアクションが必要になる通知があります。

サービス正常性通知には、複数のクラスがあります。  

- **操作が必要:** Azure でアカウントにおける不審な状況が見つかった場合、ユーザーと連携してこれに対応します。 必要なアクションの詳細や、Azure のエンジニアリング チームやサポートに問い合わせる方法が記載された通知が、Azure から送信されます。  
- **支援復旧:** イベントが発生し、引き続きその影響があることをエンジニアが確認している状態です。 Azure エンジニアリング チームが、サービスの復旧のためにユーザーと直接やり取りをする必要があります。  
- **インシデント:** サービスに影響するイベントが現在、サブスクリプションの 1 つ以上のリソースに影響を与えています。  
- **メンテナンス:** サブスクリプションの 1 つ以上のリソースに影響を与える可能性のある計画済みメンテナンス アクティビティです。  
- **情報:** リソースの使用状況を改善するのに役立つ可能性のある最適化に関する情報です。 
- **セキュリティ:** Azure で実行されるソリューションに関する緊急のセキュリティ関連情報です。

各サービスの正常性通知には、その範囲とリソースへの影響に関する詳細が含まれます。 詳細には次のものが含まれます。

プロパティ名 | 説明
-------- | -----------
channels | 次のいずれかの値:**Admin** または **Operation**。
correlationId | 通常は文字列形式の GUID。 同じアクションに属するイベントは、通常、同じ correlationId を共有します。
eventDataId | イベントの一意識別子。
eventName | イベントのタイトル。
level | イベントのレベル
resourceProviderName | 影響を受けるリソースのリソース プロバイダーの名前。
resourceType| 影響を受けるリソースの種類。
subStatus | 通常は対応する REST 呼び出しの HTTP 状態コードですが、サブステータスを示す他の文字列が含まれる場合もあります。 例: OK (HTTP 状態コード:200)、作成済み (HTTP 状態コード:201)、受理 (HTTP 状態コード:202)、コンテンツなし (HTTP 状態コード:204)、無効な要求 (HTTP 状態コード:400)、見つかりません (HTTP 状態コード:404)、競合 (HTTP 状態コード:409)、内部サーバー エラー (HTTP 状態コード:500)、サービス利用不可 (HTTP 状態コード:503)、ゲートウェイ タイムアウト (HTTP 状態コード:504)。
eventTimestamp | イベントに対応する要求を処理する Azure サービスによってイベントが生成されたときのタイムスタンプ。
submissionTimestamp | イベントがクエリで使用できるようになったときのタイムスタンプ。
subscriptionId | このイベントが記録された Azure サブスクリプション。
status | 操作の状態を説明する文字列。 一般的な値は次のとおりです。**Started**、**In Progress**、**Succeeded**、**Failed**、**Active**、**Resolved**。
operationName | 操作の名前。
category | このプロパティは常に **ServiceHealth** です。
resourceId | 影響を受けるリソースのリソース ID。
Properties.title | この通信のローカライズされたタイトル。 既定は英語です。
Properties.communication | HTML マークアップによる通信のローカライズされた詳細。 既定は英語です。
Properties.incidentType | 次のいずれかの値:**ActionRequired**、**Informational**、**Incident**、**Maintenance**、**Security**。
Properties.trackingId | このイベントに関連付けられているインシデント。 インシデントに関連するイベントを関連付けるために使用します。
Properties.impactedServices | インシデントの影響を受けるサービスやリージョンが記述された、エスケープされた JSON BLOB。 このプロパティには、(それぞれ **ServiceName** を持つ) サービスのリストと、(それぞれ **RegionName** を持つ) 影響を受けたリージョンのリストが含まれます。
Properties.defaultLanguageTitle | 英語で行われる通信。
Properties.defaultLanguageContent | HTML マークアップまたはプレーン テキストとして英語で行われる通信。
Properties.stage | **Incident** および **Security** で使用可能な値は **Active**、**Resolved** または **RCA** です。 **ActionRequired** または **Informational** で使用可能な値は **Active** のみです。 **Maintenance** で使用可能な値:**Active**、**Planned**、**InProgress**、**Canceled**、**Rescheduled**、**Resolved**、**Complete**。
Properties.communicationId | このイベントが関連付けられている通信。

### <a name="details-on-service-health-level-information"></a>サービス正常性レベルの詳細情報

**操作が必要** (properties.incidentType == ActionRequired)
- Informational - 既存のサービスへの影響を防ぐために管理者の操作が必要です
    
**メンテナンス** (properties.incidentType == Maintenance)
- Warning - 緊急のメンテナンス
- Informational - 標準的な計画済みメンテナンス

**情報** (properties.incidentType == Information)
- Informational - 既存のサービスへの影響を防ぐために管理者の操作が必要になる場合があります

**セキュリティ** (properties.incidentType == Security)
- Error - 複数の領域にまたがり多様なサービスを利用する際に発生する問題で、範囲は多岐にわたり多くのお客様に影響します。
- Warning - 特定のサービス、あるいは特定の領域を利用した際に発生する問題で、そうしたサービスを利用される一部のお客様に影響します。
- Informational - 管理操作あるいは待機時間に影響し、サービスの可用性には影響しない問題です。

**サービスに関する問題** (properties.incidentType == Incident)
- Error - 複数の領域にまたがり多様なサービスを利用する際に発生する問題で、範囲は多岐にわたり多くのお客様に影響します。
- Warning - 特定のサービス、あるいは特定の領域を利用した際に発生する問題で、そうしたサービスを利用される一部のお客様に影響します。
- Informational - 管理操作あるいは待機時間に影響し、サービスの可用性には影響しない問題です。


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal でのサービス正常性通知の表示
1.  [Azure Portal](https://portal.azure.com) で、**[モニター]** を選択します。

    ![Azure Portal メニューのスクリーンショット。[モニター] が選択されている](./media/service-notifications/home-monitor.png)

    Azure Monitor は、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.  **[Alerts]**(アラート) を選択します。

    ![モニターのアクティビティ ログのスクリーンショット。[アラート] が選択されている](./media/service-notifications/service-health-summary.png)
4. **[+ アクティビティ ログ アラートの追加]** を選択し、今後サービスの通知を受け取るようにアラートを設定します。 詳細については、「[サービス通知のアクティビティ ログ アラートを作成する](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)」を参照してください。

## <a name="next-steps"></a>次の手順
[サービス正常性通知が投稿されるたびにアラートを受け取る](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。  
[アクティビティ ログのアラート](../../azure-monitor/platform/activity-log-alerts.md)について詳しく学習します。

