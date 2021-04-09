---
title: Reliable Collection での作業
description: Azure Service Fabric アプリケーション内で Reliable Collection を操作するためのベスト プラクティスについて説明します。
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787877"
---
# <a name="working-with-reliable-collections"></a>Reliable Collection での作業
Service Fabric は、Reliable Collection を使用して .NET 開発者が利用できるステートフルなプログラミング モデルを提供します。 具体的には、Service Fabric は Reliable Dictionary と Reliable Queue のクラスを提供します。 これらのクラスを使用すると、状態がパーティション分割され (拡張性のため)、レプリケートされ (可用性のため)、パーティション内でトランザクションが行われます (ACID セマンティックのため)。 リライアブル ディクショナリ オブジェクトの一般的な使い方と、実際の動作を見てみましょう。

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
   await Task.Delay(100);
}
```

リライアブル ディクショナリ オブジェクト (取り消しができない ClearAsync を除く) に対するすべての操作で、ITransaction オブジェクトが必要です。 このオブジェクトは 1 つのパーティション内の Reliable Dictionary や Reliable Queue オブジェクトに対して試みているすべての変更に関連付けられています。 ITransaction オブジェクトを取得するには、そのパーティションの StateManager の CreateTransaction メソッドを呼び出します。

上記のコードでは、ITransaction オブジェクトは、Reliable Dictionary の AddAsync メソッドに渡されます。 内部的には、キーを受け付けるディクショナリのメソッドは、そのキーに関連付けられている読み取り/書き込みロックを受け取ります。 メソッドがキーの値を変更する場合、メソッドはキーの書き込みロックを受け取り、メソッドがキーの値を読み取るだけである場合は、キーの読み取りロックを受け取ります。 AddAsync はキーの値を新しく渡された値に変更するため、キーの書き込みロックを受け取ります。 そのため、2 つ (またはそれ以上) のスレッドが同じキーを使用して同時に値を追加しようとすると、1 つのスレッドで書き込みロックを取得し、もう 1 つのスレッドがブロックします。 既定では、メソッドはロックの取得のため最大 4 秒間ブロックします。4 秒後に、メソッドは、TimeoutException をスローします。 メソッドのオーバーロードが存在する場合は、必要に応じて明示的なタイムアウト値を渡すことができます。

通常は、TimeoutException に応答するコードを記述します。このコードは、TimeoutException を取得して操作全体を (上記のコードのように) やり直します。 この単純なコードでは、毎回 100 ミリ秒を渡す Task.Delay を呼び出しています。 しかし、実際には、ある種の指数バックオフ遅延を代わりに使用する方がいい場合もあります。

ロックが取得されると、AddAsync はキーと値のオブジェクト参照を ITransaction オブジェクトに関連付けられた内部一時ディクショナリに追加します。 これは、read-your-own-writes セマンティクスを提供するためです。 つまり、AddAsync を呼び出した後で TryGetValueAsync を同じ ITransaction オブジェクトを使用して呼び出すと、トランザクションをまだコミットしていなくても値が返されます。

> [!NOTE]
> 新しいトランザクションで TryGetValueAsync を呼び出すと、最後にコミットされた値への参照が返されます。 変更を永続化およびレプリケートするメカニズムがバイパスされるため、この参照を直接変更しないでください。 リライアブル ディクショナリ API を使用することがキーの値を変更する唯一の方法となるよう、値を読み取り専用にすることをお勧めします。

次に、AddAsync はキーと値のオブジェクトをバイト配列にシリアル化し、そのバイト配列をローカル ノードのログ ファイルに追加します。 最後に、AddAsync はすべてのセカンダリ レプリカに同じキー/値の情報が含まれるように、バイト配列を送信します。 キー/値の情報がログ ファイルに書き込まれていても、関連付けられているトランザクションがコミットされるまではその情報がディクショナリの一部と見なされません。

上記のコードでは、CommitAsync への呼び出しによって、トランザクションのすべての操作がコミットされます。 具体的には、ローカル ノードのログ ファイルにコミットの情報が追加され、さらにコミット レコードがすべてのセカンダリ レプリカに送信されます。 レプリカのクォーラム (大多数) が応答すると、すべてのデータ変更が永続的と見なされ、ITransaction オブジェクトによって操作されたキーに関連付けられたすべてのロックがリリースされるため、他のスレッド/トランザクションで同じキーとその値を操作できるようになります。

CommitAsync が呼び出されない場合 (通常はスローされている例外が原因です)、ITransaction オブジェクトは破棄されます。 コミットされていない ITransaction オブジェクトを破棄すると、Service Fabric は中断情報をローカル ノードのログ ファイルに追加するため、セカンダリ レプリカには何も送信する必要はありません。 その後、トランザクションによって操作されたキーに関連付けられたすべてのロックがリリースされます。

## <a name="volatile-reliable-collections"></a>揮発性の Reliable Collection 
レプリケートされたキャッシュなど、一部のワークロードでは、不定期のデータ損失を許容できる可能性があります。 データがディスクに永続化されないようにすると、Reliable Dictionary に書き込むときに、待機時間とスループットを向上させることができます。 永続化の欠如のトレードオフとして、クォーラム損失が発生した場合に完全なデータ損失が発生します。 クォーラム損失はめったに発生しないため、パフォーマンスの向上は、これらのワークロードでまれに発生するデータ損失に値する可能性があります。

現在、揮発性のサポートは、Reliable Dictionary と Reliable Queue でのみ使用でき、ReliableConcurrentQueues では使用できません。 揮発性コレクションを使用するかどうかを判断するには、[注意事項](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)の一覧を参照してください。

サービス内で揮発性のサポートを有効にするには、次のように、サービスの種類の宣言内の ```HasPersistedState``` フラグを ```false``` に設定します。
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>既存の永続化されたサービスを揮発性に、またその逆にすることはできません。 これを行うには、既存のサービスを削除してから、更新されたフラグを使用してサービスをデプロイする必要があります。 つまり、```HasPersistedState``` フラグを変更する場合は、完全なデータ損失の発生を受け入れる必要があります。 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>一般的な落とし穴とその回避方法
リライアブル コレクションの内部での動作を理解したところで、それらの間違った使い方の一般的な例を見てみましょう。 次のコードをご覧ください。

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

標準の .NET ディクショナリを使用すると、キー/値をディクショナリに追加して、(LastLogin などの) プロパティの値を変更できます。 ただし、このコードは Reliable Dictionary では正しく動作しません。 AddAsync への呼び出しによってキー/値のオブジェクトがバイト配列にシリアル化され、その配列がローカル ファイルに保存され、さらにセカンダリ レプリカにも送信されることを先ほど説明しました。 後からプロパティの値を変更すると、メモリのプロパティの値しか変更されず、ローカル ファイルやレプリカに送信されたデータは影響を受けません。 プロセスがクラッシュすると、メモリの中身は破棄されます。 新しいプロセスの開始時、または別のレプリカがプライマリになった場合は、古いプロパティ値が使用されます。

上記のような間違いは非常によくあることです。 そして、プロセスが実際に停止しないと、間違いに気付きにくいものです。 コードを正しく記述するには、2 つの行を逆にします。


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

よくある間違いの例をもう 1 つ示します。

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

やはり、標準の .NET ディクショナリの場合、上記のコードは問題なく動作します。開発者がキーを使って値を検索するという一般的なパターンです。 値が存在する場合、開発者はプロパティの値を変更します。 しかし、Reliable Collection の場合、このコードによって先ほどと同じ問題が発生します。**オブジェクトを Reliable Collection に指定した後は、変更してはいけません。**

Reliable Collection で値を更新する正しい方法は、既存の値への参照を取得して、これにより参照されるオブジェクトを変更不可と見なすことです。 次に、元のオブジェクトの正確なコピーである新しいオブジェクトを作成します。 これで、この新しいオブジェクトの状態を変更し、新しいオブジェクトをコレクションに書き込むことができます。オブジェクトはバイト配列にシリアル化され、ローカル ファイルに追加され、レプリカに送信されます。 変更をコミットすると、メモリ内オブジェクト、ローカル ファイル、すべてのレプリカの状態が同じになります。 これで問題なしです。

次のコードは、Reliable Collection の値を更新する正しい方法を示しています。

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>変更不可のデータ型を定義してプログラマ エラーを防ぐ
変更不可と見なすオブジェクトの状態を変更するコードを誤って生成してしまった場合にエラーを報告するコンパイラがあると便利です。 しかし、C# コンパイラには、このような機能はありません。 プログラマのバグが発生する可能性を避けるため、Reliable Collection で使用する型を変更不可の型として定義することを強くお勧めします。 つまり、核となる値の型 (Int32、UInt64 のような数値型、DateTime、Guid、TimeSpan など) に従うということです。 文字列を使用することもできます。 コレクション プロパティをシリアル化/逆シリアル化すると、パフォーマンスへの影響が頻繁に発生するので、コレクション プロパティは避けることが賢明です。 ただし、コレクション プロパティを使用する場合は、.NET の変更不可コレクション ライブラリ ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)) を使用することを強くお勧めします。 このライブラリは https://nuget.org からダウンロードできます。クラスをシールして、なるべくフィールドを読み取り専用にすることもお勧めします。

下記の UserInfo 型は、前述の推奨事項を利用して変更不可の型を定義する方法を示しています。

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

次に示すように、ItemId 型も変更不可の型です。

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>スキーマのバージョン管理 (アップグレード)
Reliable Collection は、内部で .NET の DataContractSerializer を使用してオブジェクトをシリアル化します。 シリアル化されたオブジェクトは、プライマリ レプリカのローカル ディスクに対して永続化し、セカンダリ レプリカにも転送されます。 サービスが高度になると、サービスが必要とするデータ (スキーマ) の種類を変更したくなる場合があります。 データのバージョン管理は十分に注意して行ってください。 何よりも、古いデータを常に逆シリアル化できるようにする必要があります。 具体的には、逆シリアル化コードが無限の下位互換性を持っている必要があります。つまり、バージョン 333 のサービス コードが、5 年前のバージョン 1 のサービス コードによる Reliable Collection のデータを操作できる必要があります。

さらに、アップグレード ドメインのサービス コードは一度に 1 つアップグレードされます。 そのため、アップグレード中は、バージョンが異なる 2 つのサービス コードが同時に実行されます。 古いバージョンのサービス コードでは新しいスキーマを処理できない場合があるため、新しいバージョンのサービス コードで新しいスキーマを使用することは避けてください。 可能であれば、サービスのバージョンが 1 バージョンごとに上位互換するように設計する必要があります。 つまり、V1 のサービス コードが明らかに処理しないスキーマ要素を無視できるようにする必要があります。 ただし、明らかに理解できないデータはすべて保存して、ディクショナリのキーまたは値を更新する際に書き戻せるようにする必要があります。

> [!WARNING]
> キーのスキーマは変更できますが、キーのハッシュ コードと等号アルゴリズムは変更できないようにしてください。 これらのいずれかのアルゴリズムの動作を変更すると、以後 Reliable Dictionary 内のキーを検索することができなくなります。
> .NET の文字列はキーとして使用できますが、文字列自体をキーとして使用し、String.GetHashCode の結果はキーとして使用しないでください。

または、いわゆる 2 段階アップグレードを実行することができます。 2 段階アップグレードでは、V1 から V2 にサービスをアップグレードします。V2 には新しいスキーマの変更を処理できるコードが含まれていますが、このコードは実行しません。 V2 コードが V1 のデータを読み取ると、V1 に対する操作を行い、V1 のデータを書き込みます。 次に、すべてのアップグレード ドメインでアップグレードが完了したら、アップグレードが完了したことを実行中の V2 のインスタンスに何らかの方法で通知できます。 (1 つの通知方法として、構成のアップグレードのロールアウトがあります。これにより 2 段階アップグレードが行われます。)これで、V2 のインスタンスが V1 のデータを読み取り、V2 のデータに変換し、処理を行って、V2 のデータとして書き出すことができます。 他のインスタンスが V2 のデータを読み取るときに、変換の必要はありません。インスタンスは操作を行い、V2 のデータを書き出すだけです。

## <a name="next-steps"></a>次のステップ
上位互換性のあるデータ コントラクトを作成する方法については、「[上位互換性のあるデータ コントラクト](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)」を参照してください。

データ コントラクトのバージョン管理のベスト プラクティスについては、「[データ コントラクトのバージョン管理](/dotnet/framework/wcf/feature-details/data-contract-versioning)」を参照してください。

バージョン トレラントなデータ コントラクトを実装する方法については、「[バージョン トレラントなシリアル化コールバック](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)」を参照してください。

複数のバージョンで相互運用できるデータ構造を提供する方法については、「[IExtensibleDataObject インターフェイス](/dotnet/api/system.runtime.serialization.iextensibledataobject)」を参照してください。
