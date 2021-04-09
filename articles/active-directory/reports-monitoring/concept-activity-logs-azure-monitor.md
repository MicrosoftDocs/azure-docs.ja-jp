---
title: Azure Monitor での Azure Active Directory アクティビティ ログ | Microsoft Docs
description: Azure Monitor での Azure Active Directory アクティビティ ログの概要です
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73130c906d4d9f0da51db1b666e8562570cce40f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571275"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure Monitor の Azure AD アクティビティ ログ

Azure Active Directory (Azure AD) のアクティビティ ログを複数のエンドポイントにルーティングして、長期の保持期間とデータの分析情報を得ることができます。 この機能では次のことができます。

* データを長期間保持するには、Azure AD アクティビティ ログを Azure ストレージ アカウントにアーカイブします。
* Azure AD アクティビティ ログを Azure イベント ハブにストリーム配信して、Splunk や QRadar などの一般的なセキュリティ情報およびイベント管理 (SIEM) ツールを使用して分析することができます。
* Azure AD アクティビティ ログをイベント ハブにストリーミングすることで、独自のカスタム ログ ソリューションと統合することができます。
* Azure AD アクティビティ ログを Azure Monitor ログに送信して、接続データに対する高度な視覚化、監視、およびアラートを有効にします。

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>サポートされるレポート

この機能を使用して、Azure AD 監査ログとサインイン ログを Azure ストレージ アカウント、イベント ハブ、Azure Monitor ログ、またはカスタム ソリューションにルーティングすることができます。 

* **[監査ログ]** :テナント内で実行されたすべてのタスクの履歴は、[監査ログ アクティビティ レポート](concept-audit-logs.md)で把握できます。
* **サインイン ログ**:監査ログによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](concept-sign-ins.md)で判断することができます。

> [!NOTE]
> 現時点では、B2C 関連の監査およびサインインのアクティビティ ログはサポートされません。
>

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure portal で Azure AD の監査ログにアクセスするための Azure AD Free、Basic、Premium 1 または Premium 2 の[ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)。 
* Azure AD テナント。
* Azure AD テナントの "**グローバル管理者**" または "**セキュリティ管理者**" であるユーザー。
* Azure portal で Azure AD のサインイン ログにアクセスするための Azure AD Premium 1 または Premium 2 の[ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)。 

監査ログのデータをルーティングする場所によっては、次のいずれかが必要です。

* *ListKeys* アクセス許可が付与された Azure ストレージ アカウント。 BLOB ストレージ アカウントではなく、一般的なストレージ アカウントを使用することをお勧めします。 ストレージの料金情報については、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=storage)を参照してください。 
* サードパーティ製のソリューションと統合するための Azure Event Hubs の名前空間。
* Azure Monitor ログにログを送信する Azure Log Analytics ワークスペース。

## <a name="cost-considerations"></a>コストに関する考慮事項

既に Azure AD のライセンスをお持ちの場合は、ストレージ アカウントとイベント ハブの設定に Azure サブスクリプションが必要です。 Azure サブスクリプションは無料で利用できますが、アーカイブに使用するストレージ アカウントや、ストリーミングに使用するイベント ハブなどの Azure リソースの利用には料金が発生します。 データ量とそれにより発生するコストは、テナントのサイズによって大幅に変わる場合があります。 

### <a name="storage-size-for-activity-logs"></a>アクティビティ ログのストレージ サイズ

すべての監査ログ イベントは、約 2 KB のデータ ストレージを使用します。 サインイン イベント ログは約 4 KB のデータ ストレージです。 テナントに 100,000 人のユーザーがいる場合、1 日あたり約 150 万のイベントが発生するため、1 日あたり約 3 GB のデータ ストレージが必要になります。 書き込みは約 5 分間のバッチで発生するため、1 か月につき約 9,000 の書き込み操作が予測されます。 


次の表は、1 年以上保持される米国西部の汎用 v2 ストレージ アカウントの料金の見積もりをテナントのサイズ別に示します。 アプリケーションで予測されるデータ量に基づくより正確な見積りを作成するには、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/details/storage/blobs/)を使用します。


