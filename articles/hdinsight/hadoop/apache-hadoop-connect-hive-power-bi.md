---
title: Power BI で Apache Hive データを視覚化する - Azure HDInsight
description: Microsoft Power BI を利用し、Azure HDInsight で処理された Hive データを視覚化する方法について説明します。
keywords: hdinsight,hadoop,hive,対話型クエリ,対話型 hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 69353968f6b38f0d16b68c58b9b00c3e6d45850b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446870"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Azure HDInsight 上の Microsoft Power BI で ODBC を使用して Apache Hive データを視覚化する

ODBC を使用して Microsoft Power BI Desktop を Azure HDInsight に接続し、Apache Hive データを視覚化する方法について説明します。

>[!IMPORTANT]
> Power BI Desktop の汎用 ODBC コネクタを介してインポートするために、Hive ODBC ドライバーを利用することができます。 ただし、Hive クエリ エンジンの非対話的な性質を与えられた BI ワークロードに対しては、この方法はお勧めしません。 この場合は、[HDInsight 対話型クエリ コネクタ](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)および [HDInsight Spark コネクタ](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)を選択した方が、良いパフォーマンスを得られます。

この記事では、`hivesampletable` Hive テーブルのデータを Power BI に読み込みます。 Hive テーブルには、携帯電話の使用データが含まれます。 その使用データを世界地図に示します。

![HDInsight Power BI の地図レポート](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

この情報は、新しい[対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスター タイプにも適用されます。 直接クエリを使用して HDInsight 対話型クエリに接続する方法については、「[Azure HDInsight の直接クエリを使用して Microsoft Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 このクラスターは、Hive を含む HDInsight クラスターか、新しくリリースされた対話型クエリ クラスターのいずれかです。 クラスターの作成については、「[クラスターの作成](apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** 。 [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からコピーをダウンロードできます。

## <a name="create-hive-odbc-data-source"></a>Hive ODBC データ ソースの作成

「[Hive ODBC データ ソースの作成](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)」を参照してください。

## <a name="load-data-from-hdinsight"></a>HDInsight からデータを読み込む

hivesampletable Hive テーブルはすべての HDInsight クラスターに付属しています。

1. Power BI Desktop を起動します。

2. 最上位メニューから、 **[ホーム]**  >  **[データの取得]**  >  **[その他...]** に移動します。

    ![HDInsight Power BI でデータを開く](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. **[データの取得]** ダイアログで、左側から **[その他]** を選択し、右側から **[ODBC]** を選択して、一番下にある **[接続]** を選択します。

4. **[ODBC から]** ダイアログで、ドロップダウン リストから最後のセクションで作成したデータ ソース名を選択して、 **[OK]** を選択します。

5. **[ナビゲーター]** ダイアログで、 **[ODBC] > [HIVE] > [既定]** の順に展開し、 **[hivesampletable]** を選択して、 **[読み込み]** を選択します。

6. **[ODBC ドライバー]** ダイアログで、 **[既定またはカスタム]** を選択して、 **[接続]** を選択します。

## <a name="visualize-data"></a>データの視覚化

前の手順の続きです。

1. [視覚化] ウィンドウで **[マップ]** を選択します。  地球儀のアイコンです。

    ![HDInsight Power BI のレポート カスタマイズ](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. **[フィールド]** ウィンドウで、 **[country]** と **[devicemake]** を選択します。 データが地図に描かれます。
3. 地図を広げます。

## <a name="next-steps"></a>次の手順

この記事では、Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  詳細については、次の記事を参照してください。

* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](./apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Apache Hadoop に接続する](apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。
