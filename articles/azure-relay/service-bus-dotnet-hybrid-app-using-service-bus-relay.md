---
title: Azure Windows Communication Foundation (WCF) Relay ハイブリッド オンプレミス/クラウド アプリケーション (.NET) | Microsoft Docs
description: Azure Relay を使用してオンプレミスの WCF サービスをクラウドの Web アプリケーションに公開する方法について説明します。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 22b582634b623b39545eca225c8df0130606c2bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983881"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Azure Relay を使用してオンプレミスの WCF サービスをクラウドの Web アプリケーションに公開する

この記事では、Microsoft Azure と Visual Studio を使ってハイブリッド クラウド アプリケーションを作成する方法について説明します。 ここではクラウド内の複数の Azure リソースを使用するアプリケーションを作成します。 このチュートリアルでは、次のことについて説明します。

* Web ソリューションによって使用される Web サービスを作成、または既存の Web サービスを適合させる方法。
* Azure Windows Communication Foundation (WCF) Relay サービスを使用して、他の場所でホストされている Azure アプリケーションと Web サービスの間でデータを共有する方法。

このチュートリアルでは、次のタスクを行います。

> [!div class="checklist"]
>
> * このチュートリアルの前提条件をインストールする。
> * シナリオを確認する。
> * 名前空間を作成する。
> * オンプレミスのサーバーを作成する。
> * ASP.NET アプリケーションを作成する。
> * アプリをローカルで実行します。
> * Web アプリを Azure にデプロイする。
> * Azure でアプリを実行する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* [Visual Studio 2015 またはそれ以降](https://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2019 を使用します。
* Azure SDK for .NET。 [SDK のダウンロード ページ](https://azure.microsoft.com/downloads/)からインストールします。

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Azure Relay がハイブリッド ソリューションで役立つ理由

ビジネス ソリューションは、通常、カスタム コードと既存の機能の組み合わせで構成されます。 カスタム コードで、新しい独自のビジネス要件に対応します。 既に配置されているソリューションやシステムは、既存の機能を提供します。

ソリューション アーキテクトたちは、スケーラビリティが高く運用コストが低いクラウドを使い始めています。 その中で、ソリューションの構成要素として使用する既存のサービス資産は企業ファイアウォールの内部にあり、クラウド ソリューションからの到達が難しいということがわかってきました。 多くの内部サービスは、企業ネットワークと外部との境界で簡単に公開できるような方法では構築されたり、ホストされたりしていません。

[Azure Relay](https://azure.microsoft.com/services/service-bus/) では、既存の WCF Web サービスを利用し、企業ネットワーク インフラストラクチャに侵入的な変更を加えることなく、そのようなサービスから企業の境界の外側にあるソリューションに安全にアクセスできるようにします。 このような Relay サービスは既存の環境内でもホストされていますが、受信セッションや要求のリッスンは、クラウドでホストされている Relay サービスにデリゲートしています。 さらに Azure Relay は、[Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) 認証を使用して、これらのサービスを未承認のアクセスから保護します。

## <a name="review-the-scenario"></a>シナリオを確認する

このチュートリアルでは、商品在庫ページに商品の一覧を表示する ASP.NET Web サイトを作成します。

![シナリオ][0]

このチュートリアルでは、既存のオンプレミスのシステムに商品情報が格納されているものとし、Azure Relay を使用してそのシステムにアクセスします。 単純なコンソール アプリケーションで実行される Web サービスで、この状況をシミュレートします。 これには、インメモリ セットの製品が含まれています。 このコンソール アプリケーションをユーザー自身のコンピューターで実行し、Web ロールを Azure にデプロイできます。 こうすることで、Azure データセンターで実行されている Web ロールがコンピューターにどのように呼び出されるかがわかります。 少なくとも 1 つのファイアウォールとネットワーク アドレス変換 (NAT) レイヤーの背後にコンピューターがほぼ確実に存在する場合でも、この呼び出しは発生します。

## <a name="set-up-the-development-environment"></a>開発環境を設定する

Azure アプリケーションの開発を開始する前に、ツールをダウンロードして、開発環境を設定します。

1. SDK の[ダウンロード ページ](https://azure.microsoft.com/downloads/)から、Azure SDK for .NET をインストールします。
1. **[.NET]** 列で、使用している [[Visual Studio]](https://www.visualstudio.com) のバージョンを選択します。 このチュートリアルでは、Visual Studio 2019 を使用します。
1. インストーラーの実行や保存を求めるメッセージが表示されたら、 **[実行]** を選択します。
1. **[Web Platform Installer]** ダイアログ ボックスで、 **[インストール]** を選択してインストールを続行します。

インストールが完了すると、アプリの開発を開始するために必要なものがすべて揃います。 SDK には、Visual Studio で Azure アプリケーションを簡単に開発するためのツールが用意されています。

## <a name="create-a-namespace"></a>名前空間の作成

最初の手順として、名前空間を作成し、[Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) キーを取得します。 名前空間により、リレー サービスが公開する各アプリケーションにアプリケーション境界が設けられます。 SAS キーは、サービスの名前空間が作成されると、システムによって自動的に生成されます。 サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Azure が認証する資格情報になります。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>オンプレミスのサーバーを作成する

まず、シミュレートするオンプレミスの商品カタログ システムを構築します。  このプロジェクトは Visual Studio コンソール アプリケーションです。[Azure Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)を使用して、Service Bus ライブラリと構成設定を組み込みます。 <a name="create-the-project"></a>

1. Microsoft Visual Studio を管理者として起動します。 これを行うには、Visual Studio のプログラム アイコンを右クリックして **[管理者として実行]** を選択します。
1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、C# の **[コンソール アプリ (.NET Framework)]** を選択し、 **[次へ]** を選択します。
1. プロジェクトに *ProductsServer* と名前を付け、 **[作成]** を選択します。

   ![新しいプロジェクトを構成する][11]

1. **ソリューション エクスプローラー** で **ProductsServer** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。
1. **[参照]** を選択し、**WindowsAzure.ServiceBus** を探して選択します。 **[インストール]** を選択して、使用条件に同意します。

   ![NuGet パッケージを選択][13]

   これで、必要なクライアント アセンブリを参照できるようになりました。

1. 商品のコントラクト用に新しいクラスを追加します。 **ソリューション エクスプローラー** で、**ProductsServer** プロジェクトを右クリックし、 **[追加]**  >  **[クラス]** を選択します。
1. **[名前]** に *ProductsContract.cs* という名前を入力し、 **[追加]** を選択します。

ソリューションに対して次のコード変更を行います。

1. *ProductsContract.cs* で、名前空間の定義を次のコードに置き換えます。このコードはサービスのコントラクトを定義します。

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. *Program.cs* で、名前空間の定義を次のコードに書き換えます。このコードは、プロファイル サービスとそのホストを追加します。

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. **ソリューション エクスプローラー** で、**App.config** をダブルクリックして、Visual Studio エディターでそのファイルを開きます。 `<system.ServiceModel>` 要素の下 (ただし、`<system.ServiceModel>` 内) に、次の XML コードを追加します。 `yourServiceNamespace` を名前空間の名前に、`yourKey` を先ほどポータルから取得した SAS キーに置き換えてください。

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > `transportClientEndpointBehavior` が原因で発生するエラーは、この例では単に警告であり、障害となっている問題障害となっている問題ではありません。

1. 引き続き *App.config* 内で、`<appSettings>` 要素内の接続文字列の値を、前の手順でポータルから取得した接続文字列に置き換えます。

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Ctrl + Shift + B キーを押すか **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドし、これまでの作業が正確にできていることを確認します。

## <a name="create-an-aspnet-application"></a>ASP.NET アプリケーションを作成する

このセクションでは、商品サービスから取得したデータを表示する単純な ASP.NET アプリケーションを構築します。

### <a name="create-the-project"></a>プロジェクトを作成する

1. Visual Studio が管理者として実行されていることを確認します。
1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、C# の **[ASP.NET Web アプリケーション (.NET Framework)]** を選択し、 **[次へ]** を選択します。
1. プロジェクトに *ProductsPortal* と名前を付け、 **[作成]** を選択します。
1. **[新しい ASP.NET Web アプリケーションを作成する]** で **[MVC]** を選択し、**[認証]** の **[変更]** を選択します。

   ![ASP.NET Web アプリケーションを選択][16]

1. **[認証の変更]** で、 **[認証なし]** を選択して、 **[OK]** を選択します。 このチュートリアルでは、ユーザーがサインインする必要がないアプリケーションをデプロイします。

    ![認証を指定][18]

1. **[新しい ASP.NET Web アプリケーションを作成する]** に戻り、 **[作成]** を選択して MVC アプリを作成します。
1. 新しい Web アプリの Azure リソースを構成する [Web アプリの発行](../app-service/quickstart-dotnet-framework.md#launch-the-publish-wizard)の手順に従います。 その後、このチュートリアルに戻り、次の手順に進みます。
1. **ソリューション エクスプローラー** で **[モデル]** を右クリックし、 **[追加]**  >  **[クラス]** を選択します。
1. クラスに *Product.cs* と名前を付け、 **[追加]** を選択します。

    ![製品モデルを作成][17]

### <a name="modify-the-web-application"></a>Web アプリケーションを変更する

1. Visual Studio で、*Product.cs* ファイルの既存の名前空間定義を次のコードに書き換えます。

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. **ソリューション エクスプローラー** で **[コントローラー]** を展開し、**HomeController.cs** をダブルクリックして Visual Studio でファイルを開きます。
1. *HomeController.cs* の既存の名前空間の定義を次のコードに書き換えます。

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. **ソリューション エクスプローラー** で、 **[表示]**  >  **[共有]** を展開し、 **_Layout.cshtml** をダブルクリックして Visual Studio エディターでファイルを開きます。
1. `My ASP.NET Application` のすべての出現箇所を *Northwind Traders Products* に変更します。
1. `Home`、`About`、および `Contact` のリンクを削除します。 次の例では、強調表示されたコードを削除します。

    ![生成されたリスト項目を削除][41]

1. **ソリューション エクスプローラー** で **[表示]**  >  **[ホーム]** を展開し、**Index.cshtml** をダブルクリックして Visual Studio エディターでファイルを開きます。 ファイルの内容全体を次のコードに置き換えます。

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. ここまでの作業が正確にできていることを確認するために、Ctrl + Shift + B キーを押してプロジェクトをビルドします。

### <a name="run-the-app-locally"></a>アプリをローカルで実行する

アプリケーションを実行して、動作を確認します。

1. **ProductsPortal** がアクティブなプロジェクトであることを確認します。 **ソリューション エクスプローラー** で、プロジェクト名を右クリックして **[スタートアップ プロジェクトに設定]** を選択します。
1. Visual Studio で F5 キーを押します。

アプリケーションがブラウザーに表示され、実行されます。

![スクリーンショットは、ブラウザーで実行され、URL が強調表示されているアプリケーションの例を示しています。][21]

## <a name="put-the-pieces-together"></a>統合する

次の手順では、オンプレミスの商品サーバーと ASP.NET アプリケーションを連結します。

1. まだ開いていない場合は、Visual Studio で、「[ASP.NET アプリケーションを作成する](#create-an-aspnet-application)」セクションで作成した **ProductsPortal** プロジェクトを開きます。
1. 「[オンプレミスのサーバーを作成する](#create-an-on-premises-server)」に記載されている手順に従い、NuGet パッケージを Reference プロジェクトに追加します。 **ソリューション エクスプローラー** で **ProductsPortal** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。
1. *WindowsAzure.ServiceBus* を検索し、 **[WindowsAzure.ServiceBus]** 項目を選択します。 次に、インストールを完了し、このダイアログ ボックスを閉じます。
1. **ソリューション エクスプローラー** で、**ProductsPortal** プロジェクトを右クリックし、 **[追加]**  >  **[既存の項目]** を選択します。
1. **ProductsServer** コンソール プロジェクトの *ProductsContract.cs* ファイルに移動します。 *ProductsContract.cs* を選択します。 **[追加]** の横にある下矢印を選択し、 **[リンクとして追加]** を選択します。

   ![リンクとして追加][24]

1. 次に、Visual Studio エディターで *HomeController.cs* ファイルを開き、名前空間定義を次のコードに置き換えます。 `yourServiceNamespace` を実際のサービス名前空間の名前に、`yourKey` を実際の SAS キーに置き換えてください。 このコードを使用すると、クライアントからオンプレミスのサービスを呼び出し、その結果を返すことができます。

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. **ソリューション エクスプローラー** で **ProductsPortal** ソリューションを右クリックします。 プロジェクトではなく、ソリューションを右クリックしてください。 **[追加]**  >  **[既存のプロジェクト]** を選択します。
1. **ProductsServer** プロジェクトに移動し、*ProductsServer.csproj* ソリューション ファイルをダブルクリックして追加します。
1. **ProductsPortal** にデータを表示するには、**ProductsServer** が実行されている必要があります。 **ソリューション エクスプローラー** で **ProductsPortal** ソリューションを右クリックし、 **[プロパティ]** を選択して **[プロパティ ページ]** を表示します。
1. **[共通プロパティ]**  >  **[スタートアップ プロジェクト]** の順に選択し、 **[マルチ スタートアップ プロジェクト]** を選択します。 **[ProductsServer]** と **[ProductsPortal]** がこの順で表示されていることと、その両方の **[アクション]** が **[開始]** であることを確認します。

      ![マルチ スタートアップ プロジェクト][25]

1. 左側の **[共通プロパティ]**  >  **[プロジェクトの依存関係]** を選択します。
1. **[プロジェクト]** には **[ProductsPortal]** を選択します。 **[ProductsServer]** が選択されていることを確認してください。

    ![プロジェクト依存関係][26]

1. **[プロジェクト]** には **[ProductsServer]** を選択します。 **ProductsPortal** が選択されていないことを確認してから、 **[OK]** を選択して変更を保存します。

## <a name="run-the-project-locally"></a>プロジェクトをローカルで実行する

アプリケーションをローカルでテストするには、Visual Studio で F5 キーを押します。 まず、オンプレミスのサーバー **ProductsServer** を起動し、次に **ProductsPortal** アプリケーションをブラウザー ウィンドウで起動する必要があります。 この時点で、オンプレミスのシステムの商品サービスから取得された商品在庫一覧データが表示されます。

![Web アプリケーション][10]

**[ProductsPortal]** ページで **[最新の情報に更新]** を選択します。 ページを更新するたびに、**ProductsServer** から `GetProducts()` が呼び出され、サーバー アプリにメッセージが表示されます。

次のセクションに進む前に、両方のアプリケーションを終了します。

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Azure Web アプリに ProductsPortal プロジェクトをデプロイする

次の手順は、Azure Web アプリを **ProductsPortal** フロントエンドに再発行することです。

1. **ソリューション エクスプローラー** で **[ProductsPortal]** プロジェクトを右クリックし、 **[発行]** を選択します。 **[発行]** ページで **[発行]** を選択します。

   > [!NOTE]
   > デプロイの後で **ProductsPortal** Web プロジェクトが自動的に起動するとき、ブラウザー ウィンドウにエラー メッセージが表示されることがあります。 これは想定されたエラーです。**ProductsServer** アプリケーションがまだ実行されていないため、このようなエラーが発生します。
   >

1. デプロイされた Web アプリの URL をコピーします。 この URL は後で必要になります。 この URL は、Visual Studio の **[Azure App Service のアクティビティ]** ウィンドウからも入手できます。

   ![デプロイされたアプリの URL][9]

1. ブラウザー ウィンドウを閉じてアプリケーションの実行を停止します。

<a name="set-productsportal-as-web-app"></a>アプリケーションをクラウドで実行する前に、**ProductsPortal** が Visual Studio で Web アプリとして起動していることを確認する必要があります。

1. Visual Studio で **[ProductsPortal]** プロジェクトを右クリックし、 **[プロパティ]** を選択します。
1. **[Web]** を選択します。 **[開始アクション]** で **[URL の開始]** を選択します。 以前にデプロイした Web アプリの URL (この例では `https://productsportal20190906122808.azurewebsites.net/`) を入力します。

    ![URL の開始][27]

1. **[ファイル]**  >  **[すべて保存]** を選択します。
1. **[ビルド]**  >  **[ソリューションのリビルド]** を選択します。

## <a name="run-the-application"></a>アプリケーションの実行

F5 キーを押して、アプリケーションをビルドして実行します。 次のように、まず、オンプレミスのサーバー (**ProductsServer** コンソール アプリケーション) を起動し、次に **ProductsPortal** アプリケーションをブラウザー ウィンドウで起動する必要があります。

   ![Azure で Web アプリを実行][1]

オンプレミスのシステムの商品サービスから取得された商品在庫一覧データが Web アプリ上に表示されます。 URL を確認し、**ProductsPortal** が Azure Web アプリとしてクラウドで実行されていることを確かめます。

   > [!IMPORTANT]
   > **ProductsServer** コンソール アプリケーションが実行されており、**ProductsPortal** アプリケーションにデータを提供できる状態になっている必要があります。 ブラウザーにエラーが表示された場合は、**ProductsServer** が次のメッセージを読み込んで表示するまで数秒待ってからブラウザーを更新してください。
   >

ブラウザーで、 **[ProductsPortal]** ページを更新します。 ページを更新するたびに、**ProductsServer** から `GetProducts()` が呼び出され、サーバー アプリにメッセージが表示されます。

![更新された出力][38]

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
>[オンプレミスの WCF サービスをネットワーク外部の WCF クライアントに公開する](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
