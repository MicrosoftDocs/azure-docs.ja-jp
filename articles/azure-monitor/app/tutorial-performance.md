---
title: Azure Application Insights を使用してパフォーマンスに関する問題を診断する | Microsoft Docs
description: Azure Application Insights を使用して、アプリケーションのパフォーマンスに関する問題を検出して診断するためのチュートリアルです。
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: df7cfff7d5bf1b89f88105f79c072d1d7e731b31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100597654"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Azure Application Insights を使用してパフォーマンスに関する問題を検出して診断する

Azure Application Insights は、アプリケーションの運用とパフォーマンスを分析するために役立つテレメトリを収集します。  この情報を使用して、発生する可能性がある問題を識別したり、ユーザーに最も影響を与える可能性があるアプリケーションの機能強化を識別したりできます。  このチュートリアルでは、アプリケーションのサーバー コンポーネントのパフォーマンスとクライアントの観点から見たパフォーマンスの両方を分析するプロセスを示します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * サーバー側の操作のパフォーマンスを識別する
> * サーバーの操作を分析して、パフォーマンス低下の根本原因を特定する
> * 最も低速なクライアント側の操作を識別する
> * クエリ言語を使用してページ ビューの詳細を分析する


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- 次のワークロードを使って、[Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールします。
    - ASP.NET および Web の開発
    - Azure の開発
- .NET アプリケーションを Azure にデプロイし、[Application Insights SDK の有効化](../app/asp-net.md)を実行します。
- アプリケーションに対する[Application Insights プロファイラーの有効化](../app/profiler.md#installation)を実行します。

## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。

## <a name="identify-slow-server-operations"></a>低速のサーバー操作を識別する
Application Insights は、アプリケーションのさまざまな操作に対するパフォーマンスの詳細を収集します。 最も実行時間が長い操作を識別することで、潜在的な問題を診断したり、アプリケーションの全体的なパフォーマンスを向上させるための継続的な開発にとって最善のターゲットを診断したりできます。

1. **[Application Insights]** を選択してから、ご利用のサブスクリプションを選択します。  
1. **[パフォーマンス]** パネルを開くには、 **[調査]** メニューの **[パフォーマンス]** を選択するか、 **[サーバー応答時間]** グラフをクリックします。

    ![パフォーマンス](media/tutorial-performance/1-overview.png)

2. **[パフォーマンス]** パネルには、アプリケーションの各操作の数と平均実行時間が表示されます。  この情報を使用して、ユーザーに最も影響を与えている操作を識別できます。 この例では、**GET Customers/Details** と **GET Home/Index** が相対的に実行時間が長く、呼び出し数が多いため、調査対象に適しています。  他にも実行時間が長い操作がありますが、呼び出し回数が少ないため、それらを改善しても効果はごくわずかである可能性があります。  

    ![パフォーマンス サーバー パネル](media/tutorial-performance/2-server-operations.png)

3. この時点では、グラフには、一定期間中の選択された操作の平均実行時間が表示されています。 95 パーセンタイルに切り替えて、パフォーマンスの問題を見つけることができます。 関心のある操作をピン留めすることで、グラフに操作を追加します。  これにより、調査する価値があるピークがあることが示されます。  グラフの時間枠を狭めることで、これをさらに分離します。

    ![ピン留め操作](media/tutorial-performance/3-server-operations-95th.png)

4.  右側のパフォーマンス パネルには、選択した操作のさまざまな要求に対する実行時間の分布が表示されます。  95 パーセンタイル付近から始まるように時間枠を狭めます。 "上位 3 件の依存関係" の分析情報カードから、トランザクションが遅い原因が外部の依存関係である可能性が高いことが一目で分かります。  サンプル数のボタンをクリックすると、サンプルの一覧が表示されます。 任意のサンプルを選択して、トランザクションの詳細を表示することができます。

5.  トランザクションの合計実行時間のうち、Fabrikamaccount Azure Table の呼び出しが最も多くの時間を占めていることがわかります。 また、例外によって呼び出しが失敗したこともわかります。 一覧内の任意の項目をクリックすると、右側に詳細が表示されます。 [トランザクションの診断エクスペリエンスの詳細](../app/transaction-diagnostics.md)

    ![操作のエンドツーエンドの詳細](media/tutorial-performance/4-end-to-end.png)
    

6.  [**プロファイラー**](../app/profiler-overview.md)を使用すると、操作を実行するために使用された実際のコードと、各ステップを実行するために要求された時間が表示され、詳細情報とコード レベルの診断を確認できます。 プロファイラーは定期的に実行されるため、一部の操作はトレースされていない場合があります。  時間の経過と共に、より多くの操作がトレースされます。  操作に対してプロファイラーを起動するには、 **[プロファイラーのトレース]** をクリックします。
5.  トレースは、各操作の個別のイベントを示すため、操作全体の実行時間の長さの根本原因を診断できます。  上の例の最も実行時間が長い操作をクリックします。
6.  **[ホット パス]** をクリックして、操作の実行時間を長くしているイベントの特定のパスを強調表示します。  この例では、最も遅い呼び出しが *FabrikamFiberAzureStorage.GetStorageTableData* メソッドから行われていることがわかります。 ほとんどの時間を費やしている部分は、*CloudTable.CreateIfNotExist* メソッドです。 関数が呼び出されるたびにこのコード行が実行される場合は、不要なネットワークの呼び出しと CPU リソースが消費されます。 コードを修正する最善の方法は、1 回だけ実行する一部のスタートアップ メソッドにこの行を追加することです。

    ![Profiler の詳細](media/tutorial-performance/5-hot-path.png)

7.  画面の上部にある **[パフォーマンスのヒント]** は、過剰な実行時間が待機によるものであるという評価を支持しています。  さまざまな種類のイベントを解釈するためのドキュメントを確認するには、 **[待機中]** リンクをクリックします。

    ![パフォーマンスのヒント](media/tutorial-performance/6-perf-tip.png)

8.   さらに分析するために、 **[トレースのダウンロード]** をクリックして、トレースをダウンロードできます。 [PerfView](https://github.com/Microsoft/perfview#perfview-overview) を使用してこのデータを表示できます。

## <a name="use-logs-data-for-server"></a>サーバーのログ データを使用する
 Logs には、Application Insights によって収集されたすべてのデータを分析するために使用できる高度なクエリ言語が用意されています。 これを使用して、要求とパフォーマンスのデータに対して、詳細な分析を実行できます。

1. 操作の詳細パネルに戻り、![Logs アイコン](media/tutorial-performance/app-viewinlogs-icon.png)**[ログに表示 (Analytics)]** をクリックします

2. Logs が開き、それぞれのビューのクエリがパネルに表示されます。  これらのクエリをそのまま実行するか、要件に合わせて変更できます。  最初のクエリは、この操作の一定期間の実行時間を示します。

    ![Logs クエリ](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>低速のクライアント操作を識別する
Application Insights は、最適化するためのサーバー プロセスを識別するだけでなく、クライアント ブラウザーの観点から分析することもできます。  これは、クライアント コンポーネントの潜在的な改善を識別し、複数の場所にあるさまざまなブラウザーの問題を特定するためにも役立ちます。

1. **[調査]** で **[ブラウザー]** を選択し、 **[ブラウザーのパフォーマンス]** をクリックするか、 **[調査]** で **[パフォーマンス]** を選択し、右上のサーバーとブラウザーのトグル ボタンをクリックして **[ブラウザー]** タブに切り替え、ブラウザーのパフォーマンス概要を開きます。 これにより、アプリケーションのさまざまなテレメトリをブラウザーの観点から見た概要が視覚的に表示されます。

    ![ブラウザーの概要](media/tutorial-performance/8-browser.png)

2. 操作名のいずれかを選択し、右下にある青いサンプル ボタンをクリックして、操作を選択します。 これにより、エンドツーエンドのトランザクションの詳細が表示され、右側に **[ページ ビュー プロパティ]** が表示されます。 これにより、ページを要求しているクライアントの詳細 (ブラウザーの種類、その場所など) を表示できます。 この情報は、特定の種類のクライアントに関連するパフォーマンスの問題があるかどうかを判断する際に役立ちます。

    ![ページ ビュー](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>クライアントのログ データを使用する
Application Insights では、サーバーのパフォーマンスのために収集されたデータと同様に、すべてのクライアント データを、Logs を使用した詳細な分析で使用できます。

1. ブラウザーの概要に戻り、![Logs アイコン](media/tutorial-performance/app-viewinlogs-icon.png) **[ログに表示 (Analytics)]** をクリックします。

2. Logs が開き、それぞれのビューのクエリがパネルに表示されます。 最初のクエリは、一定期間のさまざまなページ ビューの実行時間を示します。

    ![Logs クエリ](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>次のステップ
ここでは、ランタイムの例外を識別する方法を学習しました。次のチュートリアルに進んで、エラーに応答してアラートを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [アプリケーションの正常性に基づくアラート](./tutorial-alert.md)

