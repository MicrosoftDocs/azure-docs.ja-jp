---
title: Azure HDInsight での Apache HBase のパフォーマンスに関する問題のトラブルシューティング
description: Azure HDInsight で最適なパフォーマンスを得るためのさまざまな Apache HBase パフォーマンス チューニング ガイドラインおよびヒント。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266504"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight での Apache HBase のパフォーマンスに関する問題のトラブルシューティング

このドキュメントでは、Azure HDInsight で最適なパフォーマンスを得るためのさまざまな Apache HBase パフォーマンス チューニング ガイドラインおよびヒントについて説明します。 これらのヒントの多くは、特定のワークロードと読み取り/書き込み/スキャン パターンによって異なります。 運用環境に適用する前に、構成の変更内容を徹底的にテストします。

## <a name="hdinsight-hbase-performance-insights"></a>HDInsight HBase パフォーマンスに関する分析情報

ほとんどの HBase ワークロードで最もボトルネックとなっているのは、先書きログ (WAL) です。 これは、書き込みパフォーマンスに重大な影響を及ぼします。 HDInsight HBase は、分離されたストレージ コンピューティング モデルを備えています。つまり、データは Azure Storage にリモートで格納されますが、リージョン サーバーは VM 上でホストされます。 最近までは、先書きログも Azure Storage に書き込まれていたため、HDInsight の場合はこのボトルネックが増幅されていました。 [高速書き込み](./apache-hbase-accelerated-writes.md)機能は、Azure Premium SSD マネージド ディスクに先書きログを書き込むことによってこの問題を解決するように設計されています。 これは、書き込みパフォーマンスを大幅に向上させ、書き込み集中型のワークロードの一部で発生する多くの問題を解決するのに役立ちます。

[Premium ブロック BLOB ストレージ アカウント](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)をご自分のリモート ストレージとして使用することで、読み取り操作が大幅に改善します。 このオプションは、先書きログ機能が有効になっている場合にのみ使用できます。

## <a name="compaction"></a>圧縮

圧縮は、コミュニティで基本的に意見が一致しているもう 1 つの潜在的なボトルネックです。  既定では、HDInsight HBase クラスターでは主な圧縮が無効になっています。 これは、圧縮がリソースを大量に消費するプロセスであるため、お客様が自身のワークロードの特性に応じて、つまりピーク時以外の時間に柔軟にスケジュールできるようにするためです。 また、このストレージは、ほとんどのオンプレミス インスタンスで一般的なローカル HDFS とは対照的に、リモート (Azure ストレージでサポートされる) であるため、データのローカリティは重要ではありません。これは、主要な圧縮の最も重要な目標の 1 つです。

お客様が自身の都合に応じて主要な圧縮を入念にスケジュールすることを前提とします。 このメンテナンスが行われていない場合、圧縮は、長期的には読み取りパフォーマンスに大きく影響します。

特にスキャン操作の場合、100 ミリ秒を大きく超過した平均待機時間は、問題の原因になります。 主要な圧縮が正確にスケジュールされているかどうかを確認します。

## <a name="know-your-apache-phoenix-workload"></a>ご利用の Apache Phoenix ワークロードを把握する

次の質問に答えることで、ご利用の Apache Phoenix ワークロードをよりよく理解することができます。

* すべての "読み取り" がスキャンに変換されていますか。
    * そうである場合、これらのスキャンにはどのような特性がありますか。
    * これらのスキャン用に Phoenix テーブル スキーマを最適化しましたか (適切なインデックス作成など)。
* "読み取り" によって生成されるクエリ プランを理解するために、`EXPLAIN` ステートメントを使用しましたか。
* 書き込みは "upsert-selects" ですか。
    * その場合は、スキャンも実行されます。 スキャンに予想される待機時間は、HBase でのポイント取得の 10 ミリ秒に対して、平均で 100 ミリ秒台となります。  

## <a name="test-methodology-and-metrics-monitoring"></a>テスト方法とメトリックの監視

YCSB、JMeter、Pherf などのベンチマークを使用してパフォーマンスをテストおよび調整する場合は、次のことを確認してください。

1. クライアント コンピューターがボトルネックになることはありません (クライアント コンピューター上での CPU 使用率を確認してください)。

1. クライアントの帯域幅を飽和状態にするために、クライアント側の構成 (スレッド数など) が適切に調整されています。

1. テスト結果は、正確かつ体系的に記録されます。

クエリのパフォーマンスが突然悪化した場合 (アプリケーション コード内でのバグの可能性を確認する)、突然大量の無効なデータが生成されて、読み取りの待ち時間が自然に長くなっていませんか。

## <a name="migration-issues"></a>移行の問題

Azure HDInsight に移行する場合は、必ず体系的かつ正確に移行してください。自動化での移行をお勧めします。 手動での移行は避けてください。 以下のことを確認してください。

