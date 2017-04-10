---
title: "Azure Storage での Azure PowerShell の使用 | Microsoft Docs"
description: "Azure Storage 用の Azure PowerShell コマンドレットを使用して、ストレージ アカウントの作成と管理、BLOB、テーブル、キュー、およびファイルの操作、ストレージ分析の構成と照会、共有アクセス署名の作成を行う方法について説明します。"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 8b9e174523b3d08be20a4d30cc83c11f5bc93cd5
ms.lasthandoff: 03/06/2017

---

# <a name="using-azure-powershell-with-azure-storage"></a>Azure Storage での Azure PowerShell の使用
## <a name="overview"></a>概要
Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。 タスクベースのコマンドライン シェルとスクリプト言語であり、システム管理に特化して設計されています。 PowerShell を使用すると、Azure サービスやアプリケーションの管理を容易に制御して自動化できます。 たとえば、このコマンドレットを使用して、[Azure Portal](https://portal.azure.com) で実行できるタスクと同じタスクを実行できます。

このガイドでは、 [Azure Storage 用コマンドレット](https://msdn.microsoft.com/library/azure/mt269418.aspx) を使用して、Azure Storage でさまざまな開発タスクや管理タスクを実行する方法について説明します。

このガイドは、[Azure Storage](https://azure.microsoft.com/documentation/services/storage/) と [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx) を使った経験がある読者を対象としています。 また、PowerShell と Azure Storage の使用方法を示すための多くのスクリプトを用意しています。 各スクリプトの実行前に、使用する構成に基づいてスクリプト変数を更新する必要があります。

このガイドの最初のセクションでは、Azure Storage と PowerShell について簡単に説明します。 詳細と手順については、「 [Azure Storage で Azure PowerShell を使用するための前提条件](#prerequisites-for-using-azure-powershell-with-azure-storage)」から開始してください。

## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>5 分で始める、Azure Storage と PowerShell の使用
このセクションでは、PowerShell から Azure Storage にアクセスする方法を 5 分で説明します。

**Azure を初めて使用する場合:** Microsoft Azure サブスクリプションと、そのサブスクリプションに関連付けられた Microsoft アカウントを入手してください。 Azure の購入オプションについて詳しくは、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページ、[購入オプション](https://azure.microsoft.com/pricing/purchase-options/)に関するページ、[メンバー プラン](https://azure.microsoft.com/pricing/member-offers/) (MSDN、Microsoft Partner Network、BizSpark、その他の Microsoft プログラムのメンバー向け) に関するページをご覧ください。

Azure サブスクリプションの詳細については、「 [Azure Active Directory (Azure AD) の管理者ロールの割り当て](https://msdn.microsoft.com/library/azure/hh531793.aspx) 」をご覧ください。

**Microsoft Azure アカウントとサブスクリプションを作成済みである場合:**

1. 最新の [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/latest) をダウンロードしてインストールします。
2. Windows PowerShell Integrated Scripting Environment (ISE) の起動: ローカル コンピューターで、 **[スタート]** メニューを表示します。 「 **管理ツール** 」と入力し、クリックして実行します。 **[管理ツール]** ウィンドウで、**[Windows PowerShell ISE]** を右クリックし、**[管理者として実行]** をクリックします。
3. **Windows PowerShell ISE** で、**[ファイル]** > **[新規作成]** の順にクリックして、新しいスクリプト ファイルを作成します。
4. ここでは、Azure Storage にアクセスするための基本的な PowerShell コマンドを示すシンプルなスクリプトを取り上げます。 このスクリプトでは、Azure アカウントをローカルの PowerShell 環境に追加するための Azure アカウント資格情報が最初に求められます。 次に、既定の Azure サブスクリプションが設定され、Azure で新しいストレージ アカウントが作成されます。 さらに、この新しいストレージ アカウントで新しいコンテナーが作成され、既存の画像ファイル (BLOB) がこのコンテナーにアップロードされます。 このスクリプトにより、コンテナー内のすべての BLOB がリストされると、ローカル コンピューターに新しい格納先ディレクトリが作成され、画像ファイルがダウンロードされます。
5. 次のコードのセクションで、**#begin** と **#end** の間のスクリプトを選びます。 Ctrl キーを押しながら C キーを押し、クリップボードにコピーします。

    ```powershell
    # begin
    # Update with the name of your subscription.
    $SubscriptionName = "YourSubscriptionName"
       
    # Give a name to your new storage account. It must be lowercase!
    $StorageAccountName = "yourstorageaccountname"
       
    # Choose "West US" as an example.
    $Location = "West US"
       
    # Give a name to your new container.
    $ContainerName = "imagecontainer"
       
    # Have an image file and a source directory in your local computer.
    $ImageToUpload = "C:\Images\HelloWorld.png"
       
    # A destination directory in your local computer.
    $DestinationFolder = "C:\DownloadImages"
       
    # Add your Azure account to the local PowerShell environment.
    Add-AzureAccount
       
    # Set a default Azure subscription.
    Select-AzureSubscription -SubscriptionName $SubscriptionName –Default
       
    # Create a new storage account.
    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location
       
    # Set a default storage account.
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
       
    # Create a new container.
    New-AzureStorageContainer -Name $ContainerName -Permission Off
       
    # Upload a blob into a container.
    Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload
       
    # List all blobs in a container.
    Get-AzureStorageBlob -Container $ContainerName
       
    # Download blobs from the container:
    # Get a reference to a list of all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName
       
    # Create the destination directory.
    New-Item -Path $DestinationFolder -ItemType Directory -Force  
       
    # Download blobs into the local destination directory.
    $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
       
    # end
    ```

6. **Windows PowerShell ISE**で、Ctrl キーを押しながら V キーを押してスクリプトを貼り付けます。 **[ファイル]** > **[保存]** の順にクリックします。 **[名前を付けて保存]** ダイアログ ウィンドウで、スクリプト ファイルの名前を "mystoragescript" などとして入力します。 [ **Save**] をクリックします。
7. ここで、構成設定に基づいてスクリプト変数を更新する必要があります。 たとえば、 **$SubscriptionName** 変数を自分のサブスクリプションで更新する必要があります。 その他の変数は、スクリプトで指定されたままにすることも、必要に応じて更新することも可能です。
   
   * **$SubscriptionName:** この変数は、自分のサブスクリプション名で更新する必要があります。 次の&3; つの方法のいずれかに従って、サブスクリプションの名前を確認します。
     
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **Windows PowerShell ISE** で、**[ファイル]** > **[新規作成]** の順にクリックして、新しいスクリプト ファイルを作成します。 次のスクリプトを新しいスクリプト ファイルにコピーし、**[デバッグ]** > **[実行/続行]** の順にクリックします。 次のスクリプトでは、Azure アカウントをローカルの PowerShell 環境に追加するための Azure アカウント資格情報が最初に求められ、次に、ローカルの PowerShell セッション接続されているすべてのサブスクリプションが表示されます。 このチュートリアルに従って作業する際に使用するサブスクリプションの名前を書き留めます。
     
    ```powershell
    Add-AzureAccount 
      Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName
    ```

    b. [Azure Portal](https://portal.azure.com) でサブスクリプション名を検索してコピーするには、左側の [ハブ] メニューで **[サブスクリプション]** をクリックします。 このガイドのスクリプトを実行する際に使用するサブスクリプションの名前をコピーします。
     
     ![Azure ポータル](./media/storage-powershell-guide-full/Subscription_Previewportal.png)

    c. [Azure クラシック ポータル](https://manage.windowsazure.com/)でサブスクリプション名を検索してコピーするには、スクロール ダウンしてポータルの左側にある **[設定]** をクリックします。 **[サブスクリプション]** をクリックすると、お客様のサブスクリプションの一覧が表示されます。 このガイドで取り上げられているスクリプトを実行する際に使用するサブスクリプションの名前をコピーします。
     
     ![Azure クラシック ポータル](./media/storage-powershell-guide-full/Subscription_currentportal.png)

   * **$StorageAccountName:** スクリプトの所定の名前を使用するか、ストレージ アカウントの新しい名前を入力します。 **重要:** ストレージ アカウントの名前は、Azure 上で一意である必要があります。 また、小文字にする必要もあります。
   * **$Location:** スクリプトの所定の "West US" を使用するか、その他の Azure の場所 (East US、North Europe など) を選択します。
   * **$ContainerName:** スクリプトの所定の名前を使用するか、コンテナーの新しい名前を入力します。
   * **$ImageToUpload:** ローカル コンピューター上の画像へのパスを入力します。たとえば、"C:\Images\HelloWorld.png" などです。
   * **$DestinationFolder:** Azure Storage からダウンロードしたファイルを格納するローカル ディレクトリへのパスを入力します。たとえば、"C:\DownloadImages" などです。
8. "mystoragescript.ps1" ファイル内のスクリプト変数を更新したら、**[ファイル]** > **[保存]** の順にクリックします。 次に、**[デバッグ]** > **[実行]** の順にクリックするか、**F5** キーを押して、スクリプトを実行します。  

スクリプトを実行すると、ダウンロードした画像ファイルを格納するローカルの格納先フォルダーの準備が整います。 次のスクリーンショットは、この出力の例を示しています。

![BLOB をダウンロードする](./media/storage-powershell-guide-full/Blobdownload.png)

> [!NOTE]
> 「5 分で始める、Azure Storage と PowerShell の使用」のセクションでは、Azure Storage で Azure PowerShell を使用する方法について簡単に説明しました。 詳細と手順については、以下のセクションを参照することをお勧めします。
> 

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Azure Storage で Azure PowerShell を使用するための前提条件
このガイドで取り上げている PowerShell コマンドレットを実行するには、既に説明したように Azure サブスクリプションとアカウントが必要です。

Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。 Azure PowerShell のインストールと設定の詳細については、 [Azure PowerShell のインストールと構成方法](/powershell/azureps-cmdlets-docs)に関するページを参照してください。 このガイドを使用する前に、最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。

コマンドレットは、標準の Windows PowerShell コンソールまたは Windows PowerShell Integrated Scripting Environment (ISE) から実行できます。 たとえば、 **Windows PowerShell ISE**を開くには、[スタート] メニューで、「管理ツール」と入力し、クリックして実行します。 [管理ツール] ウィンドウで、[Windows PowerShell ISE] を右クリックし、[管理者として実行] をクリックします。

## <a name="how-to-manage-storage-accounts-in-azure"></a>Azure でストレージ アカウントを管理する方法

PowerShell を使用した Azure でのストレージ アカウントの管理を見ていきましょう。

### <a name="how-to-set-a-default-azure-subscription"></a>既定の Azure サブスクリプションを設定する方法
Azure PowerShell を使用して Azure Storage を管理するには、Azure Active Directory 認証または証明書ベースの認証を使用して、Azure でのクライアント環境を認証する必要があります。 詳細については、 [Azure PowerShell のインストールと構成方法](/powershell/azureps-cmdlets-docs) に関するページを参照してください。 このガイドでは、Azure Active Directory 認証を使用します。

1. Windows PowerShell ISE で、次のコマンドを入力し、Azure アカウントをローカルの PowerShell 環境に追加します。

    ```powershell
    Add-AzureAccount
    ```

2. [Microsoft Azure へのサインイン] ウィンドウで、アカウントに関連付けられた電子メール アドレスとパスワードを入力します。 Azure により資格情報が認証および保存され、ウィンドウが閉じます。

3. 次のコマンドを実行し、ローカルの PowerShell 環境内の Azure アカウントを表示して、自分のアカウントが含まれていることを確認します。
   
    ```powershell
    Get-AzureAccount
    ```
4. 次のコマンドレットを実行し、ローカルの PowerShell セッションに接続されているすべてのサブスクリプションを表示して、自分のサブスクリプションが含まれていることを確認します。

    ```powershell
    Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`
    ```
5. 既定の Azure サブスクリプションを設定するには、Select-AzureSubscription コマンドレットを実行します。

    ```powershell
    $SubscriptionName = 'Your subscription Name'
    Select-AzureSubscription -SubscriptionName $SubscriptionName –Default
    ```

6. Get-AzureSubscription コマンドレットを実行することで、既定のサブスクリプションの名前を確認します。

    ```powershell
    Get-AzureSubscription -Default
    ```

7. Azure Storage に使用できるすべての PowerShell コマンドレットを確認するには、次を実行します。
    
    ```powershell
    Get-Command -Module Azure -Noun *Storage*`
    ```

### <a name="how-to-create-a-new-azure-storage-account"></a>新しい Azure ストレージ アカウントを作成する方法
Azure Storage を使用するには、ストレージ アカウントが必要です。 コンピューターを構成してサブスクリプションに接続できるようにすると、新しい Azure ストレージ アカウントを作成できます。

1. Get-AzureLocation コマンドレットを実行し、使用できるデータセンターの場所をすべて見つけます。

    ```powershell
    Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes
    ```

2. New-AzureStorageAccount コマンドレットを実行し、新しいストレージ アカウントを作成します。 次の例では、"West US" というデータセンターに新しいストレージ アカウントを作成します。
   
    ```powershell
    $location = "West US"
    $StorageAccountName = "yourstorageaccount"
    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location
    ```

> [!IMPORTANT]
> ストレージ アカウントの名前は Azure 内で一意であり、小文字でなければなりません。 名前付け規則と制限について詳しくは、「[Azure ストレージ アカウントについて](storage-create-storage-account.md)」および「[コンテナー、BLOB、およびメタデータの名前付けおよび参照](http://msdn.microsoft.com/library/azure/dd135715.aspx)」をご覧ください。
> 
> 

### <a name="how-to-set-a-default-azure-storage-account"></a>既定の Azure ストレージ アカウントを設定する方法
サブスクリプションで複数のストレージ アカウントを持つことができます。 それらのいずれかのアカウントを選択し、同じ PowerShell セッションのすべてのストレージ コマンドに対する既定のストレージ アカウントとして設定できます。 そうすることにより、ストレージ コンテキストを明示的に指定しなくても、Azure PowerShell ストレージ コマンドを実行できます。

1. サブスクリプションの既定のストレージ アカウントを設定するために、Set-AzureSubscription コマンドレットを実行します。

    ```powershell
    $SubscriptionName = "Your subscription name"
    $StorageAccountName = "yourstorageaccount"  
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

2. 次に、Get-AzureSubscription コマンドレットを実行し、ストレージ アカウントが既定のサブスクリプション アカウントに関連付けられていることを確認します。 このコマンドは、現在のサブスクリプションにおけるサブスクリプション プロパティを返します。これには、現在のストレージ アカウントが含まれます。

    ```powershell
    Get-AzureSubscription –Current
    ```

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>サブスクリプション内のすべての Azure ストレージ アカウントをリストする方法
Azure サブスクリプションごとに最大 100 個のストレージ アカウントを作成できます。 制限に関する最新情報については、 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)に関するページを参照してください。

次のコマンドレットを実行し、現在のサブスクリプションのストレージ アカウントの名前とステータスを確認します。

```powershell
Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus
```

### <a name="how-to-create-an-azure-storage-context"></a>Azure ストレージ コンテキストを作成する方法
Azure ストレージ コンテキストは、ストレージ資格情報をカプセル化するための PowerShell 内のオブジェクトです。 後続のコマンドレットを実行する際にストレージ コンテキストを使用すると、ストレージ アカウントとそのアクセス キー明示的に指定しなくても、要求を認証できます。 ストレージ コンテキストは、ストレージ アカウント名とアクセス キーを使用する、Shared Access Signature (SAS) トークンを使用する、接続文字列または匿名を使用するなどのさまざまな方法で作成できます。 詳細については、「 [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx)」を参照してください。  

次の&3; つの方法のいずれかを使用してストレージ コンテキストを作成します。

* [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) コマンドレットを実行し、Azure ストレージ アカウントのプライマリ ストレージ アクセス キーを確認します。 次に、 [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) コマンドレットを呼び出し、ストレージ コンテキストを作成します。

    ```powershell
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    ```

* Azure ストレージ コンテナーの Shared Access Signature トークンを生成し、これを使用してストレージ コンテキストを作成します。

    ```powershell
    $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken
    ```

    詳しくは、「[New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx)」および「[Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。

* 場合によっては、新しいストレージ コンテキストの作成時にサービスのエンドポイントを指定することもあります。 これは、BLOB サービスでストレージ アカウントのカスタム ドメイン名を登録した場合に必要になることがあります。または、アクセスしているストレージ リソースに対して Shared Access Signature を使用します。 次に示すように、接続文字列でサービスのエンドポイントを設定し、このエンドポイントを使用して新しいストレージ コンテキストを作成します。

    ```powershell
    $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString
    ```

ストレージ接続文字列を構成する方法の詳細については、 [接続文字列の構成](storage-configure-connection-string.md)に関するページを参照してください。

これで、コンピューターを設定できました。また、Azure PowerShell を使用してサブスクリプションとストレージ アカウントを管理する方法を確認できました。次のセクションでは、Azure BLOB と BLOB スナップショットを管理する方法について説明します。

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Azure Storage キーを取得および再生成する方法
Azure Storage アカウントには&2; つのアカウント キーがあります。 次のコマンドレットを使用すると、ご自身のキーを取得できます。

```powershell
Get-AzureStorageKey -StorageAccountName "yourstorageaccount"
```

特定のキーを取得するには、次のコマンドレットを使用します。 有効な値は Primary と Secondary です。  

```powershell
(Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
    
(Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary
```

キーを再生成するには、次のコマンドレットを使用します。 -KeyType の有効な値は "Primary" と "Secondary" です

```powershell
New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”
    
New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”
```

## <a name="how-to-manage-azure-blobs"></a>Azure BLOB の管理方法
Azure Blob Storage は、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化データを格納するためのサービスです。 このセクションでは、Azure BLOB Storage サービスの概念について理解しているユーザーを対象としています。 詳しくは、「[.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)」および「[BLOB サービスの概念](http://msdn.microsoft.com/library/azure/dd179376.aspx)」をご覧ください。

### <a name="how-to-create-a-container"></a>コンテナーを作成する方法
Azure Storage のすべての BLOB はコンテナーに格納する必要があります。 New-AzureStorageContainer コマンドレットを使用して、プライベート コンテナーを作成できます。

```powershell
$StorageContainerName = "yourcontainername"
New-AzureStorageContainer -Name $StorageContainerName -Permission Off
```

> [!NOTE]
> **Off**、**Blob**、**Container** という&3; つのレベルの匿名読み取りアクセスがあります。 BLOB に対する匿名アクセスを許可しない場合は、Permission パラメーターを **Off**に設定します。 既定では、新しいコンテナーはプライベートであり、アカウント所有者のみがアクセスできます。 BLOB リソースに対する匿名パブリック読み取りアクセスを許可するが、コンテナー メタデータまたはコンテナー内の BLOB の一覧に対するアクセスは許可しない場合は、Permission パラメーターを **BLOB**に設定します。 BLOB リソース、コンテナー メタデータ、コンテナー内の BLOB の一覧に対する完全パブリック読み取りアクセスを許可する場合は、Permission パラメーターを **Container**に設定します。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](storage-manage-access-to-resources.md)」を参照してください。
> 
> 

### <a name="how-to-upload-a-blob-into-a-container"></a>BLOB をコンテナーにアップロードする方法
Azure Blob Storage では、ブロック BLOB とページ BLOB がサポートされています。 詳細については、「 [ブロック BLOB、追加 BLOB、ページ BLOB について](http://msdn.microsoft.com/library/azure/ee691964.aspx)」を参照してください。

BLOB をコンテナーにアップロードする場合は、 [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) コマンドレットを使用します。 既定では、このコマンドにより、ローカル ファイルがブロック BLOB にアップロードされます。 BLOB の種類を指定する場合は、-BlobType パラメーターを使用します。

次の例では、 [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) コマンドレットを実行して、指定したフォルダー内のすべてのファイルを取得し、パイプライン演算子を使用することでそれらを次のコマンドレットに渡します。 [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) コマンドレットは、ローカル ファイルをコンテナーにアップロードします。

```powershell
Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"
```

### <a name="how-to-download-blobs-from-a-container"></a>BLOB をコンテナーからダウンロードする方法
次の例は、BLOB をコンテナーからダウンロードする方法を示しています。 この例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのプライマリ アクセス キーが含まれます。 次に、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを使って、BLOB の参照を取得します。 さらに、[Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) コマンドレットを使って、BLOB をローカルの格納先フォルダーにダウンロードします。

```powershell
#Define the variables.
$ContainerName = "yourcontainername"
$DestinationFolder = "C:\DownloadImages"

#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#List all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

#Download blobs from a container.
New-Item -Path $DestinationFolder -ItemType Directory -Force
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx
```

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>あるストレージ コンテナーから別のストレージ コンテナーに BLOB をコピーする方法
BLOB は、ストレージ アカウントとリージョン間にまたがって非同期的にコピーできます。 次の例は、2 つの異なるストレージ アカウントで、BLOB をあるストレージ コンテナーから別のストレージ コンテナーにコピーする方法を示しています。 この例では、まずソースとコピー先のストレージ アカウントの変数を設定し、次にそれぞれのアカウントのストレージ コンテキストを作成します。 さらに、 [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) コマンドレットを使用して、BLOB をソース コンテナーからコピー先のコンテナーにコピーします。 この例では、ソースとコピー先のストレージ アカウントとコンテナーが既に存在していると想定しています。

```powershell
#Define the source storage account and context.
$SourceStorageAccountName = "yoursourcestorageaccount"
$SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
$SrcContainerName = "yoursrccontainername"
$SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

#Define the destination storage account and context.
$DestStorageAccountName = "yourdeststorageaccount"
$DestStorageAccountKey = "Storage key for yourdeststorageaccount"
$DestContainerName = "destcontainername"
$DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

#Get a reference to blobs in the source container.
$blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

#Copy blobs from one container to another.
$blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext
```

この例で実行するのは非同期コピーです。 [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) コマンドレットを実行すると、各コピーの状態を監視できます。

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>セカンダリの場所から BLOB をコピーする方法
RA-GRS が有効になっているアカウントのセカンダリの場所から BLOB をコピーすることができます。

```powershell
#define secondary storage context using a connection string constructed from secondary endpoints.
$SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext
```

### <a name="how-to-delete-a-blob"></a>BLOB を削除する方法
BLOB を削除するには、まず BLOB の参照を取得し、次にその Remove-AzureStorageBlob コマンドレットを呼び出します。 次の例では、特定のコンテナー内のすべての BLOB を削除します。 この例では、まずストレージ アカウントの変数を設定し、次にストレージ コンテキストを作成します。 さらに、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを使って BLOB の参照を取得し、[Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) コマンドレットを実行して Azure Storage のコンテナーから BLOB を削除します。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$ContainerName = "containername"
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#Get a reference to all the blobs in the container.
$blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

#Delete blobs in a specified container.
$blobs| Remove-AzureStorageBlob
```

## <a name="how-to-manage-azure-blob-snapshots"></a>Azure BLOB のスナップショットを管理する方法
Azure では BLOB のスナップショットを作成できます。 スナップショットは、ある時点で作成された読み取り専用の BLOB です。 作成されたスナップショットは、読み取り、コピー、削除はできますが、変更はできません。 スナップショットを使用すると、BLOB をある時点での表示内容のままバックアップできます。 詳細については、「 [BLOB のスナップショットの作成](http://msdn.microsoft.com/library/azure/hh488361.aspx)」を参照してください。

### <a name="how-to-create-a-blob-snapshot"></a>BLOB のスナップショットを作成する方法
BLOB のスナップショットを作成するには、まず BLOB の参照を取得し、次にその `ICloudBlob.CreateSnapshot` メソッドを呼び出します。 次の例では、まずストレージ アカウントの変数を設定し、次にストレージ コンテキストを作成します。 さらに、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレッドを使って BLOB の参照を取得し、[ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) メソッドを実行してスナップショットを作成します。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$ContainerName = "yourcontainername"
$BlobName = "yourblobname"
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#Get a reference to a blob.
$blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

#Create a snapshot of the blob.
$snap = $blob.ICloudBlob.CreateSnapshot()
```

### <a name="how-to-list-a-blobs-snapshots"></a>BLOB のスナップショットをリストする方法
1 個の BLOB に対して必要な数だけスナップショットを作成できます。 BLOB に関連付けられたスナップショットをリストして、現在のスナップショットを追跡できます。 次の例では、事前定義済みの BLOB を使用し、 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを呼び出して、その BLOB のスナップショットをリストします。  

```powershell
#Define the blob name.
$BlobName = "yourblobname"

#List the snapshots of a blob.
Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }
```

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>BLOB のスナップショットをコピーする方法
BLOB のスナップショットをコピーして復元できます。 詳細と制限については、「 [BLOB のスナップショットの作成](http://msdn.microsoft.com/library/azure/hh488361.aspx)」を参照してください。 次の例では、まずストレージ アカウントの変数を設定し、次にストレージ コンテキストを作成します。 さらに、コンテナーと BLOB 名変数を定義します。 また、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを使って BLOB の参照を取得し、[ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) メソッドを実行してスナップショットを作成します。 さらに、 [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) コマンドレットを実行し、ソース BLOB に対して ICloudBlob オブジェクトを使用して BLOB のスナップショットをコピーします。 この例を実行する前に、必ず構成に基づいて変数を更新してください。 次の例では、ソースとコピー先のコンテナーとソース BLOB が既に存在していると想定しています。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#Define the variables.
$SrcContainerName = "yoursourcecontainername"
$DestContainerName = "yourdestcontainername"
$SrcBlobName = "yourblobname"
$DestBlobName = "CopyBlobName"

#Get a reference to a blob.
$blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

#Create a snapshot of a blob.
$snap = $blob.ICloudBlob.CreateSnapshot()

#Copy the snapshot to another container.
Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName
```

これで、Azure PowerShell を使用して Azure BLOB と BLOB スナップショットを管理できるようになりました。次のセクションでは、テーブル、キュー、およびファイルを管理する方法について説明します。

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Azure テーブルとテーブル エンティティを管理する方法
Azure Table Storage サービスは NoSQL データストアであり、これを使用することで、構造化された非リレーショナル データの巨大なセットを格納して照会できます。 このサービスのメイン コンポーネントは、テーブル、エンティティ、プロパティです。 テーブルは、エンティティのコレクションです。 エンティティは、プロパティのセットです。 各エンティティには、最大 252 個のプロパティを含めることができます。これらはすべて名前と値のペアです。 このセクションでは、Azure Table Storage サービスの概念について理解しているユーザーを対象としています。 詳しくは、「[テーブル サービス データ モデルについて](http://msdn.microsoft.com/library/azure/dd179338.aspx)」および「[.NET を使用して Azure Table Storage を使用する](storage-dotnet-how-to-use-tables.md)」をご覧ください。

以下のサブセクションでは、Azure PowerShell を使用して Azure Table Storage サービスを管理する方法について説明します。 **テーブル**の**作成**、**削除**、**取得**、**テーブル エンティティの追加**、**照会**、**削除**の各シナリオについて説明します。

### <a name="how-to-create-a-table"></a>テーブルの作成方法
各テーブルは、Azure ストレージ アカウント内に存在します。 次の例は、Azure Storage にテーブルを作成する方法を示しています。 この例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) コマンドレットを使って、Azure Storage にテーブルを作成します。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

#Create a new table.
$tabName = "yourtablename"
New-AzureStorageTable –Name $tabName –Context $Ctx
```

### <a name="how-to-retrieve-a-table"></a>テーブルを取得する方法
あるストレージ アカウント内の&1; 個またはすべてのテーブルを照会して取得できます。 次の例は、 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使用して特定のテーブルを取得する方法を示しています。

```powershell
#Retrieve a table.
$tabName = "yourtablename"
Get-AzureStorageTable –Name $tabName –Context $Ctx
```

パラメーターなしで Get-AzureStorageTable コマンドレットを呼び出した場合は、ストレージ アカウントのすべてのストレージ テーブルを取得します。

### <a name="how-to-delete-a-table"></a>テーブルを削除する方法
[Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) コマンドレットを使用すると、ストレージ アカウントからテーブルを削除できます。  

```powershell
#Delete a table.
$tabName = "yourtablename"
Remove-AzureStorageTable –Name $tabName –Context $Ctx
```

### <a name="how-to-manage-table-entities"></a>テーブル エンティティを管理する方法
現在、Azure PowerShell では、テーブル エンティティを直接管理するコマンドレットは用意されていません。 テーブル エンティティに対する操作を実行するには、 [Azure Storage Client Library for .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)で指定されているクラスを使用します。

#### <a name="how-to-add-table-entities"></a>テーブル エンティティを追加する方法
テーブルにエンティティを追加するには、まず、エンティティのプロパティを定義するオブジェクトを作成します。 1 個のエンティティは、最大 255 個のプロパティを含むことができます。これには、**PartitionKey**、**RowKey**、**Timestamp** の 3 個のシステム プロパティも含まれます。 **PartitionKey** と **RowKey** の値を挿入または更新することはユーザーが担当します。 一方、サーバーは **Timestamp** の値を管理します。この値は変更できません。 **PartitionKey** と **RowKey** が組み合わさって、テーブル内の各エンティティを一意に識別します。

* **PartitionKey**: エンティティが格納されるパーティションを決定します。
* **RowKey**: パーティション内のエンティティを一意に識別します。

1 個のエンティティに対して最大 252 個のカスタム プロパティを定義できます。 詳細については、「 [Table サービス データ モデルについて](http://msdn.microsoft.com/library/azure/dd179338.aspx)」を参照してください。

次の例は、エンティティをテーブルに追加する方法を示しています。 この例では、employee テーブルを取得して、そこに複数のエンティティを追加する方法を取り上げます。 まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使って特定のテーブルを取得します。 このテーブルが存在しない場合は、Azure Storage にテーブルを作成するために [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) コマンドレットが使われます。 次に、各エンティティのパーティションと行キーを指定することでテーブルにエンティティを追加する、カスタム関数 Add-Entity が定義されます。 この Add-Entity 関数は、[Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) クラスで [New-Object](http://technet.microsoft.com/library/hh849885.aspx) コマンドレットを呼び出し、エンティティ オブジェクトを作成します。 その後、このエンティティ オブジェクトで [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) メソッドを呼び出して、テーブルに追加します。

```powershell
#Function Add-Entity: Adds an employee entity to a table.
function Add-Entity() {
    [CmdletBinding()]
    param(
       $table,
       [String]$partitionKey,
       [String]$rowKey,
       [String]$name,
       [Int]$id
    )

  $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
  $entity.Properties.Add("Name", $name)
  $entity.Properties.Add("ID", $id)

  $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
}

#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
$TableName = "Employees"

#Retrieve the table if it already exists.
$table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

#Create a new table if it does not exist.
if ($table -eq $null)
{
   $table = New-AzureStorageTable –Name $TableName -Context $Ctx
}

#Add multiple entities to a table.
Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4
```

#### <a name="how-to-query-table-entities"></a>テーブル エンティティを照会する方法
テーブルを照会するには、[Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) クラスを使います。 次の例では、このガイドのエンティティの追加方法に関するセクションで指定されたスクリプトを実行済みであると想定しています。 この例では、まず、ストレージ コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使って、作成済みの Employees テーブルの取得を試みます。 Microsoft.WindowsAzure.Storage.Table.TableQuery クラスで [New-Object](http://technet.microsoft.com/library/hh849885.aspx) コマンドレットを呼び出すと、新しいクエリ オブジェクトが作成されます。 そして、文字列フィルターで指定されたとおりの 1 を値とする "ID" 列があるエンティティを見つけます。 詳しくは、「[テーブルおよびエンティティのクエリ](http://msdn.microsoft.com/library/azure/dd894031.aspx)」をご覧ください。 このクエリを実行すると、フィルター条件に一致するすべてのエンティティが返されます。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
$TableName = "Employees"

#Get a reference to a table.
$table = Get-AzureStorageTable –Name $TableName -Context $Ctx

#Create a table query.
$query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

#Define columns to select.
$list = New-Object System.Collections.Generic.List[string]
$list.Add("RowKey")
$list.Add("ID")
$list.Add("Name")

#Set query details.
$query.FilterString = "ID gt 0"
$query.SelectColumns = $list
$query.TakeCount = 20

#Execute the query.
$entities = $table.CloudTable.ExecuteQuery($query)

#Display entity properties with the table format.
$entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize
```

#### <a name="how-to-delete-table-entities"></a>テーブル エンティティを削除する方法
パーティション キーと行キーを使用してエンティティを削除できます。 次の例では、このガイドのエンティティの追加方法に関するセクションで指定されたスクリプトを実行済みであると想定しています。 この例では、まず、ストレージ コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使って、作成済みの Employees テーブルの取得を試みます。 このテーブルが存在する場合は、[Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) メソッドを呼び出し、パーティションと行キーの値に基づいてエンティティを取得します。 次に、このエンティティを [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) メソッドに渡して削除します。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

#Retrieve the table.
$TableName = "Employees"
$table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

#If the table exists, start deleting its entities.
if ($table -ne $null) 
{
    #Together the PartitionKey and RowKey uniquely identify every  
    #entity within a table.
    $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
    $entity = $tableResult.Result
    if ($entity -ne $null)
    {
        #Delete the entity.
        $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
}
```

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Azure キューとキュー メッセージを管理する方法
Azure Queue Storage は、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 このセクションでは、Azure Queue Storage サービスの概念について理解しているユーザーを対象としています。 詳細については、「 [.NET を使用して Azure Queue Storage を使用する](storage-dotnet-how-to-use-queues.md)」を参照してください。

このセクションでは、Azure PowerShell を使用して Azure Queue Storage サービスを管理する方法を取り上げます。 キュー メッセージの**挿入**と**削除**、および**キューの作成**、**削除**、**取得**の各シナリオについて説明します。

### <a name="how-to-create-a-queue"></a>キューの作成方法
次の例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) コマンドレットを呼び出して、"queuename" という名前のキューを作成します。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
$QueueName = "queuename"
$Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx
```

Azure Queue サービスでの名前付け規則の詳細については、「 [キューおよびメタデータの名前付け](http://msdn.microsoft.com/library/azure/dd179349.aspx)」を参照してください。

### <a name="how-to-retrieve-a-queue"></a>キューを取得する方法
あるストレージ アカウント内の特定のキューまたはすべてのキューの一覧を照会して取得できます。 次の例は、 [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) コマンドレットを使用して特定のキューを取得する方法を示しています。

```powershell
#Retrieve a queue.
$QueueName = "queuename"
$Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx
```

パラメーターなしで [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) コマンドレットを呼び出した場合は、すべてのキューの一覧を取得します。

### <a name="how-to-delete-a-queue"></a>キューを削除する方法
キューとキューに含まれるすべてのメッセージを削除するには、Remove-AzureStorageQueue コマンドレットを呼び出します。 次の例は、Remove-AzureStorageQueue コマンドレットを使用して特定のキューを削除する方法を示しています。

```powershell
#Delete a queue.
$QueueName = "yourqueuename"
Remove-AzureStorageQueue –Name $QueueName –Context $Ctx
```

#### <a name="how-to-insert-a-message-into-a-queue"></a>メッセージをキューに挿入する方法
既存のキューにメッセージを挿入するには、まず [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) クラスの新しいインスタンスを作成します。 次に、 [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) メソッドを呼び出します。 CloudQueueMessage は、文字列 (UTF-8 形式) またはバイト配列から作成できます。

次の例は、メッセージをキューに追加する方法を示しています。 この例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) コマンドレットを使って、特定のキューを取得します。 このキューが存在する場合は、[New-Object](http://technet.microsoft.com/library/hh849885.aspx) コマンドレットを使って、[Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) クラスのインスタンスが作成されます。 その後、このメッセージ オブジェクトで [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) メソッドを呼び出して、キューに追加します。 次のコードでは、キューを取得し、メッセージ "MessageInfo" を挿入します。

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

#Retrieve the queue.
$QueueName = "queuename"
$Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

#If the queue exists, add a new message.
if ($Queue -ne $null) {
   # Create a new message using a constructor of the CloudQueueMessage class.
   $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

   # Add a new message to the queue.
   $Queue.CloudQueue.AddMessage($QueueMessage)
}
```

#### <a name="how-to-de-queue-at-the-next-message"></a>次のメッセージでデキューする方法
コードでは、2 つの手順でキューからメッセージをデキューします。 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) メソッドを呼び出すと、キュー内の次のメッセージが取得されます。 **GetMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 キューからのメッセージの削除を完了するには、 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) メソッドも呼び出す必要があります。 このようにメッセージを&2; つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 コードでは、メッセージが処理された直後に **DeleteMessage** を呼び出します。

```powershell
# Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

# Retrieve the queue.
$QueueName = "queuename"
$Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

$InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Get the message object from the queue.
$QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
# Delete the message.
$Queue.CloudQueue.DeleteMessage($QueueMessage)
```

## <a name="how-to-manage-azure-file-shares-and-files"></a>Azure ファイル共有とファイルを管理する方法
Azure File Storage は、標準的な SMB プロトコルを使用して、アプリケーション用の共有ストレージを提供します。 Microsoft Azure の仮想マシンとクラウド サービスでは、マウントされている共有を介して、アプリケーション コンポーネント間でファイル データを共有できます。オンプレミスのアプリケーションでは、File Storage API または Azure PowerShell を介して、共有内のファイル データにアクセスできます。

Azure File ストレージについて詳しくは、「[Windows で Azure File Storage を使用する](storage-dotnet-how-to-use-files.md)」と「[ファイル サービス REST API](http://msdn.microsoft.com/library/azure/dn167006.aspx)」をご覧ください。

## <a name="how-to-set-and-query-storage-analytics"></a>ストレージ分析を設定して照会する方法
[Azure Storage Analytics](storage-analytics.md) を使って、Azure ストレージ アカウントからメトリックを収集し、ストレージ アカウントに送信される要求に関するログ データを収集することができます。 ストレージ メトリックを使用すると、ストレージ アカウントの正常性を監視でき、ストレージ ログを使用すると、ストレージ アカウントに関する問題の診断とトラブルシューティングができます。 Azure Portal または Windows PowerShell を使用して監視を構成できます。また、ストレージ クライアント ライブラリを使用したプログラムで監視を構成することもできます。 ストレージ ログはサーバー側で発生し、ストレージ アカウント内の成功した要求と失敗した要求の両方について詳細を記録することが可能になります。 これらのログを使用すると、読み取り、書き込み、削除の各操作の詳細、失敗した要求の原因を確認できます。

PowerShell を使用し、ストレージ メトリックのデータを有効にして表示する方法については、「 [PowerShell を利用してストレージ メトリックを有効にする方法](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell)」を参照してください。

PowerShell を使用してストレージ ログを有効にする方法とログ データを取得する方法については、「[PowerShell を使用してストレージ ログを有効にする方法](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell)」および「[ストレージ ログのログ データの検索](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata)」をご覧ください。
ストレージ メトリックとストレージ ログを使用してストレージの問題のトラブルシューティングを行う方法の詳細については、 [Microsoft Azure Storage の監視、診断、トラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)に関するページを参照してください。

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Shared Access Signature (SAS) や格納されているアクセス ポリシーの選択を管理する方法
共有アクセス署名は、Azure Storage を使用するあらゆるアプリケーションのセキュリティ モデルの重要な部分となります。 アカウント キーを知らせずに、ストレージ アカウントへの制限付きアクセス許可をクライアントに付与する場合に便利です。 既定では、ストレージ アカウントの所有者のみがそのアカウントを使って BLOB、テーブル、キューなどにアクセスできます。 サービスやアプリケーションで、アクセス キーを共有せずに他のクライアントでそれらのリソースを使えるようにするには、次の&3; つの方法があります。

* 匿名ユーザーにコンテナーや BLOB への読み込みアクセスを許可するようにコンテナーのアクセス許可を設定します。 これはテーブルやキューでは許可されません。
* Shared Access Signature を使用して、特定の期間のコンテナー、BLOB、キュー、テーブルへの制限付きアクセス権を付与します。
* 保存されたアクセス ポリシーを使用して、コンテナー、BLOB、キュー、テーブルに対する共有アクセス署名の制御を追加します。 保存されたアクセス ポリシーによって、開始時刻、有効期限、署名の許可の変更や、署名が発行された後の取り消しが行えるようになります。

Shared Access Signature の形式は、次の&2; つのいずれかです。

* **アドホック SAS**: アドホック SAS を作成すると、開始時刻、有効期限、SAS へのアクセス許可がすべて、SAS URI で指定されます。 この種類の SAS は、コンテナー、BLOB、テーブル、キューで作成できます。これは取り消しできません。
* **保存されているアクセス ポリシーのある SAS:**保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナー、テーブル、またはキュー) で定義されており、これを使用して、1 つ以上の共有アクセス署名のコンテナーを管理できます。 保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。 この種類の SAS は、取り消しできません。

詳しくは、「[Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」と「[コンテナーと BLOB への匿名読み取りアクセスを管理する](storage-manage-access-to-resources.md)」をご覧ください。

次のセクションでは、Azure テーブルに対する Shared Access Signature トークンと保存されたアクセス ポリシーを作成する方法を学習します。 Azure PowerShell では、コンテナー、BLOB、キューに対して類似するコマンドレットが提供されます。 このセクションのスクリプトを実行するには、 [Azure PowerShell Version 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) 以降のバージョンをダウンロードしてください。

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>ポリシー ベースの Shared Access Signature トークンを作成する方法
New-AzureStorageTableStoredAccessPolicy コマンドレットを使用して新しい保存アクセス ポリシーを作成します。 次に、 [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) コマンドレットを呼び出し、Azure Storage テーブルに対する新しいポリシー ベースの共有アクセス署名トークンを作成します。

```powershell
$policy = "policy1"
New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx
```

### <a name="how-to-create-an-ad-hoc-non-revocable-shared-access-signature-token"></a>アドホック (取り消し不可) の Shared Access Signature トークンを作成する方法
次のように [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) コマンドレットを使用して、Azure Storage テーブルに対する新しいアドホック (取り消し不可) の Shared Access Signature トークンを作成します。

```powershell
New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx
```
    
### <a name="how-to-create-a-stored-access-policy"></a>保存されているアクセス ポリシーを作成する方法
次のように New-AzureStorageTableStoredAccessPolicy コマンドレットを使用して、Azure ストレージ テーブルの新しく保存されているアクセス ポリシーを作成します。

```powershell
$policy = "policy1"
New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
```
    
### <a name="how-to-update-a-stored-access-policy"></a>保存されているアクセス ポリシーを更新する方法
次のように Set-AzureStorageTableStoredAccessPolicy コマンドレットを使用して、Azure ストレージ テーブルの既存の保存されているアクセス ポリシーを更新します。

```powershell
Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx
```

### <a name="how-to-delete-a-stored-access-policy"></a>保存されているアクセス ポリシーを削除する方法
次のように Remove-AzureStorageTableStoredAccessPolicy コマンドレットを使用して、Azure ストレージ テーブルの保存されているアクセス ポリシーを削除します。

```powershell
Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx
```

## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>米国政府対応の Azure Storage や Azure China を使用する方法
[米国政府対応の Azure Government](https://azure.microsoft.com/features/gov/)、[グローバル Azure 対応の AzureCloud](https://portal.azure.com)、[中国の 21Vianet が運営する Azure 対応の AzureChinaCloud](http://www.windowsazure.cn/) など、Microsoft Azure ではそれぞれ独立した環境をサポートしています。 米国政府対応の新しい Azure 環境や Azure China をデプロイできます。

AzureChinaCloud で Azure Storage を使用するには、AzureChinaCloud に関連付けられたストレージ コンテキストを作成する必要があります。 次の手順に従って開始します。

1. [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) コマンドレットを実行し、使用できる Azure 環境を確認します。
   
    ```powershell
    Get-AzureEnvironment
    ```

2. Azure China のアカウントを Windows PowerShell に追加します。
   
    ```powershell
    Add-AzureAccount –Environment AzureChinaCloud
    ```

3. AzureChinaCloud アカウントのストレージ コンテキストを作成します。
   
    ```powershell
    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud
    ```

[U.S.Azure Government](https://azure.microsoft.com/features/gov/) で Azure Storage を使用するには、新しい環境を定義し、その環境で新しいストレージ コンテキストを作成する必要があります。

1. [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) コマンドレットを実行し、使用できる Azure 環境を確認します。

    ```powershell
    Get-AzureEnvironment
    ```

2. Azure 米国政府のアカウントを Windows PowerShell に追加します。
   
    ```powershell
    Add-AzureAccount –Environment AzureUSGovernment
    ```

3. AzureUSGovernment アカウントのストレージ コンテキストを作成します。
   
    ```powershell
    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment
    ```
     
詳細については、次を参照してください。

* [Microsoft Azure Government 開発者向けガイド](../azure-government-developer-guide.md)
* [Azure in China アプリケーションに関する開発者用メモ](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>次のステップ
このガイドでは、Azure PowerShell を使用して Azure Storage を管理する方法を確認しました。 詳細についての関連記事とリソースがあります。

* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Storage の PowerShell コマンドレット](http://msdn.microsoft.com/library/azure/dn806401.aspx)
* [Windows PowerShell リファレンス](https://msdn.microsoft.com/library/ms714469.aspx)

[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next

