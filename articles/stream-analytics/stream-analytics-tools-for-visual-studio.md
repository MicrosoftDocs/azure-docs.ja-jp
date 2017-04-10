---
title: "Azure Stream Analytics ツールを Visual Studio に使用する | Microsoft Docs"
description: "Visual Studio の Azure Stream Analytics ツールに関する入門チュートリアル"
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d0125dda4df69279e49a9fad4dc28dcbf6368322
ms.lasthandoff: 03/29/2017


---
# <a name="use-azure-stream-analytics-tool-for-visual-studio"></a>Azure Stream Analytics ツールを Visual Studio に使用する
Visual Studio の Azure Stream Analytics ツールが一般公開されます。 このツールを使用することで、Stream Analytics ユーザーのトラブルシューティング エクスペリエンスがさらに充実するとともに、複雑なクエリを記述したり、ローカルでクエリを記述することができます。 また、Stream Analytics ジョブを Visual Studio プロジェクトにエクスポートする機能も用意されています。

## <a name="introduction"></a>はじめに
このチュートリアルでは、Azure Stream Analytics ツールを Visual Studio に使用して、Azure Stream Analytics ジョブの作成、ローカルでのテスト、管理、およびデバッグを行う方法を説明します。 

このチュートリアルを読むと、次のことができるようになります。
* Visual Studio の Azure Stream Analytics ツールについて理解を深める。
* Stream Analytics ジョブを構成、デプロイする。
* ローカル サンプル データを使用してジョブをローカルでテストする。
* 監視機能を使用して問題をトラブルシューティングする。
* 既存のジョブをプロジェクトにエクスポートする。

