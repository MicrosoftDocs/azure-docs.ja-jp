---
title: Reliable Actors の状態管理
description: Reliable Actors の状態が、高可用性のためにどのように管理、永続化、およびレプリケートされるかについて説明します。
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575603"
---
# <a name="reliable-actors-state-management"></a>Reliable Actors の状態管理
Reliable Actors は、ロジックと状態の両方をカプセル化できるシングル スレッド オブジェクトです。 アクターは Reliable Services 上で実行されるため、同じ永続化およびレプリケーション メカニズムを使用して、状態を確実に保持することができます。 このため、エラー後やガベージ コレクションに続く再アクティベーション時、リソース分散またはアップグレードのためにクラスター内のノード間を移動されたときでも、アクターは状態を失いません。

## <a name="state-persistence-and-replication"></a>状態の永続性とレプリケーション
各アクター インスタンスは一意の ID にマッピングされるため、すべての Reliable Actors は、 *ステートフル* であると見なされます。 つまり、同じアクター ID を繰り返し呼び出すと、同じアクター インスタンスにルーティングされます。 一方ステートレス システムでは、クライアント呼び出しが常に同じサーバーにルーティングされるとは限りません。 このため、アクター サービスは常にステートフルなサービスです。

アクターをステートフルと見なすことができても、状態を確実に格納しているとは限りません。 アクターは、データ ストレージの要件に基づいて、次のような状態の永続性とレプリケーションのレベルを選択できます。

* **永続化状態**:状態はディスクに永続化され、3 つ以上のレプリカにレプリケートされます。 永続化状態は、完全なクラスター停止があっても状態を永続化できる、最も持続性のある状態ストレージ オプションです。
* **揮発状態**:状態は 3 つ以上のレプリカにレプリケートされ、メモリだけに保持されます。 揮発状態により、ノード障害時、アクター障害時、およびアップグレードとリソース分散中の回復力が得られます。 ただし、状態はディスクに保持されません。 従って、すべてのレプリカが同時に失われると状態も失われます。
* **非永続化状態**:状態はレプリケートされず、ディスクにも書き込まれません。状態を確実に維持する必要がないアクターにのみ使用します。

永続性の各レベルは、単にサービスの *状態プロバイダー* と *レプリケーション* 構成が異なるだけです。 状態がディスクに書き込まれるかどうかは、状態プロバイダーによって決まります。これは、状態を格納する Reliable Service のコンポーネントです。 またレプリケーションは、サービスがデプロイされるレプリカ数によって決まります。 Reliable Services と同様に、状態プロバイダーもレプリカ数も手動で簡単に設定できます。 アクター フレームワークは、属性を提供します。これはアクターに使用された場合、既定の状態プロバイダーとレプリカ数の自動生成設定を自動的に選択して、上記の 3 つの永続性設定のいずれかを実現します。 StatePersistence 属性は、派生クラスによって継承されません。各 Actor 型は、独自の StatePersistence レベルを提供する必要があります。

### <a name="persisted-state"></a>永続化状態
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
この設定は、ディスクにデータを格納する状態プロバイダーを使用し、自動的にサービス レプリカ数を 3 に設定します。

### <a name="volatile-state"></a>揮発状態
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
この設定は、メモリ内のみの状態プロバイダーを使用し、レプリカ数を 3 に設定します。

### <a name="no-persisted-state"></a>非永続化状態
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
この設定は、メモリ内のみの状態プロバイダーを使用し、レプリカ数を 1 に設定します。

### <a name="defaults-and-generated-settings"></a>既定値と生成される設定
`StatePersistence`属性を使用する場合、状態プロバイダーはアクター サービスが開始される際に、ランタイムで自動的に選択されます。 ただし、レプリカ数は、コンパイル時に Visual Studio アクター ビルド ツールによって設定されます。 ビルド ツールは自動的にアクター サービスの *既定のサービス* を ApplicationManifest.xml 内に生成します。 **最小レプリカ セット サイズ** と **ターゲット レプリカ セット サイズ** のために、パラメーターが作成されます。

