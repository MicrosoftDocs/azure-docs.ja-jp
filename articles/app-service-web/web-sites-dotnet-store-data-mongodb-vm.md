---
title: "仮想マシンで実行される MongoDB に接続する Web アプリを Azure に作成する"
description: "Azure 仮想マシン上の MongoDB に接続された Azure App Service に、Git を使用して ASP.NET アプリをデプロイする方法を示すチュートリアル。"
tags: azure-portal
services: app-service\web, virtual-machines
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: adf7a472-ae00-45a8-aec4-06247e21318b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/29/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 5ce82ddd2f58fed338bd061fa963978aa63e5fdc
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-web-app-in-azure-that-connects-to-mongodb-running-on-a-virtual-machine"></a>仮想マシンで実行される MongoDB に接続する Web アプリを Azure に作成する
Git を使用すると、ASP.NET アプリケーションを Azure App Service Web Apps にデプロイできます。 このチュートリアルでは、Azure の仮想マシンで実行されている MongoDB データベースに接続する、ASP.NET MVC の単純なフロントエンド タスク一覧アプリケーションをビルドします。  [MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。 開発用コンピューターで ASP.NET アプリケーションを実行してテストした後、Git を使用してアプリケーションを App Service Web Apps にアップロードします。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="background-knowledge"></a>背景知識
このチュートリアルでは次の事項に関する知識があると楽ですが、必須ではありません。

* MongoDB の C# ドライバー。 MongoDB 用 C# アプリケーションの開発の詳細については、MongoDB の [CSharp Language Center][MongoC#LangCenter] をご覧ください。 
* ASP .NET Web アプリケーション フレームワーク。 詳しくは、[ASP.net の Web サイト][ASP.NET]をご覧ください。
* ASP .NET MVC Web アプリケーション フレームワーク。 詳しくは、[ASP.NET MVC の Web サイト][MVCWebSite]をご覧ください。
* Azure。 詳しくは、[Azure][WindowsAzure] サイトをご覧ください。

## <a name="prerequisites"></a>前提条件
* [Visual Studio Express 2013 for Web][VSEWeb] または [Visual Studio 2013][VSUlt]
* [Azure SDK for .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
* アクティブな Microsoft Azure サブスクリプション

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a> 

## <a name="create-a-virtual-machine-and-install-mongodb"></a>仮想マシンを作成して MongoDB をインストールする
このチュートリアルは、Azure に仮想マシンが作成済みであることを前提としています。 仮想マシンの作成後、仮想マシンに MongoDB をインストールする必要があります。

* Windows 仮想マシンを作成して MongoDB をインストールする方法については、「[Azure で Windows Server を実行する仮想マシンへの MongoDB のインストール][InstallMongoOnWindowsVM]」をご覧ください。

Azure に仮想マシンを作成して MongoDB をインストールしたら、仮想マシンの DNS 名 (たとえば "testlinuxvm.cloudapp.net") とエンドポイントで指定した MongoDB 用の外部ポートを忘れずに記録してください。  この情報は後で必要になります。

<a id="createapp"></a>

## <a name="create-the-application"></a>アプリケーションを作成する
このセクションでは、Visual Studio を使用して "My Task List" という ASP.NET アプリケーションを作成し、Azure App Service Web Apps への最初のデプロイを実行します。 このアプリケーションはローカルで実行されますが、Azure 上の仮想マシンに接続し、そこに作成した MongoDB インスタンスを使用します。

1. Visual Studio で、 **[新しいプロジェクト]**をクリックします。
   
    ![[開始] ページの [新しいプロジェクト]][StartPageNewProject]
2. **[新しいプロジェクト]** ウィンドウの左側のウィンドウで、**[Visual C#]** を選択し、**[Web]** を選択します。 中央のウィンドウで、**[ASP.NET Web アプリケーション]** を選択します。 ウィンドウの下部で、プロジェクトに「MyTaskListApp」という名前を付け、 **[OK]**をクリックします。
   
    ![[新しいプロジェクト] ダイアログ][NewProjectMyTaskListApp]
3. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** を選択し、**[OK]** をクリックします。
   
    ![MVC テンプレートの選択][VS2013SelectMVCTemplate]
4. Microsoft Azure にサインインしていない場合は、サインインするように求められます。 画面の指示に従って Azure にサインインします。
5. サインインしたら、App Service の Web アプリの構成を開始できます。 **[Web アプリ名]**、**[App Service プラン]**、**[リソース グループ]**、および **[リージョン]** を指定し、**[作成]** をクリックします。
   
    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)
6. プロジェクトの作成が完了したら、 **[Azure App Service の利用状況]** ウィンドウに示されているとおりに、Azure App Service で Web アプリが作成されるのを待ちます。 次に、 **[MyTaskListApp をこの Web アプリケーションに今すぐ発行する]**をクリックします。
7. **[発行]**をクリックします。
   
    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)
   
    既定の ASP.NET アプリケーションが Azure App Service Web Apps に発行されると、そのアプリケーションはブラウザーで起動します。

## <a name="install-the-mongodb-c-driver"></a>MongoDB C# ドライバーをインストールする
MongoDB では、ドライバーを通じてクライアント側の C# アプリケーションをサポートしています。このドライバーをローカルの開発用コンピューターにインストールする必要があります。 C# ドライバーは NuGet から入手できます。

MongoDB C# ドライバーをインストールするには、以下を実行します。

1. **ソリューション エクスプローラー**で、**MyTaskListApp** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
   
    ![NuGet パッケージの管理][VS2013ManageNuGetPackages]
2. **[NuGet パッケージの管理]** ウィンドウの左側のウィンドウで、**[オンライン]** をクリックします。 右側の **[オンラインで検索]** ボックスに「mongodb.driver」と入力します。  **[インストール]** をクリックして、ドライバーをインストールします。
   
    ![MongoDB C# ドライバーの検索][SearchforMongoDBCSharpDriver]
3. **[同意する]** をクリックして、10gen, Inc. のライセンス条項に同意します。
4. ドライバーがインストールされたら、 **[閉じる]** をクリックします。
    ![インストールされた MongoDB C# ドライバー][MongoDBCsharpDriverInstalled]

これで MongoDB C# ドライバーがインストールされました。  プロジェクトには、**MongoDB.Bson** ライブラリ、**MongoDB.Driver** ライブラリ、および **MongoDB.Driver.Core** ライブラリへの参照が追加されています。

![MongoDB C# ドライバーの参照][MongoDBCSharpDriverReferences]

## <a name="add-a-model"></a>モデルを追加する
**ソリューション エクスプローラー**で、*[Models]* フォルダーを右クリックし、**[追加]**、新しい **[クラス]** の順にクリックし、名前を「*TaskModel.cs*」にします。  *TaskModel.cs*で、既存のコードを次のコードに置き換えます。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;

    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }

            [BsonElement("Name")]
            public string Name { get; set; }

            [BsonElement("Category")]
            public string Category { get; set; }

            [BsonElement("Date")]
            public DateTime Date { get; set; }

            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }

        }
    }

