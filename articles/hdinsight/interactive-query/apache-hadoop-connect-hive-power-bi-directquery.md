---
title: Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する
description: Microsoft Power BI を使用して、Azure HDInsight からの対話型クエリの Hive データを視覚化する方法
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: d9639a4a116e06e17005ebddbb26379882491b33
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867832"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Azure HDInsight の直接クエリを使用して Microsoft Power BI で対話型クエリの Apache Hive データを視覚化する

この記事では、Microsoft Power BI を Azure HDInsight 対話型クエリ クラスターに接続し、直接クエリを使用して Apache Hive データを視覚化する方法について説明します。 与えられた例では、`hivesampletable` Hive テーブルから Power BI にデータを読み込みます。 `hivesampletable` Hive テーブルには、携帯電話の使用データが含まれます。 その使用データを世界地図に示します。

![HDInsight Power BI の地図レポート](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Power BI Desktop の汎用 ODBC コネクタを介してインポートするために、[Apache Hive ODBC ドライバー](../hadoop/apache-hadoop-connect-hive-power-bi.md)を利用することができます。 ただし、Hive クエリ エンジンの非対話的な性質を与えられた BI ワークロードに対しては、この方法はお勧めしません。 この場合は、[HDInsight 対話型クエリ コネクタ](./apache-hadoop-connect-hive-power-bi-directquery.md)および [HDInsight Apache Spark コネクタ](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)を選択した方が、良いパフォーマンスを得られます。

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 このクラスターは、Apache Hive を含む HDInsight クラスターか、新しくリリースされた対話型クエリ クラスターのいずれかです。 クラスターの作成については、「[クラスターの作成](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からコピーをダウンロードできます。

## <a name="load-data-from-hdinsight"></a>HDInsight からデータを読み込む

`hivesampletable` Hive テーブルはすべての HDInsight クラスターに付属しています。

1. Power BI Desktop を起動します。

2. メニュー バーから、**[ホーム]** > **[データの取得]** > **[その他...]** に移動します。

    ![HDInsight Power BI でデータを開く](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. **[データの取得]** ウィンドウで、検索ボックスに「**hdinsight**」と入力します。  

4. 検索結果から **[HDInsight 対話型クエリ]** を選択し、**[接続]** を選択します。  **[HDInsight 対話型クエリ]** が表示されない場合、Power BI Desktop を最新バージョンに更新する必要があります。

5. **[続行]** を選択して、**[サード パーティのサービスに接続中]** ダイアログを閉じます。

6. **[HDInsight 対話型クエリ]** ウィンドウで、次の情報を入力して **[OK]** を選択します。

    |プロパティ | 値 |
    |---|---|
    |サーバー |クラスターの名前 (*myiqcluster.azurehdinsight.net* など) を入力します。|
    |Database |この記事では **[既定]** を入力します。|
    |データ接続モード |この記事では、**[DirectQuery]** を選択します。|

    ![HDInsight 対話型クエリ Power BI DirectQuery 接続](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. HTTP 資格情報を入力して、**[接続]** を選択します。 既定のユーザー名は **admin** です。

8. 左側のウィンドウの **[ナビゲーター]** ウィンドウから、**hivesampletale** を選択します。

9. メイン ウィンドウで **[ロード]** を選択します。

    ![HDInsight 対話型クエリ Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>マップ上にデータを視覚化する

前の手順の続きです。

1. [視覚化] ウィンドウで **[マップ]** (地球アイコン) を選択します。 メイン ウィンドウに汎用マップが表示されます。

    ![HDInsight Power BI のレポート カスタマイズ](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. [フィールド] ウィンドウで **[country]** と **[devicemake]** を選択します。 数秒後、データ ポイントが付いた世界地図がメイン ウィンドウに表示されます。

3. 地図を広げます。

## <a name="next-steps"></a>次の手順
この記事では、Microsoft Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  データ視覚化の詳細については、次の記事を参照してください。

* [Azure HDInsight 上の Microsoft Power BI で ODBC を使用して Apache Hive データを視覚化する](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](./../hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Apache Hadoop に接続する](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。
