---
title: Azure Application Insights でカスタム ダッシュボードを作成する | Microsoft Docs
description: Azure Application Insights を使ってカスタム KPI ダッシュボードを作成するチュートリアルです。
keywords: ''
services: application-insights
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: fa91933c2243cd1a2737f93f796314330b6cf5a0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541499"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Azure Application Insights を使ってカスタム KPI ダッシュボードを作成する

Azure Portal で複数のダッシュボードを作成し、各ダッシュボードには、さまざまなリソース グループとサブスクリプションに及ぶ複数の Azure リソースからのデータを視覚化したタイルを含めることができます。  Azure Application Insights からさまざまなグラフやビューをピン留めして、お使いのアプリケーションの正常性やパフォーマンスの全体像が把握できるカスタム ダッシュボードを作成できます。 このチュートリアルでは、Azure Application Insights の複数のデータや視覚エフェクトを含むカスタム ダッシュボードの作成について説明します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure でカスタム ダッシュボードを作成する
> * タイル ギャラリーからタイルを追加する
> * Application Insights でダッシュボードに標準メトリックを追加する
> * Application Insights でダッシュボードにカスタム メトリック グラフを追加する
> * Logs (Analytics) クエリの結果をダッシュボードに追加する



## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- .NET アプリケーションを Azure にデプロイし、[Application Insights SDK の有効化](../../azure-monitor/app/asp-net.md)を実行します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-new-dashboard"></a>新しいダッシュボードを作成する
単一のダッシュボードには、複数のアプリケーション、リソース グループ、サブスクリプションからのリソースを含めることができます。  お使いのアプリケーションの新しいダッシュボードを作成して、チュートリアルを開始します。  

1. ダッシュボード ウィンドウで、 **[新しいダッシュボード]** を選択します。

   ![新しいダッシュボード](media/tutorial-app-dashboards/1newdashboard.png)

1. ダッシュボードの名前を入力します。
1. ダッシュボードに追加できるさまざまなタイルを**タイル ギャラリー**で確認します。  ギャラリーからタイルを追加するだけでなく、グラフおよびその他のビューを、Application Insights からダッシュボードに直接ピン留めできます。
1. **マークダウン** タイルを見つけて、ダッシュボードにドラッグします。  このタイルには、お使いのダッシュボードへのわかりやすいテキストの追加に最適な、マークダウン記法のテキストを追加できます。
1. タイルのプロパティにテキストを追加し、ダッシュボード キャンバス上でサイズを変更します。
    
    ![マークダウン タイルを編集する](media/tutorial-app-dashboards/2dashboard-text.png)

1. 画面上部にある **[カスタマイズ完了]** をクリックして、タイルのカスタマイズ モードを終了します。

## <a name="add-health-overview"></a>正常性の概要を追加する
静的テキストのダッシュボードでは味気ないため、ここで Application Insights からタイルを追加して、お使いのアプリケーションの情報を表示させましょう。  Application Insights タイルはタイル ギャラリーから追加するか、Application Insights 画面から直接ピン留めできます。  これにより、既に使い慣れているグラフやビューを、ご自分のダッシュボードにピン留めする前に構成できます。  まず、お使いのアプリケーションの標準的な正常性の概要を追加します。  これには構成が不要で、ダッシュボードのカスタマイズを最小限に抑えられます。


1. ホーム画面で対象の **Application Insights** リソースを選択します。
2. **[概要]** ウィンドウで、ピン留めアイコン ![ピン留めアイコン](media/tutorial-app-dashboards/pushpin.png) をクリックして、最後に表示していたダッシュボードにタイルを追加します。  
 
