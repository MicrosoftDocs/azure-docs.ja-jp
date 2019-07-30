---
title: Visual Studio と C# を使った Apache Storm トポロジ - Azure HDInsight
description: C# で Storm トポロジを作成する方法について説明します。 Hadoop Tools for Visual Studio を使用して、Visual Studio で簡単なワード カウント トポロジを作成します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9d459f88cd252303384acb4a72d0af0cce6ee226
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428468"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio を使用した Apache Storm の C# トポロジの開発

Azure Data Lake (Apache Hadoop) Tools for Visual Studio を使用して C# Apache Storm トポロジを作成する方法を説明します。 このドキュメントでは、Visual Studio で Storm プロジェクトを作成し、ローカルでテストして、Azure HDInsight クラスターで Apache Storm にデプロイする手順について説明します。

また、C# と Java コンポーネントを使用するハイブリッド トポロジの作成方法についても説明します。

> [!NOTE]  
> このドキュメントの手順は Visual Studio を使う Windows 開発環境でのものですが、コンパイル済みのプロジェクトは、Linux または Windows ベースの HDInsight クラスターに送信できます。 SCP.NET トポロジをサポートする Linux ベースのクラスターは、2016 年 10 月 28 日より後に作成されたものだけです。

C# トポロジを Linux ベースのクラスターで使うには、プロジェクトによって使用される Microsoft.SCP.Net.SDK NuGet パッケージをバージョン 0.10.0.6 以降に更新する必要があります。 また、パッケージのバージョンは、HDInsight にインストールされている Storm のメジャー バージョンと一致する必要もあります。

| HDInsight のバージョン | Apache Storm のバージョン | SCP.NET のバージョン | 既定の Mono のバージョン |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(Windows ベースの HDInsight の場合のみ) | NA |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> Linux ベースのクラスターの C# トポロジは、.NET 4.5 を使い、Mono を使って HDInsight クラスターで実行する必要があります。 互換性のない可能性がある機能については、[Mono の互換性](https://www.mono-project.com/docs/about-mono/compatibility/)のドキュメントを参照してください。

## <a name="install-visual-studio"></a>Visual Studio のインストール

SCP.NET による C# トポロジの開発は、次のいずれかのバージョンの Visual Studio を使って行うことができます。

* Visual Studio 2012 Update 4

