---
title: "Azure Application Insights でカスタム ダッシュボードを作成する | Microsoft Docs"
description: "Azure Application Insights を使ってカスタム KPI ダッシュボードを作成するチュートリアルです。"
keywords: 
services: application-insights
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 3ad1bfe2c45fd087f094aad5b2028ab488dbb2a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Azure Application Insights を使ってカスタム KPI ダッシュボードを作成する

Azure Portal で複数のダッシュボードを作成し、各ダッシュボードには、さまざまなリソース グループとサブスクリプションに及ぶ複数の Azure リソースからのデータを視覚化したタイルを含めることができます。  Azure Application Insights からさまざまなグラフやビューをピン留めして、お使いのアプリケーションの正常性やパフォーマンスの全体像が把握できるカスタム ダッシュボードを作成できます。  このチュートリアルでは、Azure Application Insights の複数のデータや視覚エフェクトを含むカスタム ダッシュボードの作成について説明します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure でカスタム ダッシュボードを作成する
> * タイル ギャラリーからタイルを追加する
> * Application Insights でダッシュボードに標準メトリックを追加する 
> * Application Insights でダッシュボードにカスタム メトリック グラフを追加する
> * Analytics クエリの結果をダッシュボードに追加する 



## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- .NET アプリケーションを Azure にデプロイし、[Application Insights SDK の有効化](app-insights-asp-net.md)を実行します。 

## <a name="log-in-to-azure"></a>Azure へのログイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。

## <a name="create-a-new-dashboard"></a>新しいダッシュボードを作成する
単一のダッシュボードには、複数のアプリケーション、リソース グループ、サブスクリプションからのリソースを含めることができます。  お使いのアプリケーションの新しいダッシュボードを作成して、チュートリアルを開始します。  

