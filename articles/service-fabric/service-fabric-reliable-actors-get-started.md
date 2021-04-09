---
title: Azure Service Fabric でアクターベースのサービスを作成する
description: C# で Service Fabric Reliable Actors を使用して、最初のアクタベースのサービスを作成、デバッグ、およびデプロイする方法を説明します。
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 225ccb67153a33ed47af68ebb1549dce37426278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573463"
---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors の使用
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-actors-get-started.md)
> * [Linux での Java](./service-fabric-create-your-first-linux-application-with-java.md)

この記事では、Visual Studio で簡単な Reliable Actors アプリケーションを作成およびデバッグする手順について説明します。 Reliable Actors の詳細については、「[Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、マシン上に Service Fabric 開発環境 (Visual Studio など) が設定されていることを確認します。 詳細については、[開発環境の設定方法](service-fabric-get-started.md)に関するページをご覧ください。

## <a name="create-a-new-project-in-visual-studio"></a>Visual Studio での新しいプロジェクトの作成

Visual Studio 2019 以降を管理者として起動し、新しい **Service Fabric アプリケーション** プロジェクトを作成します。

![Visual Studio 用の Service Fabric ツール - 新しいプロジェクト][1]

次のダイアログ ボックスで、 **[.NET Core 2.0]** の下の **[アクター サービス]** を選択し、サービスの名前を入力します。

![Service Fabric プロジェクトのテンプレート][5]

作成されたプロジェクトの構造は次のとおりです。

![Service Fabric プロジェクトの構造][2]

## <a name="examine-the-solution"></a>ソリューションの確認

ソリューションには 3 つのプロジェクトが含まれています。

* **アプリケーション プロジェクト (MyApplication)** 。 このプロジェクトには、デプロイ用のすべてのサービスがまとめてパッケージ化されています。 アプリケーションを管理するための *ApplicationManifest.xml* と PowerShell スクリプトが含まれています。

* **インターフェイス プロジェクト (HelloWorld.Interfaces)** 。 このプロジェクトには、アクターのインターフェイス定義が含まれています。 アクター インターフェイスは、任意の名前の任意のプロジェクトで定義できます。  このインターフェイスは、アクター コントラクトを定義します。コントラクトは、アクター実装と、アクターを呼び出すクライアントによって共有されます。  クライアント プロジェクトがこれに依存する可能性があるため、通常は、アクター実装とは別のアセンブリで定義するのが妥当です。

* **アクター サービス プロジェクト (HelloWorld)** 。 このプロジェクトは、アクターをホストする Service Fabric サービスを定義します。 これには、アクターの実装である *HelloWorld.cs* が含まれています。 アクター実装は、基本型 `Actor` から派生し、*MyActor.Interfaces* プロジェクトで定義されたインターフェイスを実装するクラスです。 またアクター クラスは、`ActorService` インスタンスおよび `ActorId` を受け取り、ベース `Actor` クラスに渡すコンストラクターを実装する必要があります。
    
    このプロジェクトには *Program.cs* も含まれています。これは、`ActorRuntime.RegisterActorAsync<T>()` を使用して、アクター クラスを Service Fabric ランタイムに登録します。 `HelloWorld` クラスは既に登録されています。 プロジェクトに追加された追加アクター実装も、`Main()` メソッドに登録する必要があります。

## <a name="customize-the-helloworld-actor"></a>HelloWorld アクターのカスタマイズ

プロジェクト テンプレートは `IHelloWorld` インターフェイスでいくつかのメソッドを定義し、それらのメソッドを `HelloWorld` アクター実装に実装します。  アクター サービスが単純な "Hello World" 文字列を返すように、それらのメソッドを置き換えます。

*HelloWorld.Interfaces* プロジェクトの *IHelloWorld.cs* ファイルで、インターフェイス定義を次のように置き換えます。

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

**HelloWorld** プロジェクトの **HelloWorld.cs** で、クラス定義全体を次のように置き換えます。

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

**Ctrl + B + Shift** キーを押してプロジェクトをビルドし、すべてがコンパイルされることを確認してください。

## <a name="add-a-client"></a>クライアントの追加

簡単なコンソール アプリケーションを作成して、アクター サービスを呼び出します。

1. ソリューション エクスプローラーでソリューションを右クリックし、 **[追加]**  >  **[新しいプロジェクト]** の順に選択します。

2. **.NET Core** プロジェクト タイプで、**コンソール アプリ (.NET Core)** を選択します。  プロジェクトの名前として「*ActorClient*」と入力します。
    
    ![[新しいプロジェクトの追加] ダイアログ][6]    
    
    > [!NOTE]
    > コンソール アプリケーションは、Service Fabric でクライアントとして通常使用するアプリの種類ではありませんが、ローカル Service Fabric クラスターを使用したデバッグおよびテストの例として役に立ちます。

3. コンソール アプリケーションは、インターフェイス プロジェクトおよびその他の依存関係との互換性を維持するために、64 ビット アプリケーションである必要があります。  ソリューション エクスプローラーで、**ActorClient** プロジェクトを右クリックし､ **[プロパティ]** をクリックします｡  **[ビルド]** タブで、 **[プラットフォーム ターゲット]** を **[x64]** に設定します。
    
    ![プロパティをビルドする][8]

4. クライアント プロジェクトには、信頼できるアクターの NuGet パッケージが必要です。  **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順にクリックします。  パッケージ マネージャー コンソールで、次のコマンドを入力します。
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    NuGet パッケージとそのすべての依存関係が、ActorClient プロジェクトにインストールされます。

5. クライアント プロジェクトには、インターフェイス プロジェクトへの参照も必要です。  ActorClient プロジェクトで、 **[依存関係]** を右クリックし、 **[プロジェクト参照の追加]** をクリックします。 **[プロジェクト] > [ソリューション]** (まだ選択されていない場合) の順に選択し、 **[HelloWorld.Interfaces]** の横にあるチェック ボックスをオンにします。  **[OK]** をクリックします。
    
    ![[参照の追加] ダイアログ][7]

6. ActorClient プロジェクトで、*Program.cs* のすべての内容を次のコードで置き換えます。
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>実行およびデバッグ

**F5** キーを押して、Service Fabric 開発クラスターで、アプリケーションをローカルでビルド、配置、および実行します。  デプロイ プロセス中は、 **[出力]** ウィンドウで進行状況を確認できます。

![Service Fabric デバッグ出力ウィンドウ][3]

出力に "*アプリケーションの準備が整いました*" というテキストが含まれる場合は、ActorClient アプリケーションを使用してサービスをテストできます。  ソリューション エクスプローラーで、**ActorClient** プロジェクトを右クリックし､ **[デバッグ]**  >  **[新しいインスタンスを開始]** の順にクリックします。  コマンド ライン アプリケーションには、アクター サービスからの出力が表示されます。

![アプリケーションの出力][9]

> [!TIP]
> Service Fabric Actors ランタイムは、 [アクター メソッドに関連する一部のイベントとパフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)を出力します。 これらは、診断やパフォーマンスの監視に役立ちます。

## <a name="next-steps"></a>次のステップ
「[Reliable Actors の Service Fabric プラットフォームの使用方法](service-fabric-reliable-actors-platform.md)」で詳細を参照してください。


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png
