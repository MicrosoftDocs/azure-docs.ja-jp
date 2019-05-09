---
title: Azure WCF Relay ハイブリッド オンプレミス/クラウド アプリケーション (.NET) | Microsoft Docs
description: Azure Relay を使用してオンプレミスの WCF サービスをクラウドの Web アプリケーションに公開する方法について説明します。
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 145960db27247a8535eb96640000b86d810619c0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838410"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Azure Relay を使用してオンプレミスの WCF サービスをクラウドの Web アプリケーションに公開する 
この記事では、Microsoft Azure と Visual Studio を使ってハイブリッド クラウド アプリケーションを作成する方法について説明します。 複数の Azure リソースをクラウドで運用するアプリケーションを作成します。

* Web ソリューションによって使用される Web サービスを作成、または既存の Web サービスを適合させる方法。
* Azure アプリケーションと別の場所でホストされている Web サービスの間で、Azure WCF Relay サービスを使用してデータを共有する方法。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * シナリオを確認する。
> * 名前空間を作成する。
> * オンプレミスのサーバーを作成する
> * ASP.NET アプリケーションを作成する
> * アプリをローカルで実行します。
> * Web アプリを Azure にデプロイする
> * Azure でアプリを実行する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [Visual Studio 2015 またはそれ以降](https://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2017 を使用します。
- Azure SDK for .NET。 [SDK のダウンロード ページ](https://azure.microsoft.com/downloads/)からインストールします。

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Azure Relay がハイブリッド ソリューションで役立つ理由
ビジネス ソリューションは、通常、新しい独自のビジネス要件に対処するために記述されるカスタム コードと、既に配置されているソリューションとシステムによって提供される既存の機能の組み合わせによって構成されます。

ソリューション アーキテクトたちは、スケーラビリティが高く運用コストが低いクラウドを使い始めています。 その中で、ソリューションの構成要素として利用する既存のサービス資産は企業ファイアウォールの内部にあり、クラウド ソリューションからのアクセスが難しいということがわかってきました。 多くの内部サービスは、企業ネットワークと外部との境界で簡単に公開できるような方法では構築されたり、ホストされたりしていません。

[Azure Relay](https://azure.microsoft.com/services/service-bus/) は、既存の Windows Communication Foundation (WCF) Web サービスを使用し、企業ネットワークのインフラストラクチャを大幅に変更することなく、企業の境界の外部にあるソリューションからそれらのサービスに安全にアクセスできる使用事例として設計されています。 このような Relay サービスは既存の環境内でもホストされていますが、受信セッションや要求のリッスンは、クラウドでホストされている Relay サービスにデリゲートしています。 さらに Azure Relay は、[Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) 認証を使用して、これらのサービスを未承認のアクセスから保護します。

## <a name="review-the-scenario"></a>シナリオを確認する
このチュートリアルでは、商品在庫ページに商品の一覧を表示する ASP.NET Web サイトを作成します。

![シナリオ][0]

このチュートリアルでは、既存のオンプレミスのシステムに商品情報が格納されているものとし、Azure Relay を使用してそのシステムにアクセスします。 これを、単純なコンソール アプリケーションで実行された、メモリ内の商品のセットが基盤となっている Web サービスでシミュレートします。 このコンソール アプリケーションをユーザー自身のコンピューターで実行し、Web ロールを Azure にデプロイできます。 そうすることで、Azure データセンターで実行されている Web ロールが実際にどのようにコンピューターを呼び出すかを確認できます。ただし、ユーザーのコンピューターは、ほぼ確実に少なくとも 1 つのファイアウォールとネットワーク アドレス変換 (NAT) レイヤーの背後に配置されます。

## <a name="set-up-the-development-environment"></a>開発環境を設定する

Azure アプリケーションの開発を開始する前に、ツールをダウンロードして、開発環境を設定します。

1. SDK の[ダウンロード ページ](https://azure.microsoft.com/downloads/)から、Azure SDK for .NET をインストールします。
2. **[.NET]** 列で、使用している [Visual Studio](https://www.visualstudio.com) のバージョンをクリックします。 このチュートリアルの手順では、Visual Studio 2017 を使用します。
3. インストーラーの実行や保存を求めるメッセージが表示されたら、**[実行]** をクリックします。
4. **Web Platform Installer** の **[インストール]** をクリックし、インストールの手順を進めます。
5. インストールが完了すると、アプリケーションの開発に必要なツールがすべて揃います。 SDK には、Visual Studio で Azure アプリケーションを簡単に開発するためのツールが用意されています。

## <a name="create-a-namespace"></a>名前空間の作成
最初の手順として、名前空間を作成し、[Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) キーを取得します。 名前空間により、リレー サービスが公開する各アプリケーションにアプリケーション境界が設けられます。 サービス名前空間が作成された時点で、SAS キーが生成されます。 サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Azure が認証する資格情報になります。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>オンプレミスのサーバーを作成する
まず、シミュレートするオンプレミスの商品カタログ システムを構築します。  このプロジェクトは Visual Studio コンソール アプリケーションです。[Azure Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)を使用して、Service Bus ライブラリと構成設定を組み込みます。

### <a name="create-the-project"></a>プロジェクトを作成する
1. 管理者特権を使用して、Microsoft Visual Studio を起動します。 これを行うには、Visual Studio のプログラム アイコンを右クリックして **[管理者として実行]** をクリックします。
2. Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。
3. **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[コンソール アプリケーション (.NET Framework)]** をクリックします。 **[名前]** ボックスに、名前として「**ProductsServer**」と入力します。

   ![New Project dialog box][11]
4. **[OK]** をクリックして **ProductsServer** プロジェクトを作成します。
5. 既に Visual Studio 用の NuGet パッケージ マネージャーをインストールしている場合は、次のステップに進みます。 まだインストールしていない場合は、[NuGet][NuGet] にアクセスし、[[Install NuGet (NuGet のインストール)]](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) をクリックします。 メッセージに従って NuGet パッケージ マネージャーをインストールし、Visual Studio を再起動します。
6. ソリューション エクスプローラーで **ProductsServer** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
7. **[参照]** タブをクリックして、**WindowsAzure.ServiceBus** を検索します。 **[WindowsAzure.ServiceBus]** パッケージを選択します。
8. **[インストール]** をクリックして、使用条件に同意します。

   ![NuGet パッケージを選択][13]

   これで、必要なクライアント アセンブリを参照できるようになりました。
8. 商品のコントラクト用に新しいクラスを追加します。 ソリューション エクスプローラーで、**[ProductsServer]** プロジェクトを右クリックし、**[追加]** をクリックしてから **[クラス]** をクリックします。
9. **[名前]** ボックスに「**ProductsContract.cs**」と入力します。 **[追加]** をクリックします。
10. **ProductsContract.cs** で、名前空間の定義を次のコードに置き換えます。このコードはサービスのコントラクトを定義します。

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
11. Program.cs で、名前空間の定義を次のコードに書き換えます。このコードは、プロファイル サービスとそのホストを追加します。

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
12. ソリューション エクスプローラーで、**App.config** ファイルをダブルクリックして、Visual Studio エディターでそのファイルを開きます。 `<system.ServiceModel>` 要素の下 (ただし、`<system.ServiceModel>` 内) に、次の XML コードを追加します。*yourServiceNamespace* は実際の名前空間の名前に置き換え、*yourKey* は前の手順で Portal から取得した SAS キーに置き換えてください。

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
    "transportClientEndpointBehavior" に起因するエラーは、あくまで警告です。この問題によってこのサンプルの実行が阻害されることはありません。
    
13. 引き続き App.config 内で、`<appSettings>` 要素内の接続文字列の値を、前の手順でポータルから取得した接続文字列に置き換えます。

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. **Ctrl + Shift + B** キーを押すか、**[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてアプリケーションをビルドし、ここまでの作業に問題がないことを確認します。

## <a name="create-an-aspnet-application"></a>ASP.NET アプリケーションを作成する

このセクションでは、商品サービスから取得したデータを表示する単純な ASP.NET アプリケーションを構築します。

### <a name="create-the-project"></a>プロジェクトを作成する

1. Microsoft Visual Studio 2013 が管理者特権で実行されていることを確認します。
2. Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。
3. **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[ASP.NET Web アプリケーション (.NET Framework)]** をクリックします。 プロジェクト名として、「**ProductsPortal**」と入力します。 次に、 **[OK]** をクリックします

   ![[新しいプロジェクト] ダイアログ][15]

4. **[新しい ASP.NET Web アプリケーション]** ダイアログ ボックスの **[ASP.NET テンプレート]** の一覧で、**[MVC]** をクリックします。

   ![ASP.NET Web アプリケーションを選択][16]

6. **[認証の変更]** ボタンをクリックします。 **[認証の変更]** ダイアログ ボックスで、**[認証なし]** が選択されていることを確認した後、**[OK]** をクリックします。 このチュートリアルでは、ユーザー ログインの必要がないアプリケーションをデプロイします。

    ![認証を指定][18]

7. **[新しい ASP.NET Web アプリケーション]** ダイアログに戻り、**[OK]** をクリックして MVC アプリを作成します。
8. 次に、新しい Web アプリの Azure リソースを構成する必要があります。 [この記事の「Azure に発行する」セクション](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard)の手順に従います。 完了したら、このチュートリアルに戻り、次の手順に進んでください。
10. ソリューション エクスプローラーで **[Models]** を右クリックし、**[追加]**、**[クラス]** の順にクリックします。 **[名前]** ボックスに、名前として「**Product.cs**」と入力します。 **[追加]** をクリックします。

    ![製品モデルを作成][17]

### <a name="modify-the-web-application"></a>Web アプリケーションを変更する

1. Visual Studio で、Product.cs ファイルの既存の名前空間定義を次のコードに書き換えます。

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
2. ソリューション エクスプローラーで **Controllers** フォルダーを展開し、**HomeController.cs** ファイルをダブルクリックして Visual Studio で開きます。
3. **HomeController.cs** の既存の名前空間の定義を次のコードに書き換えます。

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
4. ソリューション エクスプローラーで、Views\Shared フォルダーを展開し、**_Layout.cshtml** をダブルクリックして Visual Studio エディターで開きます。
5. **My ASP.NET Application** となっている箇所をすべて **Northwind Traders Products** に置き換えます。
6. **Home**、**About**、および **Contact** の各リンクを削除します。 次の例では、強調表示されたコードを削除します。

    ![生成されたリスト項目を削除][41]

7. ソリューション エクスプローラーで、Views\Home フォルダーを展開し、**Index.cshtml** をダブルクリックして Visual Studio エディターで開きます。 ファイルの内容全体を次のコードに置き換えます。

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
8. ここまでの作業が正確にできていることを確認するために、**Ctrl + Shift + B** キーを押してプロジェクトをビルドします。

### <a name="run-the-app-locally"></a>アプリをローカルで実行する

アプリケーションを実行して、動作を確認します。

1. **ProductsPortal** がアクティブなプロジェクトであることを確認します。 ソリューション エクスプローラーで、プロジェクト名を右クリックして **[スタートアップ プロジェクトに設定]** を選択します。
2. Visual Studio で **F5** キーを押します。
3. アプリケーションがブラウザーに表示され、実行されます。

   ![Web アプリケーション][21]

## <a name="put-the-pieces-together"></a>統合する

次の手順では、オンプレミスの商品サーバーと ASP.NET アプリケーションを連結します。

1. 「[ASP.NET アプリケーションを作成する](#create-an-aspnet-application)」で作成した **ProductsPortal** プロジェクトが閉じている場合は、Visual Studio でもう一度開きます。
2. 「オンプレミスのサーバーを作成する」に記載されている手順に従い、NuGet パッケージを Reference プロジェクトに追加します。 ソリューション エクスプローラーで **ProductsPortal** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. **WindowsAzure.ServiceBus** を検索し、**[WindowsAzure.ServiceBus]** 項目を選択します。 次に、インストールを完了し、このダイアログ ボックスを閉じます。
4. ソリューション エクスプローラーで **ProductsPortal** プロジェクトを右クリックし、**[追加]** をクリックしてから **[既存の項目]** をクリックします。
5. **ProductsServer** コンソール プロジェクトの **ProductsContract.cs** ファイルに移動します。 ProductsContract.cs をクリックして強調表示します。 **[追加]** の横の下向き矢印をクリックしてから、**[リンクとして追加]** をクリックします。

   ![リンクとして追加][24]

6. 次に、Visual Studio エディターで **HomeController.cs** ファイルを開き、名前空間定義を次のコードに置き換えます。*yourServiceNamespace* は実際のサービス名前空間の名前、*yourKey* は SAS キーに置き換えてください。 これで、クライアントからオンプレミスのサービスを呼び出し、その結果を返すことができます。

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
7. ソリューション エクスプローラーで、**ProductsPortal** ソリューションを右クリックします (必ずプロジェクトではなくソリューションを右クリックしてください)。 **[追加]**、**[既存のプロジェクト]** の順にクリックします。
8. **ProductsServer** プロジェクトに移動し、**ProductsServer.csproj** ソリューション ファイルをダブルクリックして追加します。
9. **ProductsPortal** にデータを表示するために、**ProductsServer** が実行されている必要があります。 ソリューション エクスプローラーで **ProductsPortal** ソリューションを右クリックして、**[プロパティ]** をクリックします。 **[プロパティ ページ]** ダイアログ ボックスが表示されます。
10. 左側で、**[スタートアップ プロジェクト]** をクリックします。 左側で、**[マルチ スタートアップ プロジェクト]** をクリックします。 **[ProductsServer]** と **[ProductsPortal]** がこの順で表示されていることと、その両方にアクションとして **[開始]** が設定されていることを確認します。

      ![マルチ スタートアップ プロジェクト][25]

11. 引き続き **[プロパティ]** ダイアログ ボックスで、左側の **[プロジェクトの依存関係]** をクリックします。
12. **[プロジェクト]** リストで **[ProductsServer]** をクリックします。 **[ProductsPortal]** は選択されていないことを確認してください。
13. **[プロジェクト]** リストで **[ProductsPortal]** をクリックします。 **[ProductsServer]** が選択されていることを確認してください。

    ![プロジェクト依存関係][26]

14. **[プロパティ ページ]** ダイアログ ボックスで **[OK]** をクリックします。

## <a name="run-the-project-locally"></a>プロジェクトをローカルで実行する

アプリケーションをローカルでテストするには、Visual Studio で **F5** キーを押します。 まず、オンプレミスのサーバー (**ProductsServer**) を起動し、次に **ProductsPortal** アプリケーションをブラウザー ウィンドウで起動する必要があります。 この時点で、オンプレミスのシステムの商品サービスから取得された商品在庫一覧データが表示されます。

![Web アプリケーション][10]

**ProductsPortal** ページで **[最新の情報に更新]** をクリックします。 ページを更新するたびに、**ProductsServer** から `GetProducts()` が呼び出され、サーバー アプリにメッセージが表示されます。

次の手順に進む前に、両方のアプリケーションを終了します。

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Azure Web アプリに ProductsPortal プロジェクトをデプロイする

次の手順は、Azure Web アプリを **ProductsPortal** フロントエンドに再発行することです。 以下の手順を実行します。

1. ソリューション エクスプローラーで **[ProductsPortal]** プロジェクトを右クリックし、**[発行]** をクリックします。 次に、**[発行]** ページで **[発行]** をクリックします。

   > [!NOTE]
   > デプロイの後で **ProductsPortal** Web プロジェクトが自動的に起動するとき、ブラウザー ウィンドウにエラー メッセージが表示されることがあります。 これは想定されたエラーです。**ProductsServer** アプリケーションがまだ実行されていないため、このようなエラーが発生します。
   >
   >

2. デプロイされた Web アプリの URL をコピーしてください。次の手順でこの URL が必要になります。 この URL は、Visual Studio の [Azure App Service のアクティビティ] ウィンドウからも入手できます。

   ![デプロイされたアプリの URL][9]

3. ブラウザー ウィンドウを閉じてアプリケーションの実行を停止します。

### <a name="set-productsportal-as-web-app"></a>ProductsPortal を Web アプリとして設定する

アプリケーションをクラウドで実行する前に、**ProductsPortal** が Visual Studio で Web アプリとして起動していることを確認する必要があります。

1. Visual Studio で **[ProductsPortal]** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
2. 左側の列で、**[Web]** をクリックします。
3. **[開始動作]** セクションで **[URL の開始]** ボタンをクリックし、前の手順でデプロイした Web アプリの URL をテキスト ボックスに入力します (例: `http://productsportal1234567890.azurewebsites.net/`)。

    ![URL の開始][27]

4. Visual Studio の **[ファイル]** メニューで **[すべて保存]** をクリックします。
5. Visual Studio の [ビルド] メニューで **[ソリューションのリビルド]** をクリックします。

## <a name="run-the-application"></a>アプリケーションの実行

1. F5 キーを押して、アプリケーションをビルドして実行します。 次のスクリーンショットのように、まず、オンプレミスのサーバー (**ProductsServer** コンソール アプリケーション) を起動し、次に **ProductsPortal** アプリケーションをブラウザー ウィンドウで起動する必要があります。オンプレミスのシステムの商品サービスから取得された商品在庫一覧データが Web アプリ上に表示されることに、再度注目してください。 URL を確認し、**ProductsPortal** が Azure Web アプリとしてクラウドで実行されていることを確かめます。

   ![Azure で Web アプリを実行][1]

   > [!IMPORTANT]
   > **ProductsServer** コンソール アプリケーションが実行されており、**ProductsPortal** アプリケーションにデータを提供できる状態になっている必要があります。 ブラウザーにエラーが表示された場合は、**ProductsServer** が次のメッセージを読み込んで表示するまで数秒待ってください。 その後で、ブラウザーで **[最新の情報に更新]** をクリックします。
   >
   >

   ![サーバーからの出力][37]
2. ブラウザーに戻り、**ProductsPortal** ページで **[最新の情報に更新]** をクリックします。 ページを更新するたびに、**ProductsServer** から `GetProducts()` が呼び出され、サーバー アプリにメッセージが表示されます。

    ![更新された出力][38]

## <a name="next-steps"></a>次の手順
次のチュートリアルに進みます。 

> [!div class="nextstepaction"]
>[オンプレミスの WCF サービスをネットワーク外部の WCF クライアントに公開する](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
