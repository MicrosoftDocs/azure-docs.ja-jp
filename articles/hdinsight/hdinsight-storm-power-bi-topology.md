---
title: "Apache Storm と Power BI の使用 | Microsoft Docs"
description: "HDInsight 内の Apache Storm クラスター上で実行されている C# トポロジのデータを使用して Power BI レポートを作成します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 36fe3b9c-5232-4464-8d75-95403b6da7a1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 75ab31176abaeed2865a77689a5733666f95a253
ms.lasthandoff: 03/07/2017


---
# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>Power BI を使用した Apache Storm トポロジのデータの視覚化

Power BI を使用すると、データをレポートとして視覚的に表すことができます。 このドキュメントでは、HDInsight で Apache Storm を使用して、Power BI のデータを生成する方法の例を示します。

> [!NOTE]
> このドキュメントの手順は Visual Studio を使う Windows 開発環境でのものですが、コンパイル済みのプロジェクトは、Linux または Windows ベースの HDInsight クラスターに送信できます。 SCP.NET トポロジをサポートする Linux ベースのクラスターは、2016 年 10 月 28 日より後に作成されたものだけです。
> 
> C# トポロジを Linux ベースのクラスターで使用するには、プロジェクトによって使われる Microsoft.SCP.Net.SDK NuGet パッケージをバージョン 0.10.0.6 以降に更新します。 また、パッケージのバージョンは、HDInsight にインストールされている Storm のメジャー バージョンと一致する必要もあります。 たとえば、HDInsight バージョン 3.3 および 3.4 上の Storm は Storm バージョン 0.10.x を使いますが、HDInsight 3.5 は Storm 1.0.x を使います。
> 
> Linux ベースのクラスターの C# トポロジは、.NET 4.5 を使い、Mono を使って HDInsight クラスターで実行する必要があります。 ほとんどの機能は動作します。 しかし、[Mono の互換性](http://www.mono-project.com/docs/about-mono/compatibility/)のドキュメントで互換性のない可能性がある機能について確認する必要があります。
> 
> このプロジェクトの Linux または Windows ベースの HDInsight で機能する Java バージョンについては、「[HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (＃C)](hdinsight-storm-develop-java-event-hub-topology.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* [Power BI](https://powerbi.com) アクセス権を持つ Azure Active Directory ユーザー。
* HDInsight クラスター。 詳細については、[HDInsight での Storm の使用](hdinsight-apache-storm-tutorial-get-started-linux.md)に関する記事をご覧ください。

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* 下記のいずれかのバージョンの Visual Studio
  
  * Visual Studio 2012 ( [update 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) または [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
  * Visual Studio 2017 (任意のエディション)

* HDInsight Tools for Visual Studio: インストールの情報については、「 [HDInsight Tools for Visual Studio の使用開始](hdinsight-hadoop-visual-studio-tools-get-started.md) 」を参照してください。

## <a name="how-it-works"></a>動作のしくみ

この例では、Internet Information Services (IIS) のログ データをランダムに生成する C# Storm トポロジを使用しています。 このデータは SQL Database に書き込まれ、そこから Power BI でのレポート生成に使用されます。

この例の主な機能は、以下のファイルに実装されています。

* **SqlAzureBolt.cs**: Storm トポロジで生成された情報を SQL Database に書き込みます。
* **IISLogsTable.sql**: データの格納先となるデータベースを生成するための Transact-SQL ステートメント。

> [!WARNING]
> HDInsight クラスター上のトポロジを起動する前に、このテーブルを SQL Database に作成しておきます。

## <a name="download-the-example"></a>例のダウンロード

[HDInsight C# Storm Power BI の例](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi)をダウンロードします。 ダウンロードするには、 [git](http://git-scm.com/)を使用してフォーク/複製するか、 **ダウンロード** リンクを使用してアーカイブの ZIP ファイルをダウンロードします。

## <a name="create-a-database"></a>データベースの作成

1. データベースを作成するには、[SQL Database チュートリアル](../sql-database/sql-database-get-started.md)のドキュメントに記載されている手順を使用します。

2. 「[Visual Studio で SQL Database に接続する](../sql-database/sql-database-connect-query.md)」の手順に従ってデータベースに接続します。

3. オブジェクト エクスプローラーでデータベースを右クリックし、**[新しいクエリ]** を選択します。 ダウンロードしたプロジェクトに含まれている **IISLogsTable.sql** ファイルの内容をクエリ ウィンドウに貼り付けて、Ctrl + Shift + E キーを押してクエリを実行します。 コマンドが正常に完了した旨のメッセージが返されます。

## <a name="configure-the-sample"></a>サンプルの構成

1. [Azure ポータル](https://portal.azure.com)で SQL データベースを選択します。 [SQL Database] ブレードの **[要点]** セクションから **[データベース接続文字列の表示]** を選びます。 表示された一覧から **ADO.NET (SQL 認証)** 情報をコピーします。

2. Visual Studio でサンプルを開きます。 **ソリューション エクスプローラー**で **App.config** ファイルを開き、次のエントリを見つけます。
   
        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
   
    **##TOBEFILLED##** の値は、前の手順でコピーしたデータベースの接続文字列に置き換えてください。 **{your\_username}** と **{your\_password}** は、データベースのユーザー名とパスワードに置き換えます。

3. ファイルを保存して閉じます。

## <a name="deploy-the-sample"></a>サンプルのデプロイ

1. **ソリューション エクスプローラー**で **StormToSQL** プロジェクトを右クリックし、**[Submit to Storm on HDInsight (HDInsight の Storm に送信)]** を選びます。 [ **Storm クラスター** ] ドロップダウン ダイアログから HDInsight クラスターを選択します。
   
   > [!NOTE]
   > [ **Storm クラスター** ] ドロップダウンにサーバー名が設定されるには数秒かかることがあります。
   > 
   > メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。 2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスターの Storm があるサブスクリプションにログインします。

2. トポロジが送信されると、__[トポロジ ビューアー]__ が表示されます。 このトポロジを表示するには、一覧から SqlAzureWriterTopology を選択します。
   
    ![The topologies, with the topology selected](./media/hdinsight-storm-power-bi-topology/topologyview.png)
   
    このビューを使用してトポロジに関する情報を確認できるほか、エントリ (SqlAzureBolt など) をダブルクリックして、トポロジの特定のコンポーネントに固有の情報を表示できます。

3. トポロジを数分間実行した後、データベースの作成に使用した SQL クエリ ウィンドウに戻ります。 既存のステートメントを次のクエリで置き換えます。
   
        select * from iislogs;
   
    Ctrl + Shift + E キーを使用してクエリを実行すると、次のような結果が返されます。
   
        1    2016-05-27 17:57:14.797    255.255.255.255    /bar    GET    200
        2    2016-05-27 17:57:14.843    127.0.0.1    /spam/eggs    POST    500
        3    2016-05-27 17:57:14.850    123.123.123.123    /eggs    DELETE    200
        4    2016-05-27 17:57:14.853    127.0.0.1    /foo    POST    404
        5    2016-05-27 17:57:14.853    10.9.8.7    /bar    GET    200
        6    2016-05-27 17:57:14.857    192.168.1.1    /spam    DELETE    200
   
    これは、Storm トポロジから書き込まれたデータです。

## <a name="create-a-report"></a>レポートの作成

1. Power BI 用の [Azure SQL Database コネクタ](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) に接続します。

2. **[データベース]** で **[取得]** を選びます。

3. **[Azure SQL Database]** を選び、**[接続]** を選びます。

4. Azure SQL Database に接続するための情報を入力します。 この情報は、[Azure Portal](https://portal.azure.com) にアクセスし、該当する SQL データベースを選択すると確認できます。
   
   > [!NOTE]
   > 接続ダイアログから **[詳細オプションを有効にする]** を使用して、更新間隔とカスタム フィルターを設定することもできます。
 
5. 接続後、接続先のデータベースと同じ名前の新しいデータセットが表示されます。 データセットを選択して、レポートの設計に着手します。

6. **[フィールド]** から **[IISLOGS]** エントリを展開します。 **[URISTEM]** のチェック ボックスをオンにします。 これで、データベースに記録された URI ステム (/foo、/bar など) を列挙したレポートが作成されます。
   
    ![Creating a report](./media/hdinsight-storm-power-bi-topology/createreport.png)

7. 次に、 **[メソッド]** をレポートにドラッグします。 レポートが最新の情報に更新され、HTTP 要求に使用されたステムおよび対応する HTTP メソッドが一覧表示されます。
   
    ![adding the method data](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

8. **[視覚化]** 列から **[フィールド]** アイコンを選び、**[値]** セクションで **[メソッド]** の横の下矢印を選びます。 表示された一覧から **[カウント]**を選択します。 これで、特定の URI がアクセスされた回数を一覧表示するようにレポートが変更されます。
   
    ![Changing to a count of methods](./media/hdinsight-storm-power-bi-topology/count.png)

9. 次に **[積み上げ縦棒グラフ]** を選択して、情報の表示方法を変更します。
   
    ![Changing to a stacked chart](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

10. レポートを保存するには、**[保存]** を選択してレポートの名前を入力します。

## <a name="stop-the-topology"></a>トポロジを停止する

トポロジの実行は、意図的に停止するか、HDInsight クラスター上で Storm を削除するまで続きます。 トポロジを停止するには、以下の手順を実行します。

1. Visual Studio でトポロジ ビューアーに戻って、トポロジを選択します。

2. **[強制終了]** ボタンを選択してトポロジを停止します。
   
    ![[トポロジの概要] の [強制終了 (Kill)] ボタン](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Storm トポロジから SQL Database にデータを送信し、Power BI を使って視覚化する方法について説明しました。 HDInsight 上の Storm を使用して他の Azure テクノロジと連携する方法については、次のページを参照してください。

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)


