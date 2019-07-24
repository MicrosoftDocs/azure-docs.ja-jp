---
title: Azure Security Center でのセキュリティ イベントの監視と処理 | Microsoft Docs
description: Security Center のイベント ダッシュ ボードを使用して、Azure VM と Azure 以外のコンピューターからのセキュリティ イベントを表示する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 01f6da4f5ad6b618c444949fce8d2b7aa3367e17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58075767"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Azure Security Center でのセキュリティ イベントの監視と処理
イベント ダッシュ ボードは、時間の経過とともに収集されたセキュリティ イベントの数と、注意を払う必要がある重要なイベントの一覧の概要を示します。  

> [!NOTE]
> この機能を使用するには、ワークスペースが Log Analytics バージョン 2 を実行していて、Security Center の Standard レベルに存在する必要があります。 Standard レベルの詳細については、Security Center の[価格ページ](security-center-pricing.md)をご覧ください。
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="what-is-a-security-event"></a>セキュリティ イベントとは何ですか。
Security Center は、Microsoft Monitoring Agent を使用して、さまざまなセキュリティ関連の構成とイベントをマシンから収集し、ワークスペースにこれらのイベントを格納します。 このようなデータの例には、オペレーティング システムのログ (Windows イベント ログ)、実行中のプロセス、および Security Center と統合されたセキュリティ ソリューションからのイベントなどがあります。 またワークスペースには、Microsoft Monitoring Agent によってクラッシュ ダンプ ファイルがコピーされます。

## <a name="events-processed-dashboard"></a>処理されたイベントのダッシュボード
**イベント** ダッシュボードには、Security Center のメイン メニューまたは Security Center の**概要**ブレードからアクセスします。  

![処理されたイベントのダッシュボード][1]

**Security Center** の下の **[イベント]** レベルには、 Azure VM と Azure 以外のコンピューターから Security Center に送信されたイベントの数が表示されます。

**イベント ダッシュボード**は、時間の経過とともに処理されたイベントの数とイベントの一覧の概要を表示します。

 ![ダッシュボード][2]

 ダッシュボードの上半分は、過去 1 週間以内に処理されたすべてのイベントを表しています。 ダッシュボードの下半分には、注目すべきイベントとすべてのイベントが種類ごとに一覧表示されます。

 - **注目すべきイベント**には、Security Center によって提供されるイベント クエリとユーザーが作成して追加したイベント クエリが含まれます。 ダッシュボードには、注目すべき各イベントのカウントを示す簡易ビューも備わっています。
 - **すべてのイベント (種類ごと)** は、受信されるイベントの種類と種類ごとのカウントを示します。 イベントの種類には、SecurityEvent、CommonSecurityLog、WindowsFirewall、および W3CIISLog があります。

> [!NOTE]
> 注目すべきイベントには、[Web ベースライン評価](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment)が含まれます。 Web ベースライン評価の目的は、Web サーバーの潜在的に脆弱な設定を見つけることです。

## <a name="view-processed-event-details"></a>処理されたイベントの詳細を表示
1. **[Security Center]** メイン メニューの **[イベント]** を選択します。
2. **[イベント ダッシュボード]** ワークスペース セレクターが開きます。 ワークスペースが 1 つしかない場合、このワークスペース セレクターは表示されません。 複数のワークスペースが存在する場合は、処理されたイベントの詳細を表示するワークスペースを 1 つ選択する必要があります。 複数のワークスペースが存在する場合は、一覧から 1 つのワークスペースを選択してください。

   ![ワークスペースの一覧][3]

3. **[イベント ダッシュボード]** が開き、選択したワークスペースのイベントの詳細が表示されます。 注目すべきイベントおよびすべてのイベントを種類ごとに表示できます。  この例では、**注目すべきイベント**が選択されています。

   ![注目すべきイベント][4]

4. イベントの種類を選択して、ワークスペースのデータをさらに照会できます。 この例では、**SecurityEvent** が選択されています。

   ![イベントの種類の選択][5]