* Visual Studio 2013 Update 4 または [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 または [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (任意のエディション)

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio のインストール

Data Lake Tools for Visual Studio は、[Data Lake Tools for Visual Studio の使用開始](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)に関するページの手順に従ってインストールしてください。

## <a name="install-java"></a>Java のインストール

Visual Studio から Storm トポロジを送信すると、そのトポロジと依存関係を含んだ zip ファイルが SCP.NET によって生成されます。 これらの zip ファイルは、Java を使って作成されます。Java で使われる形式の方が、Linux ベースのクラスターとの互換性が高いためです。

1. Java Developer Kit (JDK) 7 以降を開発環境にインストールします。 Oracle JDK は、[Oracle](https://aka.ms/azure-jdks) から入手できます。 [他の Java ディストリビューション](https://openjdk.java.net/)を使用してもかまいません。

2. `JAVA_HOME` 環境変数は、Java があるディレクトリを指している必要があります。

3. `PATH` 環境変数には、`%JAVA_HOME%\bin` ディレクトリが含まれている必要があります。

Java と JDK が正しくインストールされているかどうかは、次の C# コンソール アプリケーションを使用して確認できます。

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm テンプレート

Data Lake Tools for Visual Studio には次のテンプレートがあります。

| プロジェクトの種類 | 対象 |
| --- | --- |
| Storm アプリケーション |空の Storm トポロジ プロジェクト。 |
| Storm Azure SQL ライターのサンプル |Azure SQL Database に書き込む方法。 |
| Storm Azure Cosmos DB リーダーのサンプル |Azure Cosmos DB から読み取る方法。 |
| Storm Azure Cosmos DB ライターのサンプル |Azure Cosmos DB に書き込む方法。 |
| Storm EventHub リーダーのサンプル |Azure Event Hubs から読み取る方法 |
| Storm EventHub ライターのサンプル |Azure Event Hubs に書き込む方法 |
| Storm HBase リーダーのサンプル |HDInsight クラスター上の HBase から読み取る方法。 |
| Storm HBase ライターのサンプル |HDInsight クラスター上の HBase に書き込む方法。 |
| Storm ハイブリッドのサンプル |Java コンポーネントを使用する方法。 |
| Storm サンプル |基本的なワード カウント トポロジ。 |

> [!WARNING]  
> すべてのテンプレートが Linux ベースの HDInsight で動作するとは限りません。 テンプレートで使用されている NuGet パッケージは Mono と互換性がない場合があります。 [Mono の互換性](https://www.mono-project.com/docs/about-mono/compatibility/)に関するドキュメントを確認し、[.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) を使用して潜在的な問題を特定してください。

このドキュメントの手順では、基本的な種類の Storm アプリケーション プロジェクトを使用してトポロジを作成します。

### <a name="apache-hbase-templates-notes"></a>Apache HBase テンプレートに関する注意

HBase のリーダーとライターのテンプレートは、HBase Java API ではなく、HBase REST API を利用して HDInsight クラスターの HBase と通信します。

### <a name="eventhub-templates-notes"></a>EventHub テンプレートに関する注意

> [!IMPORTANT]  
> EventHub リーダー テンプレートに含まれている Java ベースの EventHub スパウト コンポーネントは、HDInsight バージョン 3.5 以降の Storm では動作しない可能性があります。 このコンポーネントの更新バージョンは、[GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib) で入手できます。

このコンポーネントを使用し、HDInsight 3.5 で Storm を使用するトポロジの例については、[GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) を参照してください。

## <a name="create-a-c-topology"></a>C# トポロジの作成

1. Visual Studio を開いて、 **[ファイル]**  >  **[新規]** の順に選び、 **[プロジェクト]** を選びます。

2. **[新しいプロジェクト]** ウィンドウで、 **[インストール済み]**  >  **[テンプレート]** の順に展開して、 **[Azure Data Lake]** を選択します。 テンプレートの一覧から、 **[Storm Application]** を選択します。 画面の下部に、アプリケーションの名前として「 **WordCount** 」と入力します。

    ![[新しいプロジェクト] ウィンドウのスクリーン ショット](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. プロジェクトの作成後、次のファイルが生成されます。

   * **Program.cs**このファイルでは、プロジェクトのトポロジが定義されています。 既定では、スパウトとボルト 1 つずつで構成される既定のトポロジが作成されます。

   * **Spout.cs**:乱数を出力するスパウトの例です。

   * **Bolt.cs**:スパウトによって出力される数字の数を保持するボルトの例です。

     プロジェクトを作成すると、最新の [SCP.NET パッケージ](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)が NuGet によってダウンロードされます。

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>スパウトを実装する

1. **Spout.cs**を開きます。 スパウトは、外部ソースからデータをトポロジに読み込む場合に使用されます。 スパウトの主なコンポーネントは次のとおりです。

   * **NextTuple**:スパウトが新しいタプルの出力を許可されると、Storm によって呼び出されます。

   * **Ack** (トランザクション トポロジのみ):スパウトから送信されたタプルに対してトポロジ内の他のコンポーネントによって開始される受信確認を処理します。 タプルの受信確認によって、ダウンストリーム コンポーネントで処理が正常に完了したことがスパウトに通知されます。

   * **Fail** (トランザクション トポロジのみ):トポロジの他のコンポーネントの処理に失敗したタプルを処理します。 Fail メソッドを実装することにより、再処理用のタプルを再出力できるようになります。

2. **Spout** クラスの内容が、次のテキストに置き換えられます。このスパウトは、トポロジにセンテンスをランダムに出力します。

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>ボルトを実装する

1. プロジェクトから既存の **Bolt.cs** ファイルを削除します。

2. **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[追加]**  >  **[新しいアイテム]** の順に選択します。 一覧から **[Storm Bolt]** を選び、名前として「**Splitter.cs**」と入力します。 この操作を繰り返して「**Counter.cs**」という名前の 2 つ目のボルトを作成します。

   * **Splitter.cs**:文を個別の単語に分割し、単語の新しいストリームを出力するボルトが実装されています。

   * **Counter.cs**:各単語をカウントし、単語の新しいストリームと各単語のカウントを出力するボルトが実装されています。

     > [!NOTE]  
     > これらのボルトはストリームに読み書きしますが、ボルトを使用してデータベースやサービスなどのソースとやりとりすることもできます。

3. **Splitter.cs**を開きます。 既定では、次の 1 つのメソッドのみが含まれます:**Execute**。 Execute メソッドは、ボルトが処理のタプルを受信したときに呼び出されます。 ここで、受信したタプルを読み取って処理し、送信タプルを出力できます。

4. **Splitter** クラスの内容を次のコードに置き換えます。

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. **Counter.cs** を開いて、クラスの内容を次のコードで置き換えます。

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>トポロジの定義

スパウトとボルトはコンポーネント間のデータ フローを定義するグラフに配置されます。 このトポロジのグラフは次のようになります。

![コンポーネントの配置方法の図](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

センテンスはスパウトから出力され、Splitter ボルトのインスタンスに配布されます。 Splitter ボルトがセンテンスを単語に分け、Counter ボルトに配布します。

ワード カウントは Counter インスタンスにローカルに保持されるため、特定の単語が同じ Counter ボルト インスタンスに届くようにする必要があります。 各インスタンスが特定の単語を追跡します。 Splitter ボルトでは状態が保持されないため、Splitter のどのインスタンスがどのセンテンスを受信しても問題ありません。

**Program.cs** を開きます。 重要なメソッドは **GetTopologyBuilder** です。これは、Storm に送信されるトポロジの定義に使われます。 **GetTopologyBuilder** の内容を次のコードに置き換えて前述のトポロジを実装します。

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>トポロジを送信する

1. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[HDInsight の Storm に送信]** を選択します。

   > [!NOTE]  
   > メッセージが表示されたら、Azure サブスクリプションの資格情報を入力します。 2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスターの Storm があるサブスクリプションにサインインします。

2. **[Storm クラスター]** ドロップダウン リストから HDInsight クラスターの Storm を選択して、 **[送信]** を選択します。 送信が成功したかどうかは、 **[出力]** ウィンドウを使用して確認できます。

3. トポロジが正常に送信されたら、クラスターの **Storm トポロジ** が表示されます。 一覧から **[WordCount]** トポロジを選択して、実行中のトポロジに関する情報を表示します。

   > [!NOTE]  
   > また、**サーバー エクスプローラー**から **Storm トポロジ**を表示することもできます。 その場合、 **[Azure]**  >  **[HDInsight]** の順に展開し、HDInsight クラスターの Storm を右クリックして、 **[Storm トポロジの表示]** を選択します。

    トポロジ内のコンポーネントに関する情報を表示するには、ダイアグラム内のコンポーネントをダブルクリックします。

4. **[トポロジの概要]** ビューで、 **[強制終了]** を選んでトポロジを停止します。

   > [!NOTE]  
   > Storm トポロジは非アクティブ化されるか、クラスターが削除されるまで実行し続けます。

## <a name="transactional-topology"></a>トランザクションのトポロジ

前のトポロジは、トランザクションではなく、 トポロジ内のコンポーネントは、リプレイ メッセージに機能を実装しません。 トランザクションのトポロジの例として、プロジェクトを作成し、プロジェクトの種類として **[Storm Sample]\(Storm サンプル\)** を選択します。

トランザクションのトポロジは次の内容を実装してデータのリプレイをサポートします。

* **Metadata caching**:エラーが発生した場合にデータを取得して再度出力できるように、スパウトでは出力されたデータに関するメタデータを保存する必要があります。 サンプルが出力するデータは少量であるため、各タプルの生データはリプレイのディクショナリに保存されます。

* **Ack**:トポロジ内の各ボルトでは、`this.ctx.Ack(tuple)` を呼び出してタプルが正常に処理されたことを応答できます。 すべてのボルトでタプルを確認したら、スパウトの `Ack` メソッドが呼び出されます。 `Ack` メソッドにより、スパウトはリプレイのキャッシュされたデータを削除できます。

* **Fail**:各ボルトでは、`this.ctx.Fail(tuple)` を呼び出してタプルの処理が失敗したことを示すことができます。 エラーがスパウトの `Fail` メソッドに伝達され、そこでキャッシュされたメタデータを使用してタプルをリプレイされます。

* **Sequence ID**:タプルの出力時に、一意のシーケンス ID を指定できます。 この値は、リプレイ (Ack と Fail) 処理するタプルを示します。 たとえば、 **Storm サンプル** プロジェクトではデータの出力時に次の値を使用します。

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    このコードにより、センテンスを含むタプルが、**lastSeqId** に含まれるシーケンス ID 値と共に既定のストリームに出力されます。 この例では、**lastSeqId** は出力されるタプルごとに増加します。

**Storm サンプル** プロジェクトのように、コンポーネントがトランザクションであるかどうかは、構成に基づいて実行時に設定できます。

## <a name="hybrid-topology-with-c-and-java"></a>C# と Java のハイブリッド トポロジ

また、Data Lake Tools for Visual Studio は、C# のコンポーネントと Java のコンポーネントが混在するハイブリッド トポロジの作成に使用できます。

ハイブリッド トポロジの例として、プロジェクトを作成して **[Storm ハイブリッド サンプル]** を選択します。 このサンプルの種類は、次の概念を示します。

* **Java スパウト**と **C# ボルト**:**HybridTopology_javaSpout_csharpBolt** で定義されています。

    * **HybridTopologyTx_javaSpout_csharpBolt** で定義されたトランザクション バージョン。

* **C# スパウト**と **Java ボルト**:**HybridTopology_csharpSpout_javaBolt** で定義されています。

    * **HybridTopologyTx_csharpSpout_javaBolt** で定義されたトランザクション バージョン。

  > [!NOTE]  
  > このバージョンは、Closure コードをテキスト ファイルから Java コンポーネントとして使用する方法も示しています。


プロジェクトの送信時に使用されるトポロジを切り替えるには、クラスターへの送信前に `[Active(true)]` ステートメントを使用するトポロジに移動します。

> [!NOTE]  
> 必要なすべての Java ファイルは、このプロジェクトの一部として **JavaDependency** フォルダーに提供されています。

ハイブリッド トポロジの作成と送信を行う際には、次の点を考慮してください。

* **JavaComponentConstructor** は、スパウトまたはボルトの Java クラスのインスタンスを作成するために使用します。

* **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** は、Java オブジェクトから JSON に、Java コンポーネントのデータをシリアル化するときに使用します。

* トポロジをサーバーに送信するとき、 **[追加の構成]** オプションを使って **[Java ファイルのパス]** を指定する必要があります。 ここで指定するパスは、Java クラスを含む JAR ファイルのあるディレクトリである必要があります。

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET バージョン 0.9.4.203 には、Event Hub スパウト (Event Hubs から読み取る Java スパウト) の操作専用に新しいクラスとメソッドが導入されています。 Event Hub スパウトを使用するトポロジを作成する場合は、次のメソッドを使用します。

* **EventHubSpoutConfig** クラス:スパウト コンポーネントの構成を含むオブジェクトを作成します。

* **TopologyBuilder.SetEventHubSpout** メソッド:トポロジに、Event Hub スパウト コンポーネントを追加します。

> [!NOTE]  
> スパウトによって生成されたデータをシリアル化するには、**CustomizedInteropJSONSerializer** を引き続き使用する必要があります。

## <a id="configurationmanager"></a>ConfigurationManager の使用

**ConfigurationManager** を使ってボルトおよびスパウト コンポーネントから構成値を取得しないでください。 これを行った場合、null ポインター例外が発生する可能性があります。 代わりに、プロジェクトの構成は、トポロジ コンテキストのキーと値のペアとして Storm トポロジに渡されます。 構成値に依存する各コンポーネントは、初期化の間にコンテキストから値を取得する必要があります。

次のコードでは、これらの値を取得する方法を示します。

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

`Get` メソッドを使ってコンポーネントのインスタンスを取得する場合は、メソッドが `Context` パラメーターと `Dictionary<string, Object>` パラメーターの両方をコンストラクターに渡していることを確認する必要があります。 次の例は、これらの値を正しく渡している基本的な `Get` メソッドです。

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>SCP.NET を更新する方法

SCP.NET の最新リリースでは、NuGet からパッケージをアップグレードできます。 新しい更新プログラムが利用できる場合、アップグレードの通知が表示されます。 手動でアップグレードを確認するには、次の手順に従います。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選びます。

2. パッケージ マネージャーから **[更新プログラム]** を選択します。 更新プログラムが利用できる場合、それが一覧表示されます。 パッケージに対応する **[更新]** をクリックすると、そのパッケージがインストールされます。

> [!IMPORTANT]  
> NuGet を使用しない以前のバージョンの SCP.NET を利用してプロジェクトが作成された場合、次の手順で新しいバージョンに更新する必要があります。
>
> 1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選びます。
> 2. **[検索]** フィールドを使って **Microsoft.SCP.Net.SDK** を検索し、プロジェクトに追加します。

## <a name="troubleshoot-common-issues-with-topologies"></a>トポロジの一般的な問題のトラブルシューティング

### <a name="null-pointer-exceptions"></a>null ポインター例外

Linux ベースの HDInsight クラスターで C# トポロジを使う場合、ボルトおよびスパウト コンポーネントが **ConfigurationManager** を使って実行時に構成設定を読み取ると、null ポインター例外が返される可能性があります。

プロジェクトの構成は、トポロジ コンテキストのキーと値のペアとして Storm トポロジに渡されます。 これは、初期化時にコンポーネントに渡されるディクショナリ オブジェクトから取得できます。

詳細については、このドキュメントの [ConfigurationManager](#configurationmanager) に関するセクションをご覧ください。

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Linux ベースの HDInsight クラスターで C# トポロジを使うと、次のエラーが発生する可能性があります。

    System.TypeLoadException: Failure has occurred while loading a type.

このエラーは Mono がサポートする .NET のバージョンと互換性のないバイナリを使うと発生します。

Linux ベースの HDInsight クラスターでは、.NET 4.5 用にコンパイルされたバイナリをプロジェクトで使う必要があります。

### <a name="test-a-topology-locally"></a>トポロジをローカルでテストする

トポロジのクラスターへのデプロイは簡単ですが、状況によっては、トポロジをローカルでテストする必要が生じる場合があります。 次の手順を使用して、使用している開発環境のローカルでこの記事のサンプル トポロジを実行してテストします。

> [!WARNING]  
> ローカル テストは、基本的な C# のみトポロジの場合にだけ機能します。 ハイブリッド トポロジまたは複数のストリームを使用するトポロジには、ローカル テストは使用できません。

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[プロパティ]** を選びます。 プロジェクトのプロパティで、 **[出力の種類]** を **[コンソール アプリケーション]** に変更します。

    ![プロジェクトのプロパティのスクリーンショット ([出力の種類] を強調表示)](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > トポロジをクラスターにデプロイする前に、必ず **[出力の種類]** を **[クラス ライブラリ]** に戻すようにしてください。

2. **ソリューション エクスプローラー**で、プロジェクトを右クリックして、 **[追加]**  >  **[新しいアイテム]** の順に選択します。 **[クラス]** を選んで、クラス名として「**LocalTest.cs**」と入力します。 最後に **[追加]** をクリックします。

3. **LocalTest.cs** を開いて、先頭に次の **using** ステートメントを追加します。

    ```csharp
    using Microsoft.SCP;
    ```

4. **LocalTest** クラスの内容として次のコードを使用します。

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    コード コメントに目を通してください。 このコードでは、 **LocalContext** を使用して開発環境でコンポーネントを実行し、ローカル ドライブ上でテキスト ファイルにコンポーネント間のデータ ストリームを保存します。

1. **Program.cs** を開いて、**Main** メソッドに次を追加します。

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. 変更を保存して、**F5** キーをクリックするか、 **[デバッグ]**  >  **[デバッグの開始]** を使ってプロジェクトを起動します。 コンソール ウィンドウが表示され、テストの進行に合わせてステータスを記録します。 **[テストの完了]** と表示されたら、いずれかのキーを押してウィンドウを閉じます。

3. **Windows エクスプローラー**を使って、プロジェクトが含まれるディレクトリを見つけます 例: **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**。 このディレクトリで、**Bin** を開き、 **[デバッグ]** をクリックします。 テストの実行時に生成された、sentences.txt、counter.txt、splitter.txt というテキスト ファイルが表示されます。 それぞれのテキスト ファイルを開いてデータを確認します。

   > [!NOTE]  
   > これらのファイルでは、文字列データは 10 進数の値の配列として保存されます。 たとえば、**splitter.txt** ファイルの \[[97,103,111]] は *and* という単語を示します。

> [!NOTE]  
> HDInsight クラスターの Storm にデプロイする前に、必ず **[プロジェクトの種類]** を **[クラス ライブラリ]** に戻すようにしてください。

### <a name="log-information"></a>ログ情報

トポロジ コンポーネントからの情報は、 `Context.Logger`を使用して簡単に記録できます。 たとえば、次のコマンドでは情報ログ エントリが作成されます。

```csharp
Context.Logger.Info("Component started");
```

記録された情報は、**サーバー エクスプローラー**で **[Hadoop Service Log]** を探して閲覧できます。 HDInsight クラスターの Storm のエントリを展開して、 **[Hadoop Service Log]** を展開します。 最後に、閲覧するログ ファイルを選択します。

> [!NOTE]  
> ログは、クラスターで使用する Azure ストレージ アカウントに保存されます。 Visual Studio でログを表示するには、ストレージ アカウントを所有する Azure サブスクリプションにサインインする必要があります。

### <a name="view-error-information"></a>エラー情報の表示

トポロジの実行中に発生したエラーを表示するには、次の手順に従います。

1. **サーバー エクスプローラー**で、HDInsight クラスターの Storm を右クリックして **[Storm トポロジの表示]** を選びます。

2. **スパウト**と**ボルト**については、 **[最新のエラー]** の列に直近のエラーの情報が表示されます。

3. エラーの一覧にあるコンポーネントの **[Spout Id]** または **[Bolt Id]** を選びます。 詳細ページが表示され、詳細なエラー情報がページ下部の **[エラー]** セクションに表示されます。

4. さらに詳細な情報を取得するには、 **[Executors]** セクションから **[ポート]** を選ぶと、過去数分間の Storm ワーカー ログが表示されます。

### <a name="errors-submitting-topologies"></a>トポロジ送信エラー

HDInsight へのトポロジの送信時にエラーが発生した場合、HDInsight クラスターでのトポロジの送信を処理するサーバー側コンポーネントに関するログを確認できます。 これらのログを取得するには、コマンド ラインで次のコマンドを使用します。

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

__sshuser__ をクラスターの SSH ユーザー アカウントに置き換えます。 __clustername__ を HDInsight クラスターの名前に置き換えます。 `scp` と `ssh` の使用方法の詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

送信エラーには、さまざまな理由が考えられます。

* JDK がインストールされていないか、パスにない。
* 必要な Java 依存関係が送信内容に含まれていない。
* 依存関係が適合しない。
* トポロジの名前が重複している。

`hdinsight-scpwebapi.out` ログに `FileNotFoundException` が含まれている場合、次の状況が原因として考えられます。

* JDK が開発環境のパスに存在しない。 JDK が開発環境にインストールされていること、また `%JAVA_HOME%/bin` がパスにあることを確認してください。
* Java の依存関係が不足している。 必要な .jar ファイルを必ず含めて送信してください。

## <a name="next-steps"></a>次の手順

Event Hubs からのデータ処理の例は、「[HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する](apache-storm-develop-csharp-event-hub-topology.md)」を参照してください。

ストリーム データを複数のストリームに分割する C# トポロジの例は、「 [C# Storm example (C# Storm の例)](https://github.com/Blackmist/csharp-storm-example)」を参照してください。

C# トポロジの作成の詳細については、[GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md) を参照してください。

HDInsight を使用するさまざまな方法や、HDInsight での Storm のその他の例については、次のドキュメントを参照してください。

**Microsoft SCP.NET**

* [SCP プログラミング ガイド](apache-storm-scp-programming-guide.md)

**HDInsight での Apache Storm**

* [Deploy and monitor topologies with Apache Storm on HDInsight (HDInsight の Storm を使用したトポロジのデプロイと監視)](apache-storm-deploy-monitor-topology.md)
* [HDInsight での Apache Storm のトポロジ例](apache-storm-example-topology.md)

**HDInsight での Apache Hadoop**

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](../hadoop/hdinsight-use-hive.md)
* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](../hadoop/hdinsight-use-pig.md)
* [HDInsight 上の Apache Hadoop で Apache Hadoop MapReduce を使用する](../hadoop/hdinsight-use-mapreduce.md)

**HDInsight での Apache HBase**

* [HDInsight での Apache HBase の使用](../hbase/apache-hbase-tutorial-get-started-linux.md)