2.  ポータルのメイン画面で、**新しいダッシュボード**を選びます。

    ![新しいダッシュボード](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. ダッシュボードの名前を入力します。
4. ダッシュボードに追加できるさまざまなタイルを**タイル ギャラリー**で確認します。  ギャラリーからタイルを追加するだけでなく、グラフおよびその他のビューを、Application Insights からダッシュボードに直接ピン留めできます。
5. **マークダウン** タイルを見つけて、ダッシュボードにドラッグします。  このタイルには、お使いのダッシュボードへのわかりやすいテキストの追加に最適な、マークダウン記法のテキストを追加できます。
6. タイルのプロパティにテキストを追加し、ダッシュボード キャンバス上でサイズを変更します。
    
    ![マークダウン タイルを編集する](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. 画面上部にある **[カスタマイズ完了]** をクリックしてタイルのカスタマイズ モードを終了し、その後 **[変更の発行]** をクリックして変更を保存します。

    ![マークダウン タイルを配置したダッシュボード](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>正常性の概要を追加する
静的テキストだけのダッシュボードでは味気ないため、ここで Application Insights からタイルを追加して、お使いのアプリケーションの情報を表示させましょう。  Application Insights タイルはタイル ギャラリーから追加するか、Application Insights 画面から直接ピン留めできます。  これにより、既に使い慣れているグラフやビューを、ご自分のダッシュボードにピン留めする前に構成できます。  まず、お使いのアプリケーションの標準的な正常性の概要を追加します。  これには構成が不要で、ダッシュボードのカスタマイズを最小限に抑えられます。


1. Azure メニューで **[Application Insights]** を選び、その後お使いのアプリケーションを選びます。
2. **概要タイムライン**でコンテキスト メニューを選び、**[ダッシュボードにピン留めする]** をクリックします。  これで、表示されていた最後のダッシュボードにタイルを追加します。  

    ![概要タイムラインをピン留めする](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. 画面のトップで **[ダッシュボードの表示]** をクリックしてダッシュボードに戻ります。
4. これで概要タイムラインがダッシュボードに追加されました。  これをクリックして適切な位置にドラッグし、その後**[カスタマイズ完了]** と **[変更の発行]** をクリックします。  これで、有用な情報が表示されたタイルをダッシュボードで確認できるようになりました。

    ![概要タイムラインが表示されたダッシュボード](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>カスタム メトリック グラフを追加する
**[メトリック]** パネルでは、オプションのフィルターおよびグループ化によって、時間の経過と共に Application Insights によって収集されるメトリックをグラフ化できます。  Application Insights のその他すべてのものと同じように、ダッシュボードにこのグラフを追加できます。  これにはまず、少しのカスタマイズが必要です。

1. Azure メニューで **[Application Insights]** を選び、その後お使いのアプリケーションを選びます。
1. **[メトリック]** を選びます。  
2. 空のグラフが既に作成されており、メトリックを追加するように求められます。  グラフにメトリックを追加し、必要に応じてフィルターやグループ化を追加します。  次の例には、成功したかどうかでグループ化したサーバー要求の数が表示されています。  成功または失敗した要求を、実行中のビューで確認できます。

    ![メトリックを追加する](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. グラフのコンテキスト メニューを選び、**[ダッシュボードにピン留めする]** を選びます。  表示していた最後のダッシュボードにビューが追加されます。

    ![メトリック グラフをピン留めする](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. 画面のトップで **[ダッシュボードの表示]** をクリックしてダッシュボードに戻ります。

4. これで、概要タイムラインがダッシュボードに追加されました。  これをクリックして適切な位置にドラッグし、**[カスタマイズ完了]**、**[変更の発行]** の順にクリックします。 

    ![メトリックが表示されたダッシュボード](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>メトリックス エクスプローラー
**メトリックス エクスプローラー**はメトリックに似ていますが、ダッシュボードに追加すると非常に多くのカスタマイズが可能になります。  メトリックをグラフ化するのにどれを使うかは、特定の基本設定や要件によって異なります。

1. Azure メニューで **[Application Insights]** を選び、その後お使いのアプリケーションを選びます。
1. **[メトリックス エクスプローラー]** を選びます。 
2. クリックしてグラフを編集し、1 つ以上のメトリックと、必要に応じて詳細な構成を選びます。  次の例は、平均応答時間を追跡した折れ線グラフを示しています。
3. 上部右にあるピン アイコンをクリックしてダッシュボードにグラフを追加し、適切な位置にドラッグします。

    ![メトリックス エクスプローラー](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. メトリックス エクスプローラーのタイルを 1 度ダッシュボードに追加すれば、より多くのカスタマイズができます。  タイルを右クリックして **[タイﾄルの編集]** を選び、カスタム タイトルを追加します。  必要に応じて、さらにその他のカスタマイズを行います。

    ![メトリックス エクスプローラーつきのダッシュボード](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. これでメトリックス エクスプローラー のグラフがダッシュボードに追加されました。

    ![メトリックス エクスプローラーつきのダッシュボード](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Analytics クエリの追加
Azure Application Insights の Analytics は、Application Insights によって収集されたすべてのデータを分析できる、高度なクエリ言語を備えています。  グラフやその他のビューと同じように、ダッシュボードに Analytics クエリの出力を追加できます。   

Azure Application Insights の Analytics は独立したサービスであるため、 ダッシュボードを共有して Analytics クエリを含める必要があります。  Azure のダッシュボードを共有する場合は、他のユーザーやリソースが利用できる Azure リソースとして発行します。  

1. ダッシュボード画面のトップで、**[共有]** をクリックします。

    ![ダッシュボードの発行](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. **ダッシュボード名**を同じ名前にして、**サブスクリプション名**を選び、ダッシュボードを共有します。  **[発行]**をクリックします。  これで、ほかのサービスとサブスクリプションでダッシュボードを利用できるようになりました。  必要に応じて、ダッシュボードにアクセス権を持つ特定のユーザーを定義できます。
1. Azure メニューで **[Application Insights]** を選び、その後お使いのアプリケーションを選びます。
2. 画面トップにある **[分析]** をクリックして Analytics ポータルを開きます。

    ![Analytics を開始する](media/app-insights-tutorial-dashboards/start-analytics.png)

3. 次のクエリを入力すると、最も多く要求された上位 10 ページと要求の数を返します。

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. **[移動]** をクリックしてして、クエリの結果を検証します。
5. ピン アイコンをクリックして、ダッシュボードの名前を選択します。  最後のダッシュボードが使用された以前の手順とは異なり、このオプションでダッシュボードを選ぶ理由は、Analytics コンソールが独立したサービスであり、使用可能なすべての共有ダッシュボードから選ぶ必要があるためです。

    ![Analytics クエリをピン留めする](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. ダッシュボードに戻る前に別のクエリを追加しますが、今回はグラフとしてレンダリングして、ダッシュボードで Analytics クエリを視覚化するさまざまな方法が表示されるようにします。  次のクエリで開始します。このクエリでは、上位 10 個の操作をまとめて、ほとんどは除外しています。

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. **[グラフ]** を選び、その後 **[ドーナツ]** に変更して出力を視覚化します。

    ![Analytics グラフ](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. ピン アイコンをクリックしてグラフをダッシュボードにピン留めし、今回はリンクを選んでダッシュボードに戻ります。
4. 選択されたフォーマットで、クエリの結果がダッシュボードに追加されました。  クリックしてそれぞれを適切な位置にドラッグし、**[編集完了]** をクリックします。
5. 各タイルを右クリックして **[タイトルの編集]** を選び、わかりやすいタイトルをつけます。

    ![Analytics が表示されたダッシュボード](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. **[変更の発行]** をクリックして、Application Insights のさまざまなグラフや視覚エフェクトを含んだダッシュボードに変更をコミットします。


## <a name="next-steps"></a>次のステップ
これでカスタム ダッシュボードを作成する方法の説明は終了しましたが、ケース スタディを含む Application Insights のドキュメントの残りの部分もご確認ください。

> [!div class="nextstepaction"]
> [詳細な診断](app-insights-devops.md)