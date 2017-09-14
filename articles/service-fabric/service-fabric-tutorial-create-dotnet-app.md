---
title: "Service Fabric の .NET アプリケーションを作成する | Microsoft Docs"
description: "ASP.NET Core のフロントエンドとリライアブル サービスのステートフルなバックエンドを含むアプリケーションを作成し、クラスターにアプリケーションをデプロイする方法を説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: ef50adf3af19bce494c3256308b443c8eaccdcea
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>ASP.NET Core Web API フロントエンド サービスとステートフルなバックエンド サービスを含むアプリケーションを作成およびデプロイする
このチュートリアルは、シリーズの第 1 部です。  ASP.NET Core Web API フロント エンドとステートフルなバックエンド サービスを含む Azure Service Fabric アプリケーションを作成し、データを格納する方法を説明します。 最後まで読み進めていけば、ASP.NET Core Web フロントエンドからクラスター内のステートフルなバックエンド サービスに投票結果を保存するアプリケーションが完成します。 投票アプリケーションを手動で作成しない場合は、完成したアプリケーションの[ソース コードをダウンロード](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)し、「[投票のサンプル アプリケーションの概要](#walkthrough_anchor)」に進むことができます。

![アプリケーション ダイアグラム](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Core Web API サービスをステートフル リライアブル サービスとして作成する
> * ASP.NET Core Web アプリケーション サービスをステートレス Web サービスとして作成する
> * リバース プロキシを使用してステートフル サービスと通信する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * .NET Service Fabric アプリケーションを構築する
> * [アプリケーションをリモート クラスターにデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Visual Studio Team Services を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
- [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>ASP.NET Web API サービスをリライアブル サービスとして作成する
まず、ASP.NET Core を使用して投票アプリケーション Web フロントエンドを作成します。 ASP.NET Core は軽量のクロスプラットフォーム Web 開発フレームワークであり、これを使用すると、最新の Web UI と Web API を作成できます。 Service Fabric と ASP.NET Core を統合する方法を十分に理解するには、「[Service Fabric リライアブル サービスでの ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)」の記事全体を読むことを強くお勧めします。 ここでは、このチュートリアルに従って、すぐに開始することができます。 ASP.NET Core の詳細については、[ASP.NET Core のドキュメント](https://docs.microsoft.com/aspnet/core/)を参照してください。

> [!NOTE]
> このチュートリアルは、[Visual Studio 2017 用の ASP.NET Core ツール](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc)に基づいています。 Visual Studio 2015 用の .NET Core ツールは、更新されなくなりました。

1. Visual Studio を**管理者**として起動します。

2. **[ファイル]**->**[新規]**->**[プロジェクト]** の順に選択してプロジェクトを作成します。

3. **[新しいプロジェクト]** ダイアログで、**[クラウド]、[Service Fabric アプリケーション]** の順に選択します。

4. アプリケーションに **Voting** という名前を付けて、**[OK]** をクリックします。

   ![Visual Studio の [新しいプロジェクト] ダイアログ](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. **[新しい Service Fabric サービス]** ページで、**[ステートレス ASP.NET Core]** を選択し、サービスに "**VotingWeb**" という名前を付けます。
   
   ![Choosing ASP.NET web service in the new service dialog](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 次のページには、一連の ASP.NET Core プロジェクト テンプレートが表示されます。 このチュートリアルでは、**[Web アプリケーション]** を選択します。 
   
   ![ASP.NET プロジェクトの種類を選択する](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio によって、アプリケーションとサービス プロジェクトが作成され、ソリューション エクスプローラーに表示されます。

   ![アプリケーションと ASP.NET Core Web API サービスの作成後に表示されるソリューション エクスプローラー]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>VotingWeb サービスに AngularJS を追加する
組み込みの[ブラウザー サポート](/aspnet/core/client-side/bower)を使用して、[AngularJS](http://angularjs.org/) をサービスに追加します。 *bower.json* を開き、angular と angular-bootstrap のエントリを追加し、変更を保存します。

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
*bower.json* ファイルを保存すると、Angular がプロジェクトの *wwwroot/lib* フォルダーにインストールされます。 また、*Dependencies/Bower* フォルダー内に表示されます。

### <a name="update-the-sitejs-file"></a>site.js ファイルを更新する
*wwwroot/js/site.js* ファイルを開きます。  ホーム ビューに使用されるコンテンツを JavaScript で置き換えます。

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Index.cshtml ファイルを更新する
ホーム コントローラーに固有のビューである *Views/Home/Index.cshtml* ファイルを開きます。  その内容を次のコードに置き換え、変更を保存します。

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>_Layout.cshtml ファイルを更新する
ASP.NET アプリの既定のレイアウトである *Views/Shared/_Layout.cshtml* ファイルを開きます。  その内容を次のように置き換え、変更を保存します。

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

</head>
<body>
    <div class="container body-content">
        @RenderBody()
    </div>

    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/lib/angular/angular.js"></script>
    <script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
    <script src="~/js/site.js"></script>

    @RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>VotingWeb.cs ファイルを更新する
*VotingWeb.cs* ファイルを開きます。このファイルで、WebListener Web サーバーを使用してステートレス サービス内に ASP.NET Core WebHost が作成されます。  ファイルの先頭に `using System.Net.Http;` ディレクティブを追加します。  `CreateServiceInstanceListeners()` 関数を次の内容に置き換え、変更を保存します。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>VotesController.cs ファイルを追加する
投票アクションを定義するコントローラーを追加します。 **Controllers** フォルダーを右クリックし、**[追加]、[新しい項目]、[クラス]** の順に選択します。  ファイルに "VotesController.cs" と名前を付け、**[追加]** をクリックします。  ファイルの内容を次のように置き換え、変更を保存します。  後述する「[VotesController.cs ファイルを更新する](#updatevotecontroller_anchor)」では、バックエンド サービスから投票データを読み込み、書き込むようにこのファイルを変更します。  ここでは、コントローラーから静的文字列データをビューに返します。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```



### <a name="deploy-and-run-the-application-locally"></a>ローカルでのアプリケーションのデプロイとデバッグ
アプリケーションを実行できる状態になりました。 Visual Studio で `F5` キーを押して、デバッグ用にアプリケーションをデプロイします。 Visual Studio を**管理者**として開いていない場合、`F5` キーを押すとエラーが生じます。

> [!NOTE]
> 初めてアプリケーションをローカルで実行してデプロイすると、Visual Studio によってデバッグ用にローカル クラスターが作成されます。  クラスターの作成には、しばらく時間がかかる場合があります。 Visual Studio の出力ウィンドウにクラスターの作成状態が表示されます。

この時点で、Web アプリは次の図のようになります。

![ASP.NET Core フロントエンド](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

アプリケーションのデバッグを停止するには、Visual Studio に戻って **Shift + F5** キーを押します。

## <a name="add-a-stateful-back-end-service-to-your-application"></a>アプリケーションにステートフルなバックエンド サービスを追加する
ASP.NET Web API サービスがアプリケーションで実行されるようになりました。次に、ステートフル リライアブル サービスを追加して、アプリケーションにデータを格納しましょう。

Service Fabric では、Reliable Collection によってデータがサービス内に一貫して確実に格納されます。 Reliable Collection は、C# コレクションを使用したことがあるユーザーには馴染みのある可用性が高く信頼できる一連のコレクション クラスです。

このチュートリアルでは、Reliable Collection にカウンター値を格納するサービスを作成します。

1. ソリューション エクスプローラーで、アプリケーション プロジェクトの **[サービス]** を右クリックし、**[追加]、[Service Fabric サービスの新規作成]** の順に選択します。
   
    ![既存アプリケーションへの新しいサービスの追加](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. **[新しい Service Fabric サービス]** ダイアログ ボックスで、**[ステートフル ASP.NET Core]** を選択し、サービスに "**VotingData**" という名前を付けて **[OK]** をクリックします。

    ![Visual Studio の [新しいサービス] ダイアログ](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    作成したサービス プロジェクトでは、2 つのサービスがアプリケーションに含まれます。 アプリケーションのビルドを続けながら、同じ方法でさらにサービスを追加することができます。 それぞれを個別にバージョン管理およびアップグレードできます。

3. 次のページには、一連の ASP.NET Core プロジェクト テンプレートが表示されます。 このチュートリアルでは、**[Web API]** を選択します。

    ![ASP.NET プロジェクトの種類を選択する](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio によってサービス プロジェクトが作成され、ソリューション エクスプローラーに表示されます。

    ![ソリューション エクスプローラー
](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>VoteDataController.cs ファイルを追加する

**VotingData** プロジェクトで **Controllers** フォルダーを右クリックし、**[追加]、[新しい項目]、[クラス]** の順に選択します。 ファイルに "VoteDataController.cs" と名前を付け、**[追加]** をクリックします。 ファイルの内容を次のように置き換え、変更を保存します。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>サービスへの接続
次の手順では、2 つのサービスを接続し、フロントエンド Web アプリケーションで、バックエンド サービスの投票情報の取得および設定ができるようにします。

Service Fabric は、Reliable Services との通信方法において完全な柔軟性を提供します。 1 つのアプリケーション内に、TCP 経由でアクセス可能なサービスが含まれる場合があります。 そのほかにも、HTTP REST API を使用してアクセスできるサービスや、Web ソケットを介してアクセスできるサービスが含まれる場合があります。 使用可能なオプションとトレードオフについては、[サービスとの通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照してください。

このチュートリアルでは、[ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md) を使用しています。

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>VotesController.cs ファイルを更新する
**VotingWeb** プロジェクトで *Controllers/VotesController.cs* ファイルを開きます。  `VotesController` クラス定義の内容を次のように置き換え、変更を保存します。

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>投票のサンプル アプリケーションの概要
この投票アプリケーションは次の 2 つのサービスから成ります。
- Web フロントエンド サービス (VotingWeb) - ASP.NET Core Web フロントエンド サービス。Web ページを表示すると共に、バックエンド サービスとやり取りするための Web API を公開します。
- バックエンド サービス (VotingData) - ASP.NET Core Web サービス。ディスク上に永続化された信頼性の高いディクショナリに投票結果を保存する API を公開します。

![アプリケーション ダイアグラム](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

アプリケーションで票を投じると、次のイベントが発生します。
1. JavaScript が Web フロントエンド サービスの Web API に HTTP PUT 要求として投票要求を送信します。

2. Web フロントエンド サービスがプロキシを使用して HTTP PUT 要求を検出し、バックエンド サービスに転送します。

3. バックエンド サービスが受信要求を受け取り、更新された結果を信頼性の高いディクショナリに保存すると、それがクラスター内の複数のノードにレプリケートされてディスク上に永続化されます。 アプリケーションのデータはすべてクラスターに保存されるため、データベースは必要ありません。

## <a name="debug-in-visual-studio"></a>Visual Studio でのデバッグ
Visual Studio でアプリケーションをデバッグするときは、ローカルの Service Fabric 開発クラスターを使用します。 デバッグのエクスペリエンスは実際のシナリオに合わせて調整することができます。 このアプリケーションでは、信頼性の高いディクショナリを使ってバックエンド サービスにデータを保存します。 既定では、デバッガーを停止すると、Visual Studio によってアプリケーションが削除されます。 アプリケーションが削除されると、バックエンド サービス内のデータも削除されます。 デバッグ セッションの終了後もデータを維持するには、Visual Studio の **Voting** プロジェクトのプロパティで、**[アプリケーション デバッグ モード]** を変更してください。

コードでどのような処理が実行されているのかを確認するには、次の手順に従います。
1. **VotesController.cs** ファイルを開き、Web API の **Put** メソッド (47 行目) にブレークポイントを設定します。このファイルは、Visual Studio のソリューション エクスプローラーで検索できます。

2. **VoteDataController.cs** ファイルを開き、この Web API の **Put** メソッド (50 行目) にブレークポイントを設定します。

3. ブラウザーに戻り、投票の選択肢をクリックするか、新しい選択肢を追加します。 Web フロントエンドの API コントローラーで 1 つ目のブレークポイントに到達します。
    
    1. ここは、JavaScript がブラウザーからフロントエンド サービスの Web API コントローラーに要求を送信する部分です。
    
    ![投票フロントエンド サービスの追加](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. 最初にバックエンド サービスの ReverseProxy の URL を構築します **(1)**。
    3. 次に HTTP PUT 要求を ReverseProxy に送信します **(2)**。
    4. 最後にバックエンド サービスからの応答をクライアントに返します **(3)**。

4. **F5** キーを押して続行します。
    1. 今度は、バックエンド サービスのブレークポイントに到達します。
    
    ![投票バックエンド サービスの追加](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. メソッド **(1)** の先頭行では、`StateManager` を使用して信頼性の高いディクショナリ (`counts`) を取得または追加しています。
    3. 信頼性の高いディクショナリ内の値とのすべてのやり取りにはトランザクションが必要です。この using ステートメント **(2)** によってトランザクションが作成されます。
    4. その後、トランザクションで、投票の選択肢に関連したキーの値を更新し、操作をコミットします **(3)**。 コミット メソッドから制御が戻ると、ディクショナリ内のデータが更新され、クラスター内の他のノードにレプリケートされます。 これでデータが安全にクラスターに保存され、バックエンド サービスは、データの可用性を維持したまま他のノードにフェールオーバーすることができます。
5. **F5** キーを押して続行します。

デバッグ セッションを停止するには、**Shift + F5** キーを押します。


## <a name="next-steps"></a>次のステップ
チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Core Web API サービスをステートフル リライアブル サービスとして作成する
> * ASP.NET Core Web アプリケーション サービスをステートレス Web サービスとして作成する
> * リバース プロキシを使用してステートフル サービスと通信する

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure にアプリケーションをデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
