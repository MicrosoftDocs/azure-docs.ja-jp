---
title: Power Query を使用して Excel を Apache Hadoop に接続する - Azure HDInsight
description: ビジネス インテリジェンス コンポーネントを活用し、HDInsight 上の Hadoop に格納されているデータに Power Query for Excel でアクセスする方法を説明します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 687a9884c861b4cf72e51f9179e6c0b43968801d
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202909"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Power Query を使用して Excel を Apache Hadoop に接続する
マイクロソフトのビッグ データ ソリューションの重要な特徴の 1 つに、Azure HDInsight での Microsoft ビジネス インテリジェンス (BI) コンポーネントと Apache Hadoop クラスターの統合があります。 主な例は、Microsoft Power Query for Excel アドインを使用して Hadoop クラスターと関連付けられたデータを格納する Azure Storage アカウントに Excel を接続する機能です。 この記事では、Power Query をセットアップして、HDInsight で管理される Hadoop クラスターに関連付けられたデータの照会に使用する方法を説明します。

### <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 その構成方法については、「[Azure HDInsight の概要]」[hdinsight-get-started]を参照してください。
* **ワークステーション** 。
* **Office 2016、Office 2013 Professional Plus、Office 365 ProPlus、Excel 2013 Standalone、または Office 2010 Professional Plus**。

## <a name="install-power-query"></a>Power Query をインストールする
Power Query は、HDInsight クラスターで実行される Hadoop ジョブによって出力されたデータや生成されたデータをインポートすることができます。

Excel 2016 では、Power Query は [データ] リボンの [取得と変換] セクションに統合されています。 以前のバージョンの Excel の場合は、[Microsoft ダウンロード センター][powerquery-download]から Microsoft Power Query for Excel をダウンロードして、インストールします。

## <a name="import-hdinsight-data-into-excel"></a>HDInsight データを Excel へインポート
Power Query for Excel アドインを使うと、HDInsight クラスターから Excel にデータを簡単にインポートして、そこで PowerPivot や Power Map のような BI ツールを使用してデータの調査、分析、表示ができます。

**HDInsight クラスターから Excel にデータをインポートするには**

1. Excel を開きます。
2. 新しい空のブックを作成します。
3. Excel のバージョンに応じて、次の手順を実行します。

   - Excel 2016

     - **[データ]** メニューをクリックし、**[Get & Transform Data]\(データの取得と変換\)** リボンの **[データの取得]** をクリックして、**[From Azure]\(Azure から\)**、**[From Azure HDInsight(HDFS)]\(Azure HDInsight(HDFS) から\)** を順にクリックします。

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - **[Power Query]** メニューをクリックし、**[Azure から]**、**[Microsoft Azure HDInsight から]** の順にクリックします。
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **注:****[Power Query]** メニューが表示されない場合は、**[ファイル]** > **[オプション]** > **[アドイン]** をクリックして、ページ下部にある **[管理]** ボックスの一覧の **[COM アドイン]** を選択します。 **[設定]** をクリックして、Power Query for Excel アドインのボックスがオンになっていることを確認します。
       
       **注:** Power Query では、**[その他のデータ ソース]** をクリックして、HDFS からデータをインポートすることもできます。
4. **[アカウント名]** にクラスターに関連付けられた Azure BLOB ストレージ アカウントの名前を入力し、**[OK]** をクリックします。 既定のストレージ アカウントまたはリンクされたストレージ アカウントを指定できます。  書式は *https://&lt;StorageAccountName>.blob.core.windows.net/* です。
5. **アカウント キー**に BLOB ストレージ アカウントのキーを入力し、**[保存]** をクリックします。 (アカウント情報を入力するのは、最初にこのストアにアクセスするときだけです。)
6. クエリ エディターの左側にある **[ナビゲーター]** ウィンドウで、BLOB ストレージ コンテナーの名前をダブルクリックします。 既定で、コンテナー名はクラスター名と同じです。
7. **[名前]** 列 (フォルダーのパスは **../hive/warehouse/hivesampletable/**) で **HiveSampleData.txt** を見つけて、HiveSampleData.txt の左側の **[バイナリ]** をクリックします。 HiveSampleData.txt はすべてのクラスターに用意されています。 必要に応じて、独自のファイルを使用できます。
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. 列名を変更することもできます。 準備ができたら **[閉じて読み込む]** をクリックします。  ブックにデータが読み込まれます。
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>次の手順
この記事では、Power Query を使用して HDInsight から Excel にデータを取得する方法を学習しました。 同様に、Azure SQL Database に HDInsight からデータを取得することもできます。 また、HDInsight にデータをアップロードすることもできます。 詳細については、次の記事を参照してください。

* [Azure HDInsight の Microsoft Power BI で Apache Hive データを視覚化する](apache-hadoop-connect-hive-power-bi.md)。
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](./../hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