これらのパラメーターは手動で変更できます。 ただし`StatePersistence`属性が変更されるたびに、選択された`StatePersistence`属性の既定のレプリカ セット サイズ値にパラメーターは設定され、前の値はオーバーライドされます。 つまり、`StatePersistence`属性の値を変更すると、ServiceManifest.xml に設定した値がビルド時 *のみ* にオーバーライドされます。

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>状態マネージャー
各アクター インスタンスは、独自の状態マネージャーを持ちます。これは、辞書のようなデータ構造で、キーと値のペアを確実に保持します。 状態マネージャーは、状態プロバイダーのラッパーです。 永続化設定の使用に関係なくデータの格納に使用できます。 データ保持中のローリング アップグレードを通じて、実行中のアクター サービスを揮発 (メモリ内のみ) 状態設定から永続化状態設定に変更できることは保証されません。 ただし、実行中のサービスのレプリカ数を変更することはできます。

状態マネージャー キーは、文字列でなければなりません。 値はジェネリックでカスタム型を含む任意のタイプです。 状態マネージャーに格納される値は、データ コントラクト シリアル化可能である必要があります。アクター状態の永続性設定によっては、これらの値がレプリケーション中にネットワーク経由で他のノードに送信されたり、ディスクに書き込まれたりする可能性があるためです。

状態マネージャーは、Reliable Dictionary に含まれているものと同様、状態管理のための一般的な辞書メソッドを公開します。

アクター状態の管理例については、「[Reliable Actors 状態のアクセス、保存、および削除](service-fabric-reliable-actors-access-save-remove-state.md)」を参照してください。

## <a name="best-practices"></a>ベスト プラクティス
アクター状態の管理について推奨されるプラクティスとトラブルシューティングのヒントを次に示します。

### <a name="make-the-actor-state-as-granular-as-possible"></a>アクターの状態はできるだけ詳細に定める
これは、アプリケーションのパフォーマンスとリソース使用率を高めるうえで非常に重要です。 アクターの "名前付き状態" に対する書き込み/更新があった場合は、その "名前付き状態" に対応する値全体がシリアル化され、ネットワーク経由でセカンダリ レプリカに送信されます。  セカンダリは、それをローカル ディスクに書き込み、プライマリ レプリカに返答します。 プライマリは、セカンダリ レプリカのクォーラムからの確認を受け取ると、状態をローカル ディスクに書き込みます。 たとえば、メンバーが 20 個、サイズが 1 MB のクラスの場合、 サイズが 1 KB のクラス メンバーを 1 つ変更しただけでも、1 MB 全体のシリアル化とネットワーク、およびディスク書き込みのコストを支払うことになります。 同様に、値がコレクション (リスト、配列、ディクショナリなど) の場合は、メンバーのいずれか 1 つを変更しただけでも、コレクション全体のコストを支払うことになります。 アクター クラスの StateManager インターフェイスは、ディクショナリのようなものです。 アクターの状態を表すデータ構造は、常にこのディクショナリの上にモデル化する必要があります。
 
### <a name="correctly-manage-the-actors-life-cycle"></a>アクターのライフ サイクルを正しく管理する
アクター サービスの各パーティションでの状態のサイズ管理については、明確なポリシーを定めるようにしてください。 アクター サービスでは、固定数のアクターを使用し、それらをできるだけ再利用するようにしてください。 新しいアクターを継続的に作成する場合は、それらを使い終わった後に、それらを削除する必要があります。 アクター フレームワークでは、存在する各アクターについてのメタデータが格納されます。 アクターのすべての状態を削除しても、そのアクターに関するメタデータが削除されることはありません。 アクターを削除 (「[アクターとその状態を削除する](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)」を参照) し、そのアクターに関するすべての情報をシステムから削除する必要があります。 追加のチェックとして、アクター サービスを定期的に照会 (「[アクターの列挙](service-fabric-reliable-actors-enumerate.md)」を参照) し、アクターの数が想定の範囲内であることを確認するようにしてください。
 
アクター サービスのデータベース ファイルのサイズが必要以上に大きくなっている場合は、前述のガイドラインに従っていることを確認してください。 これらのガイドラインに従ってもデータベース ファイル サイズの問題が解消されない場合は、[サポート チケットを開いて](service-fabric-support.md)製品チームに支援を要請してください。

## <a name="next-steps"></a>次のステップ

Reliable Actors に格納される状態は、ディスクに書き込まれて、高可用性のためにレプリケートされる前に、シリアル化される必要があります。 [Actor 型のシリアル化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)についてさらに学習してください。

次に、[アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)についてさらに学習してください。