## <a name="prerequisites"></a>前提条件
このチュートリアルの前提条件は次のとおりです。
* **Stream Analytics ジョブを作成**する前に、「[Stream Analytics を使って IoT ソリューションを構築する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)」に記載された手順を完了します。 
* Visual Studio 2015、Visual Studio 2013 Update 4、Visual Studio 2012 のいずれか。 サポートされるエディションは Enterprise (Ultimate/Premium)、Professional、Community です。Express エディションはサポートされません。 Visual Studio 2017 は現在、サポートされていません。 
* Microsoft Azure SDK for .NET バージョン 2.7.1 以上。  [Web Platform Installer を使用してインストールします](http://www.microsoft.com/web/downloads/platform.aspx)。
* [Visual Studio の Azure Stream Analytics ツール](http://aka.ms/asatoolsvs)のインストール。

## <a name="create-a-stream-analytics-project"></a>Stream Analytics プロジェクトの作成
Visual Studio で、**[ファイル] メニュー**をクリックし、**[新しいプロジェクト]** を選択します。 左のテンプレートの一覧から **[Stream Analytics]** を選択して **[Azure Stream Analytics アプリケーション]** をクリックします。
他のプロジェクトの場合と同じように、下にプロジェクト名、場所、およびソリューション名を入力します。

![Azure Stream Analytics プロジェクトの作成](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

**ソリューション エクスプローラー**に生成されたプロジェクトの**料金**が表示されます。

![Azure Stream Analytics プロジェクトの作成](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>適切なサブスクリプションの選択
1. Visual Studio の **[表示]** メニューで**サーバー エクスプローラー**を開きます。
2. Azure のアカウントを使用してログインします。 

## <a name="define-input-sources"></a>入力ソースの定義
1.    **ソリューション エクスプローラー**で**[入力]** ノードを展開し、**Input.json** の名前を **EntryStream.json** に変更します。 **[EntryStream.json]** をダブルクリックします。
2.    **[入力のエイリアス]** が **EntryStream** になりました。 この入力のエイリアスは、クエリ スクリプトで使用されることに注意してください。 
3.    [ソースの種類] は **[データ ストリーム]** です。
4.    [ソース] は **[イベント ハブ]** です。
5.    [Service Bus 名前空間] は、ドロップダウンにある **TollData** の名前空間を指定します。
6.    [イベント ハブの名前] は **entry** に設定する必要があります。
7.    [イベント ハブ ポリシー名] は **RootManageSharedAccessKey** です (既定値)。
8.    **[イベントのシリアル化の形式]** に **[JSON]** を、**[エンコード]** に **[UTF8]** を選択します。
   
   設定は次のようになります。
   
   ![入力ソースの定義](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9.    ページ下部の **[保存]** をクリックしてウィザードを終了します。 入力ソースをもう 1 つ追加して、終了ストリームを作成できます。 [入力] ノードを右クリックして、**[新しい項目]** をクリックします。
   
   ![入力ソースの定義](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10.    ポップアップ ウィンドウで **[Azure Stream Analytics Input]**(Azure Stream Analytics の入力) を選択し、名前を **ExitStream.json** に変更します。 **[追加]**をクリックします。
   
   ![入力ソースの定義](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11.    プロジェクトで **[ExitStream.json]** をダブルクリックし、入力ストリームと同じ手順に従って入力します。 次のスクリーンショットに従ってイベント ハブの名前の値を入力してください。
   
   ![入力ソースの定義](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   以下のように、2 つの入力ストリームを定義しました。
   
   ![入力ソースの定義](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   次に、車両登録データの BLOB ファイルに対する参照データ入力を追加します。
   
12.    プロジェクトの **[入力]** ノードを右クリックして、ストリーム入力と同じ手順に従います。ただし、[データ ストリーム] の代わりに **[参照データ]** を選択し、[入力のエイリアス] に **[登録]** を指定します。
   
   ![入力ソースの定義](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13.    [ストレージ アカウント] には、**tolldata** を含むものを選択します。 コンテナー名は **tolldata**、**[パス パターン]** は **registration.json** とする必要があります。 このファイル名は大文字と小文字が区別されるため、小文字で入力してください。
14.    **[保存]** をクリックしてウィザードを終了します。

これで、すべての入力が定義されました。

## <a name="define-output"></a>出力の定義
1.    **ソリューション エクスプローラー**で、**[入力]** ノードを展開して **[Output.json]** をダブルクリックします。
2.    [出力のエイリアス] を **[出力]** に設定し [シンク] を [SQL Database] に設定します。
2.    データベース名を **TollDataDB** と入力します。
3.    **[ユーザー名]** フィールドに「**tolladmin**」、**[パスワード]** フィールドに 「**123toll!**」、および **[テーブル]** フィールドに「**TollDataRefJoin**」と入力します。
4.    **[保存]**をクリックします。

![出力の定義](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Azure Stream Analytics クエリ
このチュートリアルでは、通行料金データに伴う実務上の課題を解決していきたいと思います。必要な答えを導くために、Azure Stream Analytics で使用できる Stream Analytics クエリを構築します。
初めての Stream Analytics ジョブを作成する前に、シンプルなシナリオとクエリ構文について詳しく見ていきましょう。

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure Stream Analytics クエリ言語について
料金所ブースに入る車両の台数をカウントしなければならなくなったとします。 絶えず発生するイベントであるため、"期間" の定義が必要となります。 そこで目的を少し変更し、"3 分ごとに料金所ブースに入る車両の台数" を調べることにします。 これを一般に "タンブリング カウント" といいます。

以下に示したのは、それを実現する Azure Stream Analytics クエリです。

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

ご覧のとおり、Azure Stream Analytics には SQL に似たクエリ言語が使用されており、さらに、時間に関連したクエリ要素を指定するための拡張機能がいくつか追加されています。

詳細については、クエリにおける[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)と[時間枠](https://msdn.microsoft.com/library/azure/dn835019.aspx)の概念についての記事を MSDN でご覧ください。

初めての Azure Stream Analytics クエリを作成したら、TollApp フォルダーの以下のパスに格納されているサンプル データ ファイルを使ってクエリをテストします。

<seg>
  **..\TollApp\TollApp\Data**</seg>

このフォルダには次のファイルが含まれています。 •    Entry.json •    Exit.json •    Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>テスト: 料金所ブースに入る車両の台数
プロジェクトで、[Script.asaql] をダブルクリックしてスクリプトをエディターで開き、前のセクションのスクリプトをエディターに貼り付けます。 クエリ エディターでは Intellisense、構文の色分け、およびエラーのマーカーがサポートされています。

![クエリの編集](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>Azure Stream Analytics クエリのローカルでのテスト

1. まずクエリをコンパイルして、構文エラーがないかを確認します。 [TBD]
2. サンプル データに対してこのクエリを検証するには、[入力] を右クリックしてローカル サンプル データを使用し、コンテキスト メニューから **[Add local input]**(ローカル入力の追加) を選択します。
   
   ![ローカル入力の追加](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
   ポップアップ ウィンドウで、ローカル パスからサンプル データを選択します。 **[保存]**をクリックします。
   
   ![ローカル入力の追加](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   **local_EntryStream.json** の名前のファイルが、入力フォルダに自動的に追加されます。
   
   ![ローカル入力の追加](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. クエリ エディターで [ローカルで実行する] をクリックしてください。 または F5 キーを押します。
   
   ![ローカルの実行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   コンソール出力から出力パスを検索し、いずれかのキーを押して結果フォルダーを開きます。
   
   ![ローカルの実行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. ローカル フォルダーで結果を確認します。
   
   ![ローカルの実行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>サンプル入力
また、入力ソースからの入力データをローカル ファイルにサンプリングできます。 [入力構成ファイル] を右クリックして、**[サンプル データ]** を選択します。 

![サンプル データ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

現在サンプリングできるのは、イベント ハブまたは IoT ハブのみであることに注意してください。 他の入力ソースはサポートされていません。  ポップアップ ダイアログ ウィンドウで、サンプル データを保存するためのローカル パスを入力します。 **[サンプル]** をクリックします。

![サンプル データ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
進捗状況が出力ウィンドウに表示されます。 

![サンプル データ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>Azure Stream Analytics クエリの Azure への送信
**クエリ エディター**で、**[Submit To Azure in script editor]**(スクリプト エディターで Azure に送信する) をクリックします。

![ジョブの送信](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
[新しい Stream Analytics ジョブの作成] を選択します。 ジョブ名を次のように入力します。 適切なサブスクリプションを選択します。 [Submit] をクリックします。

![ジョブの送信](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-job"></a>ジョブの開始
ジョブが作成されて、ジョブ ビューが自動的に開きます。 **緑の**ボタンをクリックしてジョブを開始します。

![ジョブの開始](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
既定の設定を選択して **[開始]** をクリックします。
 
![ジョブの開始](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

ジョブの状態が **[実行中]** に変更されて、入力/出力イベントが表示されます。

![ジョブの開始](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Visual Studio での結果の確認
1. Visual Studio のサーバー エクスプローラーを開いて **TollDataRefJoin** テーブルを右クリックします。
2. **[テーブル データの表示]** をクリックすると、ジョブの出力結果が表示されます。
   
   ![Selection of "Show Table Data" in Server Explorer](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>ジョブのメトリックスの表示
一部の基本的なジョブの統計情報は、**ジョブのメトリックス**で見ることができます。 

![ジョブのメトリックス](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-job-in-server-explorer"></a>サーバー エクスプローラーのジョブの一覧
**サーバー エクスプローラー**で **[Stream Analytics ジョブ]** をクリックし、**[更新]** をクリックします。 **[Stream Analytics ジョブ]** の下にジョブが表示されます。

![ジョブのリスト](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>ジョブ ビューを開く
ジョブ ノードを展開し、**[ジョブ ビュー]** ノードをダブルクリックしてジョブ ビューを開きます。

![ジョブ ビュー](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>既存のジョブをプロジェクトにエクスポートする
既存のジョブをプロジェクトにエクスポートする方法は 2 つあります。
1. **サーバー エクスプローラー** の **[Stream Analytics ジョブ]** ノードでジョブ ノードを右クリックします。 コンテキスト メニューから **[Export to New Stream Analytics Project]**(新しい Stream Analytics プロジェクトにエクスポートする) をクリックします。
   
   ![ジョブをエクスポートする](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   **ソリューション エクスプローラー**に生成されたプロジェクトが表示されます。
   
   ![ジョブをエクスポートする](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
2. ジョブ ビューで、**[Generate Project]**(プロジェクトの生成) をクリックします。
   
   ![ジョブをエクスポートする](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>既知の問題と制限
 
1. クエリに地理空間関数が含まれる場合、ローカル テストは機能しません。 
2. JavaScript UDF の追加や変更はエディターでサポートされていません。
3. ローカル テストでは、JSON 形式での出力の保存はサポートされません。 
4. Power BI の出力と ADLS の出力はサポートされません。



## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)