| ログのカテゴリ | ユーザーの数 | 1 日あたりのイベント | 1 か月あたりのデータ量 (概算) | 1 か月あたりのコスト (概算) | 1 年あたりのコスト (概算) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100,000 | 150 万&nbsp; | 90 GB | 1\.93 ドル | 23.12 ドル |
| Audit | 1,000 | 15,000 | 900 MB | 0\.02 ドル | 0\.24 ドル |
| サインイン | 1,000 | 34,800 | 4 GB | 0\.13 ドル | 1\.56 ドル |
| サインイン | 100,000 | 1,500 万&nbsp; | 1.7 TB | 35.41 ドル | 424.92 ドル |
 









### <a name="event-hub-messages-for-activity-logs"></a>アクティビティ ログのイベント ハブのメッセージ

イベントは約 5 分間隔でバッチ処理され、その期間のすべてのイベントが含まれる単一のメッセージとして送信されます。 イベント ハブ内のメッセージの最大サイズは 256 KB で、その期間内のすべてのメッセージの合計サイズがそれを超える場合、複数のメッセージが送信されます。 

たとえば、ユーザーが 100,000 人を超える大規模なテナントでは、通常は 1 秒で約 18 のイベントが発生します。これは、5 分間で 5,400 のイベントに相当します。 監査ログはイベントごとに約 2 KB なので、これは 10.8 MB のデータに相当します。 そのため、その 5 分の間隔で 43 のメッセージがイベント ハブに送信されます。 

