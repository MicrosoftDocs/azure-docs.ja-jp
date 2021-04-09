---
title: Azure Application Insights でカスタム ダッシュボードを作成する | Microsoft Docs
description: Azure Application Insights を使ってカスタム KPI ダッシュボードを作成するチュートリアルです。
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 701480300101b019830c57d9aa000534fa63bb6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100597666"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Azure Application Insights を使ってカスタム KPI ダッシュボードを作成する

Azure Portal で複数のダッシュボードを作成し、各ダッシュボードには、さまざまなリソース グループとサブスクリプションに及ぶ複数の Azure リソースからのデータを視覚化したタイルを含めることができます。  Azure Application Insights からさまざまなグラフやビューをピン留めして、お使いのアプリケーションの正常性やパフォーマンスの全体像が把握できるカスタム ダッシュボードを作成できます。 このチュートリアルでは、Azure Application Insights の複数のデータや視覚エフェクトを含むカスタム ダッシュボードの作成について説明します。

 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure でカスタム ダッシュボードを作成する
> * タイル ギャラリーからタイルを追加する
> * Application Insights でダッシュボードに標準メトリックを追加する
> * Application Insights でダッシュボードにカスタム メトリック グラフを追加する
> * Logs (Analytics) クエリの結果をダッシュボードに追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- .NET アプリケーションを Azure にデプロイし、[Application Insights SDK の有効化](../app/asp-net.md)を実行します。

> [!NOTE]
> ダッシュボードを操作するために必要なアクセス許可については、「[ダッシュボードのアクセス制御について](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards)」を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-new-dashboard"></a>新しいダッシュボードを作成する

> [!WARNING]
> Application Insights リソースを別のリソース グループまたはサブスクリプションに移動する場合は、古いタイルを削除し、同じ Application Insights リソースから新しいタイルを新しい場所にピン留めすることによって、ダッシュボードを手動で更新する必要があります。

単一のダッシュボードには、複数のアプリケーション、リソース グループ、サブスクリプションからのリソースを含めることができます。  お使いのアプリケーションの新しいダッシュボードを作成して、チュートリアルを開始します。  