## <a name="add-the-data-access-layer"></a>データ アクセス レイヤーを追加する
**ソリューション エクスプローラー**で、*[MyTaskListApp]* プロジェクトを右クリックし、**[追加]**、**[新しいフォルダー]** の順にクリックし、名前を「*DAL*」にします。  *[DAL]* フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 クラス ファイルに「 *Dal.cs*」という名前を付けます。  *Dal.cs*で、既存のコードを次のコードに置き換えます。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;


    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;

            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";

            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";

            // Default constructor.        
            public Dal()
            {
            }

            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }

            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }

            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }

            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }

            # region IDisposable

            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }

            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        if (mongoServer != null)
                        {
                            this.mongoServer.Disconnect();
                        }
                    }
                }

                this.disposed = true;
            }

            # endregion
        }
    }

## <a name="add-a-controller"></a>コントローラーを追加する
**ソリューション エクスプローラー**で *Controllers\HomeController.cs* ファイルを開いて、既存のコードを次のコードに置き換えます。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;

    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/

            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }

            //
            // GET: /MyTask/Create

            public ActionResult Create()
            {
                return View();
            }

            //
            // POST: /MyTask/Create

            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
                    return RedirectToAction("Index");
                }
                catch
                {
                    return View();
                }
            }

            public ActionResult About()
            {
                return View();
            }

            # region IDisposable

            new protected void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }

            new protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        this.dal.Dispose();
                    }
                }

                this.disposed = true;
            }

            # endregion

        }
    }