3. タイルがダッシュボードにピン留めされたことを示す通知が右上に表示されます。 通知内の **[ダッシュボードにピン留めしました]** をクリックしてダッシュボードに戻るか、ダッシュボード ウィンドウを使用します。
4. これで、タイルがダッシュボードに追加されます。 タイルの位置を変更するには、 **[編集]** を選択します。 タイルをクリックして目的の位置にドラッグした後、 **[カスタマイズ完了]** をクリックします。 これで、有用な情報が表示されたタイルをダッシュボードで確認できるようになりました。

    ![概要タイムラインが表示されたダッシュボード](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>カスタム メトリック グラフを追加する
**[メトリック]** パネルでは、オプションのフィルターおよびグループ化によって、時間の経過と共に Application Insights によって収集されるメトリックをグラフ化できます。  Application Insights のその他すべてのものと同じように、ダッシュボードにこのグラフを追加できます。  これにはまず、少しのカスタマイズが必要です。

1. ホーム画面で対象の **Application Insights** リソースを選択します。
1. **[メトリック]** を選びます。  
2. 空のグラフが既に作成されており、メトリックを追加するように求められます。  グラフにメトリックを追加し、必要に応じてフィルターやグループ化を追加します。  次の例には、成功したかどうかでグループ化したサーバー要求の数が表示されています。  成功または失敗した要求を、実行中のビューで確認できます。

    ![メトリックを追加する](media/tutorial-app-dashboards/metrics.png)

4. 右側の **[ダッシュボードにピン留めする]** を選択します。 表示していた最後のダッシュボードにビューが追加されます。

3.  タイルがダッシュボードにピン留めされたことを示す通知が右上に表示されます。 通知内の **[ダッシュボードにピン留めしました]** をクリックしてダッシュボードに戻るか、ダッシュボード ブレードを使用します。

4. これで、タイルがダッシュボードに追加されます。 タイルの位置を変更するには、 **[編集]** を選択します。 タイルをクリックして目的の位置にドラッグした後、 **[カスタマイズ完了]** をクリックします。

## <a name="add-logs-analytics-query"></a>Logs (Analytics) クエリの追加
Azure Application Insights の Logs (Analytics) には、Application Insights によって収集されたすべてのデータを分析できる、高度なクエリ言語が備わっています。 グラフやその他のビューと同じように、ダッシュボードにログ クエリの出力を追加できます。

Azure Application Insights の Logs (Analytics) は独立したサービスであるため、ログ クエリを含めるには、ダッシュボードを共有する必要があります。 Azure のダッシュボードを共有する場合は、他のユーザーやリソースが利用できる Azure リソースとして発行します。  

1. ダッシュボード画面のトップで、 **[共有]** をクリックします。

    ![ダッシュボードの発行](media/tutorial-app-dashboards/8dashboard-share.png)

2. **ダッシュボード名**を同じ名前にして、**サブスクリプション名**を選び、ダッシュボードを共有します。  **[発行]** をクリックします。  これで、ほかのサービスとサブスクリプションでダッシュボードを利用できるようになりました。  必要に応じて、ダッシュボードにアクセス権を持つ特定のユーザーを定義できます。
1. ホーム画面で対象の **Application Insights** リソースを選択します。
2. 左側の [監視] の下の **[Logs (Analytics)]** をクリックして、Logs (Analytics) ポータルを開きます。
3. 次のクエリを入力すると、最も多く要求された上位 10 ページと要求の数を返します。

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. クエリの結果を検証するために、 **[実行]** をクリックします。
5. ピン留めアイコン ![ピン留めアイコン](media/tutorial-app-dashboards/pushpin.png) をクリックし、ダッシュボードの名前を選択します。 最後のダッシュボードが使用された前の手順とは異なり、このオプションでダッシュボードを選ぶ理由は、Logs (Analytics) コンソールが独立したサービスであり、使用可能なすべての共有ダッシュボードから選ぶ必要があるためです。

5. ダッシュボードに戻る前に別のクエリを追加しますが、今回はグラフとしてレンダリングして、ログ クエリをさまざまな方法でダッシュボードに視覚化できることを確認します。 次のクエリで開始します。このクエリでは、上位 10 個の操作をまとめて、ほとんどは除外しています。

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. **[グラフ]** を選び、その後 **[ドーナツ]** に変更して出力を視覚化します。

    ![Logs (Analytics) グラフ](media/tutorial-app-dashboards/11querychart.png)

6. 右上のピン留めアイコン ![ピン留めアイコン](media/tutorial-app-dashboards/pushpin.png) をクリックして、グラフをダッシュボードにピン留めし、今回はダッシュボードに戻るリンクを選択します。
4. 選択されたフォーマットで、クエリの結果がダッシュボードに追加されました。  それぞれをクリックして適切な位置にドラッグし、 **[カスタマイズ完了]** をクリックします。
5. 各タイトルで鉛筆アイコン ![鉛筆アイコン](media/tutorial-app-dashboards/pencil.png) を選択して、わかりやすいタイトルを付けます。

5. **[共有]** を選択して、変更をダッシュボードに再発行します。これで、ダッシュボードに Application Insights のさまざまなグラフや視覚エフェクトが含まれます。


## <a name="next-steps"></a>次の手順
これでカスタム ダッシュボードを作成する方法の説明は終了しましたが、ケース スタディを含む Application Insights のドキュメントの残りの部分もご確認ください。

> [!div class="nextstepaction"]
> [詳細な診断](../../azure-monitor/app/devops.md)
