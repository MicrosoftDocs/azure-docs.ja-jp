---
title: "サービス正常性通知とは | Microsoft Docs"
description: "サービス正常性通知を使用すると、Microsoft Azure によって発行されるサービスの正常性に関するメッセージを確認できます。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: d85281c02b792921f12cc62e6d60bef3e7c13b3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="service-health-notifications"></a>サービス正常性通知
## <a name="overview"></a>概要

この記事では、Azure Portal を使用してサービスの正常性通知を表示する方法について説明します。

サービス正常性通知を使用すると、Azure チームが発行した、登録しているリソースに影響を与えている可能性のあるサービスの正常性に関するメッセージを表示できます。 これらの通知はアクティビティ ログのイベントのサブクラスであり、[アクティビティ ログ] ブレードでも確認できます。 サービス正常性通知は、クラスに応じて情報のみまたはアクションを伴うものである場合があります。

サービス正常性通知には、次の 5 つのクラスがあります。  

- **要アクション:** お使いのアカウントに不審な状況が見られることがあります。 この状況に対応するために、ユーザーとやり取りをする必要がある場合があります。 実行する必要があるアクションや、Azure のエンジニアリング チームやサポートに問い合わせる方法の詳細が記載された通知をマイクロソフトが送信します。  
- **支援復旧:** イベントが発生し、引き続きその影響があることをエンジニアが確認している状態です。 エンジニアリング チームはサービスの復旧のために、ユーザーと直接やり取りをする必要があります。  
- **インシデント:** サービスに影響するイベントがサブスクリプションの 1 つ以上のリソースに影響を与えています。  
- **メンテナンス:** サブスクリプションの 1 つ以上のリソースに影響を与える可能性のある定期的なメンテナンスに関する通知です。  
- **情報:** リソースの利用状況に改善に役立つ可能性のある最適化案について通知することがあります。  
- **セキュリティ:** Azure で実行中のソリューションに関する緊急のセキュリティ関連情報です。

各サービス正常性通知には、その範囲とリソースへの影響に関する詳細が記載されています。 次の詳細が含まれます。

プロパティ名 | 説明
-------- | -----------
channels | 値は "Admin" または "Operation" のいずれか
correlationId | 通常は文字列形式の GUID。 そのイベントは同じ uber アクションに属し、通常は同じ correlationID を共有します。
eventDataId | イベントの一意の識別子
eventName | イベントのタイトル
最小限のレベル | イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose”
resourceProviderName | 影響を受けるリソースのリソース プロバイダーの名前。
resourceType| 影響を受けるリソースの種類。
subStatus | 通常は対応する REST 呼び出しの HTTP 状態コードですが、副状態を示す他の文字列が含まれる場合もあります。たとえば、OK (HTTP Status Code: 200)、Created (HTTP Status Code: 201)、Accepted (HTTP Status Code: 202)、No Content (HTTP Status Code: 204)、Bad Request (HTTP Status Code: 400)、Not Found (HTTP Status Code: 404)、Conflict (HTTP Status Code: 409)、Internal Server Error (HTTP Status Code: 500)、Service Unavailable (HTTP Status Code: 503)、Gateway Timeout (HTTP Status Code: 504) などの一般的な値が含まれる場合があります。
eventTimestamp | イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。
submissionTimestamp |   イベントがクエリで使用できるようになったときのタイムスタンプ。
subscriptionId | このイベントが記録された Azure サブスクリプション
status | 操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。
operationName | 操作の名前。
カテゴリ | "ServiceHealth"
resourceId | 影響を受けるリソースのリソース ID。
Properties.title | この通信のローカライズされたタイトル。 既定の言語は英語です。
Properties.communication | HTML マークアップによる通信のローカライズされた詳細。 既定は英語です。
Properties.incidentType | 使用可能な値: AssistedRecovery、ActionRequired、Information、Incident、Maintenance、Security
Properties.trackingId | このイベントに関連付けられているインシデントを識別します。 インシデントに関連するイベントを関連付けるために使用します。
Properties.impactedServices | インシデントの影響を受けるサービスやリージョンが記述されるエスケープされた JSON blob。 それぞれ ServiceName が指定された Services の一覧、およびそれぞれ RegionName が指定された ImpactedRegions の一覧です。
Properties.defaultLanguageTitle | 英語で行われる通信
Properties.defaultLanguageContent | HTML マークアップまたはプレーン テキストとして英語で行われる通信
Properties.stage | AssistedRecovery、ActionRequired、Information、Incident、Security で使用可能な値: Active、Resolved。 Maintenance で使用可能な値: Active、Planned、InProgress、Canceled、Rescheduled、Resolved、Complete
Properties.communicationId | このイベントが関連付けられている通信。


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal でサービス正常性通知を確認する
1.  [ポータル](https://portal.azure.com)で、**[モニター]** サービスに移動します。

    ![監視](./media/monitoring-service-notifications/home-monitor.png)
2.  **[モニター]** オプションをクリックして、[モニター] ブレードを開きます。 このブレードは、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.  これで **[サービスの通知]** セクションをクリックします。

    ![監視](./media/monitoring-service-notifications/service-health-summary.png)
4.  行項目のいずれかをクリックして詳細を表示します。

5. **[+ アクティビティ ログ アラートの追加]** オペレーションをクリックして通知を受け取り、今後この種類のサービス通知を受け取るようにします。 サービス通知のアラートを構成する方法について詳しくは、[こちらをクリック](monitoring-activity-log-alerts-on-service-notifications.md)してください。

## <a name="next-steps"></a>次のステップ:
[サービス正常性通知が投稿されるたびにアラートを受け取る](monitoring-activity-log-alerts-on-service-notifications.md)  
[アクティビティ ログのアラート](monitoring-activity-log-alerts.md)の詳細を確認する
