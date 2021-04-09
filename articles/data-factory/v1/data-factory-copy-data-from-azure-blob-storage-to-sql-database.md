---
title: Blob Storage から SQL Database にデータをコピーする - Azure
description: このチュートリアルでは、Azure Data Factory パイプラインでコピー アクティビティを使用して、Blob Storage から SQL Database にデータをコピーする方法を示します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 24cedc6a1e0be66e9a924a50e25257f18b7f96a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376889"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>チュートリアル:Data Factory を使用した Blob Storage から SQL Database へのデータのコピー
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。

このチュートリアルでは、BLOB ストレージから SQL Database にデータをコピーするパイプラインを備えたデータ ファクトリを作成します。

コピー アクティビティにより、Azure Data Factory でデータ移動が実行されます。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」をご覧ください。  

> [!NOTE]
> Data Factory サービスの詳細については、 [Azure Data Factory サービスの概要](data-factory-introduction.md) に関する記事をご覧ください。
>
>

## <a name="prerequisites-for-the-tutorial"></a>このチュートリアルの前提条件
このチュートリアルを開始する前に、以下の前提条件を満たしている必要があります。

* **Azure サブスクリプション**。  サブスクリプションがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/) のページを参照してください。
* **Azure ストレージ アカウント**。 このチュートリアルでは、BLOB ストレージを **ソース** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「 [ストレージ アカウントの作成](../../storage/common/storage-account-create.md) 」をご覧ください。
* **Azure SQL データベース**。 このチュートリアルでは、Azure SQL Database を **コピー先** データ ストアとして使用します。 このチュートリアルで使用できるデータベースが Azure SQL Database にない場合は、[Azure SQL Database でデータベースを作成および構成する方法](../../azure-sql/database/single-database-create-quickstart.md)に関するページを参照してデータベースを作成してください。
* **SQL Server 2012/2014 または Visual Studio 2013**。 サンプル データベースを作成し、結果データをデータベースに表示するには、SQL Server Management Studio または Visual Studio を使用します。  

## <a name="collect-blob-storage-account-name-and-key"></a>BLOB ストレージ アカウントの名前とキーを収集する
このチュートリアルを実行するには、Azure ストレージ アカウントのアカウント名とアカウント キーが必要です。 Azure ストレージ アカウントの **アカウント名** と **アカウント キー** をメモしておきます。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューの **[すべてのサービス]** をクリックし、 **[ストレージ アカウント]** を選択します。

    ![参照 - ストレージ アカウント](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. **[ストレージ アカウント]** ブレードで、このチュートリアルで使用する **Azure ストレージ アカウント** を選択します。
4. **[設定]** にある **[アクセス キー]** リンクを選択します。
5. **[ストレージ アカウント名]** テキスト ボックスの隣にある (イメージの) **[コピー]** ボタンをクリックし、任意の場所 (たとえばテキスト ファイル) に貼り付けて保存します。
6. **key1** についても、前のコピー手順を繰り返すか、メモしておきます。

    ![スストレージ アクセス キー](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. **[X]** をクリックしてすべてのブレードを閉じます。

## <a name="collect-sql-server-database-user-names"></a>SQL サーバー、データベース、ユーザーの名前を収集する
このチュートリアルを実行するには、論理 SQL サーバー名、データベース名、ユーザー名が必要です。 Azure SQL Database の **サーバー**、**データベース**、**ユーザー** の名前をメモしておきます。

1. **Azure Portal** で、左側にある **[すべてのサービス]** をクリックし、 **[SQL データベース]** を選択します。
2. **[SQL データベース]** ブレードで、このチュートリアルで使用する **データベース** を選択します。 **データベース名** をメモしておきます。  
3. **[SQL データベース]** ブレードで、 **[設定]** の **[プロパティ]** をクリックします。
4. **[サーバー名]** と **[サーバー管理ログイン]** の値をメモしておきます。
5. **[X]** をクリックしてすべてのブレードを閉じます。

## <a name="allow-azure-services-to-access-sql-server"></a>Azure サービスに SQL サーバーへのアクセスを許可する
Data Factory サービスからサーバーにアクセスできるように、サーバーで **[Azure サービスへのアクセスを許可する]** の設定が確実に **オン** になっているようにします。 この設定を確認して有効にするには、次の手順を実行します。

1. 左側にある **[すべてのサービス]** ハブをクリックし、 **[SQL サーバー]** をクリックします。
2. サーバーを選択し、 **[設定]** の **[ファイアウォール]** をクリックします。
3. **[ファイアウォールの設定]** ブレードの **[Azure サービスへのアクセスを許可する]** で **[オン]** をクリックします。
4. **[X]** をクリックしてすべてのブレードを閉じます。

## <a name="prepare-blob-storage-and-sql-database"></a>Blob Storage と SQL Database を準備する
ここからは、次の手順を実行して、チュートリアルで使用する Azure Blob Storage と Azure SQL Database を準備します。  

1. メモ帳を起動します。 次のテキストを貼り付け、**emp.txt** という名前でハード ドライブの **C:\ADFGetStarted** フォルダーに保存します。

    ```
    John, Doe
    Jane, Doe
    ```
2. [Azure Storage エクスプローラー](https://storageexplorer.com/)などのツールを使用して **adftutorial** コンテナーを作成し、このコンテナーに **emp.txt** ファイルをアップロードします。

3. 次の SQL スクリプトを使用して、 **emp** テーブルを Azure SQL Database に作成します。  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **SQL Server 2012/2014 がコンピューターにインストールされている場合** は、「[SQL Server Management Studio を使用した Azure SQL Database の管理](../../azure-sql/database/single-database-manage.md)」の手順に従い、サーバーに接続して SQL スクリプトを実行します。

    クライアントから論理サーバーへのアクセスが許可されていない場合は、コンピューター (IP アドレス) からのアクセスを許可するように、サーバーのファイアウォールを構成する必要があります。 サーバーのファイアウォールを構成する手順については、[こちらの記事](../../azure-sql/database/firewall-configure.md)を参照してください。

## <a name="create-a-data-factory"></a>Data Factory の作成
これで前提条件を完了しました。 データ ファクトリを作成するには、次のいずれかの方法を使用します。 上部にあるドロップダウン リストのいずれかのオプションまたは次のリンクをクリックして、チュートリアルを実行します。     

* [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 入力データを変換して出力データを生成するのではありません。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換する初めてのパイプラインを作成する方法のチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。
>
> 2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) には、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 詳細については、[Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)に関するページを参照してください。