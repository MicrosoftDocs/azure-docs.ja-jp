---
title: "C# でアクター ベースの Azure マイクロサービスを初めて作成する | Microsoft Docs"
description: "このチュートリアルでは、Service Fabric Reliable Actors を使用して簡単なアクターベースのサービスを作成、デバッグ、デプロイする手順について説明します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/06/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 41f9742befcc034f46f166b1f3f3d81ef67f83e7
ms.lasthandoff: 03/10/2017


---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors の使用
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-actors-get-started.md)
> * [Linux での Java](service-fabric-reliable-actors-get-started-java.md)
> 
> 

ここでは、Azure Service Fabric Reliable Actors の基本と、Visual Studio で簡単な Reliable Actors アプリケーションを作成、デバッグ、デプロイする手順について説明します。

## <a name="installation-and-setup"></a>インストールとセットアップ
開始する前に、マシン上に Service Fabric 開発環境がセットアップされていることを確認します。
セットアップする必要がある場合は、 [開発環境のセットアップ方法](service-fabric-get-started.md)に関する詳細な手順を参照してください。

## <a name="basic-concepts"></a>基本的な概念
いくつかの基本的な概念さえ理解すれば、Reliable Actors の使用を開始できます。

* **アクター サービス**。 Reliable Actors は、Service Fabric インフラストラクチャにデプロイできる Reliable Services にパッケージ化されます。 Actor インスタンスは、名前付きのサービス インスタンス内でアクティブ化されます。
* **アクター登録**。 Reliable Services と同様に、Reliable Actor サービスは Service Fabric ランタイムに登録する必要があります。 さらに、アクターの型を Actor ランタイムに登録する必要があります。
* **アクター インターフェイス**。 アクター インターフェイスは、アクターの厳密に型指定されたパブリック インターフェイスを定義するために使用されます。 Reliable Actor モデルの用語では、アクター インターフェイスに、アクターが理解し、処理できるメッセージの種類が定義されています。 アクター インターフェイスは、他のアクターとクライアント アプリケーションがメッセージをアクターに (非同期に) "送信" するために使用されます。 Reliable Actors は複数のインターフェイスを実装できます。
* **ActorProxy クラス**。 ActorProxy クラスは、アクター インターフェイスを介して公開されるメソッドを呼び出すためにクライアント アプリケーションで使用されます。 ActorProxy クラスは、次の&2; つの重要な機能を提供します。
  
  * 名前解決: クラスター内のアクターを特定できます (ホストされているクラスターのノードを検索できます)。
  * エラー処理: たとえば、アクターをクラスター内の別のノードに再配置する必要があるエラーが発生した後に、メソッドの呼び出しを再試行し、アクターの場所を再解決することができます。

アクター インターフェイスに関する次の規則に注意する必要があります。

* アクター インターフェイス メソッドはオーバー ロードできません。
* アクター インターフェイス メソッドには、out、ref、optional パラメーターを使用できません。
* ジェネリック インターフェイスはサポートされません。

## <a name="create-a-new-project-in-visual-studio"></a>Visual Studio での新しいプロジェクトの作成
Visual Studio 2015 または Visual Studio 2017 を管理者として起動し、新しい Service Fabric アプリケーション プロジェクトを作成します。

![Visual Studio 用の Service Fabric ツール - 新しいプロジェクト][1]

次のダイアログ ボックスで、作成するプロジェクトの種類を選択できます。

![Service Fabric プロジェクトのテンプレート][5]

HelloWorld プロジェクトで、Service Fabric Reliable Actors サービスを使用してみましょう。

ソリューションの作成後は、次の構造を確認します。

![Service Fabric プロジェクトの構造][2]

## <a name="reliable-actors-basic-building-blocks"></a>Reliable Actors の基本的な構成要素
通常の Reliable Actors ソリューションは、次の&3; つのプロジェクトで構成されます。

* **アプリケーション プロジェクト (MyActorApplication)**。 デプロイメントのためにすべてのサービスを一緒にパッケージ化するプロジェクトです。 アプリケーションを管理するための *ApplicationManifest.xml* と PowerShell スクリプトが含まれています。
* **インターフェイス プロジェクト (MyActor.Interfaces)**。 アクターのインターフェイス定義が含まれているプロジェクトです。 MyActor.Interfaces プロジェクトでは、ソリューションでアクターによって使用されるインターフェイスを定義できます。 アクター インターフェイスは、任意の名前でどのプロジェクトでも定義できます。ただし、このインターフェイスは、アクター実装とアクターを呼び出すクライアントが共有するアクター コントラクトを定義するため、通常は、他の複数のプロジェクトで共有できる、アクター実装とは別のアセンブリで定義します。

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **アクター サービス プロジェクト (MyActor)**。 アクターをホストする Service Fabric サービスの定義に使用されるプロジェクトです。 アクターの実装が含まれています。 アクター実装は、基本型 `Actor` から派生し、MyActor.Interfaces プロジェクトで定義されたインターフェイスを実装するクラスです。 またアクター クラスは、`ActorService` インスタンスおよび `ActorId` を受け取り、ベース `Actor` クラスに渡すコンストラクターを実装する必要があります。 これにより、プラットフォームの依存関係をコンストラクターで注入できるようになります。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

アクター サービスは、Service Fabric ランタイムのサービスの種類に登録する必要があります。 アクター サービスでアクター インスタンスを実行するには、アクター型もアクター サービスに登録する必要があります。 `ActorRuntime` 登録メソッドは、アクターに対してこの処理を実行します。

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Visual Studio で新しいプロジェクトから開始する場合に、アクター定義が&1; つだけであれば、Visual Studio によって生成されるコードには既定で登録が含まれます。 サービスで他のアクターを定義する場合は、次のコードを使用してアクター登録を追加する必要があります。

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> Service Fabric Actors ランタイムは、 [アクター メソッドに関連する一部のイベントとパフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)を出力します。 これらは、診断やパフォーマンスの監視に役立ちます。
> 
> 

## <a name="debugging"></a>デバッグ
Visual Studio 用の Service Fabric ツールは、ローカル マシンでのデバッグをサポートします。 デバッグ セッションを開始するには、F5 キーを押します。 (必要に応じて) Visual Studio でパッケージをビルドします。 また、ローカルの Service Fabric クラスターにアプリケーションをデプロイし、デバッガーをアタッチします。

デプロイ プロセス中は、 **[出力]** ウィンドウで進行状況を確認できます。

![Service Fabric デバッグ出力ウィンドウ][3]

## <a name="next-steps"></a>次のステップ
* [Reliable Actors の Service Fabric プラットフォームの使用方法](service-fabric-reliable-actors-platform.md)
* [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [コード サンプル](https://github.com/Azure/servicefabric-samples)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

