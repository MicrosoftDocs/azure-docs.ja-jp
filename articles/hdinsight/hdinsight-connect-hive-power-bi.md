---
title: "Azure HDInsight の Power BI でビッグ データを視覚化する | Microsoft Docs"
description: "Microsoft Power BI を利用し、Azure HDInsight で処理された Hive データを視覚化する方法について説明します。"
keywords: "hdinsight,hadoop,hive,対話型クエリ,対話型 hive,LLAP,odbc"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: 9a17c984f5ca801dfa017f1d6fc8fa5ccdeaf39b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-hive-data-with-microsoft-power-bi-in-azure-hdinsight"></a>Azure HDInsight の Microsoft Power BI で Hive データを視覚化する

Microsoft Power BI を Azure HDInsight に接続し、Hive データを視覚化する方法について説明します。 現在のところ、Power BI は、HDInsight への ODBC 接続にのみ対応しています。 このチュートリアルでは、hivesampletable Hive テーブルから Power BI にデータを読み込みます。 Hive テーブルには、携帯電話の使用データが含まれます。 その使用データを世界地図に示します。

![HDInsight Power BI の地図レポート](./media/hdinsight-connect-power-bi/hdinsight-power-bi-visualization.png)

この情報は、新しい[対話型クエリ](./hdinsight-hadoop-use-interactive-hive.md) クラスター タイプにも適用されます。

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 このクラスターは、Hive を含む HDInsight クラスターか、新しくリリースされた対話型クエリ クラスターのいずれかです。 クラスターの作成については、「[クラスターの作成](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からコピーをダウンロードできます。

## <a name="create-hive-odbc-data-source"></a>Hive ODBC データ ソースの作成

「[Hive ODBC データ ソースの作成](hdinsight-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source)」を参照してください。

## <a name="load-data-from-hdinsight"></a>HDInsight からデータを読み込む

hivesampletable Hive テーブルはすべての HDInsight クラスターに付属しています。

1. Power BI Desktop にサインインします。
2. **[ホーム]** タブをクリックし、**[外部データ]** リボンの **[データを取得]** をクリックし、**[その他]** を選択します。

    ![HDInsight Power BI でデータを開く](./media/hdinsight-connect-power-bi/hdinsight-power-bi-open-odbc.png)
3. **[データを取得]** ウィンドウの左側から **[その他]** をクリックし、右側から **[ODBC]** をクリックし、一番下にある **[接続]** をクリックします。
4. **[ODBC から]** ウィンドウで、前のセクションで作成したデータ ソース名を選択して **[OK]** をクリックします。
5. **[ナビゲーター]** ウィンドウで **[ODBC]、[HIVE]、[既定]** の順に展開し、**[hivesampletable]** を選択し、**[読み込み]** をクリックします。

## <a name="visualize-date"></a>データを視覚化する

前の手順の続きです。

1. [視覚化] ウィンドウで **[マップ]** を選択します。  地球儀のアイコンです。

    ![HDInsight Power BI のレポート カスタマイズ](./media/hdinsight-connect-power-bi/hdinsight-power-bi-customize.png)
2. [フィールド] ウィンドウで **[country]** と **[devicemake]** を選択します。 データが地図に描かれます。
3. 地図を広げます。

## <a name="next-steps"></a>次のステップ
この記事では、Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  詳細については、次の記事を参照してください。

* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](./hdinsight-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Hadoop に接続する](./hdinsight-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Hive クエリを実行する](./hdinsight-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./hdinsight-upload-data.md)。
