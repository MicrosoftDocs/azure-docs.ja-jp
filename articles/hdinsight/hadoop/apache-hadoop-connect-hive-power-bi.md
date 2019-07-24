---
title: Power BI で Apache Hive データを視覚化する - Azure HDInsight
description: Microsoft Power BI を利用し、Azure HDInsight で処理された Hive データを視覚化する方法について説明します。
keywords: hdinsight,hadoop,hive,対話型クエリ,対話型 hive,LLAP,odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: hrasheed
ms.openlocfilehash: 9fa1d0efc98627b976118a9e81ca430e1645a946
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437443"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Azure HDInsight 上の Microsoft Power BI で ODBC を使用して Apache Hive データを視覚化する

ODBC を使用して Microsoft Power BI を Azure HDInsight に接続し、Apache Hive データを視覚化する方法について説明します。

>[!IMPORTANT]
> Power BI Desktop の汎用 ODBC コネクタを介してインポートするために、Hive ODBC ドライバーを利用することができます。 ただし、Hive クエリ エンジンの非対話的な性質を与えられた BI ワークロードに対しては、この方法はお勧めしません。 この場合は、[HDInsight 対話型クエリ コネクタ](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)および [HDInsight Spark コネクタ](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)を選択した方が、良いパフォーマンスを得られます。

このチュートリアルでは、hivesampletable Hive テーブルから Power BI にデータを読み込みます。 Hive テーブルには、携帯電話の使用データが含まれます。 その使用データを世界地図に示します。

![HDInsight Power BI の地図レポート](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

この情報は、新しい[対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスター タイプにも適用されます。 直接クエリを使用して HDInsight 対話型クエリに接続する方法については、「[Azure HDInsight の直接クエリを使用して Microsoft Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)」をご覧ください。



## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 このクラスターは、Hive を含む HDInsight クラスターか、新しくリリースされた対話型クエリ クラスターのいずれかです。 クラスターの作成については、「[クラスターの作成](apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からコピーをダウンロードできます。

## <a name="create-hive-odbc-data-source"></a>Hive ODBC データ ソースの作成

「[Hive ODBC データ ソースの作成](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)」を参照してください。

## <a name="load-data-from-hdinsight"></a>HDInsight からデータを読み込む

hivesampletable Hive テーブルはすべての HDInsight クラスターに付属しています。

1. Power BI Desktop にサインインします。
2. **[ホーム]** タブをクリックし、**[外部データ]** リボンの **[データを取得]** をクリックし、**[その他]** を選択します。

    ![HDInsight Power BI でデータを開く](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. **[データを取得]** ウィンドウの左側から **[その他]** をクリックし、右側から **[ODBC]** をクリックし、一番下にある **[接続]** をクリックします。
4. **[ODBC から]** ウィンドウで、前のセクションで作成したデータ ソース名を選択して **[OK]** をクリックします。
5. **[ナビゲーター]** ウィンドウで **[ODBC]、[HIVE]、[既定]** の順に展開し、**[hivesampletable]** を選択し、**[読み込み]** をクリックします。

## <a name="visualize-data"></a>データの視覚化

前の手順の続きです。

1. [視覚化] ウィンドウで **[マップ]** を選択します。  地球儀のアイコンです。

    ![HDInsight Power BI のレポート カスタマイズ](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. [フィールド] ウィンドウで **[country]** と **[devicemake]** を選択します。 データが地図に描かれます。
3. 地図を広げます。

## <a name="next-steps"></a>次の手順
この記事では、Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  詳細については、次の記事を参照してください。

* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](./../hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](./apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Apache Hadoop に接続する](apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。
