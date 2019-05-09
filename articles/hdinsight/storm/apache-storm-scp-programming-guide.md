---
title: Azure HDInsight における Storm の SCP.NET プログラミング ガイド
description: SCP.NET を使用して、Azure HDInsight で実行されている Storm で使用するための .NET ベースの Storm トポロジを作成する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: 1ad9661d85c7ec91f361cdc4d126e0a91e376b66
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853292"
---
# <a name="scp-programming-guide"></a>SCP プログラミング ガイド
SCP は、リアルタイムで、信頼性が高く、整合性のとれた高パフォーマンスのデータ処理アプリケーションを構築するためのプラットフォームです。 OSS コミュニティが設計したストリーム処理システムである [Apache Storm](https://storm.incubator.apache.org/) を基に構築されています。 Storm は Nathan Marz によって設計され、Twitter によってオープン ソース化されました。 信頼性の高い分散化した調整と状態管理を実現するために、別の Apache プロジェクトである [Apache ZooKeeper](https://zookeeper.apache.org/)を利用しています。 

SCP プロジェクトでは、Storm を Windows に移植しただけでなく、Windows エコシステム向けの拡張機能とカスタマイズを追加しました。 拡張機能には .NET 開発者の経験が活かされており、ライブラリが含まれています。カスタマイズには Windows ベースのデプロイメントが含まれています。 

この拡張機能とカスタマイズにより、OSS プロジェクトをフォークする必要がなく、Storm を基に構築された派生エコシステムを利用できます。

## <a name="processing-model"></a>処理モデル
SCP のデータは、タプルの継続的なストリームとしてモデル化されます。 通常は、まずタプルがキューに配置されます。その後、取り出されて、Storm トポロジ内でホストされているビジネス ロジックによって変換されます。最後に、出力が別の SCP システムにタプルとしてパイプされるか、分散ファイル システムなどのストアまたは SQL Server などのデータベースにコミットされます。

![データ ストアに格納される、処理のためにデータを提供するキューの図](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Storm では、アプリケーション トポロジが計算グラフを定義します。 トポロジの各ノードには処理ロジックが含まれ、ノード間のリンクがデータ フローを示します。 トポロジに入力データを挿入するノードは "_スパウト_" と呼ばれ、データの配列を決定するために使用できます。 入力データは、ファイル ログ、トランザクション データベース、システム パフォーマンス カウンターなどに存在する可能性があります。入力と出力の両方のデータ フローがあるノードは "_ボルト_" と呼ばれ、実際のデータのフィルター、選択、および集約を実行します。

SCP では、ベスト エフォート型、"少なくとも 1 回"、および "厳密に 1 回" のデータ処理をサポートします。 分散ストリーミング処理アプリケーションで、データの処理中に、ネットワークの停止、コンピューターの故障、ユーザー コード エラーなどのさまざまなエラーが発生することがあります。"少なくとも 1 回" の処理により、エラーが発生したときに、同じデータを自動的に再生することによって、すべてのデータが 1 回以上処理されるようになります。 "少なくとも 1 回" の処理は、単純で信頼性が高く、多くのアプリケーションに適しています。 ただし、アプリケーションが正確なカウントを必要とする場合は、"少なくとも 1 回" の処理では不十分です。アプリケーション トポロジ内で同じデータが再生される可能性があるためです。 この場合、"厳密に 1 回" の処理を使用すると、データが複数回再生されて処理された場合でも正しい結果になります。

SCP を使用することで、.NET 開発者は、実際に Java 仮想マシン (JVM) を Storm と共に利用して、リアルタイムなデータ処理アプリケーションを開発できます。 .NET と JVM は、TCP ローカル ソケットを経由して通信します。 基本的には、それぞれのスパウトとボルトは .NET と Java のプロセス ペアで、.NET プロセスでユーザー ロジックをプラグインとして実行します。

SCP を基にデータ処理アプリケーションを構築するには、次の複数の手順が必要です。

* キューからデータをプルするようにスパウトを設計して実装します。
* 入力データを処理するようにボルトを設計して実装し、データベースなどの外部ストアにデータを保存します。
* トポロジを設計し、そのトポロジを送信して実行します。 トポロジは、頂点、および頂点間のデータ フローを定義します。 SCP はトポロジの仕様を取得して、各頂点を 1 つの論理ノードで実行する Storm クラスターにその仕様をデプロイします。 フェールオーバーとスケーリングは Storm のタスク スケジューラが実行します。

このドキュメントでは、SCP を使用したデータ処理アプリケーションの構築方法について、いくつかの簡単な例を使用して説明します。

## <a name="scp-plugin-interface"></a>SCP プラグイン インターフェイス
SCP プラグイン (またはアプリケーション) は、スタンドアロンの EXE であり、開発フェーズで Visual Studio 内で実行することも、開発後の運用環境で Storm パイプラインにプラグインすることもできます。 SCP プラグインの記述は、他の標準の Windows コンソール アプリケーションの記述と同じです。 SCP.NET プラットフォームでは、スパウトとボルトのインターフェイスを宣言します。ユーザー プラグイン コードでは、これらのインターフェイスを実装する必要があります。 この設計の主な目的は、ユーザーが自身のビジネス ロジックに注力し、他の処理を SCP.NET プラットフォームに任せることができるようにすることです。

ユーザー プラグイン コードでは、次のインターフェイスの 1 つを実装する必要があります。どれを実装するかは、トポロジがトランザクションか非トランザクションか、およびコンポーネントがスパウトかボルトかによって異なります。

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin は、あらゆる種類のプラグインに共通のインターフェイスです。 現在は、ダミー インターフェイスです。

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout は非トランザクション スパウト用のインターフェイスです。

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

`NextTuple()` を呼び出した場合、C\# ユーザー コードで 1 つまたは複数のタプルを出力できます。 出力するものがない場合、このメソッドは何も出力せずに戻ります。 `NextTuple()`、`Ack()`、および `Fail()` はすべて、C\# プロセスの単一スレッドの厳密なループで呼び出されることに注意してください。 出力するタプルがない場合は、CPU が浪費されないように、NextTuple を短時間 (10 ミリ秒など) スリープにすることをお勧めします。

`Ack()` と `Fail()` は、spec ファイルで ACK メカニズムが有効である場合にのみ呼び出されます。 `seqId` は、確認されたか失敗したタプルを特定するために使用されます。 そのため、ACK が非トランザクション トポロジで有効である場合は、スパウトで次の emit 関数を使用する必要があります。

    public abstract void Emit(string streamId, List<object> values, long seqId); 

ACK が非トランザクション トポロジでサポートされていない場合、`Ack()` と `Fail()` を空の関数としておくことができます。

これらの関数の `parms` 入力パラメーターは空の Dictionary であり、将来のために予約されています。

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt は非トランザクション ボルト用のインターフェイスです。

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

新しいタプルが利用可能になると、それを処理するために `Execute()` 関数が呼び出されます。

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout はトランザクション スパウト用のインターフェイスです。

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

非トランザクション スパウトの場合と同様に、`NextTx()`、`Ack()`、および `Fail()` はすべて、C\# プロセスの単一スレッドの厳密なループで呼び出されます。 出力するデータがない場合は、CPU が浪費されないように、 `NextTx` を短時間 (10 ミリ秒) スリープにすることをお勧めします。

`NextTx()` は、呼び出されると、新しいトランザクションを開始し、出力パラメーター `seqId` を使用してトランザクションを特定します。このパラメーターは、`Ack()` と `Fail()` でも使用されます。 `NextTx()` では、ユーザーがデータを Java 側に出力できます。 このデータは、再生をサポートするために ZooKeeper に格納されます。 ZooKeeper の能力は限定的であるため、ユーザーはトランザクション スパウトでメタデータのみを出力し、大量のデータを出力しないようにする必要があります。

Storm はトランザクションが失敗すると自動的に再生するため、 `Fail()` は通常のケースでは呼び出されません。 ただし、SCP がトランザクション スパウトによって出力されたメタデータを確認できる場合は、メタデータが無効のときに `Fail()` が呼び出されることがあります。

これらの関数の `parms` 入力パラメーターは空の Dictionary であり、将来のために予約されています。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt はトランザクション ボルト用のインターフェイスです。

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` が呼び出されます。 `FinishBatch()` が呼び出されます。 `parms` 入力パラメーターは、将来のために予約されています。

トランザクション トポロジには、`StormTxAttempt` という重要な概念があります。 これには、`TxId` と `AttemptId` という 2 つのフィールドがあります。 `TxId` は、特定のトランザクションを識別するために使用します。トランザクションによっては、トランザクションが失敗したか再生された場合に複数回試行する場合があります。 SCP.NET は、Storm の Java での処理と同様に、各 `StormTxAttempt` を処理する新しい ISCPBatchBolt オブジェクトを作成します。 この設計の目的は、並列トランザクション処理をサポートすることです。 トランザクションの試行が終了すると、対応する ISCPBatchBolt オブジェクトが破棄され、ガベージ コレクションが実施されます。

## <a name="object-model"></a>オブジェクト モデル
SCP.NET は、開発者がプログラムに使用できる重要なオブジェクトの簡単なセットも提供しています。 **Context**、**StateStore**、および **SCPRuntime** です。 これらのオブジェクトについては、このセクションでこの後説明します。

### <a name="context"></a>Context
Context は、アプリケーションに実行環境を提供します。 各 ISCPPlugin インスタンス (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) には対応する Context インスタンスがあります。 Context が提供する機能は、2 つの部分に分かれています。(1) C\# プロセス全体で使用できる静的部分と、(2) 特定の Context インスタンスのみが使用できる動的部分です。

### <a name="static-part"></a>静的部分
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` は、ログ目的で提供されています。

`pluginType` は、C\# プロセスのプラグインの種類を示すために使用します。 C\# プロセスをローカル テスト モード (Java なし) で実行する場合、プラグインの種類は `SCP_NET_LOCAL` です。

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` は、Java 側の構成パラメーターを取得するためのものです。 これらのパラメーターは、C\# プラグインの初期化時に Java 側から渡されます。 `Config` パラメーターには、`stormConf` と `pluginConf` の 2 つの部分があります。

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` は Storm で定義されているパラメーターで、`pluginConf` は SCP で定義されているパラメーターです。 例: 

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` は、トポロジ コンテキストを取得するために提供され、複数の並列処理を含むコンポーネントで最も役に立ちます。 たとえば次のようになります。

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

### <a name="dynamic-part"></a>動的部分
次のインターフェイスは、特定の Context インスタンスに関係しています。 Context インスタンスは、SCP.NET プラットフォームで作成され、ユーザー コードに渡されます。

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

ACK をサポートしている非トランザクション スパウトには、次のメソッドが提供されます。

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

ACK をサポートする非トランザクション ボルトには、受け取ったタプルに対して明示的に `Ack()` または `Fail()` を実行する必要があります。 新しいタプルを出力する場合は、新しいタプルのアンカーを指定する必要もあります。 次のメソッドが提供されています。

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` は、メタデータ サービス、モノトニックなシーケンスの生成、および wait-free 調整を提供します。 高度なレベルでの分散コンカレンシーの抽象化は、`StateStore` に基づいて構築されています。これには、分散ロック、分散キュー、バリア、およびトランザクション サービスが含まれます。

SCP アプリケーションでは、 `State` オブジェクトを使用して、[Apache ZooKeeper](https://zookeeper.apache.org/) に情報 (特にトランザクション トポロジの情報) を保持できます。 それによって、トランザクション スパウトがクラッシュして再起動する場合、ZooKeeper から必要な情報を取得し、パイプラインを再起動できます。

`StateStore` オブジェクトには主として、次のメソッドがあります。

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

`State` オブジェクトには主として、次のメソッドがあります。

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

`Commit()` メソッドは、simpleMode が true に設定されている場合は、ZooKeeper の対応する ZNode を削除します。 それ以外の場合は、現在の ZNode を削除し、COMMITTED\_PATH に新しいノードを追加します。

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime は、次の 2 つのメソッドを提供します。

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` は、SCP ランタイム環境を初期化するために使用します。 このメソッドでは、C\# プロセスで Java 側に接続し、構成パラメーターとトポロジ コンテキストを取得します。

`LaunchPlugin()` は、メッセージ処理ループを開始するために使用します。 このループでは、C\# プラグインが Java 側から (タプルと制御信号を含む) メッセージを受信し、多くの場合ユーザー コードで指定されているインターフェイス メソッドを呼び出して、メッセージを処理します。 `LaunchPlugin()` メソッドの入力パラメーターは、ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt インターフェイスを実装するオブジェクトを返すことができるデリゲートです。

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

ISCPBatchBolt では、`parms` の `StormTxAttempt` を取得し、それを使用して、再生試行かどうかを判断することができます。 多くの場合、再生試行のチェックはコミット ボルトで実行されます。これについては、`HelloWorldTx` の例で説明します。

一般的には、SCP プラグインは次の 2 つのモードで実行できます。

1. ローカル テスト モード:このモードでは、SCP プラグイン (C\# ユーザー コード) は、開発フェーズ中は Visual Studio 内で実行されます。 このモードでは `LocalContext` を使用し、出力されたタプルをローカル ファイルにシリアル化してメモリに読み取ることができます。
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. 通常モード:このモードでは、SCP プラグインは Storm Java プロセスで起動されます。
   
    次に、SCP プラグインを起動する例を示します。
   
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

## <a name="topology-specification-language"></a>トポロジ仕様言語
SCP Topology Specification は、SCP トポロジを記述して構成するためのドメイン固有の言語です。 これは Storm の Clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) に基づいており、SCP によって拡張されます。

トポロジの仕様は、***runspec*** コマンドを介して Storm クラスターに直接送信されて実行されます。

トランザクション トポロジを定義するために、SCP.NET は次の関数を追加しました。

| **新しい関数** | **パラメーター** | **説明** |
| --- | --- | --- |
| **tx-topolopy** |topology-name<br />spout-map<br />bolt-map |トポロジ名、スパウト定義マップ、およびボルト定義マップでトランザクション トポロジを定義します。&nbsp; |
| **scp-tx-spout** |exec-name<br />args<br />fields |トランザクション スパウトを定義します。 ***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br /><br />***fields*** はスパウトの出力フィールドです。 |
| **scp-tx-batch-bolt** |exec-name<br />args<br />fields |トランザクション バッチ ボルトを定義します。 ***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br /><br />*fields* はボルトの出力フィールドです。 |
| **scp-tx-commit-bolt** |exec-name<br />args<br />fields |トランザクション コミッター ボルトを定義します。 ***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br /><br />***fields*** はボルトの出力フィールドです。 |
| **nontx-topolopy** |topology-name<br />spout-map<br />bolt-map |トポロジ名、スパウト定義マップ、&nbsp;およびボルト定義マップで非トランザクション トポロジを定義します。 |
| **scp-spout** |exec-name<br />args<br />fields<br />parameters |非トランザクション スパウトを定義します。 ***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br /><br />***fields*** はスパウトの出力フィールドです。<br /><br />***parameters*** は省略可能です。"nontransactional.ack.enabled" などのパラメーターを指定する場合に使用します。 |
| **scp-bolt** |exec-name<br />args<br />fields<br />parameters |非トランザクション ボルトを定義します。 ***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br /><br />***fields*** はボルトの出力フィールドです。<br /><br />***parameters*** は省略可能です。"nontransactional.ack.enabled" などのパラメーターを指定する場合に使用します。 |

SCP.NET には、次のキーワードが定義されています。

| **キーワード** | **説明** |
| --- | --- |
| **:name** |トポロジ名を定義します。 |
| **:topology** |前述の関数を使用してトポロジを定義し、構築します。 |
| **:p** |各スパウトまたはボルトに対する並列処理のヒントを定義します。 |
| **:config** |構成パラメーターを定義するか、既存のパラメーターを更新します。 |
| **:schema** |ストリームのスキーマを定義します。 |

使用頻度が高いパラメーターを次に示します。

| **パラメーター** | **説明** |
| --- | --- |
| **"plugin.name"** |C# プラグインの exe ファイル名 |
| **"plugin.args"** |プラグイン引数 |
| **"output.schema"** |出力スキーマ |
| **"nontransactional.ack.enabled"** |ACK が非トランザクション トポロジで有効かどうか |

runspec コマンドはビットと共にデプロイされます。次のように使用します。

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***resource-dir*** パラメーターは省略可能です。C\# アプリケーションを組み込む場合は指定する必要があり、このディレクトリにはアプリケーション、依存関係、および構成が含まれます。

***classpath*** パラメーターも省略可能です。 spec ファイルに Java スパウトまたはボルトが含まれている場合に、Java classpath を指定するために使用します。

## <a name="miscellaneous-features"></a>その他の機能
### <a name="input-and-output-schema-declaration"></a>入力スキーマと出力スキーマの宣言
ユーザーは C\# プロセスでタプルを出力できます。プラットフォームはそのタプルを byte にシリアル化し、Java 側に転送する必要があり、Storm はこのタプルをターゲットに転送します。 その一方で、ダウンストリーム コンポーネントでは、C\# プロセスが Java 側からタプルを受信し、プラットフォーム別に元の型に変換します。これらの操作はすべてプラットフォームによってバックグランドで実行されます。

シリアル化と非シリアル化をサポートするためには、ユーザー コードで入力と出力のスキーマを宣言する必要があります。

入力/出力ストリーム スキーマは、ディクショナリとして定義されます。 キーは StreamId です。 値は列の型です。 コンポーネントでは、マルチストリームを宣言できます。

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


Context オブジェクトには、次の API を追加しました。

    public void DeclareComponentSchema(ComponentStreamSchema schema)

開発者は、出力されたタプルがそのストリームに定義されているスキーマに従っていることを確認する必要があります。そうでないと、システムによりランタイム例外がスローされます。

### <a name="multi-stream-support"></a>マルチストリームのサポート
SCP では、同時に複数の異なるストリームから出力または受信するユーザー コードをサポートしています。 このサポートは Context オブジェクトに反映されており、Emit メソッドは省略可能なストリーム ID パラメーターを受け取ります。

SCP.NET Context オブジェクトに 2 つのメソッドが追加されました。 これらのメソッドは、タプルを出力して StreamId を指定するために使用します。 StreamId は文字列で、C\# とトポロジ定義の仕様の両方で一貫している必要があります。

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

存在しないストリームに出力すると、ランタイム例外が発生します。

### <a name="fields-grouping"></a>フィールドのグループ化
Storm に組み込まれているフィールドのグループ化は、SCP.NET では適切に動作しません。 JVM プロキシ側では、すべてのフィールドのデータ型が実際には byte[] で、フィールドのグループ化では byte[] オブジェクトのハッシュ コードを使用して、グループ化を実行します。 byte[] オブジェクトのハッシュ コードは、このオブジェクトのメモリ内のアドレスです。 そのため、同じコンテンツを共有するが、アドレスが異なる 2 つの byte[] オブジェクトをグループ化することはできません。

SCP.NET はカスタマイズしたグループ化メソッドを追加しました。そのメソッドでは、byte[] のコンテンツを使用してグループ化します。 **SPEC** ファイルでは、構文は次のようになります。

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


ここでは、

1. "scp-field-group" は、"SCP によって実装されたカスタマイズされたフィールドのグループ化" という意味です。
2. ":tx" または ":non-tx" は、トランザクション トポロジかどうかを示します。 tx トポロジと non-tx トポロジでは開始インデックスが異なるため、この情報が必要になります。
3. [0,1] は、0 から始まるフィールド ID のハッシュ セットを示しています。

### <a name="hybrid-topology"></a>ハイブリッド トポロジ
ネイティブ Storm は Java で記述されています。 さらに、SCP.NET は、C\# 開発者が独自のビジネス ロジックを処理する C\# コードを記述できるように拡張されています。 ただし、C\# のスパウトとボルトだけでなく、Java のスパウトとボルトも含まれるハイブリッド トポロジもサポートしています。

### <a name="specify-java-spoutbolt-in-spec-file"></a>spec ファイルでの Java スパウトとボルトの指定
spec ファイルでは、"scp-spout" と "scp-bolt" を使用して Java スパウトとボルトを指定することもできます。次に例を示します。

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

ここで `microsoft.scp.example.HybridTopology.Generator` は Java スパウト クラスの名前です。

### <a name="specify-java-classpath-in-runspec-command"></a>runSpec コマンドでの Java Classpath の指定
Java スパウトまたはボルトを含むトポロジを送信する場合は、最初に Java スパウトまたはボルトをコンパイルして Jar ファイルを取得する必要があります。 次に、トポロジを送信する際に Jar ファイルを含む Java Classpath を指定する必要があります。 たとえば次のようになります。

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

ここで、**examples\\HybridTopology\\java\\target\\** は Java スパウト/ボルト Jar ファイルを含むフォルダーです。

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java と C\# 間のシリアル化と逆シリアル化
SCP コンポーネントには、Java 側と C\# 側が含まれます。 ネイティブ Java スパウト/ボルトと対話するには、次の図に示すように、Java 側と C\# 側の間でシリアル化や逆シリアル化が実行される必要があります。

![Java コンポーネントに送信される SCP コンポーネントに送信する java コンポーネントの図](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Java 側でのシリアル化と C\# 側での逆シリアル化**
   
   まず、Java 側でのシリアル化と C\# 側での逆シリアル化の既定の実装を提供します。 Java 側のシリアル化メソッドは、spec ファイルで次のように指定できます。
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   C\# 側の逆シリアル化メソッドは、次のように C\# ユーザー コードで指定する必要があります。
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   データ型があまり複雑でない場合は、この既定の実装でほとんどのケースに対応できます。 ユーザー データ型が複雑すぎる場合や、既定の実装のパフォーマンスがユーザーの要求を満たしていない場合には、ユーザーは独自の実装をプラグインできます。
   
   Java 側でのシリアル化インターフェイスは、次のように定義します。
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   C\# 側での逆シリアル化インターフェイスは、次のように定義します。
   
   パブリック インターフェイス ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **C 側でのシリアル化\#と Java 側での逆シリアル化**
   
   C\# 側のシリアル化メソッドは、次のように C\# ユーザー コードで指定する必要があります。
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Java 側のシリアル化メソッドは、SPEC ファイルで次のように指定する必要があります。
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   ここで、"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" はデシリアライザーの名前で、"microsoft.scp.example.HybridTopology.Person" はデータが逆シリアル化されるターゲット クラスです。
   
   ユーザーは C\# シリアライザーと Java デシリアライザーの独自の実装をプラグインすることもできます。 このコードは、C\# のシリアライザーのインターフェイスです。
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   このコードは、Java デシリアライザーのインターフェイスです。
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP ホスト モード
このモードでは、ユーザーはコードを DLL にコンパイルし、SCP で提供された SCPHost.exe を使用してトポロジを送信できます。 spec ファイルは次のコードのようになります。

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

ここでは、`plugin.name` は SCP SDK で提供された`SCPHost.exe` として指定されています。 SCPHost.exe は、次の 3 つのパラメーターを受け取ります。

1. 最初のパラメーターは DLL 名です。この例では、`"HelloWorld.dll"` です。
2. 2 番目のパラメーターはクラス名です。この例では、`"Scp.App.HelloWorld.Generator"` です。
3. 3 番目のパラメーターは静的パブリック メソッドの名前です。このメソッドを呼び出して ISCPPlugin のインスタンスを取得できます。

ホスト モードでは、ユーザー コードは DLL としてコンパイルされ、SCP プラットフォームによって呼び出されます。 そのため、SCP プラットフォームは処理ロジック全体を詳細に制御できます。 SCP ホスト モードでは開発が簡素化され、柔軟性が向上し、後のリリースに対する下位互換性も向上するため、SCP ホスト モードでトポロジを送信することをお勧めします。

## <a name="scp-programming-examples"></a>SCP プログラミングの例
### <a name="helloworld"></a>HelloWorld
**HelloWorld** は、SCP.NET を体験できる簡単な例です。 **generator** というスパウトと、**splitter** と **counter** という 2 つのボルトを持つ非トランザクション トポロジを使用します。 **generator** スパウトは、文を無作為に生成し、それらの文を **splitter** に出力します。 **splitter** ボルトは、文を単語に分割し、それらの単語を **counter** ボルトに出力します。 "counter" ボルトはディクショナリを使用して、各単語の出現回数を記録します。

この例には、**HelloWorld.spec** と **HelloWorld\_EnableAck.spec** の 2 つの spec ファイルがあります。 C\# コードでは、Java 側から pluginConf を取得することによって、ACK が有効かどうか確認できます。

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

スパウトでは、ACK が有効な場合、ディクショナリを使用して、確認されなかったタプルをキャッシュします。 Fail() が呼び出されると、失敗したタプルが再生されます。

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

### <a name="helloworldtx"></a>HelloWorldTx
**HelloWorldTx** の例では、トランザクション トポロジの実装方法を示します。 **generator** というスパウトが 1 つ、**partial-count** というバッチ ボルト、および **count-sum** というコミット ボルトがあります。 事前に作成された 3 つの txt ファイル、**DataSource0.txt**、**DataSource1.txt**、および **DataSource2.txt** もあります。

各トランザクションでは、**generator** スパウトが事前に作成された 3 つのファイルから無作為に 2 つのファイルを選択し、その 2 つのファイル名を **partial-count** ボルトに出力します。 **partial-count** ボルトは、受信したタプルからファイル名を取得し、そのファイルを開いてファイル内の単語数をカウントして、最後に **count-sum** ボルトに単語数を出力します。 **count-sum** ボルトは、合計数を集計します。

**厳密に 1 回**のセマンティクスを実現するには、**count-sum** コミット ボルトで、再生されたトランザクションかどうかを判断する必要があります。 この例では、静的メンバー変数があります。

    public static long lastCommittedTxId = -1; 

ISCPBatchBolt インスタンスが作成されると、入力パラメーターから `txAttempt` を取得します。

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

`FinishBatch()` が呼び出されると、再生されたトランザクションでない場合、`lastCommittedTxId` が更新されます。

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
このトポロジには Java スパウトと C\# ボルトが含まれています。 SCP プラットフォームが提供する既定のシリアル化と逆シリアル化の実装を使用します。 spec ファイルの詳細については **examples\\HybridTopology** フォルダーの **HybridTopology.spec** を参照し、Java Classpath の指定方法については **SubmitTopology.bat** を参照してください。

### <a name="scphostdemo"></a>SCPHostDemo
この例は、基本的に HelloWorld と同じです。 唯一の相違点は、ユーザー コードが DLL としてコンパイルされ、トポロジが SCPHost.exe を使用して送信されることです。 詳細な説明については、「SCP ホスト モード」を参照してください。

## <a name="next-steps"></a>次の手順
SCP を使用して作成された Apache Storm トポロジの例については、次のドキュメントを参照してください。

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight 上の Apache Storm で Azure Event Hubs からのイベントを処理する](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight 上の Apache Storm を使用して Event Hubs からの車両センサー データを処理する](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure Event Hubs から Apache HBase への抽出、変換、ロード (ETL)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
