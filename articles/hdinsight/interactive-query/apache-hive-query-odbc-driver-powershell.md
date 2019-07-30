---
title: ODBC ドライバーと PowerShell による Apache Hive のクエリの実行 - Azure HDInsight
description: Microsoft Hive ODBC ドライバーと PowerShell を使用して、Azure HDInsight 上の Apache Hive クラスターのクエリを実行します。
keywords: hive,hive odbc,powershell
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: hrasheed
ms.openlocfilehash: b02c865e953861b5ac396538fdd0f0623b0e5428
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486065"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>チュートリアル:ODBC と PowerShell による Apache Hive のクエリの実行

Microsoft ODBC ドライバーでは、Apache Hive などのさまざまな種類のデータ ソースと対話するための柔軟な方法が提供されています。 ODBC ドライバーを使用して Hive クラスターへの接続を開き、ユーザーが選択したクエリを渡して、結果を表示する PowerShell などのスクリプト言語でコードを記述できます。

このチュートリアルでは、以下のタスクを行います。

> [!div class="checklist"]
> * Microsoft Hive ODBC ドライバーをダウンロードしてインストールします
> * クラスターにリンクされている Apache Hive ODBC データ ソースを作成します
> * PowerShell を使用してクラスターからサンプル情報のクエリを実行します

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の項目を用意する必要があります。

* HDInsight 上の対話型クエリ クラスター。 その作成方法については、[Azure HDInsight の概要](../hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。 クラスターの種類として**対話型クエリ**を選択します。

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC ドライバーのインストール

[Microsoft Hive ODBC ドライバー](https://go.microsoft.com/fwlink/?LinkID=286698)をダウンロードしてインストールします。

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC データ ソースを作成する

次の手順では、Apache Hive ODBC データ ソースを作成する方法を示します。

1. Windows で、 **[スタート]**  >  **[Windows 管理ツール]**  >  **[ODBC データ ソース (32 ビット)/(64 ビット)]** の順に移動します。  **[ODBC データ ソース アドミニストレーター]** ウィンドウが開きます。

    ![OBDC データ ソース アドミニストレーター](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "ODBC データ ソース アドミニストレーターを使用して DSN を構成")

1. **[ユーザー DSN]** タブで、 **[追加]** を選択して **[データ ソースの新規作成]** ウィンドウを開きます。

1. **[Microsoft Hive ODBC Driver]** を選択してから、 **[完了]** を選択して **Microsoft Hive ODBC Driver DSN セットアップ** ウィンドウを開きます。

1. 次の値を入力または選択します。

   | プロパティ | 説明 |
   | --- | --- |
   |  データ ソース名 |データ ソースに名前を付けます。 |
   |  ホスト |「 `CLUSTERNAME.azurehdinsight.net` 」を入力します。 たとえば、`myHDICluster.azurehdinsight.net` のように指定します。 |
   |  Port |**443** を使用します。|
   |  Database |**既定値**を使用します。 |
   |  メカニズム |**[Microsoft Azure HDInsight Service]** を選択します |
   |  ユーザー名 |HDInsight クラスター ユーザーの HTTP ユーザー名を入力します。 既定のユーザー名は **admin**です。 |
   |  パスワード |HDInsight クラスター ユーザーのパスワードを入力します。 **[Save Password (Encrypted)]\(パスワードの保存 (暗号化済み)\)** チェック ボックスをオンにします。|

1. 省略可能: **[詳細オプション]** を選択します。  

   | パラメーター | 説明 |
   | --- | --- |
   |  ネイティブ クエリの使用 |これを選択すると、ODBC ドライバーは TSQL を HiveQL に変換しません。 正しい HiveQL ステートメントを送信していることが 100% 確実な場合にのみ、このオプションを使用します。 SQL Server または Azure SQL Database に接続している場合は、オフのままにします。 |
   |  ブロック単位でフェッチされた行 |大量のレコードをフェッチする場合、このパラメーターを調整してパフォーマンスを最適化する必要がある場合があります。 |
   |  既定の文字列の列の長さ、バイナリ列の長さ、10 進数の列の桁数 |データ型の長さおよび精度は、データが返される方法に影響する可能性があります。 精度が失われたり、切り捨てられたりするために間違った情報が返されます。 |

    ![詳細オプション](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "DSN の詳細構成オプション")

1. **[テスト]** を選択して、データ ソースをテストします。 データ ソースが正しく構成された場合、テスト結果に "**成功**" と表示されます。  

1. **[OK]** を選択して、[テスト] ウィンドウを閉じます。  

1. **[OK]** を選択して、**Microsoft Hive ODBC Driver DSN セットアップ**  ウィンドウを閉じます。  

1. **[OK]** を選択して、 **[ODBC データ ソース アドミニストレーター]** ウィンドウを閉じます。  

## <a name="query-data-with-powershell"></a>PowerShell を使用してデータを照会する

次の PowerShell スクリプトは、ODBC が Hive クラスターにクエリを実行する関数です。

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

次のコード スニペットでは、上記の関数を使用して、チュートリアルの最初に作成した対話型クエリ クラスターでクエリを実行します。 `DATASOURCENAME` を **Microsoft Hive ODBC Driver DSN セットアップ**画面で指定した**データ ソース名**に置き換えます。 資格情報を求めるプロンプトが表示されたら、クラスターの作成時に **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** で入力したユーザー名とパスワードを入力します。

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、HDInsight クラスター、ストレージ アカウントは、不要になったら削除します。 これを行うには、クラスターが作成されたリソース グループを選択し、 **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Microsoft Hive ODBC ドライバーと PowerShell を使用して、Azure HDInsight 対話型クエリ クラスターからデータを取得する方法について説明しました。

> [!div class="nextstepaction"]
> [ODBC を使用して Excel を Apache Hive に接続する](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
