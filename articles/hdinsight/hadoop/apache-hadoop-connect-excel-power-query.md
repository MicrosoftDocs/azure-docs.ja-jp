---
title: Power Query を使用して Excel を Apache Hadoop に接続する - Azure HDInsight
description: ビジネス インテリジェンス コンポーネントを活用し、HDInsight 上の Hadoop に格納されているデータに Power Query for Excel でアクセスする方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 13862e642c6a91fe6f3c635df2efde91672ecbad
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866813"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Power Query を使用して Excel を Apache Hadoop に接続する

マイクロソフトのビッグ データ ソリューションの重要な特徴の 1 つに、Azure HDInsight での Microsoft ビジネス インテリジェンス (BI) コンポーネントと Apache Hadoop クラスターの統合があります。 主な例は、Microsoft Power Query for Excel アドインを使用して Hadoop クラスターと関連付けられたデータを格納する Azure Storage アカウントに Excel を接続する機能です。 この記事では、Power Query をセットアップして、HDInsight で管理される Hadoop クラスターに関連付けられたデータの照会に使用する方法を説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight の Apache Hadoop クラスター。 [Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。
* Windows 10、7、Windows Server 2008 R2、またはそれ以降のオペレーティング システムを実行しているワークステーション。
* 企業向け Microsoft 365 アプリ、Office 2016、Office 2013 Professional Plus、Excel 2013 Standalone、または Office 2010 Professional Plus。

## <a name="install-microsoft-power-query"></a>Microsoft Power Query のインストール

Power Query は、HDInsight クラスターで実行される Hadoop ジョブによって出力されたデータや生成されたデータをインポートすることができます。

Excel 2016 では、Power Query は [データ] リボンの [取得と変換] セクションに統合されています。 以前のバージョンの Excel の場合は、[Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?LinkID=286689)から Microsoft Power Query for Excel をダウンロードして、インストールします。

## <a name="import-hdinsight-data-into-excel"></a>HDInsight データを Excel へインポート

Power Query for Excel アドインを使うと、HDInsight クラスターから Excel にデータを簡単にインポートして、そこで PowerPivot や Power Map のような BI ツールを使用してデータの調査、分析、表示ができます。

1. Excel を起動します。

1. 新しい空のブックを作成します。

1. Excel のバージョンに応じて、次の手順を実行します。

   * Excel 2016

     * **[データ]**  >  **[データの取得]**  >  **[Azure から]**  >  **[Azure HDInsight (HDFS) から]** を選択します。

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png" alt-text="HDI.PowerQuery.SelectHdiSource.2016" border="true":::

   * Excel 2013/2010

     * **[Power Query]**  >  **[Azure から]**  >  **[Microsoft Azure HDInsight から]** を選択します。

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png" alt-text="HDI.PowerQuery.SelectHdiSource" border="true":::

       **注:** **[Power Query]** メニューが表示されない場合は、 **[ファイル]**  >  **[オプション]**  >  **[アドイン]** をクリックして、ページ下部にある **[管理]** ボックスの一覧の **[COM アドイン]** を選択します。 **[設定]** をクリックして、Power Query for Excel アドインのボックスがオンになっていることを確認します。

       **注:** Power Query では、 **[その他のソースから]** を選択して、HDFS からデータをインポートすることもできます。

1. **[Azure HDInsight (HDFS)]** ダイアログで、 **[アカウント名または URL]** ボックスに、クラスターに関連付けられている Azure BLOB ストレージ アカウントの名前を入力します。 **[OK]** をクリックします。 既定のストレージ アカウントまたはリンクされたストレージ アカウントを指定できます。  形式は `https://StorageAccountName.blob.core.windows.net/` です。

1. **[アカウント キー]** に BLOB ストレージ アカウントのキーを入力し、 **[接続]** を選択します。 (アカウント情報を入力するのは、最初にこのストアにアクセスするときだけです。)

1. クエリ エディターの左側の **[ナビゲーター]** ウィンドウで、クラスターに関連付けられている Blob Storage コンテナーの名前をダブルクリックします。 既定で、コンテナー名はクラスター名と同じです。

1. **[名前]** 列 (フォルダー パスは **../hive/warehouse/hivesampletable/** ) で **HiveSampleData.txt** を見つけて、HiveSampleData.txt の左側の **[バイナリ]** を選択します。 HiveSampleData.txt はすべてのクラスターに用意されています。 必要に応じて、独自のファイルを使用できます。

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png" alt-text="HDI Excel Power Query でのデータのインポート" border="true":::

1. 列名を変更することもできます。 準備ができたら、 **[閉じて読み込む]** を選択します。  ブックにデータが読み込まれます。

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png" alt-text="HDI Excel Power Query でインポートされたテーブル" border="true":::

## <a name="next-steps"></a>次のステップ

この記事では、Power Query を使用して HDInsight から Excel にデータを取得する方法を学習しました。 同様に、Azure SQL Database に HDInsight からデータを取得することもできます。 また、HDInsight にデータをアップロードすることもできます。 詳細については、以下の記事をお読みください。

* [Azure HDInsight の Microsoft Power BI で Apache Hive データを視覚化する](apache-hadoop-connect-hive-power-bi.md)。
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。
