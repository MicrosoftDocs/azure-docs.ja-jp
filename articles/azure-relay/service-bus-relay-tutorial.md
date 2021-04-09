---
title: Azure Relay を使用してオンプレミスの WCF REST サービスをクライアントに公開する
description: このチュートリアルでは、Azure WCF Relay を使用してオンプレミスの WCF REST サービスを外部クライアントに公開する方法について説明します。
ms.topic: tutorial
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 7669bc07ad91933cd31bd2ccd10eaf830d98de7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710789"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>チュートリアル: Azure WCF Relay を使用してオンプレミスの WCF REST サービスを外部クライアントに公開する

このチュートリアルでは、Azure Relay を使用して WCF Relay クライアント アプリケーションとサービスを構築する方法について説明します。 [Service Bus メッセージング](../service-bus-messaging/service-bus-messaging-overview.md)を使用した同様のチュートリアルについては、「[Service Bus キューの使用](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)」を参照してください。

このチュートリアルを利用すると、WCF Relay のクライアント アプリケーションとサービス アプリケーションを作成する手順を理解できます。 元の WCF サービスと同様に、サービスは 1 つ以上のエンドポイントを公開するコンストラクトです。 各エンドポイントでは 1 つ以上のサービス操作が公開されます。 サービスのエンドポイントでは、サービスが見つかるアドレス、クライアントがサービスとやり取りする必要がある情報を含むバインド、サービスがそのクライアントに提供する機能を定義するコントラクトを指定します。 WCF と WCF Relay の主な違いは、エンドポイントがコンピューターのローカルではなくクラウドで公開される点です。

このチュートリアルの一連のセクションを実行すると、実行中のサービスが作成されます。 また、サービスの操作を呼び出すことができるクライアントも作成されます。 

このチュートリアルでは、次のタスクを行います。

