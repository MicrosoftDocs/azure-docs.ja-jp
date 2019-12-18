---
title: Visual Studio を使用して Azure Table Storage の利用を開始する (ASP.NET)
description: Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio の ASP.NET プロジェクトで Azure Table Storage の使用を開始する方法について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ec391db0e18128ad85d3bacb79fa51770aa70ea1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298878"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Azure Table Storage と Visual Studio 接続済みサービスの概要 (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概要

Azure Table Storage を使用すると、大量の構造化データを格納できるようになります。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。

このチュートリアルでは、Azure Table Storage エンティティを使用していくつかの一般的なシナリオの ASP.NET コードを記述する方法を示します。 紹介するシナリオは、テーブルの作成、テーブル エンティティの追加、クエリの実行、削除などです。 

## <a name="prerequisites"></a>前提条件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage アカウント](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC コントローラーを作成する 

1. **ソリューション エクスプローラー**で**コントローラー**を右クリックし、コンテキスト メニューで **[追加]、[コントローラー]** の順に選択します。

    ![ASP.NET MVC アプリケーションへのコントローラーの追加](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. **[スキャフォールディングの追加]** ダイアログ ボックスで **[MVC 5 コントローラー - 空]** を選択し、 **[追加]** を選択します。

    ![MVC コントローラーの種類を指定する](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. **[コントローラーの追加]** ダイアログで、コントローラーに *TablesController* という名前を付けて、 **[追加]** を選択します。

    ![MVC コントローラー指定](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. 次の *using* ディレクティブを `TablesController.cs` ファイルに追加します。

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>モデル クラスを作成する

この記事の多くの例では、**TableEntity** から派生した **CustomerEntity** という名前のクラスを使用しています。 以下の手順では、このクラスをモデル クラスとして宣言する方法について説明します。

1. **ソリューション エクスプローラー**で **[モデル]** を右クリックし、コンテキスト メニューで **[追加]、[クラス]** の順に選択します。

1. **[新しい項目の追加]** ダイアログ ボックスで、クラスに **CustomerEntity**という名前を付けます。

1. `CustomerEntity.cs` ファイルを開き、次の **using** ディレクティブを追加します。

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. このクラスを変更して、終了時に次のコードのようにクラスを宣言します。 このクラスでは、ユーザーの名を行キー、姓をパーティション キーとして使用する **CustomerEntity** という名前のエンティティ クラスを宣言します。

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>テーブルを作成する

次の手順では、テーブルの作成方法を説明します。

> [!NOTE]
> 
> このセクションでは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。 

1. `TablesController.cs` ファイルを開きます。

1. **ActionResult** を返す **CreateTable** というメソッドを追加します。

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **CreateTable** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service クライアントを表す **CloudTableClient** オブジェクトを取得します。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 目的のテーブル名への参照を表す **CloudTable** オブジェクトを取得します。 **CloudTableClient.GetTableReference** メソッドでは、Table Storage ストレージに対する要求は行われません。 テーブルが存在するかどうかにかかわらず、参照が返されます。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. テーブルがまだない場合は、**CloudTable.CreateIfNotExists** メソッドを呼び出してテーブルを作成します。 **CloudTable.CreateIfNotExists** は、テーブルが存在しないため正常に作成された場合 **true** を返します。 それ以外の場合は、**false** が返されます。    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. テーブル名で **ViewBag** を更新します。

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開して **[テーブル]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**CreateTable**」と入力し、 **[追加]** を選択します。

1. `CreateTable.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. アプリケーションを実行して **[テーブルの作成]** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![[テーブルの作成]](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    前述したように、**CloudTable.CreateIfNotExists** メソッドは、テーブルが存在しないため作成された場合にのみ **true** を返します。 そのため、テーブルが存在するときにアプリを実行した場合、メソッドは **false** を返します。 アプリを複数回実行するには、アプリを再実行する前にテーブルを削除する必要があります。 テーブルの削除は、**CloudTable.Delete** メソッドを使用して行うことができます。 また、[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) または [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用して削除することもできます。  

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する

*エンティティ*は、**TableEntity** から派生するカスタム クラスを使用して C\# オブジェクトにマップされます。 エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。 このセクションでは、ユーザーの名を行キー、姓をパーティション キーとしてそれぞれ使用するエンティティ クラスを定義する方法について説明します。 エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速にクエリを実行できます。一方、多様なパーティション キーを使用すると、並列操作のスケーラビリティが向上します。 Table service に格納するプロパティはすべて、設定値と取得値の両方を公開する、サポートされている型のパブリック プロパティである必要があります。
エンティティ クラスでは、パブリックでパラメーターのないコンストラクターを宣言する*必要があります*。

> [!NOTE]
> 
> このセクションでは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。

1. `TablesController.cs` ファイルを開きます。

1. `TablesController.cs` ファイル内のコードが **CustomerEntity** クラスにアクセスできるように、次のディレクティブを追加します。

    ```csharp
    using StorageAspnet.Models;
    ```

1. **ActionResult** を返す **AddEntity** というメソッドを追加します。

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **AddEntity** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service クライアントを表す **CloudTableClient** オブジェクトを取得します。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 新しいエンティティの追加先テーブルへの参照を表す **CloudTable** オブジェクトを取得します。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **CustomerEntity** クラスをインスタンス化および初期化します。

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. 顧客エンティティを挿入する **TableOperation** オブジェクトを作成します。

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. **CloudTable.Execute** メソッドを呼び出して挿入操作を実行します。 操作の結果は、**TableResult.HttpStatusCode** プロパティを調べることによって確認できます。 2xx の状態コードは、クライアントによって要求されたアクションが正常に処理されたことを示します。 たとえば、新しいエンティティの挿入が成功すると、HTTP 状態コード 204 が返されます。これは、操作が正常に処理され、サーバーからコンテンツが返されなかったことを意味します。

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. テーブル名と挿入操作の結果で **ViewBag** を更新します。

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開して **[テーブル]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**AddEntity**」と入力し、 **[追加]** を選択します。

1. `AddEntity.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. アプリケーションを実行して **[エンティティの追加]** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![エンティティの追加](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    エンティティが追加されたことは、「[単一のエンティティを取得する](#get-a-single-entity)」セクションの手順で確認できます。 また、[Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用して、テーブルのすべてのエンティティを表示することもできます。

## <a name="add-a-batch-of-entities-to-a-table"></a>エンティティのバッチをテーブルに追加する

エンティティは[一度に 1 つずつテーブルに追加](#add-an-entity-to-a-table)できるだけでなく、複数のエンティティをバッチで追加することもできます。 複数のエンティティをバッチで追加することで、コードと Azure Table service 間のラウンドトリップの回数が抑えられます。 次の手順では、1 回の挿入操作で複数のエンティティをテーブルに追加する方法を説明します。

> [!NOTE]
> 
> このセクションでは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。

1. `TablesController.cs` ファイルを開きます。

1. **ActionResult** を返す **AddEntities** というメソッドを追加します。

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **AddEntities** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service クライアントを表す **CloudTableClient** オブジェクトを取得します。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 新しいエンティティの追加先テーブルへの参照を表す **CloudTable** オブジェクトを取得します。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 「[エンティティをテーブルに追加する](#add-an-entity-to-a-table)」セクションの説明に従い、**CustomerEntity** モデル クラスに基づいて複数の顧客オブジェクトをインスタンス化します。

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. **TableBatchOperation** オブジェクトを取得します。

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. エンティティをバッチ挿入操作オブジェクトに追加します。

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. **CloudTable.ExecuteBatch** メソッドを呼び出してバッチ挿入操作を実行します。   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** メソッドは **TableResult** オブジェクトの一覧を返し、それぞれの **TableResult** オブジェクトを調べることで各操作が成功したか失敗したかを確認できます。 この例では、この一覧をビューに渡し、各操作の結果をビューに表示しています。 
 
    ```csharp
    return View(results);
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開して **[テーブル]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**AddEntities**」と入力し、 **[追加]** を選択します。

1. `AddEntities.cshtml` を開き、次のように変更します。

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. アプリケーションを実行して **[エンティティの追加]** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![複数エンティティの追加](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    エンティティが追加されたことは、「[単一のエンティティを取得する](#get-a-single-entity)」セクションの手順で確認できます。 また、[Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用して、テーブルのすべてのエンティティを表示することもできます。

## <a name="get-a-single-entity"></a>単一のエンティティを取得する

このセクションでは、エンティティの行キーとパーティション キーを使用してテーブルから単一のエンティティを取得する方法について説明します。 

> [!NOTE]
> 
> このセクションは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。また、「[エンティティのバッチをテーブルに追加する](#add-a-batch-of-entities-to-a-table)」のデータを使用します。 

1. `TablesController.cs` ファイルを開きます。

1. **ActionResult** を返す **GetSingle** というメソッドを追加します。

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **GetSingle** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service クライアントを表す **CloudTableClient** オブジェクトを取得します。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. エンティティの取得先テーブルへの参照を表す **CloudTable** オブジェクトを取得します。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **TableEntity** から派生したエンティティ オブジェクトを取得する取得操作オブジェクトを作成します。 最初のパラメーターは *partitionKey* で、2 番目のパラメーターは *rowKey* です。 次のコード スニペットでは、「[エンティティのバッチをテーブルに追加する](#add-a-batch-of-entities-to-a-table)」セクションに示されている **CustomerEntity** クラスとデータを使用して、*partitionKey* 値が "Smith" で *rowKey* 値が "Ben" の **CustomerEntity** エンティティをテーブルに対して照会します。

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. 取得操作を実行します。   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. 結果をビューに渡して表示します。

    ```csharp
    return View(result);
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開して **[テーブル]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**GetSingle**」と入力し、 **[追加]** を選択します。

1. `GetSingle.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. アプリケーションを実行して **[Get Single]\(単一エンティティの取得\)** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![単一エンティティの取得](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する

「[エンティティをテーブルに追加する](#add-an-entity-to-a-table)」セクションで説明したように、パーティション キーと行キーを組み合わせることでテーブル内のエンティティを一意に指定することができます。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速に照会できます。 このセクションでは、テーブルでクエリを実行して指定したパーティションのすべてのエンティティを求める方法について説明します。  

> [!NOTE]
> 
> このセクションは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。また、「[エンティティのバッチをテーブルに追加する](#add-a-batch-of-entities-to-a-table)」のデータを使用します。 

1. `TablesController.cs` ファイルを開きます。

1. **ActionResult** を返す **GetPartition** というメソッドを追加します。

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **GetPartition** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service クライアントを表す **CloudTableClient** オブジェクトを取得します。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. エンティティの取得先テーブルへの参照を表す **CloudTable** オブジェクトを取得します。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **Where** 句でクエリを指定する **TableQuery** オブジェクトをインスタンス化します。 次のコード スニペットでは、「[エンティティのバッチをテーブルに追加する](#add-a-batch-of-entities-to-a-table)」セクションに示されている **CustomerEntity** クラスとデータを使用して、**PartitionKey** (顧客の姓) の値が "Smith" であるすべてのエンティティをテーブルに対して照会します。

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. ループ内で **CloudTable.ExecuteQuerySegmented** メソッドを呼び出して、前の手順でインスタンス化したクエリ オブジェクトを渡します。  **CloudTable.ExecuteQuerySegmented** メソッドは、**TableContinuationToken** オブジェクトを返します。このオブジェクトは、**null** のときに、取得するエンティティがないことを示します。 ループ内で別のループを使用して、返されたエンティティを反復処理します。 次のコード例では、返された各エンティティは一覧に追加されます。 ループが終了すると、この一覧がビューに渡され表示されます。 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開して **[テーブル]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**GetPartition**」と入力し、 **[追加]** を選択します。

1. `GetPartition.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. アプリケーションを実行して **[Get Partition]\(パーティションの取得\)** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![パーティションの取得](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>エンティティを削除する

このセクションでは、テーブルからエンティティを削除する方法について説明します。

> [!NOTE]
> 
> このセクションは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。また、「[エンティティのバッチをテーブルに追加する](#add-a-batch-of-entities-to-a-table)」のデータを使用します。 

1. `TablesController.cs` ファイルを開きます。

1. **ActionResult** を返す **DeleteEntity** というメソッドを追加します。

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **DeleteEntity** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service クライアントを表す **CloudTableClient** オブジェクトを取得します。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. エンティティを削除するテーブルへの参照を表す **CloudTable** オブジェクトを取得します。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **TableEntity** から派生したエンティティ オブジェクトを取得する削除操作オブジェクトを作成します。 この例では、「[エンティティのバッチをテーブルに追加する](#add-a-batch-of-entities-to-a-table)」セクションに示されている **CustomerEntity** クラスとデータを使用します。 エンティティの **ETag** を有効な値に設定する必要があります。  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. 削除操作を実行します。   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. 結果をビューに渡して表示します。

    ```csharp
    return View(result);
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開して **[テーブル]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**DeleteEntity**」と入力し、 **[追加]** を選択します。

1. `DeleteEntity.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. アプリケーションを実行して **[エンティティの削除]** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![単一エンティティの取得](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>次の手順
Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。

  * [Azure Blob Storage と Visual Studio 接続済みサービスの概要 (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Azure キュー ストレージと Visual Studio 接続済みサービスの概要](../storage/vs-storage-aspnet-getting-started-queues.md)
