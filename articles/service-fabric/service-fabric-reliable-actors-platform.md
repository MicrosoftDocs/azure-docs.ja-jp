---
title: Service Fabric の Reliable Actors
description: Reliable Actors と Reliable Services の階層的な関係と、Reliable Actors による Service Fabric プラットフォームの機能の使用方法について説明します。
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789584"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>高信頼アクターの Service Fabric プラットフォームの使用方法
この記事では、Reliable Actors による Azure Service Fabric プラットフォームの使用方法について説明します。 Reliable Actors は、*アクター サービス* と呼ばれるステートフル リライアブル サービスの実装にホストされるフレームワークで実行されます。 アクター サービスには、アクターのライフサイクルとメッセージ ディスパッチを管理するうえで必要なコンポーネントがすべて含まれています。

* アクター ランタイムはライフサイクルとガベージ コレクションを管理し、シングルスレッドのアクセスを強制します。
* アクター サービス リモート処理リスナーはアクターに対するリモート アクセス呼び出しを受け取り、ディスパッチャに送信して、適切なアクター インスタンスにルーティングします。
* アクター状態プロバイダーは各種状態プロバイダー (Reliable Collections 状態プロバイダーなど) をラップし、アクターの状態管理のためのアダプターを提供します。

これらのコンポーネントで Reliable Actors フレームワークが構成されます。

## <a name="service-layering"></a>サービスのレイヤー
アクター サービス自体は Reliable Service であるため、Reliable Services の[アプリケーション モデル](service-fabric-application-model.md)、ライフサイクル、[パッケージ化](service-fabric-package-apps.md)、[デプロイメント](service-fabric-deploy-remove-applications.md)、アップグレード、スケーリングの各概念は、すべてアクター サービスにも同様に適用されます。

![アクター サービスのレイヤー][1]

上記の図は、Service Fabric アプリケーション フレームワークとユーザー コードの関係を示しています。 青色の要素は Reliable Services アプリケーション フレームワークを表しています。また、オレンジ色の要素は Reliable Actors フレームワークを表し、緑色の要素はユーザー コードを表しています。

Reliable Services では、サービスは `StatefulService` クラスを継承します。 このクラス自体は、`StatefulServiceBase` (ステートレス サービスの場合は `StatelessService`) から派生します。 Reliable Actors では、アクター サービスを使用します。 アクター サービスは、アクターが実行されるアクター パターンを実装する `StatefulServiceBase` クラスの別の実装です。 アクター サービス自体は `StatefulServiceBase` の実装にすぎないため、`StatefulService` を継承したときと同様に、`ActorService` から派生した独自のサービスを記述し、サービスレベルの機能を実装できます。次にその例を示します。

* サービスのバックアップと復元。
* すべてのアクターで共有される機能 (サーキット ブレーカーなど)。
* アクター サービス自体と個別アクターでのリモート プロシージャ コール。

詳細については、「[Implementing service-level features in your actor service](service-fabric-reliable-actors-using.md)」 (アクター サービスでのサービス レベルの機能の実装) を参照してください。

## <a name="application-model"></a>アプリケーション モデル
アクター サービスは Reliable Services であるため、アプリケーション モデルは同じです。 ただし、アクター フレームワーク ビルド ツールによって、一部のアプリケーション モデル ファイルが自動的に生成されます。

### <a name="service-manifest"></a>サービス マニフェスト
アクター フレームワーク ビルド ツールは、アクター サービスの ServiceManifest.xml ファイルの内容を自動的に生成します。 このファイルの内容は次のとおりです。

* アクター サービスの種類。 種類の名前は、アクターのプロジェクト名に基づいて生成されます。 アクターの永続化属性に応じて、HasPersistedState フラグも設定されます。
* コード パッケージ。
* 構成パッケージ。
* リソースとエンドポイント。

### <a name="application-manifest"></a>アプリケーション マニフェスト
アクター フレームワーク ビルド ツールによって、アクター サービスの既定のサービス定義が自動的に作成されます。 ビルド ツールによって既定のサービス プロパティが設定されます。

* レプリカ セットの数は、アクターの永続化属性によって決まります。 アクターの永続化属性が変更されるたびに、それに応じて既定のサービス定義のレプリカ セットの数がリセットされます。
* パーティション構成と範囲は Uniform Int64 とフル Int64 キー範囲に設定されます。

## <a name="service-fabric-partition-concepts-for-actors"></a>アクターの Service Fabric のパーティションの概念
アクター サービスはパーティション分割されたステートフル サービスです。 アクター サービスの各パーティションには、アクターのセットが含まれています。 サービス パーティションは、Service Fabric の複数のノードに自動的に分散されます。 その結果、アクター インスタンスが分散されます。

![Actor partitioning and distribution][5]

Reliable Services は、さまざまなパーティション構成とパーティション キー範囲で作成できます。 アクター サービスは、Int64 パーティション構成とフル Int64 キー範囲でアクターをパーティションにマップします。

### <a name="actor-id"></a>アクター ID
サービスで作成される各アクターには、 `ActorId` クラスで表される一意の ID が関連付けられます。 `ActorId` は、ランダム ID を生成することにより、サービス パーティション間での均一なアクターの分散に使用できるあいまいな ID 値です。

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


`ActorId` はいずれも Int64 にハッシュされます。 アクター サービスで Int64 パーティション構成とフル Int64 キー範囲を使用する必要があるのはこのためです。 ただし、`ActorID` には、GUID/UUID、文字列、Int64 などのカスタム ID 値を使用できます。

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

GUID/UUID と文字列を使用している場合、値は Int64 にハッシュされます。 ただし、Int64 を `ActorId`に明示的に指定している場合、Int64 はハッシュされることなくパーティションに直接マップされます。 この手法を使用して、どのパーティションにアクターが配置されるかを制御できます。


## <a name="next-steps"></a>次のステップ
* [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [アクターの API リファレンス ドキュメント](/dotnet/api/microsoft.servicefabric.actors)
* [.NET サンプル コード](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java サンプル コード](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