## <a name="set-up-the-styles"></a>スタイルを設定する
ページ上部のタイトルを変更するには、**ソリューション エクスプローラー**で *Views\Shared\\_Layout.cshtml* ファイルを開き、ナビゲーション ヘッダーの "Application name" を、次のように "My Task List Application" に置き換えます。

     @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

タスク一覧メニューを設定するために、*\Views\Home\Index.cshtml* ファイルを開き、既存のコードを次のコードに置き換えます。

    @model IEnumerable<MyTaskListApp.Models.MyTask>

    @{
        ViewBag.Title = "My Task List";
    }

    <h2>My Task List</h2>

    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>

        </tr>

    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>

        </tr>
    }

    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


新しいタスクを作成する機能を追加するために、*Views\Home\\* フォルダーを右クリックして、**[追加]**、**[ビュー]** の順にクリックします。  ビューの名前を「*Create*」にします。 コードを次のコードに置き換えます。

    @model MyTaskListApp.Models.MyTask

    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>

    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>

            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>

            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>

            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>

            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

**ソリューション エクスプローラー** の表示は次のようになります。

![Controllers\HomeController.cs][SolutionExplorerMyTaskListApp]

## <a name="set-the-mongodb-connection-string"></a>MongoDB 接続文字列を設定する
**ソリューション エクスプローラー**で、 *DAL/Dal.cs* ファイルを開きます。 次のコード行を見つけます。

    private string connectionString = "mongodb://<vm-dns-name>";

`<vm-dns-name>` を、このチュートリアルの「[仮想マシンを作成して MongoDB をインストールする][Create a virtual machine and install MongoDB]」で作成した MongoDB を実行する仮想マシンの DNS 名で置き換えます。  仮想マシンの DNS 名を確認するには、Azure Portal で **[仮想マシン]** をクリックし、**[DNS 名]** の値を確認します。

仮想マシンの DNS 名が "testlinuxvm.cloudapp.net" で、MongoDB が既定のポート 27017 をリッスンしている場合、その接続文字列のコード行は次のようになります。

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

仮想マシンのエンドポイントで MongoDB 用に別の外部ポートが指定されている場合は、接続文字列で次のようにしてポートを指定できます。

     private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

MongoDB の接続文字列の詳細については、「[Connections][MongoConnectionStrings]」(接続) をご覧ください。

## <a name="test-the-local-deployment"></a>ローカル デプロイをテストする
開発用コンピューターでアプリケーションを実行するには、**[デバッグ]** メニューの **[デバッグ開始]** をクリックするか、**F5** キーを押します。 IIS Express が起動し、ブラウザーが開いて、アプリケーションのホーム ページが表示されます。  新しいタスクを追加でき、そのデータは Azure の仮想マシンで実行されている MongoDB データベースに追加されます。

![My Task List アプリケーション][TaskListAppBlank]

## <a name="publish-to-azure-app-service-web-apps"></a>Azure App Service Web Apps に発行する
このセクションでは、変更を Azure App Service Web Apps に発行します。

1. ソリューション エクスプローラーで、もう一度 **MyTaskListApp** を右クリックし、**[発行]** をクリックします。
2. **[発行]**をクリックします。
   
    Web アプリが Azure App Service で実行され、Azure Virtual Machines の MongoDB データベースにアクセスできるようになります。

## <a name="summary"></a>概要
これで、Azure App Service Web Apps に ASP .NET アプリケーションをデプロイできました。 Web アプリを表示するには、次の操作を行います。

1. Azure ポータルにログインします。
2. **[Web Apps]**をクリックします。 
3. **[Web Apps]** ボックスの一覧から Web アプリを選択します。

MongoDB 用 C# アプリケーションの開発の詳細については、[CSharp Language Center][MongoC#LangCenter] をご覧ください。 

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]:../virtual-machines/windows/classic/install-mongodb.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Create a virtual machine and install MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp

