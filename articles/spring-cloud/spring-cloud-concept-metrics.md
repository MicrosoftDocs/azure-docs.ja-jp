---
title: Azure Spring Cloud のメトリックについて
description: Azure Spring Cloud でメトリックを確認する方法について説明します
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038501"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure Spring Cloud のメトリック

Azure Monitor メトリックス エクスプローラーは、グラフの描画、傾向の視覚的な相関付け、およびメトリックの値の急上昇と急降下の調査を行うことができる、Microsoft Azure portal のコンポーネントです。 メトリックス エクスプローラーを使用して、ご利用のソースの正常性と使用率を調べます。 Azure Spring Cloud では、メトリックを表示するための 2 つのオプションがあります。 **[アプリケーションの概要]** ページのグラフと、サービス レベルのメトリックのページのグラフです。

## <a name="application-overview-page"></a>[アプリケーションの概要] ページ

各アプリケーションの **[アプリケーションの概要]** ページには、アプリケーションの状態をすばやく確認できるようにするメトリック グラフが表示されます。  Azure Spring Cloud サービスのページで、 **[アプリケーション ダッシュボード]** を選択し、一覧からアプリケーションを選択します。  

次のように、1 分ごとに更新されるメトリックの 5 つのグラフが用意されています。

* **HTTP サーバー エラー**: アプリに対する HTTP 要求のエラー数。
* **受信データ**: アプリが受信したバイト数。
* **送信データ**: アプリに送信されたバイト数。
* **要求**: アプリが受信した要求数。
* **平均応答時間**: アプリからの平均応答時間。

グラフの時間範囲を、1 時間から最大 7 日間の間で選択できます。

## <a name="service-level-metric-queries"></a>サービスレベル メトリックのクエリ

Azure Spring Cloud を使用すると、さまざまなアプリケーション メトリックを監視できます。 このサービスの詳細を学習するには、Azure Monitor メトリックの[作業開始ガイド](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)を参照してください。

メトリック データを確認するには、メトリック、 **[集計]** 、時間範囲の順に選択します。  これらの概念について、以下で説明します。

### <a name="aggregation"></a>集計 

Azure はポーリングを行い、1 分ごとにメトリックを更新します。 Azure には、選択された期間のデータを集計するために、次の 3 つの方法が用意されています。

* **Total**: ターゲット出力としてすべてのメトリックを合計します。
* **Average**: ターゲット出力として期間中の平均値を使用します。
* **最大および最小**: ターゲット出力として期間中の最大および最小値を使用します。

### <a name="time-range"></a>時間範囲

既定の時間範囲を選択するか、独自に定義します。

### <a name="modifying-the-granularity-of-your-metric-query"></a>メトリック クエリの細分性を変更する

既定では、Azure によってすべての Azure Spring Cloud サービスのアプリケーションのメトリックが合計されます。 アプリケーション レベルまたはインスタンス レベルでメトリックを確認するには、フィルター機能を使用します。  
**[フィルタの追加]** を選択し、プロパティを **[アプリ]** に設定して、監視するターゲット アプリケーションを選択します。 必要に応じて、 **[Apply splitting]\(分割の適用\)** オプションを使用して、グラフ内の各アプリに分割線を描画します。

>[!TIP]
> メトリック ページに独自のグラフを構築し、**ダッシュボード**にピン留めできます。 まず、グラフに名前を付けます。  次に、右上隅の **[ダッシュボードにピン留めする]** を選択します。 これで、ポータルの**ダッシュボード**でアプリケーションを確認できるようになりました。