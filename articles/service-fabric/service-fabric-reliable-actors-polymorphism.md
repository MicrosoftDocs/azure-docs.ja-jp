---
title: Reliable Actors フレームワークにおけるポリモーフィズム | Microsoft Docs
description: Reliable Actors フレームワークで .NET のインターフェイスと型の階層を作成して、機能と API の定義を再利用します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662960"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Reliable Actors フレームワークにおけるポリモーフィズム
Reliable Actors フレームワークでは、オブジェクト指向設計で使用する手法の多くを使用してアクターを作成できます。 このような手法の 1 つがポリモーフィズムで、型とインターフェイスが汎用性の高い親から継承できるようにします。 Reliable Actors フレームワークにおける継承は、一般に .NET モデルに従いますが、追加の制約がいくつかあります。 Java/Linux の場合は、Java モデルに従います。

## <a name="interfaces"></a>インターフェイス
Reliable Actors フレームワークでは、アクター型によって実装される 1 つ以上のインターフェイスを定義する必要があります。 このインターフェイスを使用して、クライアントがアクターとの通信に使用できるプロキシ クラスを生成します。 アクター型によって実装されるすべてのインターフェイスとそのすべての親が、最終的に IActor(C#) または Actor(Java) から派生していれば、インターフェイスは他のインターフェイスから継承できます。 IActor(C#) および Actor(Java) はそれぞれ .NET および Java フレームワークにおいて、プラットフォームで定義されているアクターの基本インターフェイスです。 したがって、図形を使用する従来のポリモーフィズムの例は次のようになります。

![図形アクターのインターフェイス階層][shapes-interface-hierarchy]

## <a name="types"></a>型
プラットフォームで提供される Actor 基本クラスから派生したアクター型の階層を作成することもできます。 図形の場合、`Shape`(C#) または `ShapeImpl`(Java) 型を使用します。

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

`Shape`(C#) または `ShapeImpl`(Java) のサブタイプは、基本型のメソッドをオーバーライドできます。

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

アクター型の `ActorService` 属性に注意してください。 この属性は、この型のアクターをホストするためのサービスを自動的に作成する必要があることを Reliable Actors フレームワークに通知します。 サブタイプとの機能の共有だけを目的とし、具体的なアクターのインスタンス化には使用しない基本型を作成する場合があります。 このような場合、 `abstract` キーワードを使用して、その型に基づくアクターは作成しないことを示す必要があります。

## <a name="next-steps"></a>次の手順
* 信頼性、スケーラビリティ、一貫性のある状態を提供するために、 [Reliable Actors フレームワークで Service Fabric プラットフォームを利用する方法](service-fabric-reliable-actors-platform.md) を確認します。
* [アクターのライフサイクル](service-fabric-reliable-actors-lifecycle.md)の詳細を確認します。

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
