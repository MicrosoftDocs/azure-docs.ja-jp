---
title: ".NET での Azure Relay WCF リレーの概要 | Microsoft Docs"
description: "Azure Relay WCF リレーを使用して、別の場所にホストされた 2 つのアプリケーションに接続する方法について説明します。"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/16/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 670f23ec1dbcec3ac8c926992eb44350d8e1f1b6
ms.lasthandoff: 03/27/2017

---

# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>.NET で Azure Relay WCF リレーを使用する方法
この記事では、Service Bus Relay サービスの使用方法について説明します。 サンプルは、C# で記述され、Windows Communication Foundation (WCF) API と Service Bus アセンブリに含まれている拡張機能を使用しています。 Service Bus Relay の詳細については、「[Azure Relay とは](relay-what-is-it.md)」をご覧ください。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-service-bus-wcf-relay"></a>Service Bus WCF Relay とは
Azure Service Bus WCF [*Relay*](relay-what-is-it.md) サービスを使うと、Azure データセンターと独自のオンプレミスのエンタープライズ環境の両方で動作するハイブリッド アプリケーションを構築できます。 Service Bus Relay を使用することで、ファイアウォール接続を開放せず、または企業ネットワークのインフラストラクチャ内部を変更せずに、企業のエンタープライズ ネットワーク内部にある Windows Communication Foundation (WCF) サービスを安全にパブリック クラウドに公開することができます。

