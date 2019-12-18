---
title: SQL Server 仮想マシンにデータを移動する - Team Data Science Process
description: フラット ファイルまたはオンプレミスの SQL Server から Azure VM 上の SQL Server にデータを移動します。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 47a77def43a9577e5a3506899da47db2f684b495
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61429520"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Azure 仮想マシン上の SQL Server にデータを移動する

この記事では、フラット ファイル (CSV 形式または TSV 形式) またはオンプレミスの SQL Server から、Azure 仮想マシン上の SQL Server にデータを移動するためのオプションについて説明します。 このクラウドへのデータ移動タスクは、Team Data Science Process の一部です。

Machine Learning 用に Azure SQL Database にデータを移動するためのオプションに関する説明は、「 [Azure Machine Learning 用に Azure SQL データベースにデータを移動する](move-sql-azure.md)」を参照してください。

次の表は、Azure 仮想マシン上の SQL Server にデータを移動するためのオプションをまとめたものです。

| <b>ソース</b> | <b>移動先:Azure VM 上の SQL Server</b> |
| --- | --- |
| <b>フラット ファイル</b> |1.<a href="#insert-tables-bcp">コマンド ライン一括コピー ユーティリティ (BCP) </a><br> 2.<a href="#insert-tables-bulkquery">一括挿入 SQL クエリ </a><br> 3.<a href="#sql-builtin-utilities">SQL Server のグラフィカル組み込みユーティリティ</a> |
| <b>オンプレミスの SQL Server</b> |1.<a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Microsoft Azure VM への SQL Server データベースのデプロイ ウィザード</a><br> 2.<a href="#export-flat-file">フラット ファイルへのエクスポート</a><br> 3.<a href="#sql-migration">SQL Database 移行ウィザード </a> <br> 4.<a href="#sql-backup">データベースのバックアップと復元 </a><br> |

このドキュメントでは、SQL Server Management Studio または Visual Studio のデータベース エクスプローラーから SQL コマンドが実行されることを想定していることに注意してください。

> [!TIP]
> 別の方法として、 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して、データを Azure の SQL Server VM に移動するパイプラインの作成とスケジュール設定を実行できます。 詳細については、「 [Azure Data Factory を使用してデータをコピーする (コピー アクティビティ)](../../data-factory/copy-activity-overview.md)」を参照してください。
>
>

## <a name="prereqs"></a>前提条件
このチュートリアルでは、以下があることを前提としています。

