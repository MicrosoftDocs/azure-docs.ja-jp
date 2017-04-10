---
title: "Reliable Actors フレームワークにおけるポリモーフィズム | Microsoft Docs"
description: "Reliable Actors フレームワークで .NET のインターフェイスと型の階層を作成して、機能と API の定義を再利用します。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/28/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2fbce8754a5e3162e3f8c999b34ff25284911021
ms.openlocfilehash: 731c6542ba6d1385eeffa89a6f62e5bcf57a5c1e
ms.lasthandoff: 12/14/2016


---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Reliable Actors フレームワークにおけるポリモーフィズム
Reliable Actors フレームワークでは、オブジェクト指向設計で使用する手法の多くを使用してアクターを作成できます。 このような手法の&1; つがポリモーフィズムで、型とインターフェイスが汎用性の高い親から継承できるようにします。 Reliable Actors フレームワークにおける継承は、一般に .NET モデルに従いますが、追加の制約がいくつかあります。

## <a name="interfaces"></a>インターフェイス
Reliable Actors フレームワークでは、アクター型によって実装される&1; つ以上のインターフェイスを定義する必要があります。 このインターフェイスを使用して、クライアントがアクターとの通信に使用できるプロキシ クラスを生成します。 アクター型によって実装されるすべてのインターフェイスとそのすべての親が、最終的に IActor から派生していれば、インターフェイスは他のインターフェイスから継承できます。 IActor は、プラットフォームで定義されているアクターの基本インターフェイスです。 したがって、図形を使用する従来のポリモーフィズムの例は次のようになります。

![図形アクターのインターフェイス階層][shapes-interface-hierarchy]

## <a name="types"></a>型
プラットフォームで提供される Actor 基本クラスから派生したアクター型の階層を作成することもできます。 図形の場合、基本 `Shape` 型を使用します。

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

`Shape` のサブタイプは、基本型のメソッドをオーバーライドできます。

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

アクター型の `ActorService` 属性に注意してください。 この属性は、この型のアクターをホストするためのサービスを自動的に作成する必要があることを Reliable Actors フレームワークに通知します。 サブタイプとの機能の共有だけを目的とし、具体的なアクターのインスタンス化には使用しない基本型を作成する場合があります。 このような場合、 `abstract` キーワードを使用して、その型に基づくアクターは作成しないことを示す必要があります。

## <a name="next-steps"></a>次のステップ
* 信頼性、スケーラビリティ、一貫性のある状態を提供するために、 [Reliable Actors フレームワークで Service Fabric プラットフォームを利用する方法](service-fabric-reliable-actors-platform.md) を確認します。
* [アクターのライフサイクル](service-fabric-reliable-actors-lifecycle.md)の詳細を確認します。

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

