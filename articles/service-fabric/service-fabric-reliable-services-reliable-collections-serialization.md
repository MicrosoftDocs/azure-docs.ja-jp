---
title: Azure Service Fabric での Reliable Collection オブジェクトのシリアル化 | Microsoft Docs
description: Azure Service Fabric での Reliable Collection オブジェクトのシリアル化
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: 2445b37e8152d8f55dad6eff057d273851dc2209
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340690"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Azure Service Fabric での Reliable Collection オブジェクトのシリアル化
Reliable Collection では項目がレプリケートおよび永続化されるため、コンピューターの不具合や電源障害が発生しても、これらの項目が影響を受けることはありません。
項目をレプリケートおよび永続化するには、Reliable Collection でシリアル化する必要があります。

Reliable Collection では、Reliable State Manager を使用して、型に適したシリアライザーを取得します。
Reliable State Manager には組み込みのシリアライザーが格納されており、型に応じてカスタムのシリアライザーを登録できます。

## <a name="built-in-serializers"></a>組み込みのシリアライザー

Reliable State Manager には一般的な型のための組み込みのシリアライザーが含まれているため、既定で効率的にシリアル化できます。 その他の型については、Reliable State Manager は [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx) を使用します。
型が変わらないことが明らかで、型の名前などの情報を含める必要がないため、組み込みのシリアライザーの方が効率的です。

Reliable State Manager には、次の型用の組み込みのシリアライザーがあります。 
- Guid
- bool
- byte
- sbyte
- byte[]
- char
- string
- decimal
- double
- float
- int
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>カスタムのシリアル化

カスタムのシリアライザーは通常、パフォーマンスを向上させたり、ネットワーク上とディスクのデータを暗号化したりするために使用します。 その主な理由は、型に関する情報をシリアル化する必要がないため、汎用シリアライザーに比べて、一般的にカスタム シリアライザーの方が効率性に優れているためです。 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) を使用して、特定の型 T 用のカスタム シリアライザーを登録します。復元が開始される前にすべての Reliable Collection が適切なシリアライザーにアクセスして永続化されたデータを読み取れるようにするため、この登録は StatefulServiceBase の作成時に行う必要があります。

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> カスタム シリアライザーは組み込みのシリアライザーよりも優先されます。 たとえば int 型のカスタム シリアライザーを登録すると、整数のシリアル化には int 型用の組み込みのシリアライザーではなく、カスタム シリアライザーが使用されます。

### <a name="how-to-implement-a-custom-serializer"></a>カスタム シリアライザーの実装方法

カスタム シリアライザーは、[IStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) インターフェイスを実装する必要があります。

> [!NOTE]
> IStateSerializer\<T> には、ベース値と呼ばれる追加の T を受け取る書き込みと読み取りのオーバーロードが含まれています。 この API は差分のシリアル化に使用します。 現在、差分のシリアル化機能は公開されていません。 そのため、差分のシリアル化が公開されて有効になるまで、これら 2 つのオーバーロードは呼び出されません。

4 つのプロパティを含み、OrderKey と呼ばれるカスタムの型の例を次に示します。

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

IStateSerializer\<OrderKey> の実装例を次に示します。
baseValue を受け取る読み取りと書き込みのオーバーロードが、上位互換性のためにそれぞれのオーバーロードを呼び出していることに注意してください。

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>アップグレード可能性
[アプリケーションのローリング アップグレード](service-fabric-application-upgrade.md)では、アップグレードはノードのサブセットに、一度に 1 つのアップグレード ドメインのみに適用されます。 このプロセス中に、一部のアップグレード ドメインがアプリケーションの新しいバージョンになり、一部のアップグレード ドメインはアプリケーションの以前のバージョンになります。 ロールアウト時に、アプリケーションの新しいバージョンは、古いバージョンのデータを、アプリケーションの古いバージョンは新しいバージョンのデータを読み取ることができる必要があります。 データ形式の上位互換性と下位互換性がない場合は、アップグレードが失敗するか、データが失われたり、破損したりするおそれがあります。

組み込みのシリアライザーを使用している場合は、互換性について心配する必要はありません。
一方、カスタム シリアライザーや DataContractSerializer を使用している場合は、データの下位互換性と上位互換性を確保する必要があります。
つまり各バージョンのシリアライザーは、型のどのバージョンもシリアル化と逆シリアル化ができる必要があります。

データ コントラクト ユーザーは、フィールドの追加、削除、変更用に適切に定義されたバージョン管理規則に従う必要があります。 またデータ コントラクトでは、不明なフィールドの処理、シリアル化と逆シリアル化プロセスへのフッキング、クラスの継承に対するサポートもあります。 詳細については、[「データ コントラクトの使用」](https://msdn.microsoft.com/library/ms733127.aspx)をご覧ください。

カスタム シリアライザーのユーザーは、下位互換性と上位互換性を確保するために、使用するシリアライザーのガイドラインに従う必要があります。
すべてのバージョンをサポートする一般的な方法は、最初にサイズ情報を追加したら、後はオプションのプロパティ情報のみを追加することです。
こうすることで、各バージョンで可能な限りの読み取りを実行でき、ストリームの残りの部分を省略できます。

## <a name="next-steps"></a>次の手順
  * [シリアル化とアップグレード](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable Collection の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。
  * [PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。
  * [アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。
  * 「 [高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。
  * 「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。