1. テーブル属性 (圧縮、ブルーム フィルターなど) は、正確に移行する必要があります。

1. Phoenix テーブルの場合は、ソルトの設定を新しいクラスター サイズに適切にマップする必要があります。 たとえば、ソルト バケットの数は、クラスター内のワーカー ノードの数の倍数にすることをお勧めします。特定の倍数は、観測されるホット スポットの量の要因となります。  

## <a name="server-side-config-tunings"></a>サーバー側の構成のチューニング

HDInsight HBase では、HFiles はリモート ストレージに格納されるため、キャッシュ ミスが発生した場合、読み取りのコストは、オンプレミスのシステムよりも確実に高くなります。オンプレミスのシステムのデータは、ネットワーク待機時間が伴うためローカル HDFS によってサポートされます。 ほとんどのシナリオにおいて、HBase キャッシュ (ブロック キャッシュとバケット キャッシュ) のインテリジェントな用途は、この問題を回避するように設計されています。 ただし、これが顧客にとって問題になる場合が時々あります。 Premium ブロック BLOB アカウントを使用することが、それには多少役に立ちます。 ただし、WASB (Windows Azure ストレージ ドライバー) BLOB が、読み取りモードであると判断した内容 (シーケンシャルとランダム) に基づいてブロック内のデータを取得する場合に `fs.azure.read.request.size` などの特定のプロパティに依存する場合、読み取りでの待機時間が長いインスタンスを引き続き確認する場合があります。 経験的な実験により、読み取り要求のブロック サイズ (`fs.azure.read.request.size`) を 512 KB に設定し、HBase テーブルのブロック サイズを同じになるように一致させると、実際に最良の結果が得られることがわかりました。

HDInsight HBase では、ほとんどの大規模ノード クラスターの場合に、<ph id="ph2">`regionservers`</ph> を実行している VM に接続されたローカル SSD 上にファイルとして <ph id="ph1">`bucketcache`</ph> を提供します。 場合によっては、代わりにオフ ヒープ キャッシュを使用すると、いくらか改善されることがあります。 これには、使用可能なメモリを使用しなければならず、ファイルベースのキャッシュよりもサイズが小さくなる可能性があるという制限があるので、これが常に最適な選択であるとは限りません。

調整した他の特定のパラメーターのいくつかは、次のようないくつかの理由で、程度に差はあるものの、役立つように思われました。

1. `memstore` のサイズを既定値の 128 MB から 256 MB に増やします。この設定は、通常、負荷の高い書き込みのシナリオで推奨されます。

1. 圧縮専用のスレッド数を、既定値の 1 から 4 に増やします。 この設定は、軽微な圧縮が頻繁に見られる場合に適切です。

1. ストアに制限があるため、`memstore` のフラッシュをブロックするのは避けます。 このバッファーを提供するために、`Hbase.hstore.blockingStoreFiles` を 100 に増やすことができます。

1. フラッシュを制御する場合は、次のように既定値に対処できます。

    1. `Hbase.regionserver.maxlogs` は、32 から 140 に増やすことができます (WAL 制限があるのでフラッシュは回避)。

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0.55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0.60.

1. スレッド プール チューニングのための Phoenix 固有の構成:

    1. `Phoenix.query.queuesize` は 10000 まで増やすことができます。

    1. `Phoenix.query.threadpoolsize` は 512 まで増やすことができます。

1. その他の Phoenix 固有の構成:

    1. インデックス参照の数が多い場合は、`Phoenix.rpc.index.handler.count` を 50 に設定できます。

    1. `Phoenix.stats.updateFrequency` – 既定値の 15 分から 1 時間に増やすことができます。

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` – 30 分から 1 時間に増やすことができます。

    1. `Phoenix.coprocessor.maxmetadatacachesize` – 20 MB から 50 MB に増やすことができます。

1. RPC タイムアウト – HBase RPC タイムアウト、HBase クライアント スキャナー タイムアウト、および Phoenix クエリ タイムアウトを 3 分に増やすことができます。 ここで重要なのは、`hbase.client.scanner.caching` パラメーターが、サーバー側とクライアント側で一致する値に設定されていることです。 それ以外の場合、この設定により、クライアント側で `OutOfOrderScannerException` に関連するエラーが発生します。 大規模なスキャンの場合、この設定は小さい値に設定する必要があります。 この値を 100 に設定します。

## <a name="other-considerations"></a>その他の考慮事項

チューニングで考慮すべきその他のパラメーターは次のとおりです。

1. `Hbase.rs.cacheblocksonwrite` – この設定は、HDI では既定で true に設定されます。

1. 後で行うために軽微な圧縮を延期できる設定。

1. 読み取り要求および書き込み要求用に予約するキューの割合を調整するなどの試験的な設定。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

- [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

- さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