次の表は、米国西部の基本的なイベント ハブの 1 か月あたりの推定コストを示しています。これは、ユーザーのサインイン動作などのさまざまな要因によってテナントごとに異なる可能性があるイベント データの量によって変わります。アプリケーションで予測されるデータ量の正確な見積りを作成するには、[Event Hubs の料金計算ツール](https://azure.microsoft.com/pricing/details/event-hubs/)を使用します。

| ログのカテゴリ | ユーザーの数 | 1 秒あたりのイベント数 | 5 分間隔あたりのイベント数 | 間隔あたりの量 | 間隔あたりのメッセージ数 | 1 か月あたりのメッセージ数 | 1 か月あたりのコスト (概算) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100,000 | 18 | 5,400 | 10.8 MB | 43 | 371,520 | 10.83 ドル |
| Audit | 1,000 | 0.1 | 52 | 104 KB | 1 | 8,640 | 10.80 ドル |
| サインイン | 100,000 | 18000 | 5,400,000 | 10.8 GB | 42188 | 364,504,320 | 23.9 ドル |  
| サインイン | 1,000 | 178 | 53,400 | 106.8&nbsp;MB | 418 | 3,611,520 | 11.06 ドル |  

### <a name="azure-monitor-logs-cost-considerations"></a>Azure Monitor ログのコストに関する考慮事項



| ログのカテゴリ | ユーザーの数 | 1 日あたりのイベント | 1 か月 (30 日) あたりのイベント | 1 か月あたりの推定コスト (USD) |
|:-|--|--|--|-:|
| 監査とサインイン | 100,000 | 16,500,000 | 495,000,000 | $1093.00 |
| Audit | 100,000 | 1,500,000 | 45,000,000 | $246.66 |
| サインイン | 100,000 | 15,000,000 | 450,000,000 | $847.28 |










Azure Monitor ログの管理に関連するコストをレビューするには、[Azure Monitor ログでデータ ボリュームと保有期間を制御してコストを管理する方法](../../azure-monitor/logs/manage-cost-storage.md)に関する記事をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

このセクションでは、Azure Monitor の Azure AD のログに関するよく寄せられる質問に回答し、既知の問題について説明します。

**Q:どのようなログが含まれますか。**

**A**: この機能を使用してサインイン アクティビティ ログと監査のログの両方をルーティングすることができます。ただし、現時点では B2C に関連する監査イベントは含まれません。 現在サポートされているログの種類や機能ベースのログについて調べるには、[監査ログ スキーマ](reference-azure-monitor-audit-log-schema.md)と[サインイン ログ スキーマ](reference-azure-monitor-sign-ins-log-schema.md)に関するページを参照してください。 

---

**Q:アクション後、対応するログがイベント ハブに表示されるまでどれくらいの時間がかかりますか。**

**A**: ログは、アクションの実行後、2 分から 5 分後にイベント ハブに表示されます。 Event Hubs の詳細については、「[Azure Event Hubs とは](../../event-hubs/event-hubs-about.md)」を参照してください。

---

**Q:アクション後、対応するログがストレージ アカウントに表示されるまでどれくらいの時間がかかりますか。**

**A**: Azure ストレージ アカウントの場合、待ち時間はアクションの実行後 5 分から 15 分です。

---

**Q:管理者が診断設定の保持期間を変更すると、どうなりますか。**

**A**: 新しいリテンション ポリシーは、変更後に収集されたログに適用されます。 ポリシーの変更より前に収集されたログは、影響を受けません。

---

**Q:データを格納する料金はどれくらいかかりますか。**

**A**: ストレージの料金は、ログのサイズと選択した保持期間によって変わります。 生成されたログの量に依存する、テナントの推定コストの一覧については、[アクティビティ ログのストレージ サイズ](#storage-size-for-activity-logs)に関するセクションを参照してください。

---

**Q:データをイベント ハブにストリーミングする料金はどれくらいかかりますか。**

**A**: ストリーミングの料金は、1 分あたりに受信するメッセージの数によって異なります。 この記事では、コストの計算方法を示し、メッセージ数に基づくコスト見積もりの一覧を示します。 

---

**Q:Azure AD アクティビティ ログを SIEM システムと統合する方法を教えてください。**

**A**: 次の 2 つの方法で行います。

- Azure Monitor と Event Hubs を使用して、ログを SIEM システムにストリーム配信する。 最初に[ログをイベント ハブにストリーム配信](tutorial-azure-monitor-stream-logs-to-event-hub.md)してから、構成されたイベント ハブで [SIEM ツールを設定](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub)します。 

- [レポート Graph API](concept-reporting-api.md) を使用してデータにアクセスし、独自のスクリプトを使用してそれを SIEM システムにプッシュする。

---

**Q:現時点ではどの SIEM ツールがサポートされてますか。** 

**A**: **A**: 現在、Azure Monitor は [Splunk](./howto-integrate-activity-logs-with-splunk.md)、IBM QRadar、[Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory)、[ArcSight](./howto-integrate-activity-logs-with-arcsight.md)、LogRhythm、および Logz.io でサポートされています。 コネクタのしくみの詳細については、「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)」を参照してください。

---

**Q:Azure AD アクティビティ ログを Splunk インスタンスと統合する方法を教えてください。**

**A**: 最初に [Azure AD アクティビティ ログをイベント ハブにルーティング](./tutorial-azure-monitor-stream-logs-to-event-hub.md)してから、[アクティビティ ログを Splunk と統合する](./howto-integrate-activity-logs-with-splunk.md)手順に従ってください。

---

**Q:Azure AD アクティビティ ログを Sumo Logic と統合する方法を教えてください。** 

**A**: 最初に [Azure AD アクティビティ ログをイベント ハブにルーティング](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)してから、[Azure AD アプリケーションをインストールして SumoLogic のダッシュボードで表示する](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)手順に従ってください。

---

**Q:外部の SIEM ツールを使用せずにイベント ハブからデータにアクセスできますか。** 

**A**: はい。 カスタム アプリケーションからログにアクセスするには、[Event Hubs API](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) を使用します。 

---


## <a name="next-steps"></a>次のステップ

* [アクティビティ ログをストレージ アカウントにアーカイブする](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [アクティビティ ログをイベント ハブにルーティングする](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [アクティビティ ログと Azure Monitor の統合](howto-integrate-activity-logs-with-log-analytics.md)