![WCF Relay の概念](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Service Bus Relay を使って、既存のエンタープライズ環境内の WCF サービスをホストできます。 その後で、その WCF サービスに対する受信セッションと受信要求のリッスンを Azure 内で動作するService Bus にデリゲートできます。 これにより、Azure で実行されるアプリケーション コードや、モバイル ワーカー環境またはエクストラネット パートナー環境に対して、これらのサービスを公開できます。 Service Bus を使用すると、これらのサービスにアクセスできるユーザーを詳細なレベルで安全に制御できます。 これは、既存のエンタープライズ ソリューションのデータとアプリケーション機能を公開してクラウドで活用するため、強力で安全な手段となります。

この記事では、Service Bus Relay を使って、2 者間の安全な対話を実現する TCP チャネル バインドを使って公開される WCF Web サービスを作成する方法を示します。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの取得
[Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus) は、Service Bus API を取得し、Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。 プロジェクトに NuGet パッケージをインストールするには、次のステップを行います。

1. [ソリューション エクスプローラー] で、**[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. "Service Bus" を検索して、 **[Microsoft Azure Service Bus]** 項目を選択します。 **[インストール]** をクリックし、インストールが完了したら、次のダイアログ ボックスを閉じます。
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Service Bus を使用して SOAP Web サービスを TCP で公開および使用する
既存の WCF SOAP Web サービスを外部で使用するために公開するには、サービスのバインドとアドレスに変更を加える必要があります。 WCF サービスの設定や構成によっては、構成ファイルを変更したり、コードを変更したりする必要があります。 WCF を使用すると同じサービス上で複数のネットワーク エンドポイントを持つことができるので、既存の内部エンドポイントを保持しながら、同時に外部アクセス用のService Bus エンドポイントも追加できます。

このタスクでは、単純な WCF サービスを構築し、そのサービスに Service Bus リスナーを追加します。 この演習はある程度 Visual Studio に習熟したユーザーを対象としているため、プロジェクトの作成手順の詳しい内容については説明していません。 代わりに、コードに重点を置いています。

これらの手順を開始する前に、次の手順を完了して環境を設定してください。

1. Visual Studio 内で、"Client" と "Service" という 2 つのプロジェクトを含むコンソール アプリケーションをソリューション内に作成します。
2. Microsoft Azure Service Bus NuGet パッケージを両方のプロジェクトに追加します。 このパッケージによって、プロジェクトに必要なアセンブリ参照がすべて追加されます。

### <a name="how-to-create-the-service"></a>サービスの作成方法
最初に、サービス自体を作成します。 すべての WCF サービスは、少なくとも次の 3 つの部分で構成されます。

* 交換されるメッセージと呼び出される操作を指定するコントラクトの定義。
* そのコントラクトの実装。
* WCF サービスをホストして複数のエンドポイントを公開するホスト。

このセクションでは、これらの各コンポーネントを処理するコード例を示します。

このコントラクトでは、2 つの数値を足して結果を返す `AddNumbers` という 1 つの処理を定義します。 `IProblemSolverChannel` インターフェイスは、クライアントでプロキシの有効期間を簡単に管理できるようにするインターフェイスです。 このようなインターフェイスを作成することがベスト プラクティスと考えられます。 このコントラクト定義を別のファイルに格納しておき、そのファイルを "Client" プロジェクトと "Service" プロジェクトの両方から参照できようにすることをお勧めしますが、コードを両方のプロジェクトにコピーしてもよいでしょう。

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

コントラクトが適切に記述されたら、実装は簡単です。

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>サービス ホストをプログラムで構成する
コントラクトと実装が適切に記述されたので、次にサービスをホストします。 ホスティングは [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx) オブジェクトの内部で行い、サービスのインスタンスを管理し、メッセージをリッスンするエンドポイントをホストします。 次のコードでは、サービスに通常のローカル エンドポイントと Service Bus エンドポイントの両方を構成して、内部エンドポイントと外部エンドポイントの外観を並べて指定しています。 文字列 *namespace* は実際の名前空間の名前に置き換え、*yourKey* は前のセットアップ手順で取得した SAS キーに置き換えてください。

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

この例では、同じコントラクト実装にある 2 つのエンドポイントを作成します。 1 つはローカルで、もう 1 つは Service Bus を経由して提示されます。 大きな違いはバインドです。ローカルには [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) を使用し、Service Bus エンドポイントとアドレスには [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) を使用します。 ローカル エンドポイントは、個々のポートを含むローカル ネットワーク アドレスを持ちます。 Service Bus エンドポイントは、文字列 `sb`、使用する名前空間の名前、およびパス "solver" で構成されるエンドポイント アドレスを持ちます。 全体としては `sb://[serviceNamespace].servicebus.windows.net/solver` という URI になり、このサービス エンドポイントは完全修飾の外部 DNS 名を持つ Service Bus TCP エンドポイントとして識別されます。 プレースホルダーを置き換えたコードを **Service** アプリケーションの `Main` 関数に配置すると、このサービスが実際に機能します。 サービスで Service Bus を排他的にリッスンするには、ローカル エンドポイントの宣言を削除します。

### <a name="configure-a-service-host-in-the-appconfig-file"></a>サービス ホストを App.config ファイルで構成する
App.config ファイルを使用してホストを構成することもできます。 この場合にコードをホストするサービスは、次の例にあります。

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

エンドポイントの定義は App.config ファイルに移動します。 NuGet パッケージによって定義の範囲が App.config ファイルに既に追加されています。この定義の範囲は Service Bus に必要な構成の拡張機能です。 次の例 (先に示したコードと同じ) は **system.serviceModel** 要素のすぐ下に配置する必要があります。 このコード例では、プロジェクト C# 名前空間の名前が **Service** であると想定しています。
プレースホルダーを実際の Service Bus Service の名前空間と SAS キーに置き換えてください。

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

以上の変更を加えたサービスは、以前と同じように開始されます。2 つのアクティブなエンドポイントは、1 つはローカルに存在し、もう 1 つはクラウド内でリッスンします。

### <a name="create-the-client"></a>クライアントを作成する
#### <a name="configure-a-client-programmatically"></a>クライアントをプログラムで構成する
サービスを使用するために、[ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) オブジェクトを使用して WCF クライアントを作成できます。 Service Bus は SAS を使用して実装されるトークン ベースのセキュリティ モデルを使用します。 [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) クラスは、いくつかの既知のトークン プロバイダーを返すファクトリ メソッドが組み込まれたセキュリティ トークン プロバイダーを表します。 次の例では [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) メソッドを使用し、適切な SAS トークンの取得を処理します。 名前とキーは、前のセクションで説明したようにポータルから取得されます。

最初に、`IProblemSolver` コントラクト コードをサービスからクライアント プロジェクトに対して参照またはコピーします。

次に、クライアントの `Main` メソッドのコードを置き換えます。ここでも、プレースホルダー テキストを実際の Service Bus の名前空間と SAS キーに置き換えてください。

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

これで、クライアントとサービスを構築して実行できます (先にサービスを実行します)。クライアントはサービスを呼び出し、**9** を出力します。 クライアントとサーバーを別々のコンピューターで実行しても (ネットワークが異なっても)、通信は維持されます。 クライアント コードは、クラウド上でもローカルでも実行できます。

#### <a name="configure-a-client-in-the-appconfig-file"></a>App.config ファイルでクライアントを構成する
次のコードは、App.config ファイルを使用してクライアントを構成する方法を示しています。

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

エンドポイントの定義は App.config ファイルに移動します。 次の例 (先に示したコードと同じ) は、`<system.serviceModel>` 要素のすぐ下に配置する必要があります。 前の例と同じように、プレースホルダーを実際の Service Bus の名前空間と SAS キーに置き換えてください。

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>次のステップ
これで、Service Bus Relay の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [Azure Service Bus アーキテクチャの概要](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Service Bus のサンプルを [Azure のサンプル][Azure samples]からダウンロードするか、[Service Bus サンプルの概要][overview of Service Bus samples]をご覧ください。

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md

