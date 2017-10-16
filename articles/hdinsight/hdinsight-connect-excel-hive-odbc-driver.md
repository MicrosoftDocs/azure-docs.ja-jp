---
title: "Hive ODBC ドライバーを使用した Excel から Hadoop への接続 - Azure HDInsight | Microsoft Docs"
description: "Excel 用の Microsoft Hive ODBC ドライバーを使用できるようにセットアップし、Microsoft Excel から HDInsight クラスターのデータを照会する方法を説明します。"
keywords: hadoop excel,hive excel,hive odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: 65a93003eb829a2eaf4684d2a6ecacf74b0adc75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC ドライバーを使用した Excel から Azure HDInsight の Hadoop への接続

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft のビッグ データ ソリューションでは、Azure HDInsight を使用してデプロイした Apache Hadoop クラスターに Microsoft Business Intelligence (BI) コンポーネントを統合します。 たとえば、Microsoft Hive Open Database Connectivity (ODBC) ドライバーを使用すれば、HDInsight で Hadoop クラスターの Hive データ ウェアハウスに Excel を接続できます。

また、Microsoft Power Query for Excel アドインを使用して Excel から HDInsight クラスターや、その他の (HDInsight 以外の) Hadoop クラスターなどのデータ ソースを接続することもできます。 Power Query のインストール方法と使用方法については、「[Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]」を参照してください。

> [!NOTE]
> この記事の手順は、Linux と Windows ベースの HDInsight クラスターのどちらにも使用できますが、クライアント ワークステーションには Windows が必要です。
> 
> 

**前提条件**:

この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 その作成方法については、[Azure HDInsight の概要][hdinsight-get-started]に関するページをご覧ください。
* **ワークステーション** 。

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC ドライバーのインストール
Microsoft Hive ODBC ドライバーは、[ダウンロード センター][hive-odbc-driver-download]からダウンロードしてインストールします。

このドライバーをインストールできるのは、32 ビットまたは 64 ビットバージョンの Windows 7、Windows 8、Windows 10、Windows Server 2008 R2、および Windows Server 2012 です。 ドライバーを使用して、Azure HDInsight (バージョン 1.6 以降) と Azure HDInsight Emulator (v.1.0.0.0 以降) に接続できます。 ODBC ドライバーを使用するアプリケーションのバージョンに合致したバージョンをインストールする必要があります。 このチュートリアルでは、Office Excel のドライバーが使用されます。

## <a name="create-hive-odbc-data-source"></a>Hive ODBC データ ソースの作成
次の手順に従って、Hive ODBC データ ソースを作成します。

1. Windows 8 または Windows 10 で、Windows キーを押してスタート画面を開き、 **データ ソース**を入力します。
2. 使用している Office バージョンに応じて、**[ODBC データ ソースのセットアップ (32 ビット)]** または **[ODBC データ ソースのセットアップ (64 ビット)]** をクリックします。 Windows 7 を使用している場合は、**[管理ツール]** の **[ODBC データ ソース (32 ビット)]** または **[ODBC データ ソース (64 ビット)]** をクリックします。 **[ODBC データ ソース アドミニストレーター]** ダイアログが開きます。
   
    ![OBDC データ ソース アドミニストレーター](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "ODBC データ ソース アドミニストレーターを使用して DSN を構成")

3. ユーザー DNS から、**[追加]** をクリックすると、**データ ソースの新規作成**ウィザードが開きます。
4. **[Microsoft Hive ODBC ドライバー]** を選択し、**[完了]** をクリックします。 **[Microsoft Hive ODBC ドライバーの DNS セットアップ]** ダイアログが開きます。
5. 次の値を入力または選択します。
   
   | プロパティ | Description |
   | --- | --- |
   |  データ ソース名 |データ ソースに名前を付けます。 |
   |  Host |「&lt;HDInsightClusterName>.azurehdinsight.net」と入力します。 たとえば、「myHDICluster.azurehdinsight.net」と入力します。 |
   |  ポート |<strong>443</strong> を使用します。 (このポートは 563 から 443 に変更されました)。 |
   |  データベース |<strong>既定値</strong>を使用します。 |
   |  メカニズム |<strong>Azure HDInsight サービス</strong>を選択します。 |
   |  ユーザー名 |HDInsight クラスター ユーザーの HTTP ユーザー名を入力します。 既定のユーザー名は <strong>admin</strong>です。 |
   |  パスワード |HDInsight クラスター ユーザーのパスワードを入力します。 |
   
    </table>
   
    **[詳細オプション]**をクリックするときに、注意する必要のある重要なパラメーターがいくつかあります。
   
   | パラメーター | Description |
   | --- | --- |
   |  ネイティブ クエリの使用 |これを選択すると、ODBC ドライバーは TSQL を HiveQL に変換しません。 純粋な HiveQL ステートメントを送信していることを確認している場合にのみ使用します。 SQL Server または Azure SQL Database に接続している場合は、オフのままにします。 |
   |  ブロック単位でフェッチされた行 |大量のレコードをフェッチする場合、このパラメーターを調整してパフォーマンスを最適化する必要がある場合があります。 |
   |  既定の文字列の列の長さ、バイナリ列の長さ、10 進数の列の桁数 |データ型の長さおよび精度は、データが返される方法に影響する可能性があります。 精度が失われたり、切り捨てられたりするために間違った情報が返されます。 |

    ![詳細オプション](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN の詳細構成オプション")

1. **[テスト]** をクリックして、データ ソースをテストします。 データ ソースが正しく構成された場合、 *テストは無事に完了しました。*と表示されます。
2. **[OK]** をクリックして [テスト] ダイアログを閉じます。 新しいデータ ソースが **[ODBC データ ソース アドミニストレーター]** に表示されます。
3. **[OK]** をクリックしてウィザードを終了します。

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight から Excel へのデータのインポート
ここでは、前のセクションで作成した ODBC データ ソースを使用して、Hive テーブルから Excel ブックへデータをインポートする方法を説明します。

1. Excel で新しいブックまたは既存のブックを開きます。
2. **[データ]** タブで **[データの取得]**、**[その他のデータ ソース]**、**[ODBC]** の順にクリックすると、**データ接続ウィザード**が起動します。
   
    ![データ接続ウィザードを開く](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "データ接続ウィザードを開く")
4. 前のセクションで作成したデータ ソース名を選択し、**[OK]** をクリックします。
5. Hadoop ユーザー名 (既定のユーザー名は admin) とパスワードを入力し、**[接続]** をクリックします。
6. ナビゲーターで **[HIVE]**、**[既定]** の順に展開し、**[hivesampletable]**、**[読み込み]** の順にクリックします。 データが Excel にインポートされるまでに、数秒かかります。

    ![HDInsight Hive ODBC ナビゲーター](./media/hdinsight-connect-excel-hive-ODBC-driver/hdinsight.hive.odbc.navigator.png "データ接続ウィザードを開く")


## <a name="next-steps"></a>次のステップ
この記事では、Microsoft Hive ODBC ドライバーを使用して HDInsight サービスから Excel にデータを取得する方法を学習しました。 同様に、SQL Database に HDInsight サービスからデータを取得することもできます。 また、HDInsight サービスにデータをアップロードすることもできます。 詳細については、次を参照してください。

* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](./hdinsight-connect-hive-power-bi.md)。
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)。
* [Power Query を使用して Excel を Hadoop に接続する](./hdinsight-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Hive クエリを実行する](./hdinsight-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./hdinsight-upload-data.md)。

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


