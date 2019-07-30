---
title: Azure Relay を使用した REST のチュートリアル | Microsoft Docs
description: REST ベースのインターフェイスを表示する簡易な Azure Service Bus Relay ホスト アプリケーションを構築します。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: 4e988724f842ff12cd599eba95c31006fe208fad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422893"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Azure WCF Relay REST のチュートリアル
このチュートリアルでは、REST ベースのインターフェイスを表示する簡易な Azure Relay ホスト アプリケーションを構築する方法について説明します。 REST を使用すると、Web ブラウザーなどの Web クライアントから HTTP 要求を介して Service Bus API にアクセスできるようになります。

本チュートリアルでは、Windows Communication Foundation (WCF) REST プログラミング モデルを使用して、Azure Relay に REST サービスを構築します。 詳細については、WCF ドキュメントの[「WCF Web HTTP プログラミング モデル」](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model)と [「サービスの設計と実装」](/dotnet/framework/wcf/designing-and-implementing-services) を参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Relay 名前空間を作成する。
> * REST ベースの WCF サービス コントラクトを定義する
> * REST ベースの WCF コントラクトを実装する
> * REST ベースの WCF サービスをホストし、実行する
> * サービスを実行してテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [Visual Studio 2015 またはそれ以降](https://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2017 を使用します。
- Azure SDK for .NET。 [SDK のダウンロード ページ](https://azure.microsoft.com/downloads/)からインストールします。

## <a name="create-a-relay-namespace"></a>Relay 名前空間を作成する

Azure で Relay 機能を使用するには、最初にサービス名前空間を作成する必要があります。 名前空間は、アプリケーション内で Azure リソースをアドレス指定するためのスコープ コンテナーを提供します。 [こちらの手順](relay-create-namespace-portal.md)に従って、Relay 名前空間を作成します。

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Azure Relay で使用する REST ベースの WCF サービス コントラクトを定義する

WCF REST スタイルのサービスを作成するには、コントラクトを定義する必要があります。 コントラクトには、ホストがサポートする操作を指定します。 サービス操作は、Web サービス メソッドと考えることができます。 コントラクトを作成するには、C++、C#、または Visual Basic インターフェイスを定義します。 インターフェイスの各メソッドは、特定のサービス操作に対応しています。 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 属性を各インターフェイスに適用する必要があります。また、[OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 属性を各操作に適用する必要があります。 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) があるインターフェイスのメソッドに [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) がない場合、そのメソッドは公開されません。 これらのタスクに使用されるコードの例を手順に従って説明します。

WCF コントラクトと REST スタイルのコントラクトの主な違いは、REST スタイルの [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) にはプロパティ[WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) がある点です。 このプロパティを使用すると、インターフェイス内のメソッドを相手側のインターフェイスのメソッドにマップすることができます。 この例では、[WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) 属性を使用してメソッドを HTTP GET にリンクします。 その結果、Service Bus は、インターフェイスに送信されたコマンドをより正確に取得および解釈できるようになります。

### <a name="to-create-a-contract-with-an-interface"></a>インターフェイスを使用してコントラクトを作成するには

1. 管理者として Visual Studio を開きます。 **[スタート]** メニューの[Visual Studio] を右クリックし、 **[管理者として実行]** をクリックします。
2. 新しいコンソール アプリケーション プロジェクトを作成します。 **[ファイル]** メニューをクリックし、 **[新規作成]** 、 **[プロジェクト]** の順に選択します。 **[新しいプロジェクト]** ダイアログ ボックスで **[Visual C#]** をクリックします。 **[コンソール アプリケーション]** テンプレートを選択し、「**ImageListener**」と名前を付けます。 既定の **[場所]** を使用します。 **[OK]** をクリックしてプロジェクトを作成します。
3. C# プロジェクトの場合、`Program.cs` ファイルが作成されます。 このクラスには、空の `Main()` メソッドが含まれています。このメソッドは、コンソール アプリケーション プロジェクトを正常にビルドするために必要です。
4. Service Bus NuGet パッケージをインストールして、Service Bus と **System.ServiceModel.dll** への参照をプロジェクトに追加します。 WCF の **System.ServiceModel** と Service Bus ライブラリへの参照が、このパッケージによって自動的に追加されます。 ソリューション エクスプローラーで **ImageListener** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。 **[参照]** タブをクリックして、`Microsoft Azure Service Bus` を検索します。 **[インストール]** をクリックして、使用条件に同意します。
5. **System.ServiceModel.Web.dll** への参照をプロジェクトに明示的に追加する必要があります。
   
    a. ソリューション エクスプローラーでプロジェクト フォルダーの **[参照]** フォルダーを右クリックし、 **[参照の追加]** をクリックします。
   
    b. **[参照の追加]** ダイアログ ボックスで、左側にある **[フレームワーク]** タブをクリックし、 **[検索]** ボックスに、「**System.ServiceModel.Web**」と入力します。 **[System.ServiceModel.Web]** チェック ボックスをオンにし、 **[OK]** をクリックします。
6. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](/dotnet/api/system.servicemodel) は、WCF の基本機能にプログラムでアクセスできる名前空間です。 WCF Relay は、サービス コントラクトの定義に WCF の多くのオブジェクトと属性を使用します。 ほとんどの Relay アプリケーションで、この名前空間を使用します。 同様に、[System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) はチャネルの定義に役立ちます。チャネルは、Azure Relay とクライアント Web ブラウザーとの通信を経由するオブジェクトです。 最後に、[System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) には、Web ベースのアプリケーションを作成できる型が含まれています。
7. `ImageListener`名前空間の名前を **Microsoft.ServiceBus.Samples** に変更します。
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. 名前空間の宣言の左中かっこの直後に、**IImageContract** という新しいインターフェイスを定義し、値が `https://samples.microsoft.com/ServiceModel/Relay/` の **ServiceContractAttribute** 属性をインターフェイスに適用します。 この名前空間の値は、コードのスコープ全体で使用する名前空間とは異なります。 この名前空間の値は、このコントラクトの一意の識別子として使用されます。値にはバージョン情報が含まれています。 詳細については、[「サービスのバージョン管理」](https://go.microsoft.com/fwlink/?LinkID=180498)を参照してください。 名前空間を明示的に指定すると、既定の名前空間値がコントラクト名に追加されなくなります。
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. `IImageContract` インターフェイス内で、`IImageContract` コントラクトがインターフェイスで公開している単一操作のメソッドを宣言し、パブリック Service Bus コントラクトの一部として公開するメソッドに `OperationContractAttribute` 属性を適用します。
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. **OperationContract** 属性に、**WebGet** 値を追加します。
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    この追加により、リレー サービスから HTTP GET 要求を `GetImage` にルーティングし、`GetImage` の戻り値を HTTP GETRESPONSE 応答に変換できるようになります。 このチュートリアルの後半で、Web ブラウザーを使用してこのメソッドにアクセスし、ブラウザーに画像を表示します。
11. `IImageContract` 定義の直後に、`IImageContract` インターフェイスと `IClientChannel` インターフェイスの両方から継承するチャネルを宣言します。
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    チャネルは、サービスとクライアントが相互に情報を渡すときに経由する WCF オブジェクトです。 後で、ホスト アプリケーションでチャネルを作成します。 Azure Relay は、ブラウザーからの HTTP GET 要求を **GetImage** 実装に渡すために、このチャネルを使用します。 また、**GetImage** の戻り値を取得して、クライアント ブラウザーの HTTP GETRESPONSE に変換するためにも、このチャネルを使用します。
12. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックして、ここまでの作業に問題がないことを確認します。

### <a name="example"></a>例
次のコードに、WCF Relay コントラクトを定義する基本的なインターフェイスを示します。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>REST ベースの WCF サービス コントラクトを実装する
REST スタイルの WCF Relay サービスを作成するには、まずコントラクトを作成する必要があります。コントラクトは、インターフェイスを使用して定義します。 次の手順はインターフェイスの実装です。 ユーザー定義の **IImageContract** インターフェイスを実装する **ImageService** というクラスを作成します。 コントラクトを実装したら、App.config ファイルを使用してインターフェイスを構成します。 構成ファイルには、サービス名、コントラクト名、リレー サービスとの通信に使用するプロトコルの種類など、アプリケーションに必要な情報が含まれています。 以下の手順では、これらのタスクに使用するコード例を示します。

これまでの手順と同様に、REST スタイルのコントラクトと基本の WCF Relay コントラクトの実装にはほとんど違いがありません。

### <a name="to-implement-a-rest-style-service-bus-contract"></a>REST スタイルの Service Bus コントラクトを実装するには
1. **IImageContract** インターフェイスの定義の直後に、**ImageService** という新しいクラスを作成します。 **ImageService** クラスで **IImageContract** インターフェイスを実装します。
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    他のインターフェイス実装と同様に、別のファイルに指定した定義を実装することができます。 ただし、このチュートリアルでは、インターフェイス定義や `Main()` メソッドと同じファイルで実装します。
2. [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) 属性を **IImageService** クラスに適用して、クラスが WCF コントラクトの実装であることを示します。
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    既に説明したように、この名前空間は従来の名前空間ではありません。 この名前空間は、コントラクトを特定する WCF アーキテクチャの一部です。 詳細については、WCF ドキュメントの記事「[データ コントラクト名](https://msdn.microsoft.com/library/ms731045.aspx)」を参照してください。
3. .jpg 画像をプロジェクトに追加します。  
   
    これは、サービスが受信側ブラウザーに表示する画像です。 プロジェクトを右クリックし、 **[追加]** をクリックします。 **[既存の項目]** をクリックします。 **[既存項目の追加]** ダイアログ ボックスで、使用する .jpg を参照して選択し、 **[追加]** をクリックします。
   
    ファイルを追加するときに、 **[ファイル名:]** フィールドの横にあるドロップダウン リストで **[すべてのファイル]** が選択されていることを確認します。 以降、このチュートリアルでは、画像名が "image.jpg" という前提で説明します。 別のファイルを使用する場合は、画像のファイル名を変更するか、ファイル名に合わせてコードを変更する必要があります。
4. 実行中のサービスから画像ファイルを検出できるようにするには、**ソリューション エクスプローラー**で画像ファイルを右クリックし、 **[プロパティ]** をクリックします。 **[プロパティ]** ウィンドウの **[出力ディレクトリにコピー]** を **[新しい場合はコピーする]** に設定します。
5. **System.Drawing.dll** アセンブリへの参照をプロジェクトに追加し、次の関連する `using` ステートメントも追加します。  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. ビットマップを読み込む次のコンストラクターを **ImageService** クラスに追加し、クライアント ブラウザーに送信する準備をします。
   
    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. 前のコードの直後に、次のように、画像を含む HTTP メッセージを返す **GetImage** メソッドを **ImageService** クラスに追加します。
   
    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    この実装では、**MemoryStream** を使用して画像を取得し、ブラウザーにストリーミングする準備をします。 ゼロ位置からストリーミングを開始し、ストリーム コンテンツを jpeg 形式と宣言し、情報をストリーミングします。
8. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックします。

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Service Bus で Web サービスを実行するための構成を定義するには
1. **ソリューション エクスプローラー**で、**App.config** をダブルクリックして、Visual Studio エディターでそのファイルを開きます。
   
    **App.config** ファイルには、サービス名、エンドポイント (つまり、クライアントとホストの相互通信用に Azure Relay が公開している場所)、バインド (通信に使用するプロトコルの種類) が記載されています。 主な違いは、構成されているサービス エンドポイントが [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) バインドを参照している点です。
2. `<system.serviceModel>` XML 要素は 1 つ以上のサービスを定義する WCF 要素です。 この要素は、サービス名とエンドポイントの定義に使用されます。 `<system.serviceModel>` 要素の下 (ただし、`<system.serviceModel>` 内) に、次の内容の `<bindings>` 要素を追加します。 ここでアプリケーションに使用するバインドを定義します。 複数のバインドを定義できますが、このチュートリアルで定義するバインドは 1 つのみです。
   
    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    上記のコードでは、**relayClientAuthenticationType** を **None** に設定して、WCF Relay の [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) バインドを定義しています。 この設定は、このバインドを使用するエンドポイントは、クライアントの資格情報を必要としないことを示します。
3. `<bindings>` 要素の後に、`<services>` 要素を追加します。 バインドと同様に、1 つの構成ファイルで複数のサービスを定義できます。 ただし、このチュートリアルで定義するサービスは 1 つのみです。
   
    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    この手順では、以前に定義した既定の **webHttpRelayBinding** を使用するサービスを構成します。 また、既定の **sbTokenProvider** を使用します。この値は次の手順で定義します。
4. `<services>` 要素の後に、次の内容の `<behaviors>` 要素を作成します。このとき、"SAS_KEY" キーを、手順 1 で [Azure Portal][Azure portal] から取得した *Shared Access Signature* (SAS) に置き換えます。
   
    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. 引き続き App.config 内で、`<appSettings>` 要素内の接続文字列の値全体を、前の手順でポータルから取得した接続文字列に置き換えます。 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてソリューション全体をビルドします。

### <a name="example"></a>例
次のコードは、**WebHttpRelayBinding** バインドを使用して Service Bus で実行する REST ベース サービスのコントラクトとサービスの実装を示しています。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

次の例は、サービスに関連付けられている App.config ファイルです。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Azure Relay を使用する REST ベースの WCF サービスをホストする
この手順では、WCF Relay でコンソール アプリケーションを使用して、Web サービスを実行する方法について説明します。 この手順で作成するコードの詳細については、手順に従って例を使用して説明します。

### <a name="to-create-a-base-address-for-the-service"></a>サービスのベース アドレスを作成するには
1. `Main()` 関数の宣言で、プロジェクトの名前空間を格納する変数を作成します。 `yourNamespace` は、前に作成した Relay 名前空間の名前に置き換えてください。
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus は、名前空間の名前を使用して一意の URI を作成します。
2. 名前空間に基づくサービスのベース アドレスの `Uri` インスタンスを作成します。
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Web サービス ホストを作成および構成するには
* このセクションで作成した URI アドレスを使用して、Web サービス ホストを作成します。
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    サービス ホストは、ホスト アプリケーションをインスタンス化する WCF オブジェクトです。 この例では、作成するホストの種類 (**ImageService**) と、ホスト アプリケーションを公開するアドレスを渡します。

### <a name="to-run-the-web-service-host"></a>Web サービス ホストを実行するには
1. サービスを開きます。
   
    ```csharp
    host.Open();
    ```
    サービスが実行されます。
2. サービスが実行中であることと、サービスの停止方法を示すメッセージが表示されます。
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. 完了したら、サービス ホストを閉じます。
   
    ```csharp
    host.Close();
    ```

### <a name="example"></a>例
次の例では、チュートリアルの前の手順で説明したサービス コントラクトと実装が含まれています。ここでは、コンソール アプリケーションでサービスをホストします。 次のコードをコンパイルして、ImageListener.exe という実行可能ファイルを作成します。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>サービスを実行してテストする
ソリューションをビルドしたら、次の手順でアプリケーションを実行します。

1. **F5** キーを押すか、実行可能ファイルの場所 (ImageListener\bin\Debug\ImageListener.exe) を参照し、サービスを実行します。 次の手順を実行するために必要なため、アプリケーションを実行したままにします。
2. コマンド プロンプトのアドレスをコピーし、ブラウザーに貼り付けて画像を確認します。
3. 完了したら、コマンド プロンプト ウィンドウで **Enter** キーを押して、アプリを閉じます。

## <a name="next-steps"></a>次の手順
ここでは、Azure Relay サービスを使用するアプリケーションを構築しました。詳細については、次の記事を参照してください。

* [Azure Relay の概要](relay-what-is-it.md)
* [.NET で WCF リレー サービスを使用する方法](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
