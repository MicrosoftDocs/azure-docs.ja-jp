---
title: PowerShell を使用して SQL Server から Blob Storage にデータをコピーする
description: Azure Data Factory 内のセルフホステッド統合ランタイムを使って、オンプレミスのデータ ストアから Azure クラウドにデータをコピーする方法について説明します。
services: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: b0e4fcf771f2441d9e1061ee57e83e26b6b1a241
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922967"
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>チュートリアル:オンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーする

このチュートリアルでは、オンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーするデータ ファクトリ パイプラインを Azure PowerShell を使って作成します。 セルフホステッド統合ランタイムを作成して使用すると、オンプレミス データ ストアとクラウド データ ストア間でデータを移動できます。 

> [!NOTE]
> この記事では、Data Factory サービスの概要については詳しく取り上げません。 詳細については、[Azure Data Factory の概要](introduction.md)に関するページをご覧ください。 

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Azure Storage のリンクされたサービスを作成します。 
> * SQL Server と Azure BLOB のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

## <a name="prerequisites"></a>前提条件
### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、"*共同作成者*" または "*所有者*" ロールに属しているか、Azure サブスクリプションの "*管理者*" である必要があります。 

サブスクリプションで自分が持っているアクセス許可を表示するには、Azure Portal で右上隅にあるユーザー名をクリックし、 **[アクセス許可]** を選択します。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。 ロールにユーザーを追加する手順の例については、「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014、2016、2017
このチュートリアルでは、オンプレミスの SQL Server データベースを "*ソース*" データ ストアとして使用します。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、このオンプレミスの SQL Server データベース (ソース) から Azure Blob Storage (シンク) にデータをコピーします。 SQL Server データベース内に **emp** という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。 

1. SQL Server Management Studio を起動します。 ご使用のマシンにまだインストールされていない場合は、「[SQL Server Management Studio のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)」にアクセスしてください。 

1. 自分の資格情報で SQL Server インスタンスに接続します。 

1. サンプル データベースを作成します。 ツリー ビューで **[データベース]** を右クリックし、 **[新しいデータベース]** を選択します。 
 
1. **[新しいデータベース]** ウィンドウで、データベースの名前を入力し、 **[OK]** を選択します。 

1. **emp** テーブルを作成していくつかのサンプル データを挿入するために、次のクエリ スクリプトをデータベースに対して実行します。 ツリー ビューで、作成したデータベースを右クリックし、 **[新しいクエリ]** をクリックします。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```


### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このチュートリアルでは、コピー先/シンク データ ストアに汎用の Azure Storage アカウント (具体的には Azure Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」をご覧ください。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、オンプレミスの SQL Server データベース (ソース) からこの Azure Blob Storage (シンク) にデータをコピーします。 

#### <a name="get-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このチュートリアルでは、Azure ストレージ アカウントの名前とキーを使用します。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Azure のユーザー名とパスワードを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 

1. 左側のウィンドウの **[その他のサービス]** を選択し、「**ストレージ**」というキーワードでフィルタリングして、 **[ストレージ アカウント]** を選択します。

    ![ストレージ アカウントを検索](media/doc-common-process/search-storage-account.png)

1. ストレージ アカウントの一覧で、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、該当するストレージ アカウントを選択します。 

1. **[ストレージ アカウント]** ウィンドウで **[アクセス キー]** を選択します。

1. **[ストレージ アカウント名]** ボックスと **[key1]** ボックスの値をコピーし、メモ帳などのエディターに貼り付けます。これらの値は、後でこのチュートリアルの中で使用します。 

#### <a name="create-the-adftutorial-container"></a>adftutorial コンテナーの作成 
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure Blob Storage に作成します。 

1. **[ストレージ アカウント]** ウィンドウで **[概要]** に切り替え、 **[BLOB]** を選択します。 

    ![BLOB オプションを選択する](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. **[Blob service]** ウィンドウで **[コンテナー]** を選択します。 

1. **[新しいコンテナー]** ウィンドウの **[名前]** ボックスに「**adftutorial**」と入力し、 **[OK]** を選択します。 

    ![コンテナー名を入力する](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. コンテナーの一覧で、 **[adftutorial]** を選択します。  

1. **adftutorial** の **[コンテナー]** ウィンドウを開いたままにしておきます。 チュートリアルの最後で、このページを使用して出力を確認します。 このコンテナーには output フォルダーが Data Factory によって自動的に作成されます。手動で作成する必要はありません。


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Azure PowerShell をインストールする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

ご利用のマシンに最新バージョンの Azure PowerShell がまだインストールされていない場合はインストールします。 詳しい手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-Az-ps)」をご覧ください。 

#### <a name="log-in-to-powershell"></a>PowerShell にログインする

1. ご利用のマシンで PowerShell を起動します。このクイックスタート チュートリアルが終わるまで開いたままにしてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

1. 次のコマンドを実行して、Azure Portal へのサインインに使用する Azure ユーザー名とパスワードを入力します。
       
    ```powershell
    Connect-AzAccount
    ```        

1. 複数の Azure サブスクリプションがある場合は、次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンドを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで (`"adfrg"` のように) 指定し、コマンドを実行します。 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ``` 

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