> [!div class="checklist"]
>
> * このチュートリアルの前提条件をインストールする。
> * Relay 名前空間を作成する。
> * WCF サービス コントラクトを作成する。
> * WCF コントラクトを実装する。
> * リレー サービスに登録する WCF サービスをホストして実行する。
> * サービス コントラクト用の WCF クライアントを作成する。
> * WCF クライアントを構成する。
> * WCF クライアントを実装する。
> * アプリケーションを実行する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* [Visual Studio 2015 またはそれ以降](https://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2019 を使用します。
* Azure SDK for .NET。 [SDK のダウンロード ページ](https://azure.microsoft.com/downloads/)からインストールします。

## <a name="create-a-relay-namespace"></a>Relay 名前空間を作成する

最初の手順として、名前空間を作成し、[Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) キーを取得します。 名前空間により、リレー サービスが公開する各アプリケーションにアプリケーション境界が設けられます。 SAS キーは、サービスの名前空間が作成されると、システムによって自動的に生成されます。 サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Azure が認証する資格情報になります。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>WCF サービス コントラクトを定義する

サービス コントラクトでは、サービスがサポートする操作を指定します。 操作は、Web サービスのメソッドまたは関数です。 コントラクトを作成するには、C++、C#、または Visual Basic インターフェイスを定義します。 インターフェイスの各メソッドは、特定のサービス操作に対応しています。 各インターフェイスには、[ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 属性が適用されている必要があります。また、各操作には、[OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 属性が適用されている必要があります。 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 属性があるインターフェイスのメソッドに [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 属性がない場合、そのメソッドは公開されません。 以下の手順では、これらのタスクのコード例を示します。 コントラクトとサービスの概要については、「[サービスの設計と実装](/dotnet/framework/wcf/designing-and-implementing-services)」を参照してください。

### <a name="create-a-relay-contract-with-an-interface"></a>インターフェイスを使用してリレー コントラクトを作成する

1. Microsoft Visual Studio を管理者として起動します。 これを行うには、Visual Studio のプログラム アイコンを右クリックして **[管理者として実行]** を選択します。
1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、C# の **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。
1. プロジェクトに *EchoService* という名前を付けて、**[作成]** を選択します。

   ![コンソール アプリを作成する][2]

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[NuGet パッケージ マネージャー]** で **[参照]** を選択してから、**WindowsAzure.ServiceBus** を検索して選択します。 **[インストール]** を選択して、使用条件に同意します。

    ![Service Bus パッケージ][3]

   Service Bus ライブラリと WCF の `System.ServiceModel` への参照が、このパッケージによって自動的に追加されます。 [System.ServiceModel](/dotnet/api/system.servicemodel) は、WCF の基本機能にプログラムでアクセスできるようにする名前空間です。 Service Bus は、サービス コントラクトの定義に WCF の多くのオブジェクトと属性を使用します。

1. *Program.cs* ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. 名前空間の名前を、既定の名前である `EchoService` から `Microsoft.ServiceBus.Samples` に変更します。

   > [!IMPORTANT]
   > このチュートリアルでは、C# の名前空間 `Microsoft.ServiceBus.Samples` を使用します。これは、コントラクトベースのマネージド型の名前空間で、「[WCF クライアントを構成する](#configure-the-wcf-client)」セクションに出てくる構成ファイルで使用されます。 このサンプルをビルドするときに、任意の名前空間を指定できます。 ただし、それに応じてアプリケーション構成ファイルでコントラクトとサービスの名前空間を変更しない限り、このチュートリアルは機能しません。 *App.config* ファイルで指定する名前空間は、C# ファイルで指定した名前空間と同じである必要があります。
   >

1. `Microsoft.ServiceBus.Samples` 名前空間の宣言の直後 (ただし、名前空間内部) に、`IEchoContract` という名前の新しいインターフェイスを定義し、このインターフェイスに名前空間の値が `https://samples.microsoft.com/ServiceModel/Relay/` の `ServiceContractAttribute` 属性を適用します。 名前空間の宣言の後に次のコードを貼り付けます。

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    この名前空間の値は、コードのスコープ全体で使用する名前空間とは異なります。 代わりに、この名前空間の値は、このコントラクトの一意の識別子として使用されます。 名前空間を明示的に指定すると、既定の名前空間値がコントラクト名に追加されなくなります。

   > [!NOTE]
   > 通常、サービス コントラクトの名前空間には、バージョン情報を含む名前付けスキームが含まれています。 サービス コントラクトの名前空間にバージョン情報を含めると、サービスは、新しいサービス コントラクトを新しい名前空間を使用して定義し、それを新しいエンドポイントで公開することで、主要な変更を分離できます。 この方法では、クライアントは、古いサービス コントラクトを更新する必要なく、使用し続けることができます。 バージョン情報は、日付またはビルド番号で構成できます。 詳細については、[「サービスのバージョン管理」](/dotnet/framework/wcf/service-versioning)を参照してください。 このチュートリアルでは、サービス コントラクトの名前空間の名前付けスキームにバージョン情報は含まれません。
   >

1. `IEchoContract` インターフェイス内で、`IEchoContract` コントラクトがインターフェイスで公開している単一操作のメソッドを宣言し、次に示すように、パブリック WCF Relay コントラクトの一部として公開するメソッドに `OperationContractAttribute` 属性を適用します。

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. 次に示すように、`IEchoContract` インターフェイスの定義の直後に、`IEchoContract` インターフェイスと `IClientChannel` インターフェイスの両方から継承するチャネルを宣言します。

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    チャネルは、ホストとクライアントが相互に情報を渡すときに経由する WCF オブジェクトです。 後で、2 つのアプリケーション間で情報をエコーするチャネルに対するコードを記述します。

1. **[ビルド]**  >  **[ソリューションのビルド]** の順に選択するか、Ctrl + Shift + B キーを押して、ここまでの作業に問題がないことを確認します。

### <a name="example-of-a-wcf-contract"></a>WCF コントラクトの例

次のコードに、WCF Relay コントラクトを定義する基本的なインターフェイスを示します。

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

これでインターフェイスが作成されたので、このインターフェイスを実装することができます。

## <a name="implement-the-wcf-contract"></a>WCF コントラクトを実装する

Azure Relay を作成するには、まずインターフェイスを使用してコントラクトを作成する必要があります。 インターフェイスの作成の詳細については、前のセクションを参照してください。 次の手順では、インターフェイスを実装します。 このタスクには、ユーザー定義の `EchoService` インターフェイスを実装する `IEchoContract` というクラスの作成が含まれます。 インターフェイスを実装したら、*App.config* 構成ファイルを使用してインターフェイスを構成します。 この構成ファイルには、アプリケーションに必要な情報が含まれています。 この情報には、サービス名、コントラクト名、リレー サービスとの通信に使用されるプロトコルの種類が含まれます。 次の手順では、これらのタスクに使用するコード例を示します。 サービス コントラクトの実装方法の全般的な説明については、「[サービス コントラクトの実装](/dotnet/framework/wcf/implementing-service-contracts)」をご覧ください。

1. `IEchoContract` インターフェイスの定義の直後に、`EchoService` という新しいクラスを作成します。 `EchoService` クラスによって、`IEchoContract` インターフェイスが実装されます。

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    他のインターフェイス実装と同様に、別のファイルに指定した定義を実装することができます。 ただし、このチュートリアルでは、インターフェイス定義や `Main()` メソッドと同じファイルに実装します。

1. `IEchoContract` インターフェイスに [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) 属性を適用します。 サービスの名前と名前空間をこの属性で指定します。 指定後の `EchoService` クラスは次のようになります。

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. `EchoService` クラスの `IEchoContract` インターフェイスで定義された `Echo` メソッドを実装します。

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. **[ビルド]**  >  **[ソリューションのビルド]** の順に選択するか、Ctrl + Shift + B キーを押します。

### <a name="define-the-configuration-for-the-service-host"></a>サービス ホストの構成を定義する

構成ファイルは WCF 構成ファイルと似ています。 これには、サービス名、エンドポイント、およびバインドが含まれています。 エンドポイントは、クライアントとホストの相互通信用に Azure Relay により公開されている場所です。 バインドは、通信に使用されるプロトコルの種類です。 主な違いは、構成されているこのサービス エンドポイントで、.NET Framework に含まれていない [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) バインドが参照されている点です。 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) は、サービスによって定義されたバインドの 1 つです。

1. **ソリューション エクスプローラー** で、**App.config** をダブルクリックして、Visual Studio エディターでそのファイルを開きます。
1. `<appSettings>` 要素内のプレースホルダーを、実際のサービスの名前空間名と先ほどコピーした SAS キーに置き換えます。
1. `<system.serviceModel>` タグ内に、`<services>` 要素を追加します。 1 つの構成ファイルで複数のリレー アプリケーションを定義できます。 ただし、このチュートリアルで定義するのは 1 つだけです。

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. `<services>` 要素内に、サービスの名前を定義する `<service>` 要素を追加します。

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. `<service>` 要素内で、エンドポイント コントラクトの場所に加え、エンドポイントのバインドの種類も定義します。

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    エンドポイントは、クライアントがホスト アプリケーションを検索する場所を定義します。 このチュートリアルでは、後でこの手順を使用して、Azure Relay を介してホストを完全に公開する URI を作成します。 バインドにより、リレー サービスと通信するプロトコルとして TCP を使用することを宣言します。

1. **[ビルド]**  >  **[ソリューションのビルド]** の順に選択するか、Ctrl + Shift + B キーを押して、ここまでの作業に問題がないことを確認します。

### <a name="example-of-implementation-of-a-service-contract"></a>サービス コントラクトの実装例

次のコードは、サービス コントラクトの実装を示します。

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

次のコードは、サービス ホストに関連付けられる *App.config* ファイルの基本的な形式を示します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>リレー サービスに登録する WCF サービスをホストして実行する

この手順では、Azure Relay サービスの実行方法について説明します。

### <a name="create-the-relay-credentials"></a>リレー資格情報を作成する

1. `Main()` で、コンソール ウィンドウから読み取った名前空間と SAS キーを格納するための 2 つの変数を作成します。

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    SAS キーは、後でプロジェクトにアクセスするために使用します。 名前空間は、サービスの URI を作成するために、パラメーターとして `CreateServiceUri` に渡されます。

1. [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) オブジェクトを使用して、資格情報の種類として SAS キーを使用することを宣言します。 1 つ前の手順で追加したコードの直後に次のコードを追加します。

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>サービスのベース アドレスを作成する

前のセクションで追加したコードの後に、サービスのベース アドレスの `Uri` インスタンスを作成します。 この URI では、Service Bus スキーム、名前空間、サービス インターフェイスのパスを指定します。

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" は Service Bus スキームの省略形です。 プロトコルとして TCP を使用していることを示します。 このスキームは、以前に [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) がバインドとして指定されたときにも、構成ファイルで示されていました。

このチュートリアルでは、URI は `sb://putServiceNamespaceHere.windows.net/EchoService` です。

### <a name="create-and-configure-the-service-host"></a>サービス ホストを作成して構成する

1. まだ `Main()` で作業します。接続モードを `AutoDetect` に設定します。

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    接続モードは、サービスがリレー サービスとの通信に使用するプロトコル、つまり HTTP または TCP を表します。 既定の設定である `AutoDetect` を使用すると、サービスは、TCP が利用できる場合は TCP 経由で、TCP が利用できない場合は HTTP 経由で Azure Relay に接続しようとします。 この結果は、サービスがクライアントの通信に指定するプロトコルとは異なります。 そのプロトコルは、使用されるバインドによって決まります。 たとえば、サービスでは [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) バインドを使用できますが、このバインドは、そのエンドポイントが HTTP 経由でクライアントと通信するように指定します。 同じサービスで、サービスが TCP 経由で Azure Relay と通信するように `ConnectivityMode.AutoDetect` を指定することもできます。

1. このセクションで先に作成した URI を使用して、サービス ホストを作成します。

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    サービス ホストは、サービスをインスタンス化する WCF オブジェクトです。 ここで、作成するサービスの種類 (`EchoService` 型) とサービスを公開するアドレスをサービス ホストに渡します。

1. *Program.cs* ファイルの先頭に、[System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) と [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description) への参照を追加します。

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. `Main()` に戻り、パブリック アクセスを有効にするようにエンドポイントを構成します。

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    この手順では、プロジェクトの Atom フィードを調べることでアプリケーションがパブリックに見つけられることを、リレー サービスに通知します。 `DiscoveryType` を `private` に設定した場合、クライアントは引き続きサービスにアクセスできます。 ただし、`Relay` 名前空間を検索するときには、サービスは表示されなくなります。 代わりに、クライアントは、エンドポイントのパスを事前に認識しておく必要があります。

1. *App.config* ファイルで定義されているサービス エンドポイントにサービスの資格情報を適用します。

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    前に説明したように、構成ファイルで複数のサービスとエンドポイントを宣言しておくことができます。 宣言した場合、このコードは構成ファイルを走査し、資格情報を適用する必要があるすべてのエンドポイントを検索します。 このチュートリアルでは、構成ファイルのエンドポイントは 1 つだけです。

### <a name="open-the-service-host"></a>サービス ホストを開く

1. 引き続き `Main()` で、次の行を追加してサービスを開きます。

    ```csharp
    host.Open();
    ```

1. サービスが実行中であることをユーザーに通知し、サービスをシャットダウンする方法を説明します。

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. 完了したら、サービス ホストを閉じます。

    ```csharp
    host.Close();
    ```

1. Ctrl + Shift + B キーを押して、プロジェクトをビルドします。

### <a name="example-that-hosts-a-service-in-a-console-application"></a>コンソール アプリケーションでサービスをホストする例

完成したサービス コードは次のようになります。 このコードには、チュートリアルの前の手順で説明したサービス コントラクトと実装が含まれています。ここでは、コンソール アプリケーションでサービスをホストします。

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>サービス コントラクト用の WCF クライアントを作成する

次のタスクでは、クライアント アプリケーションを作成し、後で実装するサービス コントラクトを定義します。 これらの手順は、コントラクトの定義、*App.config* ファイルの編集、資格情報を使用したリレー サービスへの接続など、サービスの作成に使用した手順に似ています。 以下の手順では、これらのタスクに使用するコード例を示します。

1. クライアント用の現在の Visual Studio ソリューションに新しいプロジェクトを作成します。

   1. **ソリューション エクスプローラー** で (プロジェクトではなく) 現在のソリューションを右クリックし、**[追加]** > **[新しいプロジェクト]** の順に選択します。
   1. **[新しいプロジェクトの追加]** で、C# の **[コンソール アプリ (.NET Framework)]** を選択し、**[次へ]** を選択します。
   1. プロジェクトに「*EchoClient*」という名前を付けて、**[作成]** を選択します。

1. **ソリューション エクスプローラー** で **[EchoClient]** プロジェクトの **Program.cs** ファイルをダブルクリックしてエディターで開きます (まだ開いていない場合)。
1. 名前空間の名前を、既定の名前である `EchoClient` から `Microsoft.ServiceBus.Samples` に変更します。
1. [Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus)をインストールします。

   1. **ソリューション エクスプローラー** で **[EchoClient]** を右クリックし、**[NuGet パッケージの管理]** を選択します。
   1. **[参照]** を選択し、**WindowsAzure.ServiceBus** を探して選択します。 **[インストール]** を選択して、使用条件に同意します。

      ![Service Bus パッケージのインストール][4]

1. *Program.cs* ファイルに、[System.ServiceModel](/dotnet/api/system.servicemodel) 名前空間の `using` ステートメントを追加します。

    ```csharp
    using System.ServiceModel;
    ```

1. 次の例に示すように、サービス コントラクトの定義を名前空間に追加します。 この定義は **Service** プロジェクトで使用される定義と同じです。 `Microsoft.ServiceBus.Samples` 名前空間の先頭に次のコードを追加します。

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Ctrl + Shift + B キーを押して、クライアントをビルドします。

### <a name="example-of-the-echoclient-project"></a>EchoClient プロジェクトの例

次のコードは、**EchoClient** プロジェクトの *Program.cs* ファイルの現在の状態を示します。

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>WCF クライアントを構成する

この手順では、このチュートリアルで前に作成したサービスにアクセスする基本的なクライアント アプリケーションの *App.config* ファイルを作成します。 この *App.config* ファイルでは、エンドポイントのコントラクト、バインド、および名前を定義します。 以下の手順では、これらのタスクに使用するコード例を示します。

1. **ソリューション エクスプローラー** で、**[EchoClient]** プロジェクトの **App.config** をダブルクリックし、Visual Studio エディターでファイルを開きます。
1. `<appSettings>` 要素内のプレースホルダーを、実際のサービスの名前空間名と先ほどコピーした SAS キーに置き換えます。
1. `system.serviceModel` 要素内に、`<client>` 要素を追加します。

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    このコードにより、WCF スタイルのクライアント アプリケーションを定義していることを宣言します。

1. `client` 要素内で、エンドポイントの名前、コントラクト、バインドの種類を定義します。

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    このコードでは、エンドポイントの名前を定義します。 また、サービスで定義されたコントラクトと、クライアント アプリケーションが Azure Relay との通信に TCP を使用するという事実を定義します。 エンドポイント名は、次の手順でこのエンドポイント構成をサービス URI とリンクするために使用されます。

1. **[ファイル]**  >  **[すべて保存]** を選択します。

### <a name="example-of-the-appconfig-file"></a>App.config ファイルの例

次のコードは、Echo クライアントの *App.config* ファイルを示します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>WCF クライアントを実装する

このセクションでは、このチュートリアルで前に作成したサービスにアクセスする基本的なクライアント アプリケーションを実装します。 サービスと同様に、クライアントでも、Azure Relay へアクセスするために多くの同じ操作が実行されます。

* 接続モードを設定します。
* ホスト サービスの場所を示す URI を作成します。
* セキュリティ資格情報を定義します。
* 資格情報を接続に適用します。
* 接続を開きます。
* アプリケーション固有のタスクを実行します。
* 接続を閉じます。

しかし、主な違いの 1 つとして、クライアント アプリケーションではリレー サービスへの接続にチャネルが使用されます。 サービスでは **ServiceHost** の呼び出しが使用されます。 以下の手順では、これらのタスクに使用するコード例を示します。

### <a name="implement-a-client-application"></a>クライアント アプリケーションを実装する

1. 接続モードを `AutoDetect` に設定します。 **EchoClient** アプリケーションの `Main()` メソッドの中に次のコードを追加します。

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. コンソールから読み取られるサービス名前空間と SAS キーの値を保持する変数を定義します。

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Relay プロジェクトでのホストの場所を定義する URI を作成します。

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. サービス名前空間のエンドポイントの資格情報オブジェクトを作成します。

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. *App.config* ファイルに記述されている構成を読み込むチャネル ファクトリを作成します。

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    チャネル ファクトリは、サービスおよびクライアント アプリケーションが通信に使用するチャネルを作成する WCF オブジェクトです。

1. 資格情報を適用します。

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. サービスに対するチャネルを作成して開きます。

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. エコーの基本的なユーザー インターフェイスと機能を記述します。

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    このコードでは、サービスのプロキシとしてチャネル オブジェクトのインスタンスを使用します。

1. チャネルを閉じ、ファクトリを終了します。

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>このチュートリアルのコード例

完成したコードは次のようになります。 このコードは、クライアント アプリケーションを作成する方法、サービスの操作を呼び出す方法、操作の呼び出しが完了した後でクライアントを終了する方法を示します。

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>アプリケーションの実行

1. Ctrl + Shift + B キーを押して、ソリューションをビルドします。 このアクションにより、前の手順で作成したサービス プロジェクトとクライアント プロジェクトの両方がビルドされます。
1. クライアント アプリケーションを実行する前に、サービス アプリケーションが実行されていることを確認してください。 **ソリューション エクスプローラー** で、**[EchoService]** ソリューションを右クリックし、**[プロパティ]** を選択します。
1. **[プロパティ ページ]** で、**[共通プロパティ]** > **[スタートアップ プロジェクト]** の順に選択し、**[マルチ スタートアップ プロジェクト]** を選択します。 **EchoService** がリストの先頭に表示されていることを確認してください。
1. **EchoService** プロジェクトと **EchoClient** プロジェクトの両方の **[アクション]** ボックスを **[開始]** に設定します。

    ![[プロジェクト プロパティ] ページ][5]

1. **[プロジェクトの依存関係]** を選択します。 **[プロジェクト]** で、**[EchoClient]** を選択します。 **[依存先]** で、**[EchoService]** が選択されていることを確認します。

    ![プロジェクト依存関係][6]

1. **[OK]** を選択して **[プロパティ ページ]** を閉じます。
1. F5 キーを押して両方のプロジェクトを実行します。
1. 両方のコンソール ウィンドウが開き、名前空間名の入力が求められます。 先にサービスを実行しておく必要があるので、**[EchoService]** コンソール ウィンドウで名前空間を入力し、Enter キーを押します。
1. 次に、コンソールで、SAS キーの入力を求められます。 SAS キーを入力し、Enter キーを押します。

    コンソール ウィンドウの出力例を次に示します。 表示されている値は、単なる例です。

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    次の例に示すように、サービス アプリケーションによってリッスンされているアドレスがコンソール ウィンドウに出力されます。

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. **[EchoClient]** コンソール ウィンドウに、先ほどサービス アプリケーションについて入力したものと同じ情報を入力します。 クライアント アプリケーションに対して、同じサービス名前空間と SAS キーの値を入力します。
1. これらの値を入力すると、クライアントはサービスに対するチャネルを開き、次のコンソール出力例に示すように、なんらかのテキストを入力するよう求めます。

    `Enter text to echo (or [Enter] to exit):`

    サービス アプリケーションに送信するテキストを入力し、Enter キーを押します。 このテキストは、Echo サービス操作を介してサービスに送信され、次の出力例のように、サービス コンソール ウィンドウに表示されます。

    `Echoing: My sample text`

    クライアント アプリケーションは、`Echo` 操作の戻り値を受け取ります。これは元のテキストで、コンソール ウィンドウに出力されます。 クライアント コンソール ウィンドウの出力例のテキストを次に示します。

    `Server echoed: My sample text`

1. この方法で、クライアントからサービスにテキスト メッセージの送信を続けることができます。 終了したら、クライアント コンソール ウィンドウとサービス コンソール ウィンドウで Enter キーを押して両方のアプリケーションを終了します。

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
>[オンプレミスの WCF REST サービスをネットワーク外部のクライアントに公開する](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
