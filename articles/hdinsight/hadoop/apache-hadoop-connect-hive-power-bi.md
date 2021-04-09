---
title: Power BI で Apache Hive データを視覚化する - Azure HDInsight
description: Microsoft Power BI を利用し、Azure HDInsight で処理された Hive データを視覚化する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: bb448a4befb15618485b2b5951222761180a1f22
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866745"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Azure HDInsight 上の Microsoft Power BI で ODBC を使用して Apache Hive データを視覚化する

ODBC を使用して Microsoft Power BI Desktop を Azure HDInsight に接続し、Apache Hive データを視覚化する方法について説明します。

> [!IMPORTANT]
> Power BI Desktop の汎用 ODBC コネクタを介してインポートするために、Hive ODBC ドライバーを利用することができます。 ただし、Hive クエリ エンジンの非対話的な性質を与えられた BI ワークロードに対しては、この方法はお勧めしません。 この場合は、[HDInsight 対話型クエリ コネクタ](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)および [HDInsight Spark コネクタ](/power-bi/spark-on-hdinsight-with-direct-connect)を選択した方が、良いパフォーマンスを得られます。

この記事では、`hivesampletable` Hive テーブルのデータを Power BI に読み込みます。 Hive テーブルには、携帯電話の使用データが含まれます。 その使用データを世界地図に示します。

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png" alt-text="HDInsight Power BI の地図レポート" border="true":::

この情報は、新しい[対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスター タイプにも適用されます。 直接クエリを使用して HDInsight 対話型クエリに接続する方法については、「[Azure HDInsight の直接クエリを使用して Microsoft Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

この記事の操作を始める前に、以下を用意する必要があります。

* HDInsight クラスター。 このクラスターは、Hive を含む HDInsight クラスターか、新しくリリースされた対話型クエリ クラスターのいずれかです。 クラスターの作成については、「[クラスターの作成](apache-hadoop-linux-tutorial-get-started.md)」を参照してください。

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)。 [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からコピーをダウンロードできます。

## <a name="create-hive-odbc-data-source"></a>Hive ODBC データ ソースの作成

「[Hive ODBC データ ソースの作成](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)」を参照してください。

## <a name="load-data-from-hdinsight"></a>HDInsight からデータを読み込む

**hivesampletable** Hive テーブルは、すべての HDInsight クラスターに付属しています。

1. Power BI Desktop を起動します。

1. 最上位メニューから、 **[ホーム]**  >  **[データの取得]**  >  **[その他...]** に移動します。

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png" alt-text="HDInsight Excel Power BI でデータを開く" border="true":::

1. **[データの取得]** ダイアログで、左側から **[その他]** を選択し、右側から **[ODBC]** を選択して、一番下にある **[接続]** を選択します。

1. **[ODBC から]** ダイアログで、ドロップダウン リストから最後のセクションで作成したデータ ソース名を選択します。 **[OK]** をクリックします。

1. 初めて使用する場合は、 **[ODBC ドライバー]** ダイアログ ボックスが開きます。 左側のメニューから **[既定またはカスタム]** を選択します。 次に、 **[接続]** を選択して **[ナビゲーター]** を開きます。

1. **[ナビゲーター]** ダイアログで、 **[ODBC] > [HIVE] > [既定]** の順に展開し、 **[hivesampletable]** を選択して、 **[読み込み]** を選択します。

## <a name="visualize-data"></a>データの視覚化

前の手順の続きです。

1. [視覚化] ウィンドウで、 **[マップ]** (地球アイコン) を選択します。

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png" alt-text="HDInsight Power BI のレポート カスタマイズ" border="true":::

1. **[フィールド]** ウィンドウで、 **[country]** と **[devicemake]** を選択します。 データが地図に描かれます。

1. 地図を広げます。

## <a name="next-steps"></a>次のステップ

この記事では、Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  詳細については、以下の記事をお読みください。

* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](./apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Apache Hadoop に接続する](apache-hadoop-connect-excel-power-query.md)。
* [直接クエリを使用して Microsoft Power BI で対話型クエリの Apache Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)