5. **[ログ検索]** が開き、イベントの種類に関する追加の詳細が表示されます。

   ![ログ検索][6]

## <a name="add-a-notable-event"></a>注目すべきイベントの追加
Security Center は、すぐに使用できる注目すべきイベントを提供します。 [Kusto クエリ言語](../log-analytics/log-analytics-search-reference.md)を使用して、独自のクエリに基づいて注目すべきイベントを追加することができます。 **[イベント ダッシュボード]** は、注目すべきイベントを追加するために後でまた使用します。

1. **[注目すべきイベントの追加]** を選択します。

   ![注目すべきイベントの追加][7]

2. **[注目すべきカスタム イベントの追加]** が開きます。  **[表示名]** で、注目すべきイベントの名前を入力します。 **[検索クエリ]** で、イベントのクエリを入力します。

   ![クエリの入力][8]

4. **[OK]** を選択します。

## <a name="update-your-workspace-for-events-processing"></a>イベント処理用にワークスペースを更新する
Security Center でイベント処理を使用するには、ワークスペースが Log Analytics バージョン 2 を実行していて、Security Center の Standard レベルに存在する必要があります。 **[イベント ダッシュボード]** ワークスペース セレクターで、これらの要件を満たしていないワークスペースを識別できます。

![ワークスペースが要件を満たしていない][9]

ワークスペースの行が次の場合:

- **[REQUIRES UPDATE (更新が必要)]** を含んでいる - ワークスペースを Log Analytics バージョン 2 に更新する必要があります
- **[UPGRADE PLAN (プランのアップグレード )]** を含んでいる - ワークスペースを Security Center の Standard レベルにアップグレードする必要があります
- 空白になっている - ワークスペースは要件を満たしており、ワークスペースを選択すると、ダッシュボードに移動します

> [!NOTE]
> **[イベント ダッシュボード]** の **[EVENTS]** 列は、各ワークスペース内のイベントの量を示します。  一部のワークスペースでは、Security Center の Free レベルがそのワークスペースに適用されるため、この列は空白になります。 Free レベルでは、Security Center はイベントを収集しますが、イベントは Azure Monitor ログには保存されず、ダッシュボードでは使用できません。
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Log Analytics バージョン 2 でワークスペースを更新する
1. **[REQUIRES UPDATE (更新が必要)]** となっているワークスペースを選択します。
2. **[アップグレードの検索]** が開きます。 **[今すぐアップグレード]** を選択します。

   ![今すぐアップグレード][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Security Center の Standard レベルへのアップグレード
1. **[UPGRADE PLAN (プランのアップグレード)]** が含まれているワークスペースを選びます。
2. **イベント ダッシュボード**が開きます。 **[イベント ダッシュボードの試行]** を選択します。

   ![ダッシュボードの試行][11]

3. **[高度なセキュリティへのオンボード]** で、アップグレードするワークスペースを選択します。
4. **[価格]** で、**[Standard]** を選択します。
5. **[保存]** を選択します。

   ![Standard レベルにアップグレードする][12]

## <a name="next-steps"></a>次の手順
この記事では、Security Center のイベント ダッシュボードの使用方法について説明しました。 ダッシュボードのしくみと、イベント クエリを作成する方法についての詳細は、以下を参照してください。

- [Azure Monitor ログとは](../log-analytics/log-analytics-overview.md) – Azure Monitor ログの概要
- [Kusto でのログ検索について](../log-analytics/log-analytics-log-search-new.md) - Azure Monitor ログでログ検索を使用する方法と、ログ検索を作成する前に理解しておく必要がある概念について説明します
- [Kusto 検索リファレンス](../log-analytics/log-analytics-search-reference.md) - クエリ言語を使用して独自のイベントクエリを Log に書き込み方法について説明します

Security Center の詳細については、次の記事をご覧ください。

- [Security Center の概要](security-center-intro.md) - Security Center の主な機能について説明します

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
