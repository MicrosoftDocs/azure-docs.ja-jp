---
title: Storm を使用して Event Hubs のイベントを処理する - Azure HDInsight
description: Visual Studio で HDInsight Tools for Visual Studio を使用して作成した C# Storm トポロジによって Azure Event Hubs のデータを処理する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee483872535c58bde521cf0a751058fdc2fecd40
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871454"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>HDInsight 上の Apache Storm で Azure Event Hubs からのイベントを処理する (C#)

HDInsight 上の [Apache Storm](https://storm.apache.org/) から Azure Event Hubs を使用する方法について説明します。 このドキュメントでは、C# Storm トポロジを使用して Event Hubs からデータの読み取りや書き込みを行います。

> [!NOTE]  
> このプロジェクトの Java バージョンについては、「[HDInsight で Apache Storm を使用して Azure Event Hubs のイベントを処理する (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)」を参照してください。

## <a name="scpnet"></a>SCP.NET

このドキュメントの手順では SCP.NET を使用します。SCP.NET は、HDInsight の Storm で使用する C# トポロジとコンポーネントを作成しやすくする NuGet パッケージです。

HDInsight 3.4 以降では、Mono を使用して C# トポロジを実行します。 このドキュメントで使用される例は、HDInsight 3.6 で動作します。 HDInsight の独自の .NET ソリューションを作成する場合は、[Mono の互換性](https://www.mono-project.com/docs/about-mono/compatibility/)に関するドキュメントで、非互換性がないか確認してください。

### <a name="cluster-versioning"></a>クラスターのバージョン管理

プロジェクトで使用する Microsoft.SCP.Net.SDK NuGet パッケージは、HDInsight にインストールされた Storm のメジャー バージョンと一致する必要があります。 HDInsight バージョン 3.5 および 3.6 は Storm 1.x を使用するため、これらのクラスターでは SCP.NET バージョン 1.0.x.x を使用する必要があります。

C# トポロジは .NET 4.5 も対象にする必要があります。

## <a name="how-to-work-with-event-hubs"></a>イベント ハブを使用する方法

Microsoft では、Storm トポロジからの Event Hubs との通信に使用できる Java コンポーネントのセットを提供します。 これらのコンポーネントの中で HDInsight 3.6 に適合するバージョンを含んだ Java アーカイブ (JAR) ファイルは [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) に置かれています。

> [!IMPORTANT]  
> コンポーネントは Java で記述されていますが、C# トポロジから簡単に使用することができます。

この例では、次のコンポーネントを使用します。

* __EventHubSpout__: イベント ハブからデータを読み取ります。
* __EventHubBolt__: イベント ハブにデータを書き込みます。
* __EventHubSpoutConfig__: EventHubSpout の構成に使用します。
* __EventHubBoltConfig__: EventHubBolt の構成に使用します。

### <a name="example-spout-usage"></a>スパウトの使用例

SCP.NET は、トポロジに EventHubSpout を追加するためのメソッドを提供します。 これらのメソッドにより、Java コンポーネントを追加するための一般的なメソッドを使用した場合よりも、追加が簡単になります。 次の例では、SCP.NET によって提供される __SetEventHubSpout__ メソッドと **EventHubSpoutConfig** メソッドを使用してスパウトを作成する方法を示します。

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

前の例では __EventHubSpout__ という名前の新しいスパウト コンポーネントを作成し、これがイベント ハブと通信するように構成しています。 このコンポーネントの並列処理のヒントはイベント ハブのパーティション数に設定されています。 この設定によって Storm は、パーティションごとに、コンポーネントのインスタンスを作成することができます。

### <a name="example-bolt-usage"></a>ボルトの使用例

ボルトのインスタンスを作成するには、**JavaComponmentConstructor** メソッドを使用します。 次の例では、**EventHubBolt** の新しいインスタンスを作成および構成する方法を示します。

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> この例では、スパウトの例で行ったように、**EventHubBoltConfig** を作成する **JavaComponentConstructor** を使用する代わりに、文字列として渡される Clojure 式を使用します。 どちらの方法でも動作します。 最適と思われる方法をお使いください。

## <a name="download-the-completed-project"></a>完成したプロジェクトをダウンロードする

この記事で作成したプロジェクトの完全なバージョンを [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) からダウンロードできます。 ただし、この記事の手順に従って構成設定を指定する必要があります。

### <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Storm クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照し、 **[クラスターの種類]** で **[Storm]** を選択してください。

* [Azure イベント ハブ](../../event-hubs/event-hubs-create.md)。

* [Azure .NET SDK](https://azure.microsoft.com/downloads/)。

* [HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

* Java JDK 1.8 以降がインストールされている開発環境。 JDK は [Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support) からダウンロードできます。

  * **JAVA_HOME** 環境変数は、Java があるディレクトリを指している必要があります。
  * **%JAVA_HOME%/bin** ディレクトリはパス内にある必要があります。

## <a name="download-the-event-hubs-components"></a>Event Hubs のコンポーネントをダウンロードする

Event Hubs スパウトとボルト コンポーネントは [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) からダウンロードできます。

`eventhubspout` という名前のディレクトリを作成し、そのディレクトリにファイルを保存します。

## <a name="configure-event-hubs"></a>Event Hubs を構成する

Event Hubs は、この例のデータ ソースです。 [Event Hubs の使用](../../event-hubs/event-hubs-create.md)に関するページの "イベント ハブの作成" のセクションにある情報を使用します。

1. イベント ハブが作成されたら、Azure Portal の **EventHub** 設定を表示し、**[共有アクセス ポリシー]** を選択します。 **[+ 追加]** を選択して、次のポリシーを作成します。

   | 名前 | アクセス許可 |
   | --- | --- |
   | ライター |送信 |
   | リーダー |リッスン |

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png" alt-text="[共有アクセス ポリシー] ウィンドウのスクリーンショット" border="true":::

2. **reader** ポリシーと **writer** ポリシーを選択します。 両方のポリシーのプライマリ キー値をコピーして保存します (これらは後で使用します)。

## <a name="configure-the-eventhubwriter"></a>EventHubWriter の構成

1. HDInsight Tools for Visual Studio の最新バージョンをまだインストールしていない場合は、[HDInsight Tools for Visual Studio の使用開始](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)に関するページをご覧ください。

2. [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) からソリューションをダウンロードします。

3. **EventHubExample.sln** を開きます。 **EventHubWriter** プロジェクトで、**App.config** ファイルを開きます。 前に構成したイベント ハブの情報を使用して、次のキーの値を入力します。

   | Key | 値 |
   | --- | --- |
   | EventHubPolicyName |writer (*Send* 権限を持つポリシーに別の名前を使用した場合は、その名前を使用) |
   | EventHubPolicyKey |writer ポリシーのキー。 |
   | EventHubNamespace |イベント ハブが含まれている名前空間。 |
   | EventHubName |イベント ハブ名。 |
   | EventHubPartitionCount |イベント ハブのパーティションの数。 |

4. **App.config** ファイルを保存して閉じます。

## <a name="configure-the-eventhubreader"></a>EventHubReader の構成

1. **EventHubReader** プロジェクトを開きます。

2. **EventHubReader** の **App.config** ファイルを開きます。 前に構成したイベント ハブの情報を使用して、次のキーの値を入力します。

   | Key | 値 |
   | --- | --- |
   | EventHubPolicyName |reader (*listen* 権限を持つポリシーに別の名前を使用した場合は、その名前を使用) |
   | EventHubPolicyKey |reader ポリシーのキー。 |
   | EventHubNamespace |イベント ハブが含まれている名前空間。 |
   | EventHubName |イベント ハブ名。 |
   | EventHubPartitionCount |イベント ハブのパーティションの数。 |

3. **App.config** ファイルを保存して閉じます。

## <a name="deploy-the-topologies"></a>トポロジのデプロイ

1. **ソリューション エクスプローラー** で **EventHubReader** プロジェクトを右クリックし、 **[HDInsight の Storm に送信]** を選択します。

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png" alt-text="ソリューション エクスプローラーのスクリーンショット ([HDInsight の Storm に送信] を強調表示)" border="true":::

2. **[トポロジの送信]** ダイアログ ボックスで該当する **[Storm クラスター]** を選択します。 **[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした JAR ファイルがあるディレクトリを選択します。 最後に、 **[送信]** をクリックします。

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png" alt-text="[トポロジの送信] ダイアログ ボックスのスクリーンショット" border="true":::

3. トポロジが送信されると、**[Storm トポロジ ビューアー]** が表示されます。 トポロジに関する情報を表示するには、左側のウィンドウにある **[EventHubReader]** トポロジを選択します。

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png" alt-text="Storm トポロジ ビューアーのスクリーンショット" border="true":::

4. **ソリューション エクスプローラー** で **EventHubWriter** プロジェクトを右クリックし、 **[HDInsight の Storm に送信]** を選択します。

5. **[トポロジの送信]** ダイアログ ボックスで該当する **[Storm クラスター]** を選択します。 **[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした JAR ファイルがあるディレクトリを選択します。 最後に、 **[送信]** をクリックします。

6. トポロジが送信されたら、 **[Storm トポロジ ビューアー]** でトポロジ一覧を最新情報に更新し、両方のトポロジがクラスターで実行中であることを確認します。

7. **[Storm トポロジ ビューアー]** で **[EventHubReader]** トポロジを選択します。

8. ボルトのコンポーネントの概要を開くには、ダイアグラムの **[LogBolt]** コンポーネントをダブルクリックします。

9. **[Executors]** セクションで、**[ポート]** 列のリンクを 1 つ選択します。 これによって、コンポーネントで記録された情報が表示されます。 次のテキストのような情報が記録されています。

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>トポロジの停止

トポロジを停止するには、**[Storm トポロジ ビューアー]** で各トポロジを選択し、**[強制終了]** をクリックします。

:::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png" alt-text="Storm トポロジ ビューアーのスクリーンショット ([強制終了] ボタンを強調表示)" border="true":::

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ

このドキュメントでは、C# トポロジから Java Event Hubs スパウトおよびボルトを使用して、Azure Event Hubs のデータを操作する方法について説明しました。 C# トポロジの作成の詳細については、次の記事を参照してください。

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP プログラミング ガイド](apache-storm-scp-programming-guide.md)
* [HDInsight での Apache Storm のトポロジ例](apache-storm-example-topology.md)