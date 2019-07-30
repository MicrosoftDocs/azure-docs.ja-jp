---
title: Blob Storage から SQL Database へのデータのコピー - Azure | Microsoft Docs
description: このチュートリアルでは、Azure Data Factory パイプラインでコピー アクティビティを使用して、Blob Storage から SQL Database にデータをコピーする方法を示します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ad114ce3a40e11048d01c6768811089c43cdf1db
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839388"
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
* **Azure ストレージ アカウント**。 このチュートリアルでは、BLOB ストレージを **ソース** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「 [ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md) 」をご覧ください。
* **Azure SQL データベース**。 このチュートリアルでは、Azure SQL データベースを **コピー先** データ ストアとして使用します。 このチュートリアルで使用できる Azure SQL データベースがない場合の作成方法については、「 [Azure SQL Database を作成して構成する方法](../../sql-database/sql-database-get-started.md) 」を参照してください。
* **SQL Server 2012/2014 または Visual Studio 2013**。 サンプル データベースを作成し、結果データをデータベースに表示するには、SQL Server Management Studio または Visual Studio を使用します。  

## <a name="collect-blob-storage-account-name-and-key"></a>BLOB ストレージ アカウントの名前とキーを収集する
このチュートリアルを実行するには、Azure ストレージ アカウントのアカウント名とアカウント キーが必要です。 Azure ストレージ アカウントの**アカウント名**と**アカウント キー**をメモしておきます。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューの **[すべてのサービス]** をクリックし、 **[ストレージ アカウント]** を選択します。

    ![参照 - ストレージ アカウント](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. **[ストレージ アカウント]** ブレードで、このチュートリアルで使用する **Azure ストレージ アカウント**を選択します。
4. **[設定]** にある **[アクセス キー]** リンクを選択します。
5. **[ストレージ アカウント名]** テキスト ボックスの隣にある (イメージの) **[コピー]** ボタンをクリックし、任意の場所 (たとえばテキスト ファイル) に貼り付けて保存します。
6. **key1**についても、前のコピー手順を繰り返すか、メモしておきます。

    ![スストレージ アクセス キー](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. **[X]** をクリックしてすべてのブレードを閉じます。

## <a name="collect-sql-server-database-user-names"></a>SQL サーバー、データベース、ユーザーの名前を収集する
このチュートリアルを実行するには、Azure SQL サーバー名、データベース名、ユーザー名が必要です。 Azure SQL データベースの**サーバー**、**データベース**、**ユーザー**の名前をメモしておきます。

1. **Azure Portal** で、左側にある **[すべてのサービス]** をクリックし、 **[SQL データベース]** を選択します。
2. **[SQL データベース]** ブレードで、このチュートリアルで使用する**データベース**を選択します。 **データベース名**をメモしておきます。  
3. **[SQL データベース]** ブレードで、 **[設定]** の **[プロパティ]** をクリックします。
4. **[サーバー名]** と **[サーバー管理ログイン]** の値をメモしておきます。
5. **[X]** をクリックしてすべてのブレードを閉じます。

## <a name="allow-azure-services-to-access-sql-server"></a>Azure サービスに SQL サーバーへのアクセスを許可する
Data Factory サービスから Azure SQL サーバーにアクセスできるように、Azure SQL サーバーで **[Azure サービスへのアクセスを許可する]** の設定が**オン**になっていることを確認します。 この設定を確認して有効にするには、次の手順を実行します。

1. 左側にある **[すべてのサービス]** ハブをクリックし、 **[SQL サーバー]** をクリックします。
2. サーバーを選択し、 **[設定]** の **[ファイアウォール]** をクリックします。
3. **[ファイアウォールの設定]** ブレードの **[Azure サービスへのアクセスを許可する]** で **[オン]** をクリックします。
4. **[X]** をクリックしてすべてのブレードを閉じます。

## <a name="prepare-blob-storage-and-sql-database"></a>Blob Storage と SQL Database を準備する
ここからは、次の手順を実行して、チュートリアルで使用する Azure Blob Storage と Azure SQL データベースを準備します。  

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

    **SQL Server 2012/2014 がコンピューターにインストールされている場合**は、「[SQL Server Management Studio を使用した Azure SQL Database の管理](../../sql-database/sql-database-manage-azure-ssms.md)」の手順に従い、Azure SQL のサーバーに接続して SQL スクリプトを実行します。 

    クライアントから Azure SQL サーバーへのアクセスが許可されていない場合は、コンピューター (IP アドレス) からのアクセスを許可するように、Azure SQL サーバーのファイアウォールを構成する必要があります。 Azure SQL サーバーのファイアウォールを構成する手順については、 [こちらの記事](../../sql-database/sql-database-configure-firewall-settings.md) を参照してください。

## <a name="create-a-data-factory"></a>Data Factory を作成する。
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