* **Azure サブスクリプション**。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* **Azure ストレージ アカウント**。 このチュートリアルのデータを格納するには、Azure ストレージ アカウントを使用します。 Azure ストレージ アカウントがない場合は、「 [ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md) 」を参照してください。 ストレージ アカウントを作成した後は、ストレージにアクセスするために使用するアカウント キーを取得する必要があります。 「[ストレージ アクセス キーの管理](../../storage/common/storage-account-manage.md#access-keys)」を参照してください。
* **Azure VM 上に SQL Server**がプロビジョニングされていること。 手順については、「 [高度な分析のために Azure SQL Server 仮想マシンを IPython Notebook サーバーとして設定する](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)」を参照してください。
* **Azure PowerShell** がローカルにインストールされ構成されていること。 手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

## <a name="filesource_to_sqlonazurevm"></a> フラット ファイル ソースから Azure VM 上の SQL Server にデータを移動する
データがフラット ファイル (行と列の形式で配置されている) に存在する場合は、次の方法を使用して Azure の SQL Server VM にデータを移動できます。

1. [コマンド ライン一括コピー ユーティリティ (BCP)](#insert-tables-bcp)
2. [一括挿入 SQL クエリ](#insert-tables-bulkquery)
3. [SQL Server のグラフィカル組み込みユーティリティ (インポート/エクスポート、SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>コマンド ライン一括コピー ユーティリティ (BCP)
BCP は、SQL Server と一緒にインストールされるコマンド ライン ユーティリティであり、データを移動する最も簡単な方法の 1 つです。 これは、3 つの異なる SQL Server (オンプレミスの SQL Server、SQL Azure、および Azure での SQL Server VM) すべて機能します。

> [!NOTE]
> **BCP 用のデータの場所**  
> 必須ではありませんが、ターゲットの SQL Server と同じマシン上にソース データを含むファイルがある場合、高速転送 (ネットワークの速度とローカル ディスク IO の速度の差) を使用できます。 さまざまなファイル コピー ツール ([AZCopy](../../storage/common/storage-use-azcopy.md)、[Azure ストレージ エクスプローラー](https://storageexplorer.com/)、またはリモート デスクトップ プロトコル (RDP) を介した Windows のコピーと貼り付けなど) を使って、データを含むフラット ファイルを SQL Server がインストールされているマシンに移動できます。
>
>

1. データベースとテーブルがターゲットの SQL Server データベースで作成されていることを確認します。 以下に、`Create Database` コマンドと `Create Table` コマンドを使用してこれを行う方法の例を示します。

```sql
CREATE DATABASE <database_name>

CREATE TABLE <tablename>
(
    <columnname1> <datatype> <constraint>,
    <columnname2> <datatype> <constraint>,
    <columnname3> <datatype> <constraint>
)
```

1. bcp がインストールされているマシンのコマンド ラインから次のコマンドを発行して、テーブルのスキーマを記述するフォーマット ファイルを生成します。

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. 次のように bcp コマンドを使用して、データをデータベースに挿入します。 SQL Server が同じマシン上にインストールされていると想定した場合、これはコマンド ラインから動作します。

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **BCP 挿入の最適化** こうした挿入を最適化するには、「 [一括インポートを最適化するためのガイドライン](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) 」の記事を参照してください。
>
>

### <a name="insert-tables-bulkquery-parallel"></a>高速データ移動用の挿入の並列処理
移動するデータのサイズが大きい場合は、PowerShell スクリプトで複数の BCP コマンドを並行して同時に実行することによって、高速化できます。

> [!NOTE]
> **ビッグ データの取り込み** 大きなデータセットや非常に大きなデータセットのデータ読み込みを最適化するには、複数のファイル グループとパーティション テーブルを使用して、論理的および物理的なデータベース テーブルをパーティション分割します。 データを作成してパーティション テーブルに読み込む方法についての詳細は、「 [SQL パーティション テーブルの並列読み込み](parallel-load-sql-partitioned-tables.md)」を参照してください。
>
>

次のサンプル PowerShell スクリプトは、bcp を使用した並列挿入を示しています。

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="insert-tables-bulkquery"></a>一括挿入 SQL クエリ
[一括挿入 SQL クエリ](https://msdn.microsoft.com/library/ms188365)は、行/列ベースのファイルからデータをデータベースにインポートする場合に使用できます (サポートされるタイプについては「[一括エクスポートまたは一括インポートのデータの準備 (SQL Server)](https://msdn.microsoft.com/library/ms188609)」のトピックで説明されています)。

一括挿入用のいくつかのサンプル コマンドを以下に示します。  

1. インポートする前に、データを分析して任意のカスタム オプションを設定し、SQL Server データベースの日付などのすべての特別なフィールドが同じ形式になるようにします。 次に、日付形式を年-月-日 (データに年-月-日形式で日付が含まれている場合) に設定する方法の例を示します。

```sql
SET DATEFORMAT ymd;
```
1. 一括インポート ステートメントを使用してデータをインポートする:

```sql
BULK INSERT <tablename>
FROM
'<datafilename>'
WITH
(
    FirstRow = 2,
    FIELDTERMINATOR = ',', --this should be column separator in your data
    ROWTERMINATOR = '\n'   --this should be the row separator in your data
)
```

### <a name="sql-builtin-utilities"></a>
SQL Server 統合サービス (SSIS) を使用して、フラット ファイルから Azure の SQL Server VM にデータをインポートすることができます。
SSIS は 2 つの Studio 環境で使用できます。 詳細については、「 [統合サービス (SSIS) と Studio 環境](https://technet.microsoft.com/library/ms140028.aspx)」を参照してください:。

* SQL Server データ ツールの詳細については、「 [Microsoft SQL Server データ ツール](https://msdn.microsoft.com/data/tools.aspx)  
* インポート/エクスポート ウィザードの詳細については、「 [SQL Server のインポートおよびエクスポート ウィザード](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>オンプレミスの SQL Server から Azure VM 上の SQL Server にデータを移動する
次の移行方法を使用することもできます。

1. [Microsoft Azure VM への SQL Server データベースのデプロイ ウィザード](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [フラット ファイルへのエクスポート](#export-flat-file)
3. [SQL Database 移行ウィザード](#sql-migration)
4. [データベースのバックアップと復元](#sql-backup)

それぞれの方法について以下で説明します。

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Microsoft Azure VM への SQL Server データベースのデプロイ ウィザード
**Microsoft Azure VM への SQL Server データベースのデプロイ ウィザード** は、オンプレミスの SQL Server インスタンスから Azure VM の SQL Server にデータを簡単に移行するためのお勧めの方法です。 詳しい手順や、その他の代替策の詳細については、「[Azure VM の SQL Server へのデータベースの移行](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)」を参照してください。

### <a name="export-flat-file"></a>フラット ファイルへのエクスポート
「 [データの一括インポートと一括エクスポート (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) 」のトピックで説明されているように、さまざまな方法を使用してオンプレミスの SQL Server からデータを一括エクスポートできます。 このドキュメントでは、一例として一括コピー プログラム (BCP) について説明します。 データをフラット ファイルにエクスポートした後は、一括インポートを使用して別の SQL Server にそのデータをインポートできます。

1. 次のように bcp ユーティリティを使用して、オンプレミスの SQL Server からファイルにデータをエクスポートします。

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. 手順 1 でエクスポートされたテーブル スキーマに対して `create database` と `create table` を使用して、データベースとテーブルを Azure の SQL Server VM に作成します。
3. エクスポート/インポートされているデータのテーブルのスキーマを記述するためのフォーマット ファイルを作成します。 フォーマット ファイルの詳細については、「 [フォーマット ファイルの作成 (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx)」を参照してください。

    SQL Server マシンから BCP を実行する場合のフォーマット ファイルの生成

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    SQL Server に対して BCP をリモート実行する場合のフォーマット ファイルの生成

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. 「[ファイル ソースからのデータの移動](#filesource_to_sqlonazurevm) 」セクションで説明されているいずれかの方法を使用して、フラット ファイルのデータを SQL Server に移動します。

### <a name="sql-migration"></a>SQL Database 移行ウィザード
[SQL Server データベース移行ウィザード](https://sqlazuremw.codeplex.com/) は、2 つの SQL server インスタンス間でデータを移動するための使いやすい方法を提供します。 これにより、ユーザーはソースと移動先テーブル間でデータ スキーマをマップし、列の型と他のさまざまな機能を選択できます。 これは、内部で一括コピー (BCP) を使用します。 次に、SQL データベースの移行ウィザードのようこそ画面のスクリーン ショットを示します。  

![SQL Server 移行ウィザード][2]

### <a name="sql-backup"></a>データベースのバックアップと復元
SQL Server は以下のものをサポートします。

1. [データベースのバックアップと復元機能](https://msdn.microsoft.com/library/ms187048.aspx) (ローカル ファイルに対するバックアップと復元、または BLOB への BACPAC エクスポート) と[データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx) (BACPAC を使用)。
2. コピーされたデータベースを使用して Azure で SQL Server VM を直接作成する機能、または既存の SQL Azure データベースにコピーする機能。 詳細については、「 [データベース コピー ウィザードの使用](https://msdn.microsoft.com/library/ms188664.aspx)」を参照してください。

SQL Server Management Studio のデータベースのバックアップ/復元オプションのスクリーンショットを次に示します。

![SQL Server インポート ツール][1]

## <a name="resources"></a>リソース
[Azure VM の SQL Server へのデータベースの移行](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Azure Virtual Machines における SQL Server の概要](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
