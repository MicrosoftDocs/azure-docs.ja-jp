---
title: "Azure Event Hubs Archive を使用したテレメトリ データのアーカイブの概要 | Microsoft Docs"
description: "Azure Event Hubs Archive 機能の概要を示します。"
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 95a927d8c2fbfbcb6aa663985d078d5146c489aa
ms.lasthandoff: 03/22/2017


---
# <a name="azure-event-hubs-archive"></a>Azure Event Hubs Archive の概要
Azure Event Hubs Archive を利用すると、Event Hubs のストリーミング データを任意の BLOB ストレージ アカウントに自動的に配信できます。その際、時間やサイズを柔軟に指定可能です。 Archive の設定は手軽で、実行に伴う管理コストは生じません。また、Event Hubs の[スループット単位](event-hubs-what-is-event-hubs.md#capacity)に応じて自動的にスケールします。 Event Hubs Archive は Azure にストリーミング データを読み込む最も簡単な方法であり、これを利用すれば、データのキャプチャではなくデータの処理に注力できるようになります。

Event Hubs Archive を利用すると、リアルタイムおよびバッチベースのパイプラインを同じストリームで処理できます。 そのため、変化するニーズに合わせて拡大可能なソリューションを構築できます。 将来のリアルタイム処理を視野に入れてバッチベースのシステムを構築している場合も、既存のリアルタイム ソリューションに効率的なコールド パスを追加したいと考えている場合も、Event Hubs Archive ならストリーミング データの操作が容易です。

## <a name="how-event-hubs-archive-works"></a>Event Hubs Archive の機能のしくみ
Event Hubs は分散ログに似た、テレメトリの受信のための持続的バッファーです。 Event Hubs におけるスケールの鍵となるのは、 [パーティション分割されたコンシューマー モデル](event-hubs-what-is-event-hubs.md#partitions)です。 各パーティションは独立したデータのセグメントであり、個別に使用されます。 このデータは、構成可能なリテンション期間に基づいて、所定のタイミングで破棄されます。 そのため、特定のイベント ハブが "いっぱい" になることはありません。

Event Hubs Archive では、アーカイブされたデータを格納するための独自の Azure BLOB ストレージ アカウントとコンテナーを指定することができます。 このアカウントのリージョンはイベント ハブと同じであっても、別のリージョンであってもかまわないため、Event Hubs Archive 機能の柔軟性がさらに高まっています。

アーカイブされたデータは [Apache Avro][Apache Avro] 形式で書き込まれます。これはコンパクトで高速なバイナリ形式で、インライン スキーマを備えた便利なデータ構造になっています。 この形式は Hadoop エコシステムで幅広く使用されているほか、Stream Analytics や Azure Data Factory でも使用されています。 Avro の操作の詳細は、この記事の後半に記載してあります。

### <a name="archive-windowing"></a>アーカイブのウィンドウ設定
Event Hubs Archive では、アーカイブを制御する "ウィンドウ" を設定することができます。 このウィンドウは "先に来たものが優先されるポリシー" が適用される最小サイズと時間の構成です。つまり、先に生じたトリガーによってアーカイブ操作が行われます。 15 分/100 MB のアーカイブ ウィンドウを設定してあるときに 1 MB/秒で送信する場合、サイズのウィンドウが時間のウィンドウよりも前にトリガーとなります。 各パーティションのアーカイブは個別に行われ、完了したブロック BLOB がアーカイブ時に (アーカイブが実行されるタイミングとなったときに) 書き込まれます。 名前付け規則は次のとおりです。

```
[Namespace]/[EventHub]/[Partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>スループット単位へのスケーリング
Event Hubs のトラフィックは [スループット単位](event-hubs-what-is-event-hubs.md#capacity)で制御されます。 受信の場合、1 スループット単位は最大 1 MB/秒または 1,000 イベント/秒となり、送信の場合はその倍となります。 Standard Event Hubs には 1 ～ 20 のスループット単位を構成でき、クォータの引き上げの[サポート要求][support request]を通じてさらに購入することもできます。 購入済みのスループット単位を超えた使用分については、調整されます。 Event Hubs Archive は内部 Event Hubs ストレージからデータを直接コピーするため、スループット単位の送信クォータを回避でき、他の処理リーダー (Stream Analytics や Spark など) のために送信を節約できます。

Event Hubs Archive は、構成後、最初のイベントを送信するとすぐに自動的に実行されます。 そして常時実行された状態が続きます。 ダウンストリーム処理で処理が行われていることを把握しやすいように、Event Hubs はデータがないときは空のファイルを書き込みます。 これにより、バッチ プロセッサに提供可能な、予測しやすいパターンとマーカーが得られます。

## <a name="setting-up-event-hubs-archive"></a>Event Hubs Archive の設定
イベント ハブの作成時に、ポータルまたは Azure Resource Manager を使用して Archive を構成できます。 Archive は、 **[オン]** ボタンをクリックするだけで有効化できます。 ストレージ アカウントとコンテナーを構成するには、ブレードの **[コンテナー]** セクションをクリックします。 Event Hubs Archive ではストレージとのサービス対サービスの認証が使用されるため、ストレージ接続文字列を指定する必要はありません。 リソース ピッカーにより、ストレージ アカウントのリソース URI が自動的に選択されます。 Azure Resource Manager を使用している場合は、この URI を文字列として明示的に指定する必要があります。

既定の時間ウィンドウは 5 分です。 最小値は 1、最大値は 15 です。 **サイズ** ウィンドウの範囲は 10 ～ 500 MB です。

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>既存のイベント ハブへの Archive の追加
Event Hubs 名前空間内の既存の Event Hubs で Archive を構成できます。 この機能は以前の **Messaging** または **Mixed** 型の名前空間では利用できません。 既存のイベント ハブで Archive を有効にするか、Archive の設定を変更するには、名前空間をクリックして **[要点]** ブレードを読み込み、Archive の有効化または Archive 設定の変更を行う対象のイベント ハブをクリックします。 最後に、次の図に示すように、開いているブレードの **[プロパティ]** セクションをクリックします。

![][2]

Event Hubs Archive は Azure Resource Manager テンプレートを使用して構成することもできます。 詳細については、 [こちらの記事](event-hubs-resource-manager-namespace-event-hub-enable-archive.md)を参照してください。

## <a name="exploring-the-archive-and-working-with-avro"></a>アーカイブの確認と Avro の操作
Event Hubs Archive を構成すると、Azure ストレージ アカウントとコンテナー (構成された時間ウィンドウで指定されたもの) にファイルが作成されます。 これらのファイルは、[Azure Storage エクスプローラー][Azure Storage Explorer]などの任意のツールを使用して確認できます。 また、ローカルにダウンロードして操作することができます。

Event Hubs Archive によって生成されたファイルには、次の Avro スキーマがあります。

![][3]

Avro ファイルを調べるには、Apache の [Avro Tools][Avro Tools] jar を使うと簡単です。 この jar をダウンロードしたら、次のコマンドを実行して、特定の Avro ファイルのスキーマを表示できます。

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

このコマンドによって次の情報が返されます。

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

また、Avro ツールを使用してファイルを JSON 形式に変換し、その他の処理を実行することもできます。

より高度な処理を実行するには、お好みのプラットフォーム用の Avro をダウンロードしてインストールしてください。 この記事の執筆時点では、C、C++、C\#、Java、NodeJS、Perl、PHP、Python、Ruby 向けの実装があります。

Apache Avro には、[Java][Java] と [Python][Python] 向けの完全な入門ガイドが用意されています。 [Event Hubs Archive の導入](event-hubs-archive-python.md) に関する記事を読むこともできます。

## <a name="how-event-hubs-archive-is-charged"></a>Event Hubs Archive に対する課金方法
Event Hubs Archive の料金は、スループット単位と同様に時間単位で測定されます。 料金は、その名前空間で購入されたスループット単位の数に正比例します。 スループット単位が増減すると、Event Hubs Archive の規模もそれに応じたパフォーマンスを提供するために調整されます。 測定は連携して行われます。 Event Hubs Archive の料金は、スループット単位あたり 0.10 ドル/時間で、プレビュー期間中は 50% の割引が適用されます。

Event Hubs Archive は、データを Azure に読み込むうえで最も簡単な方法です。 Azure Data Lake、Azure Data Factory、Azure HDInsight を利用することで、使い慣れたツールとプラットフォームを使って、必要なスケールでバッチ処理やその他の分析を実行することができます。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs を使用する完全なサンプル アプリケーション][sample application that uses Event Hubs]
* [Event Hubs でのイベント処理のスケールアウト][Scale out Event Processing with Event Hubs] サンプル
* [Event Hubs の概要][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