1. Azure portal の左側にあるメニューのドロップダウンから **[ダッシュボード]** を選択します。

    ![Azure portal メニューのドロップダウン](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. ダッシュボード ペインで、 **[新しいダッシュボード]** 、 **[空のダッシュボード]** の順に選択します。

   ![新しいダッシュボード](media/tutorial-app-dashboards/new-dashboard.png)

3. ダッシュボードの名前を入力します。
4. ダッシュボードに追加できるさまざまなタイルを **タイル ギャラリー** で確認します。  ギャラリーからタイルを追加するだけでなく、グラフおよびその他のビューを、Application Insights からダッシュボードに直接ピン留めできます。
5. **マークダウン** タイルを見つけて、ダッシュボードにドラッグします。  このタイルには、お使いのダッシュボードへのわかりやすいテキストの追加に最適な、マークダウン記法のテキストを追加できます。 詳細については、「[Azure ダッシュボードでマークダウン タイルを使用して、カスタム コンテンツを表示する](../../azure-portal/azure-portal-markdown-tile.md)」を参照してください。
6. タイルのプロパティにテキストを追加し、ダッシュボード キャンバス上でサイズを変更します。

    [![マークダウン タイルを編集する](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. 画面上部にある **[カスタマイズ完了]** を選択して、タイルのカスタマイズ モードを終了します。

## <a name="add-health-overview"></a>正常性の概要を追加する

静的テキストのダッシュボードでは味気ないため、ここで Application Insights からタイルを追加して、お使いのアプリケーションの情報を表示させましょう。 Application Insights タイルはタイル ギャラリーから追加するか、Application Insights 画面から直接ピン留めできます。 これにより、既に使い慣れているグラフやビューを、ご自分のダッシュボードにピン留めする前に構成できます。  まず、お使いのアプリケーションの標準的な正常性の概要を追加します。  これには構成が不要で、ダッシュボードのカスタマイズを最小限に抑えられます。


1. ホーム画面で対象の **Application Insights** リソースを選択します。
2. **[概要]** ペインで、ピン留めアイコン ![ピン留めアイコン](media/tutorial-app-dashboards/pushpin.png) を選択して、ダッシュボードにタイルを追加します。
3. [ダッシュボードにピン留め] タブで、タイルの追加先となるダッシュボードを選択するか、新たに作成します。
 
3. タイルがダッシュボードにピン留めされたことを示す通知が右上に表示されます。  通知内の **[ダッシュボードにピン留めしました]** を選択してダッシュボードに戻るか、ダッシュボード ペインを使用します。
4. これで、タイルがダッシュボードに追加されます。 タイルの位置を変更するには、 **[編集]** を選択します。 タイルを選択して目的の位置にドラッグした後、 **[カスタマイズ完了]** を選択します。 これで、有用な情報が表示されたタイルをダッシュボードで確認できるようになりました。

    [![編集モードのダッシュボード](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>カスタム メトリック グラフを追加する

**[メトリック]** パネルでは、オプションのフィルターおよびグループ化によって、時間の経過と共に Application Insights によって収集されるメトリックをグラフ化できます。  Application Insights のその他すべてのものと同じように、ダッシュボードにこのグラフを追加できます。  これにはまず、少しのカスタマイズが必要です。

1. ホーム画面で対象の **Application Insights** リソースを選択します。
1. **[メトリック]** を選びます。  
2. 空のグラフが既に作成されており、メトリックを追加するように求められます。  グラフにメトリックを追加し、必要に応じてフィルターやグループ化を追加します。  次の例には、成功したかどうかでグループ化したサーバー要求の数が表示されています。  成功または失敗した要求を、実行中のビューで確認できます。

    [![メトリックを追加する](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. 右側の **[ダッシュボードにピン留めする]** を選択します。

3.  タイルがダッシュボードにピン留めされたことを示す通知が右上に表示されます。 通知内の **[ダッシュボードにピン留めしました]** を選択してダッシュボードに戻るか、ダッシュボード タブを使用します。

4. これで、タイルがダッシュボードに追加されます。 タイルの位置を変更するには、 **[編集]** を選択します。 タイルを選択して目的の位置にドラッグした後、 **[カスタマイズ完了]** を選択します。

## <a name="add-logs-query"></a>Logs のクエリを追加する

Azure Application Insights の Logs には、Application Insights によって収集されたすべてのデータを分析できる、高度なクエリ言語が備わっています。 グラフやその他のビューと同じように、ダッシュボードにログ クエリの出力を追加できます。

1. ホーム画面で対象の **Application Insights** リソースを選択します。
2. 左側の [監査] にある **[Logs]** を選択して [Logs] タブを開きます。
3. 次のクエリを入力すると、最も多く要求された上位 10 ページと要求の数を返します。

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. クエリの結果を検証するために、 **[実行]** を選択します。
5. ピン留めアイコン ![ピン留めアイコン](media/tutorial-app-dashboards/pushpin.png) をクリックし、ダッシュボードの名前を選択します。

5. ダッシュボードに戻る前に別のクエリを追加しますが、グラフとしてレンダリングして、ログ クエリをさまざまな方法でダッシュボードに視覚化できることを確認します。 次のクエリで開始します。このクエリでは、上位 10 個の操作をまとめて、ほとんどは除外しています。

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. **[グラフ]** を選び、その後 **[ドーナツ]** に変更して出力を視覚化します。

    [![上下に並んだクエリとドーナツ グラフ](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. ピン留めアイコン ![ピン留めアイコン](media/tutorial-app-dashboards/pushpin.png) をクリックして、グラフをダッシュボードにピン留めし、ダッシュボードに戻ります。
7. 選択されたフォーマットで、クエリの結果がダッシュボードに追加されました。 それぞれを選択して目的の位置にドラッグした後、 **[カスタマイズ完了]** を選択します。
8. 各タイトルで鉛筆アイコン ![鉛筆アイコン](media/tutorial-app-dashboards/pencil.png) を選択して、わかりやすいタイトルを付けます。

## <a name="share-dashboard"></a>ダッシュボードの共有

1. ダッシュボードの上部にある **[共有]** を選択して、変更内容を発行します。
2. 必要に応じて、ダッシュボードにアクセス権を持つ特定のユーザーを定義できます。 詳細については、「[Azure ロールベースのアクセス制御を使用した Azure ダッシュボードの共有](../../azure-portal/azure-portal-dashboard-share-access.md)」を参照してください。
3. **[発行]** を選択します。

## <a name="next-steps"></a>次のステップ

これでカスタム ダッシュボードを作成する方法の説明は終了しましたが、ケース スタディを含む Application Insights のドキュメントの残りの部分もご確認ください。

> [!div class="nextstepaction"]
> [詳細な診断](../app/devops.md)
