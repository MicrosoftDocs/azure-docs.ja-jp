---
title: "Azure Stream Analytics ツールを Visual Studio に使用する | Microsoft Docs"
description: "Visual Studio の Azure Stream Analytics ツールに関する入門チュートリアル"
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: samacha
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017

---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio の Azure Stream Analytics ツールの使用
## <a name="introduction"></a>はじめに
このチュートリアルでは、Visual Studio の Azure Stream Analytics ツールを使用して、Stream Analytics ジョブの作成、ローカルでのテスト、管理、デバッグを行う方法について説明します。 

このチュートリアルを読むと、次のことができるようになります。
* Visual Studio の Stream Analytics ツールについて理解を深める。
* Stream Analytics ジョブを構成、デプロイする。
* ローカル サンプル データを使用してジョブをローカルでテストする。
* 監視機能を使用して問題をトラブルシューティングする。
* 既存のジョブをプロジェクトにエクスポートする。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。
* 「[Stream Analytics を使って IoT ソリューションを構築する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)」の「Stream Analytics ジョブの作成」までの手順を完了します。 
* Visual Studio 2015、Visual Studio 2013 Update 4、または Visual Studio 2012 を使用します。 Enterprise (Ultimate/Premium)、Professional、Community の各エディションがサポートされています。 Express エディションはサポートされていません。 Visual Studio 2017 はサポートされていません。 
* Azure SDK for .NET バージョン 2.7.1 以降を使用します。 [Web プラットフォーム インストーラー](http://www.microsoft.com/web/downloads/platform.aspx)を使用してインストールします。
* [Visual Studio の Azure Stream Analytics ツール](http://aka.ms/asatoolsvs)をインストールします。

## <a name="create-a-stream-analytics-project"></a>Stream Analytics プロジェクトを作成する
1. Visual Studio で **[ファイル]** メニューをクリックし、**[新しいプロジェクト]** を選択します。 

2. 左側のテンプレートの一覧で **[Stream Analytics]** を選択し、**[Azure Stream Analytics アプリケーション]** をクリックします。

3. 他のプロジェクトの場合と同様に、プロジェクトの**名前**、**場所**、**ソリューション名**を入力します。

    ![[新しいプロジェクト] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    **ソリューション エクスプローラー**に **Toll** プロジェクトが生成されます。

    ![ソリューション エクスプローラーに生成された Toll プロジェクト](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>適切なサブスクリプションを選択する
1. Visual Studio で **[表示]** メニューをクリックし、**[サーバー エクスプローラー]** を開きます。

2. Azure アカウントでサインインします。 

## <a name="define-the-input-sources"></a>入力ソースを定義する
1.  **ソリューション エクスプローラー**で **[入力]** ノードを展開し、**Input.json** の名前を **EntryStream.json** に変更します。 **[EntryStream.json]** をダブルクリックします。
2.  **[入力のエイリアス]** に **[EntryStream]** と表示されます。 入力のエイリアスはクエリ スクリプトで使用されます。 
3.  **[ソースの種類]** で **[データ ストリーム]** を選択します。
4.  **[ソース]** で **[イベント ハブ]** を選択します。
5.  **[Service Bus 名前空間]** で、**TollData** オプションを選択します。
6.  **[イベント ハブ名]** で **[entry]** を選択します。
7.  **[イベント ハブ ポリシー名]** で、**[RootManageSharedAccessKey]** (既定値) を選択します。
8.  **[イベントのシリアル化の形式]** で、**[Json]** を選択します。 
9.  **[エンコード]** で **[UTF8]** を選択します。 設定は次のスクリーンショットのようになります。

    ![[入力] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. **[保存]** をクリックして、ウィザードを終了します。 入力ソースをもう 1 つ追加して、終了ストリームを作成できます。 **[入力]** ノードを右クリックし、**[新しい項目]** を選択します。

    ![New Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. ウィンドウで **[Azure Stream Analytics Input]\(Azure Stream Analytics の入力\)** を選択し、**[名前]** を **ExitStream.json** に変更します。 **[追加]**をクリックします。

    ![[新しい項目の追加] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. プロジェクトで **[ExitStream.json]** をダブルクリックし、Entry ストリームの場合と同じ手順に従います。 次のスクリーンショットに示すように、**[イベント ハブ名]** には「**exit**」と入力する必要があります。

    ![[ExitStream] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    これで、2 つの入力ストリームが定義されました。

    ![Entry および Exit 入力ストリーム](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    次に、車両登録データが含まれた BLOB ファイルの参照データ入力を追加します。

13. プロジェクトで **[入力]** ノードを右クリックし、ストリーム入力の場合と同じ手順に従います。 **[入力のエイリアス]** に「**Registration**」と入力し、**[ソースの種類]** で **[参照データ]**を選択します。

    ![[登録] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. **[ストレージ アカウント]** で、**tolldata** オプションを選択します。 **[コンテナー]** で **[tolldata]** を選択し、**[パス パターン]** に「**registration.json**」と入力します。 このファイル名は大文字と小文字が区別されるため、小文字で入力してください。
15. **[保存]** をクリックして、ウィザードを終了します。

これで、すべての入力が定義されました。

## <a name="define-the-output"></a>出力を定義する
1.  **ソリューション エクスプローラー**で **[入力]** ノードを展開し、**[Output.json]** をダブルクリックします。

2.  **[出力のエイリアス]** に「**output**」と入力します。 
3.  **[シンク]** で **[SQL Database]** を選択します。
4.  **[データベース]** で **[TollDataDB]** を選択します。
5.  **[ユーザー名]**に「**tolladmin**」と入力します。 
6.  **[パスワード]** に「**123toll!**」と入力します。
7.  **[テーブル]** に「**TollDataRefJoin**」と入力します。
8.  [ **Save**] をクリックします。

    ![[出力] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>Stream Analytics クエリを作成する
このチュートリアルでは、通行料金データに関するビジネス上の質問に答えます。 また、適切な答えを得るために、Stream Analytics で使用できる Stream Analytics クエリを作成します。
初めての Stream Analytics ジョブを開始する前に、簡単なシナリオとクエリ構文を確認しましょう。

### <a name="introduction-to-the-stream-analytics-query-language"></a>Stream Analytics クエリ言語の概要
料金所ブースに入る車両の台数をカウントしなければならなくなったとします。 この例は絶えず発生するイベントであるため、期間を定義する必要があります。 そこで質問を変更し、"3 分ごとに料金所に入る車両の台数" を調べることにします。 このデータ カウント方法は、一般に "タンブリング カウント" と呼ばれます。

この質問に答える Stream Analytics クエリを見てみましょう。

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Stream Analytics では SQL に似たクエリ言語が使用され、時間に関連したクエリ要素を指定するための拡張機能がいくつか追加されています。

詳細については、クエリで使用される[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)と[時間枠](https://msdn.microsoft.com/library/azure/dn835019.aspx)の概念に関する MSDN の記事をご覧ください。

初めての Stream Analytics クエリを作成したら、クエリをテストします。 TollApp フォルダーの次のパスにあるサンプル データ ファイルを使用します。

..\TollApp\TollApp\Data

このフォルダーには次のファイルが格納されています。
*   Entry.json
*   Exit.json
*   registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>料金所に入る車両の台数をカウントする
プロジェクトで **Script.asaql** をダブルクリックして、**クエリ エディター**でスクリプトを開きます。 前のセクションのスクリプトをコピーし、エディターに貼り付けます。 クエリ エディターでは、IntelliSense、構文の色分け、エラー マーカーをサポートしています。

![クエリ エディター](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Stream Analytics クエリをローカルでテストする

1. クエリをコンパイルして構文エラーがないかどうかを確認するには、プロジェクトを右クリックし、**[ビルド]** を選択します。 

2. サンプル データに対してこのクエリを検証するために、ローカル サンプル データを使用できます。 入力を右クリックし、**[ローカル入力の追加]** を選択します。

    ![ローカル入力の追加](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. ポップアップ ウィンドウで、ローカル パスからサンプル データを選択します。 [ **Save**] をクリックします。

    ![[ローカル入力の追加] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    **local_EntryStream.json** という名前のファイルが、入力フォルダーに自動的に追加されます。

    ![入力フォルダーに追加されたファイル](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. **クエリ エディター**で、**[ローカルで実行]** をクリックします。 または F5 キーを押します。

    ![[ローカルで実行]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![ローカルの実行の出力](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    任意のキーを押すと、Visual Studio の **[ASA ローカル実行の結果]** ウィンドウに出力が表示されます。 

    ![[ASA ローカル実行の結果] ウィンドウ](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. **[結果フォルダーを開く]** をクリックすると、出力ファイルを CSV 形式と JSON 形式の両方で確認できます。

    ![[結果フォルダーを開く] の出力](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>入力データをサンプリングする
入力ソースからローカル ファイルに入力データをサンプリングすることもできます。 
1. 入力構成ファイルを右クリックし、**[サンプル データ]** を選択します。 

   ![サンプル データ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    現時点では、サンプリングできるのはイベント ハブまたは IoT Hub だけです。 他の入力ソースはサポートされていません。

2. ポップアップ ウィンドウで、サンプル データの保存に使用するローカル パスを入力します。 **[サンプル]** をクリックします。

    ![[サンプル データ] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    **[出力]** ウィンドウで進行状況を確認できます。 

    ![[出力] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Stream Analytics クエリを Azure に送信する
1. **クエリ エディター**で、スクリプト エディターの **[Azure に送信]** をクリックします。

    ![[Azure に送信]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. **[新しい Azure Stream Analytics ジョブを作成する]** を選択します。 **ジョブ名**を入力し、適切な**サブスクリプション**を選択します。 **[Submit]**をクリックします。

    ![[ジョブの送信] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>ジョブを開始する
ジョブが作成されたら、ジョブ ビューが自動的に開きます。 
1. ジョブを開始するには、**緑色の矢印**ボタンをクリックします。

    ![ジョブを開始する](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 既定の設定を選択し、**[開始]** をクリックします。
 
    ![[ジョブの開始] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    ジョブの **[状態]** が **[実行中]** に変わり、**入力イベント**と**出力イベント**が表示されます。

    ![[ジョブの概要] の [状態] - [実行中]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>Visual Studio で結果を確認する
1. Visual Studio で**サーバー エクスプローラー**を開き、**TollDataRefJoin** テーブルを右クリックします。
2. **[テーブル データの表示]** を選択して、ジョブの出力を確認します。
   
    ![サーバー エクスプローラーの [テーブル データの表示] の選択](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>ジョブ メトリックを表示する
一部の基本的なジョブの統計情報は、**ジョブのメトリックス**で見ることができます。 

![[ジョブ メトリック] ウィンドウ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>サーバー エクスプローラーでジョブを表示する
**サーバー エクスプローラー**で **[Stream Analytics ジョブ]** をクリックし、**[更新]** をクリックします。 **[Stream Analytics ジョブ]** の下にジョブが表示されます。

![サーバー エクスプローラーに表示された Stream Analytics ジョブ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>ジョブ ビューを開く
ジョブ ビューを開くには、ジョブ ノードを展開し、**[ジョブ ビュー]** ノードをダブルクリックします。

![[ジョブ ビュー] ノード](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>既存のジョブをプロジェクトにエクスポートする
既存のジョブをプロジェクトにエクスポートする方法は 2 つあります。

**サーバー エクスプローラー**で、**[Stream Analytics ジョブ]** ノードのジョブ ノードを右クリックし、**[新しい Stream Analytics プロジェクトにエクスポート]** を選択します。

![[新しい Stream Analytics プロジェクトにエクスポート]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

**ソリューション エクスプローラー**にプロジェクトが生成されます。

![ソリューション エクスプローラーに生成されたプロジェクト](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
ジョブ ビューを使用することもできます。**[プロジェクトの生成]** をクリックします。

![[プロジェクトの生成]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>既知の問題と制限
 
- Power BI 出力と Azure Data Lake Store 出力はサポートされていません。
- JavaScript ユーザー定義関数の追加や変更はエディターでサポートされていません。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