1. 後で PowerShell コマンドで使用できるように、データ ファクトリ名の変数を定義します。 名前は文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意となるように更新してください。 たとえば、ADFTutorialFactorySP1127 とします。 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. データ ファクトリの場所の変数を定義します。 

    ```powershell
    $location = "East US"
    ```  

1. データ ファクトリを作成するために、次の `Set-AzDataFactoryV2` コマンドレットを実行します。 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * データ ファクトリの名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、"*共同作成者*" または "*所有者*" ロールに属しているか、Azure サブスクリプションの "*管理者*" である必要があります。
> * 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください。[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (Azure HDInsight など) は他のリージョンに配置できます。
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

このセクションでは、セルフホステッド統合ランタイムを作成し、SQL Server データベースがあるオンプレミスのマシンに関連付けます。 セルフホステッド統合ランタイムは、マシンの SQL Server データベースから Azure Blob Storage にデータをコピーするコンポーネントです。 

1. 統合ランタイムの名前に使用する変数を作成します。 一意の名前を使用し、その名前を書き留めます。 このチュートリアルの後の方で、それを使用します。 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. セルフホステッド統合ランタイムを作成します。 

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 

    出力例を次に示します。

    ```json
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. 作成された統合ランタイムの状態を取得するために、次のコマンドを実行します。

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    出力例を次に示します。
    
    ```json
    State                     : NeedRegistration
    Version                   : 
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       : 
    UpdateDelayOffset         : 
    LocalTimeZoneOffset       : 
    InternalChannelEncryption : 
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. 次のコマンドを実行して、クラウドの Data Factory サービスにセルフホステッド統合ランタイムを登録するための "*認証キー*" を取得します。 次の手順でマシンにインストールするセルフホステッド統合ランタイムを登録するために、いずれかのキーをコピーします (二重引用符は除外)。 

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```
    
    出力例を次に示します。
    
    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>統合ランタイムのインストール
1. [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) をローカルの Windows マシンにダウンロードし、インストールを実行します。 

1. **[Welcome to Microsoft Integration Runtime Setup]\(Microsoft Integration Runtime セットアップへようこそ\)** ウィザードで **[次へ]** を選択します。  

1. **[使用許諾契約書]** ウィンドウで使用条件とライセンス契約に同意し、 **[次へ]** を選択します。 

1. **[インストール先フォルダー]** ウィンドウで **[次へ]** を選択します。 

1. **[Ready to install Microsoft Integration Runtime]\(Microsoft Integration Runtime のインストール準備完了\)** ウィンドウで **[インストール]** を選択します。 

1. **[Completed the Microsoft Integration Runtime Setup]\(Microsoft Integration Runtime セットアップの完了\)** ウィザードで **[完了]** を選択します。

1. **[Integration Runtime (セルフホステッド) の登録]** ウィンドウで、前のセクションで保存したキーを貼り付け、 **[登録]** を選択します。 

    ![統合ランタイムの登録](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. **[新しい Integration Runtime (セルフホステッド) ノード]** ウィンドウで **[完了]** を選択します。 

    ![[新しい Integration Runtime ノード] ウィンドウ](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. セルフホステッド統合ランタイムが正常に登録されると、次のメッセージが表示されます。 

    ![正常に登録](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. **[Integration Runtime (セルフホステッド) の登録]** ウィンドウで **[構成マネージャーの起動]** を選択します。 

1. ノードがクラウド サービスに接続されると、次のメッセージが表示されます。

    ![ノード接続済み](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. 次の手順に従って SQL Server データベースとの接続をテストします。

    a. **[構成マネージャー]** ウィンドウで、 **[診断]** タブに切り替えます。

    b. **[データ ソースの種類]** ボックスで **[SqlServer]** を選択します。

    c. サーバー名を入力します。

    d. データベース名を入力します。 

    e. 認証モードを選択します。 

    f. ユーザー名を入力します。 

    g. ユーザー名に関連付けられているパスワードを入力します。

    h. 統合ランタイムから SQL Server に接続できることを確認するために、 **[テスト]** を選択します。  
    ![接続成功](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png) 
  
    接続に成功すると、緑色のチェック マーク アイコンが表示されます。 それ以外の場合は、失敗を表すエラー メッセージが表示されます。 問題を修正し、統合ランタイムから SQL Server インスタンスに接続できるようにします。

    前述の値はすべてメモしておいてください。後でこのチュートリアルの中で使用します。
    
## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このチュートリアルでは、Azure Storage アカウントとオンプレミスの SQL Server インスタンスをデータ ストアにリンクします。 リンクされたサービスは、Data Factory サービスが実行時に接続するために使用する接続情報を持っています。 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage のリンクされたサービスを作成する (コピー先/シンク)
この手順では、Azure Storage アカウントをデータ ファクトリにリンクします。

1. 次のコードを記述した *AzureStorageLinkedService.json* という名前の JSON ファイルを *C:\ADFv2Tutorial* フォルダーに作成します。 *ADFv2Tutorial* フォルダーがまだ存在しない場合は作成してください。  

    > [!IMPORTANT]
    > ファイルを保存する前に、\<accountName> と \<accountKey> を実際の Azure Storage アカウントの名前とキーに置き換えてください。 「[前提条件](#get-storage-account-name-and-account-key)」セクションでメモしたものです。

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. PowerShell で *C:\ADFv2Tutorial* フォルダーに切り替えます。
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. リンクされたサービス AzureStorageLinkedService を作成するために、次の `Set-AzDataFactoryV2LinkedService` コマンドレットを実行します。 

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   出力例を次に示します。

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    "ファイルが見つかりません" エラーが表示された場合は、`dir` コマンドを実行してファイルが存在することを確認します。 ファイル名の拡張子が *.txt* となっている場合 (例: AzureStorageLinkedService.json.txt) は、その拡張子を削除してからもう一度 PowerShell コマンドを実行してください。 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server のリンクされたサービスを作成して暗号化する (ソース)
この手順では、オンプレミス SQL Server インスタンスをデータ ファクトリにリンクします。

1. 次のコードを使用して、*SqlServerLinkedService.json* という名前の JSON ファイルを *C:\ADFv2Tutorial* フォルダーに作成します。

    > [!IMPORTANT]
    > SQL Server への接続に使用する認証に該当するセクションを選んでください。

    **SQL 認証を使用する場合 (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Windows 認証を使用する場合:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
    ```

    > [!IMPORTANT]
    > - SQL Server インスタンスへの接続に使用する認証に該当するセクションを選んでください。
    > - **\<integration runtime name>** は、実際の統合ランタイムの名前に置き換えます。
    > - ファイルを保存する前に、 **\<servername>** 、 **\<databasename>** 、 **\<username>** 、 **\<password>** を実際の SQL Server インスタンスの値に置き換えてください。
    > - ユーザー アカウントまたはサーバー名にバックスラッシュ (\\) を使用する必要がある場合は、エスケープ文字 (\\) に続けて入力してください。 たとえば、「*mydomain\\\\myuser*」のように入力します。 

1. 機密データ (ユーザー名、パスワードなど) を暗号化するには、`New-AzDataFactoryV2LinkedServiceEncryptedCredential` コマンドレットを実行します。  
    この暗号化によって、資格情報が Data Protection Application Programming Interface (DPAPI) を使って暗号化されます。 暗号化された資格情報は、セルフホステッド統合ランタイム ノード (ローカル マシン) のローカルに格納されます。 暗号化された資格情報が含まれる出力ペイロードは別の JSON ファイル (この場合は "*encryptedLinkedService.json*") にリダイレクトできます。
    
   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. 次のコマンドを実行します。これによって、EncryptedSqlServerLinkedService が作成されます。

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>データセットを作成する
この手順では、入力データセットと出力データセットを作成します。 これらは、オンプレミスの SQL Server Database から Azure Blob Storage にデータをコピーする操作の入出力データを表します。

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>ソース SQL Server データベースのデータセットを作成する
この手順では、SQL Server データベース インスタンス内のデータを表すデータセットを定義します。 データセットの型は、SqlServerTable です。 前の手順で作成した SQL Server のリンクされたサービスを参照します。 リンクされたサービスは、Data Factory サービスが実行時に SQL Server インスタンスに接続するために使用する接続情報を持っています。 このデータセットは、データが含まれる、データベース内の SQL テーブルを指定します。 このチュートリアルでは、ソース データが含まれている **emp** テーブルです。 

1. 以下のコードを記述した *SqlServerDataset.json* という名前の JSON ファイルを *C:\ADFv2Tutorial* フォルダー内に作成します。  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. データセット SqlServerDataset を作成するには、`Set-AzDataFactoryV2Dataset` コマンドレットを実行します。

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         : 
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Azure Blob Storage (シンク) のデータセットを作成する
この手順では、Azure Blob Storage にコピーされるデータを表すデータセットを定義します。 データセットの型は、AzureBlob です。 このチュートリアルの前の方で作成した Azure Storage のリンクされたサービスを参照します。 

リンクされたサービスは、データ ファクトリが実行時に Azure Storage アカウントに接続するために使用する接続情報を持っています。 このデータセットは、SQL Server データベースのデータのコピー先となる、Azure Storage 内のフォルダーを指定します。 このチュートリアルでは、このフォルダーは *adftutorial/fromonprem* です。ここで、`adftutorial` は BLOB コンテナーであり、`fromonprem` はフォルダーです。 

1. 以下のコードを記述した *AzureBlobDataset.json* という名前の JSON ファイルを *C:\ADFv2Tutorial* フォルダー内に作成します。

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  
    
            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. データセット AzureBlobDataset を作成するには、`Set-AzDataFactoryV2Dataset` コマンドレットを実行します。

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する。
このチュートリアルでは、コピー アクティビティのあるパイプラインを作成します。 コピー アクティビティは、入力データセットとして SqlServerDataset を使用し、出力データセットとして AzureBlobDataset を使用します。 ソースの種類が *SqlSource* に設定され、シンクの種類が *BlobSink* に設定されています。

1. 以下のコードを記述した *SqlServerToBlobPipeline.json* という名前の JSON ファイルを *C:\ADFv2Tutorial* フォルダー内に作成します。

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  
    
                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  
    
            ]
        }
    }
    ```

1. パイプライン SQLServerToBlobPipeline を作成するには、`Set-AzDataFactoryV2Pipeline` コマンドレットを実行します。

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    出力例を次に示します。

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する
SQLServerToBlobPipeline パイプラインの実行を開始し、後で監視できるようパイプライン実行 ID をキャプチャします。

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. パイプライン SQLServerToBlobPipeline の実行状態を継続的にチェックするには、PowerShell で次のスクリプトを実行し、最終的な結果を出力します。

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    サンプル実行の出力結果を次に示します。

    ```JSON
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    : 
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. 次のコマンドを実行して、パイプライン SQLServerToBlobPipeline の実行 ID を取得し、詳細なアクティビティの実行結果をチェックすることができます。 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    サンプル実行の出力結果を次に示します。

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  
    
        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>出力を検証する
このパイプラインは、`adftutorial` BLOB コンテナーに対して *fromonprem* という名前の出力フォルダーを自動的に作成します。 出力フォルダーに *dbo.emp.txt* ファイルがあることを確認してください。 

1. Azure Portal の **adftutorial** コンテナー ウィンドウで **[最新の情報に更新]** を選択して出力フォルダーを表示します。
1. フォルダーの一覧で、`fromonprem` を選択します。 
1. `dbo.emp.txt` という名前のファイルが表示されていることを確認します。

    ![出力ファイル](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Azure Blob Storage 内のある場所から別の場所にデータをコピーするものです。 以下の方法について学習しました。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Azure Storage のリンクされたサービスを作成します。 
> * SQL Server と Azure BLOB のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

Data Factory でサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。

次のチュートリアルに進んで、ソースからコピー先にデータを一括コピーする方法について学習しましょう。

> [!div class="nextstepaction"]
>[データを一括コピーする](tutorial-bulk-copy.md)
