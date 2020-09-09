---
title: Azure HDInsight における Storm の SCP.NET プログラミング ガイド
description: SCP.NET を使用して、Azure HDInsight で実行されている Storm で使用するための .NET ベースの Storm トポロジを作成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: d54a06c457451fc5323ae37b34b53411cdd6abda
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000143"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight における Apache Storm の SCP プログラミング ガイド

SCP は、リアルタイムで、信頼性が高く、整合性のとれた高パフォーマンスのデータ処理アプリケーションを構築するためのプラットフォームです。 オープンソース ソフトウェア コミュニティが設計したストリーム処理システムである、[Apache Storm](https://storm.incubator.apache.org/) の上に構築されています。 Storm は、Nathan Marz によって作成されました。 Twitter からオープンソースとして公開されました。 Storm では、[Apache ZooKeeper](https://zookeeper.apache.org/) を使用しています。これは、信頼性の高い分散化した調整と状態管理を実現する別の Apache プロジェクトです。

SCP プロジェクトでは、Storm を Windows に移植しただけでなく、Windows 環境用に拡張機能とカスタマイズも追加しました。 拡張機能には、.NET 開発者エクスペリエンスと .NET ライブラリが含まれます。 カスタマイズには、Windows ベースのデプロイが含まれます。

拡張機能とカスタマイズを使用すると、オープンソースのソフトウェア プロジェクトをフォークする必要がありません。 Storm の上に構築される派生環境を使用できます。

## <a name="processing-model"></a>処理モデル

SCP のデータは、タプルの継続的なストリームとしてモデル化されます。 通常、タプルは次のように動作します。

1. キューにフローします。
1. Storm トポロジ内にホストされているビジネス ロジックによって取得および変換されます。
1. 出力が別の SCP システムにタプルとしてパイプされるか、または分散ファイル システムなどのストアや SQL Server などのデータベースにコミットされます。

![データが、キュー、処理、データ ストアの順にフィードされる図](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Storm では、アプリケーション トポロジで計算グラフを定義します。 トポロジ内の各ノードに処理ロジックが含まれます。 ノード間のリンクは、データ フローを示します。

トポロジに入力データを挿入するノードは、"_スパウト_" と呼ばれます。 データをシーケンス処理するために使用できます。 入力データは、ファイル ログ、トランザクション データベース、システム パフォーマンス カウンターなどのソースから取得できます。

入力と出力の両方のデータ フローがあるノードは、"_ボルト_" と呼ばれます。 ここで、実際のデータのフィルター処理、選択、および集計が行われます。

SCP では、ベスト エフォート型、"少なくとも 1 回"、および "厳密に 1 回" のデータ処理がサポートされています。

分散ストリーム処理アプリケーションでは、データ処理中にエラーが発生する可能性があります。 このようなエラーには、ネットワークの停止、マシンの障害、コード内のエラーなどがあります。 "少なくとも 1 回" の処理では、エラーが発生したときに、同じデータを自動的に再生して、すべてのデータが少なくとも 1 回処理されることが保証されます。

"少なくとも 1 回" の処理は、シンプルで信頼性が高く、多くのアプリケーションに適しています。 しかし、アプリケーションが正確なカウントを必要とする場合は、"少なくとも 1 回" の処理では不十分です。アプリケーション トポロジ内で同じデータが再生される可能性があるためです。 この場合は、"厳密に 1 回" の処理を使用すると、データが複数回再生されて処理される場合でも確実に正しい結果になります。

SCP では、.NET 開発者は、Java 仮想マシン (JVM) を Storm と使用しながら、リアルタイムのデータ処理アプリケーションを作成できます。 JVM と .NET は、TCP ローカル ソケットを経由して通信されます。 1 つのスパウトと 1 つのボルトが .NET と Java のプロセス ペアになり、ここで、ユーザー ロジックが .NET プロセス内でプラグインとして実行されます。

SCP の上にデータ処理アプリケーションを構築するには、次の手順に従います。

1. キューからデータをプルするスパウトを設計して実装します。
1. 入力データを処理して、データベースなどの外部ストアに保存するボルトを設計して実装します。
1. トポロジを設計して、それを送信し実行します。

トポロジでは、頂点と、それらの間をフローするデータを定義します。 SCP では、トポロジの仕様を取得して Storm クラスター上にデプロイします。ここで、各頂点が 1 つの論理ノードで実行されます。 Storm タスク スケジューラにより、フェールオーバーとスケーリングが処理されます。

この記事では、SCP を使用したデータ処理アプリケーションの構築方法について、いくつかのシンプルな例を使用して説明します。

## <a name="scp-plug-in-interface"></a>SCP プラグイン インターフェイス

SCP プラグインはスタンドアロン アプリケーションです。 開発中は Visual Studio 内で実行でき、運用環境にデプロイした後は、Storm パイプラインにプラグインできます。

SCP プラグインの記述は、他の Windows コンソール アプリケーションの記述と同じです。 SCP.NET プラットフォームで、スパウトとボルト用にいくつかのインターフェイスを宣言します。 作成するプラグイン コードで、これらのインターフェイスを実装します。 この設計の主な目的は、その他のことを SCP.NET プラットフォームに処理させることで、開発者がビジネス ロジックに集中できるようにすることです。

作成するプラグイン コードで、次のインターフェイスのいずれかを実装します。 どのインターフェイスかは、トポロジがトランザクションか非トランザクションか、コンポーネントがスパウトかボルトかによって決まります。

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** は、多くのプラグインに共通のインターフェイスです。現在は、ダミー インターフェイスです。

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** は非トランザクション スパウト用のインターフェイスです。

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

**NextTuple** が呼び出されると、C# コードで 1 つ以上のタプルを出力できます。 出力するものがない場合、このメソッドは何も出力せずに戻ります。

**NextTuple**、**Ack**、および **Fail** メソッドはすべて、C# プロセスの単一スレッドのタイトなループ内で呼び出されます。 出力するタプルがない場合は、**NextTuple** を 10 ミリ秒ほどの短い時間スリープさせます。 このスリープにより、CPU の可用性を無駄にするのを防ぐことができます。

**Ack** メソッドと **Fail** メソッドは、仕様ファイルで受信確認メカニズムが有効になっている場合にのみ呼び出されます。 *seqId* パラメーターで、タプルが受信確認されたか、失敗したかを識別します。 非トランザクション トポロジで受信確認が有効になっている場合は、スパウトで次の **Emit** 関数が使用される必要があります。

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

非トランザクション トポロジで受信確認をサポートしていない場合は、**Ack** と **Fail** を空の関数にしておくことができます。

これらの関数の *parms* 入力パラメーターに空のディクショナリを指定します。このパラメーターは、将来使用するために予約されています。

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** は非トランザクション ボルト用のインターフェイスです。

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

新しいタプルが利用可能になったとき、それを処理するために **Execute** 関数が呼び出されます。

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** はトランザクション スパウト用のインターフェイスです。

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

非トランザクション スパウトの場合と同様に、**NextTx**、**Ack**、および **Fail** はすべて、C# プロセスの単一スレッドのタイトなループ内で呼び出されます。 出力するタプルがない場合は、**NextTx** を 10 ミリ秒ほどの短い時間スリープさせます。 このスリープにより、CPU の可用性を無駄にするのを防ぐことができます。

**NextTx** が呼び出されて新しいトランザクションを開始すると、*seqId* 出力パラメーターで、トランザクションを識別します。 このトランザクションは、**Ack** と **Fail** でも使用されます。 **NextTx** メソッドで、Java 側にデータを出力できます。 このデータは、再生をサポートするために ZooKeeper に格納されます。 ZooKeeper は容量が限られているため、コードでは、メタデータのみを出力し、一括データはトランザクション スパウトに出力しないようにする必要があります。

失敗したトランザクションは、Storm によって自動的に再生されるため、通常は **Fail** は呼び出されません。 ただし、SCP でトランザクション スパウトによって出力されたメタデータを確認できる場合は、メタデータが無効のときに **Fail** を呼び出すことができます。

これらの関数の *parms* 入力パラメーターに空のディクショナリを指定します。このパラメーターは、将来使用するために予約されています。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** はトランザクション ボルト用のインターフェイスです。

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

新しいタプルがボルトに到達すると、**Execute** メソッドが呼び出されます。 **FinishBatch** メソッドは、このトランザクションが終了したときに呼び出されます。 *parms* 入力パラメーターは、将来のために予約されています。

トランザクション トポロジの場合、**StormTxAttempt** は重要なクラスです。 次の 2 つのメンバーが含まれます。**TxId** と **AttemptId** です。 **TxId** メンバーで、特定のトランザクションを識別します。 トランザクションは、失敗して再生される場合、複数回試行される可能性があります。

Storm の Java での処理と同様に、SCP.NET では、各 **StormTxAttempt** オブジェクトを処理する新しい **ISCPBatchBolt** オブジェクトを作成します。 この設計の目的は、並列トランザクション処理をサポートすることです。 トランザクションの試行が終了すると、対応する **ISCPBatchBolt** オブジェクトが破棄され、ガベージ コレクションが実施されます。

## <a name="object-model"></a>オブジェクト モデル

SCP.NET は、開発者がプログラムに使用できる重要なオブジェクトの簡単なセットも提供しています。 オブジェクトは、**Context**、**StateStore**、および **SCPRuntime** です。 このセクションで説明します。

### <a name="context"></a>Context

**Context** オブジェクトでは、アプリケーションに実行環境を提供します。 **ISCPSpout**、**ISCPBolt**、**ISCPTxSpout**、または **ISCPBatchBolt** の各 **ISCPPlugin** インスタンスには、対応する **Context** インスタンスが含まれます。 **Context** により提供される機能は、次の 2 つの部分に分かれています。

* 静的部分。C# プロセス全体で使用できます
* 動的部分。特定の **Context** インスタンスでのみ使用できます

### <a name="static-part"></a>静的部分

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**Logger** オブジェクトは、ログを記録するために提供されています。

**pluginType** オブジェクトは、C# プロセスのプラグインの種類を示します。 プロセスを Java なしのローカル テスト モードで実行する場合、プラグインの種類は **SCP_NET_LOCAL** です。

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Config** プロパティでは、Java 側から構成パラメーターを取得して、C# プラグインの初期化時に渡します。 **Config** パラメーターは、**stormConf** と **pluginConf** の 2 つの部分に分かれています。

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**stormConf** 部分は、Storm によって定義されたパラメーターで、**pluginConf** 部分は SCP によって定義されたパラメーターです。 次に例を示します。

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**TopologyContext** 型では、トポロジ コンテキストを取得します。 これは、複数の並列コンポーネントに最も役に立ちます。 次に例を示します。

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>動的部分

次のインターフェイスは、特定の **Context** インスタンスに関連しています。これは、SCP.NET プラットフォームによって作成され、コードに渡されます。

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

受信確認をサポートする非トランザクション スパウトの場合、次のメソッドが提供されます。

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

受信確認をサポートする非トランザクション ボルトでは、受け取ったタプルを使用して **Ack** または**Fail** を明示的に呼び出す必要があります。 新しいタプルを出力する場合は、ボルトでタプルのアンカーも指定する必要があります。 次のメソッドが提供されています。

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore** オブジェクトでは、メタデータ サービス、モノトニックなシーケンスの生成、および wait-free 調整を提供します。 **StateStore** で、より高レベルの分散コンカレンシーの抽象化を構築できます。 これらの抽象化には、分散ロック、分散キュー、バリア、トランザクション サービスなどがあります。

SCP アプリケーションでは、**State** オブジェクトを使用して [Apache ZooKeeper](https://zookeeper.apache.org/) 内の情報をシリアル化できます。 この機能は、トランザクション トポロジに特に役立ちます。 トランザクション スパウトが応答を停止して再起動する場合、**State** では、ZooKeeper から必要な情報を取得し、パイプラインを再起動できます。

**StateStore** オブジェクトには、次の主要なメソッドがあります。

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**State** オブジェクトには、次の主要なメソッドがあります。

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

**simpleMode** が **true** に設定されている場合は、**Commit** メソッドで、ZooKeeper 内の対応する ZNode を削除します。 それ以外の場合は、このメソッドで、現在の ZNode を削除し、COMMITTED\_PATH に新しいノードを追加します。

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime** クラスでは、次の 2 つのメソッドを提供します。

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**Initialize** メソッドでは、SCP ランタイム環境を初期化します。 このメソッドでは、C# プロセスで Java 側に接続して、構成パラメーターとトポロジ コンテキストを取得します。

**LaunchPlugin** メソッドでは、メッセージ処理ループを開始します。 このループで、C# プラグインにより、Java 側からメッセージを受信します。 これらのメッセージには、タプルとコントロールのシグナルが含まれます。 プラグインでは、コードによって提供されることのあるインターフェイス メソッドを呼び出して、メッセージを処理します。

**LaunchPlugin** の入力パラメーターは委任です。 メソッドで、**ISCPSpout**、**ISCPBolt**、**ISCPTxSpout**、または **ISCPBatchBolt** インターフェイスを実装するオブジェクトを返すことができます。

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

**ISCPBatchBolt** の場合は、*parms* パラメーターから **StormTxAttempt** オブジェクトを取得し、それを使用して試行が再生されたかどうかを判断できます。 多くの場合、再生試行のチェックはコミット ボルトで実行されます。 この記事の後半にある HelloWorldTx の例で、このチェックについて説明します。

通常、SCP プラグインは、ローカル テスト モードと通常モードの 2 つのモードで実行できます。

#### <a name="local-test-mode"></a>ローカル テスト モード

このモードでは、C# コード内の SCP プラグインは、開発フェーズ中は Visual Studio 内で実行します。 このモードで **ILocalContext** インターフェイスを使用できます。 このインターフェイスでは、出力されたタプルをローカル ファイルにシリアル化し、それを RAM に読み取るメソッドを提供します。

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>通常モード

このモードでは、Storm Java プロセスで SCP プラグインを実行します。次に例を示します。

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>トポロジ仕様言語

SCP Topology Specification は、SCP トポロジを記述して構成するためのドメイン固有言語 (DSL) です。 これは [Storm の Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) に基づいており、SCP によって拡張されています。

**runSpec** コマンドを使用して、トポロジの仕様を Storm クラスターに直接送信して実行することができます。

SCP.NET では、トランザクション トポロジを定義する次の関数が追加されています。

| 新しい関数 | パラメーター | 説明 |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |トポロジ名、スパウト定義マップ、およびボルト定義マップを使用してトランザクション トポロジを定義します。 |
| **scp-tx-spout** |*exec-name*<br />*args*<br />*fields* |トランザクション スパウトを定義します。 この関数では、*exec-name* に指定されたアプリケーションを実行し、*args* を使用します。<br /><br />*fields* パラメーターで、スパウトの出力フィールドを指定します。 |
| **scp-tx-batch-bolt** |*exec-name*<br />*args*<br />*fields* |トランザクション バッチ ボルトを定義します。 この関数では、*exec-name* に指定されたアプリケーションを実行し、*args* を使用します。<br /><br />*fields* パラメーターで、ボルトの出力フィールドを指定します。 |
| **scp-tx-commit-bolt** |*exec-name*<br />*args*<br />*fields* |トランザクション コミット ボルトを定義します。 この関数では、*exec-name* に指定されたアプリケーションを実行し、*args* を使用します。<br /><br />*fields* パラメーターで、ボルトの出力フィールドを指定します。 |
| **nontx-topology** |*topology-name*<br />*spout-map*<br />*bolt-map* |トポロジ名、スパウト定義マップ、およびボルト定義マップを使用して非トランザクション トポロジを定義します。 |
| **scp-spout** |*exec-name*<br />*args*<br />*fields*<br />*parameters* |非トランザクション スパウトを定義します。 この関数では、*exec-name* に指定されたアプリケーションを実行し、*args* を使用します。<br /><br />*fields* パラメーターで、スパウトの出力フィールドを指定します。<br /><br />*parameters* パラメーターは省略可能です。 これを使用して、"nontransactional.ack.enabled" のようなパラメーターを指定します。 |
| **scp-bolt** |*exec-name*<br />*args*<br />*fields*<br />*parameters* |非トランザクション ボルトを定義します。 この関数では、*exec-name* に指定されたアプリケーションを実行し、*args* を使用します。<br /><br />*fields* パラメーターで、ボルトの出力フィールドを指定します<br /><br />*parameters* パラメーターは省略可能です。 これを使用して、"nontransactional.ack.enabled" のようなパラメーターを指定します。 |

SCP.NET には、次のキーワードが定義されています。

| Keyword | 説明 |
| --- | --- |
| **:name** |トポロジ名 |
| **:topology** |前の表の関数と組み込み関数を使用するトポロジ |
| **:p** |各スパウトまたはボルトに対する並列処理のヒント |
| **:config** |パラメーターを構成するか、既存のパラメーターを更新するか |
| **:schema** |ストリームのスキーマ |

SCP.NET には、頻繁に使用する次のパラメーターも定義されています。

| パラメーター | 説明 |
| --- | --- |
| "plugin.name" |C# プラグインの .exe ファイル名 |
| "plugin.args" |プラグイン引数 |
| "output.schema" |出力スキーマ |
| "nontransactional.ack.enabled" |受信確認が非トランザクション トポロジで有効かどうか |

**runSpec** コマンドはビットと共にデプロイされます。 次にコマンドの使用法を示します。

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*resource-dir* パラメーターは省略可能です。 C# アプリケーションをプラグインする場合に指定します。 アプリケーション、依存関係、および構成が含まれるディレクトリを指定します。

*classpath* パラメーターも省略可能です。 仕様ファイルに Java スパウトまたはボルトが含まれる場合に、Java classpath を指定します。

## <a name="miscellaneous-features"></a>その他の機能

### <a name="input-and-output-schema-declarations"></a>入力スキーマと出力スキーマの宣言

作成する C# プロセスでタプルを出力できます。 これを行うには、プラットフォームでタプルを **byte[]** オブジェクトにシリアル化し、オブジェクトを Java 側に転送します。 次に、Storm では、これらのタプルをターゲットに転送します。

下流コンポーネントでは、C# プロセスで、Java 側からタプルを受信し、プラットフォームの元の型に変換します。 これらの操作はすべてプラットフォームによって隠されます。

シリアル化と非シリアル化をサポートするには、コードに入力と出力のスキーマを宣言する必要があります。 スキーマは、ディクショナリとして定義されます。 ストリーム ID はディクショナリ キーです。 キー値は列の型です。 コンポーネントでは、複数のストリームを宣言できます。

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

次の関数が **Context** オブジェクトに追加されます。

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

開発者は、出力されるタプルがストリームに対して定義されたスキーマに確実に従うようにする必要があります。 そうしないと、システムからランタイム例外がスローされます。

### <a name="multistream-support"></a>複数ストリームのサポート

SCP では、コードで、複数の異なるストリームに同時に出力したり受信したりすることができます。 **Context** オブジェクトでは、このサポートを **Emit** メソッドのオプションのストリーム ID パラメーターとして反映します。

SCP.NET **Context** オブジェクトに 2 つのメソッドが追加されました。 1 つ以上のタプルが特定のストリームに出力されます。 *streamId* パラメーターは文字列です。 この値は、C# コードとトポロジ定義仕様の両方で同じである必要があります。

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

存在しないストリームに出力すると、ランタイム例外が発生します。

### <a name="fields-grouping"></a>フィールドのグループ化

Storm 内の組み込みフィールドのグループ化は、SCP.NET では正しく動作しません。 Java プロキシ側では、すべてのフィールドのデータ型は実際には **byte[]** です。 フィールドのグループ化では、**byte[]** オブジェクトのハッシュ コードを使用してグループ化を行います。 ハッシュ コードは、このオブジェクトの RAM 内のアドレスです。 そのため、同じコンテンツを共有しているが、アドレスが同じではないマルチバイト オブジェクトでは、グループ化が正しく行われません。

SCP.NET では、カスタマイズしたグループ化メソッドを追加します。そのメソッドで、**byte[]** オブジェクトのコンテンツを使用してグループ化します。 仕様ファイルの構文は次の例のようになります。

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

上記の仕様ファイルで、次のようにします。

* `scp-field-group` で、グループ化が、SCP によって実装されたカスタマイズされたフィールド グループであることを指定します。
* `:tx` または `:non-tx` で、トポロジがトランザクションであるかどうかを指定します。 開始インデックスがトランザクション トポロジと非トランザクション トポロジで異なるため、この情報が必要になります。
* `[0,1]` で、0 から始まるフィールド ID のハッシュ セットを指定します。

### <a name="hybrid-topology"></a>ハイブリッド トポロジ

ネイティブの Storm コードは Java で記述されています。 SCP.NET では、ビジネス ロジックを処理する C# コードを記述できるように Storm が拡張されました。 ただし、SCP.NET では、C# スパウトおよびボルトだけでなく、Java スパウトおよびボルトも含まれるハイブリッド トポロジもサポートしています。

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>仕様ファイルの Java スパウトおよびボルトの指定

仕様ファイルの **scp-spout** と **scp-bolt** を使用して、Java スパウトおよびボルトを指定できます。 次に例を示します。

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

ここで、`microsoft.scp.example.HybridTopology.Generator` は Java スパウト クラスの名前です。

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>runSpec コマンドでの Java classpath の指定

Java スパウトまたはボルトが含まれるトポロジを送信する場合は、最初にそれらをコンパイルして JAR ファイルを生成します。 次に、トポロジの送信時に、JAR ファイルが含まれる Java classpath を指定します。 次に例を示します。

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

ここでは、`examples\HybridTopology\java\target\` が Java スパウトおよびボルトの JAR ファイルが含まれるフォルダーです。

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java と C# 間のシリアル化と逆シリアル化

SCP コンポーネントには、Java 側と C# 側が含まれます。 ネイティブの Java スパウトおよびボルトとやりとりするには、次の図に示すように、Java 側と C# 側の間でシリアル化と逆シリアル化を行う必要があります。

![Java コンポーネントが SCP コンポーネントに送信された後、別の Java コンポーネントに送信される図](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 側のシリアル化と C# 側の逆シリアル化

まず、Java 側のシリアル化と C# 側の逆シリアル化の既定の実装を提供します。

仕様ファイルに Java 側のシリアル化のメソッドを指定します。

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

C# コードに C# 側の逆シリアル化のメソッドを指定します。

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

データ型があまり複雑でない場合は、この既定の実装でほとんどのケースに対応できます。 以下は、独自の実装を組み込むことができるケースです。

* 既定の実装では、データ型が複雑すぎまる。
* 既定の実装のパフォーマンスが要件を満たしていない。

Java 側のシリアル化インターフェイスは、次のように定義します。

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 側のシリアル化インターフェイスは、次のように定義します。

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>C# 側のシリアル化と Java 側の逆シリアル化

C# コードに C# 側のシリアル化のメソッドを指定します。

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

仕様ファイルに Java 側の逆シリアル化のメソッドを指定します。

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

ここで、`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` は逆シリアライザーの名前、`"microsoft.scp.example.HybridTopology.Person"` はデータが逆シリアル化されるターゲットのクラスです。

また、C# シリアライザーと Java デシリアライザーの独自の実装をプラグインすることもできます。

次のコードは、C# シリアライザーのインターフェイスです。

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

次のコードは、Java 逆シリアライザーのインターフェイスです。

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP ホスト モード

このモードでは、コードを DLL としてコンパイルし、SCP から提供される SCPHost.exe を使用してトポロジを送信できます。 仕様ファイルは次のコードのようになります。

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

ここでは、`"plugin.name"` は `"SCPHost.exe"` に指定されており、これは SCP SDK で提供されています。 SCPHost.exe は、3 つのパラメーターを次の順序で受け取ります。

1. DLL 名。この例では `"HelloWorld.dll"` です。
1. クラス名。この例では `"Scp.App.HelloWorld.Generator"` です。
1. 静的パブリック メソッドの名前。これを呼び出して、**ISCPPlugin** のインスタンスを取得できます。

ホスト モードで、SCP プラットフォームから呼び出せるように、コードを DLL としてコンパイルします。 プラットフォームで処理ロジック全体を完全に制御できるため、SCP ホスト モードでトポロジを送信することをお勧めします。 これにより、開発作業が簡単になります。 また、柔軟性が向上し、以降のリリースに対する下位互換性も向上します。

## <a name="scp-programming-examples"></a>SCP プログラミングの例

### <a name="helloworld"></a>HelloWorld

次のシンプルな HelloWorld の例で、SCP.NET を体験できます。 **generator** というスパウトと、**splitter** と **counter** という 2 つのボルトを持つ非トランザクション トポロジを使用します。 **generator** スパウトにより、文がランダムに生成され、それらの文が **splitter** に出力されます。 **splitter** ボルトにより、文が単語に分割され、それらの単語が **counter** ボルトに出力されます。 **counter** ボルトでは、ディクショナリを使用して各単語の出現回数が記録されます。

この例には、次の 2 つの仕様ファイルがあります。HelloWorld と HelloWorld\_EnableAck.spec です。C# コードで、Java 側から `pluginConf` オブジェクトを取得して、受信確認が有効になっているかどうかを確認できます。

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

スパウトで受信確認が有効になっている場合、受信確認されていないタプルはディクショナリによってキャッシュされます。 `Fail` が呼び出されると、失敗したタプルが再生されます。

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

次の HelloWorldTx の例は、トランザクション トポロジの実装方法を示しています。 この例には、**generator** というスパウトが 1 つ、**partial-count** というバッチ ボルト、および **count-sum** というコミット ボルトがあります。 この例には、次の 3 つの既存のテキスト ファイルもあります。DataSource0.txt、DataSource1.txt、DataSource2.txt です。

各トランザクションでは、**generator** スパウトによって、既存の 3 つのファイルから 2 つのファイルがランダムに選択され、**partial-count** ボルトに 2 つのファイル名が出力されます。 **partial-count** ボルトでは、以下が行われます。

1. 受信したタプルからファイル名を取得します。
1. 対応するファイルを開きます。
1. ファイル内の単語数をカウントします。
1. 単語数を **count-sum** ボルトに出力します。

**count-sum** ボルトは、合計数を集計します。

厳密に 1 回のセマンティクスを実現するには、**count-sum** コミット ボルトで、再生されたトランザクションかどうかを判断する必要があります。 この例には、次の静的メンバー変数があります。

```csharp
public static long lastCommittedTxId = -1; 
```

**ISCPBatchBolt** インスタンスが作成されると、入力パラメーターから `txAttempt` オブジェクトの値が取得されます。

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

`FinishBatch` が呼び出されると、`lastCommittedTxId` が再生されたトランザクションでない場合は更新されます。

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

このトポロジには、Java スパウトと C# ボルトが含まれています。 SCP プラットフォームから提供される既定のシリアル化と逆シリアル化の実装を使用します。 仕様ファイルの詳細については、examples\\HybridTopology フォルダーの HybridTopology ファイルを参照してください。 また、Java classpath を指定する方法については、SubmitTopology.bat を参照してください。

### <a name="scphostdemo"></a>SCPHostDemo

この例は、基本的には HelloWorld と同じです。 唯一の相違点は、作成したコードが DLL としてコンパイルされ、トポロジが SCPHost.exe を使用して送信されることです。 詳細については、SCP ホスト モードのセクションを参照してください。

## <a name="next-steps"></a>次のステップ

SCP を使用して作成された Apache Storm トポロジの例については、次の記事を参照してください。

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight 上の Apache Storm で Azure Event Hubs からのイベントを処理する](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight 上の Apache Storm を使用して Event Hubs からの車両センサー データを処理する](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure Event Hubs から Apache HBase への抽出、変換、ロード (ETL)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
