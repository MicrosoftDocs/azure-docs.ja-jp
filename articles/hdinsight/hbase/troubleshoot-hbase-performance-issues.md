---
title: Azure HDInsight での Apache HBase のパフォーマンスに関する問題のトラブルシューティング
description: Azure HDInsight で最適なパフォーマンスを得るためのさまざまな Apache HBase パフォーマンス チューニング ガイドラインおよびヒント。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942897"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight での Apache HBase のパフォーマンスに関する問題のトラブルシューティング

この記事では、Azure HDInsight で最適なパフォーマンスを得るためのさまざまな Apache HBase パフォーマンス チューニングのガイドラインとヒントについて説明します。 これらのヒントの多くは、特定のワークロードと読み取り/書き込み/スキャン パターンによって異なります。 運用環境で構成の変更を適用する前に、それらを十分にテストしてください。

## <a name="hbase-performance-insights"></a>HBase パフォーマンスの分析情報

ほとんどの HBase ワークロードで最もボトルネックとなっているのは、先書きログ (WAL) です。 これは、書き込みパフォーマンスに重大な影響を及ぼします。 HDInsight HBase には、別個のストレージ コンピューティング モデルがあります。 データは、仮想マシンがリージョン サーバーをホストしている場合でも、Azure Storage にリモートで格納されます。 最近までは、WAL は Azure Storage にも書き込まれていました。 HDInsight では、この動作によってこのボトルネックが悪化しています。 [高速書き込み](./apache-hbase-accelerated-writes.md)機能は、この問題を解決するように設計されています。 この機能では、WAL は Azure Premium SSD マネージド ディスクに書き込まれます。 これにより、書き込みパフォーマンスが大幅に向上し、書き込み集中型のワークロードの一部で発生する多くの問題を解決するのに役立ちます。

読み取り操作を大幅に改善するには、[Premium ブロック BLOB ストレージ アカウント](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)をリモート ストレージとして使用します。 このオプションは、WAL 機能が有効になっている場合にのみ使用できます。

## <a name="compaction"></a>圧縮

圧縮は、コミュニティで基本的に意見が一致しているもう 1 つの潜在的なボトルネックです。 既定では、HDInsight HBase クラスターで主要な圧縮は無効になっています。 圧縮が無効になっている理由は、それがリソース集中型プロセスではあっても、お客様が自分のワークロードに従ってそれを柔軟にスケジュールすることができるからです。 たとえば、ピーク時以外の時間帯にスケジュールすることができます。 また、ストレージは、ローカルの Hadoop 分散ファイルシステム (HDFS) ではなく、リモートである (Azure Storage によってバックアップされている) ため、データの局所性は問題になりません。

お客様は、必要に応じて、主要な圧縮をスケジュールします。 このメンテナンスが行われない場合、圧縮は長期的に見て、読み取りパフォーマンスに悪影響を与えます。

スキャン操作の場合、100 ミリ秒を大きく超える平均待機時間は、懸念材料になります。 主要な圧縮が正確にスケジュールされているかどうかを確認します。

## <a name="apache-phoenix-workload"></a>Apache Phoenix ワークロード

次の質問に答えることで、ご利用の Apache Phoenix ワークロードをよりよく理解することができます。

* すべての "読み取り" がスキャンに変換されていますか。
    * そうである場合、これらのスキャンにはどのような特性がありますか。
    * これらのスキャン用に Phoenix テーブル スキーマを最適化しましたか (適切なインデックス作成など)。
* "読み取り" によって生成されるクエリ プランを理解するために、`EXPLAIN` ステートメントを使用しましたか。
* 書き込みは "upsert-selects" ですか。
    * その場合は、スキャンも実行されます。 スキャンの予想される平均待機時間は、HBase でのポイント取得の 10 ミリ秒と比較して、約 100 ミリ秒です。  

## <a name="test-methodology-and-metrics-monitoring"></a>テスト方法とメトリックの監視

Yahoo! Cloud Serving Benchmark、JMeter、Pherf などのベンチマークを使用してパフォーマンスのテストと調整を行っている場合は、次の点を確認してください。

- クライアント コンピューターがボトルネックになっていない。 これを確認するには、クライアント コンピューターの CPU 使用率をチェックします。

- クライアントの帯域幅を飽和状態にするために、クライアント側の構成 (スレッド数など) が適切に調整されている。

- テスト結果は、正確かつ体系的に記録されます。

クエリのパフォーマンスが突然以前より大幅に悪化し始めた場合は、アプリケーション コードに潜在的なバグがないか確認してください。 急に大量の無効なデータが生成されている場合は、 読み取り待機時間が長くなる可能性があります。

## <a name="migration-issues"></a>移行の問題

Azure HDInsight に移行する場合は、必ず体系的かつ正確に (できれば自動化によって) 移行するようにしてください。 手動での移行は避けてください。 次のことを確認してください。

- テーブル属性が正確に移行されている。 属性は、圧縮、ブルーム フィルターなどとして含めることができます。

- Phoenix テーブルのソルトの設定が、新しいクラスター サイズに適切にマップされている。 たとえば、ソルト バケットの数は、クラスター内のワーカー ノード数の倍数である必要があります。 また、ホット スポットの数の係数である倍数を使用する必要があります。

## <a name="server-side-configuration-tunings"></a>サーバー側の構成のチューニング

HDInsight HBase では、HFiles はリモート ストレージに格納されます。 キャッシュ ミスがある場合、読み取りコストはオンプレミスのシステムよりも高くなります。これは、オンプレミス システムのデータがローカルの HDFS によってバックアップされているためです。 ほとんどのシナリオにおいて、この問題を回避するために、HBase キャッシュ (ブロック キャッシュとバケット キャッシュ) のインテリジェントな使用が設計されています。 この問題が回避されない場合は、Premium ブロック BLOB アカウントを使用すると、この問題に役立つ可能性があります。 Windows Azure Storage Blob ドライバーは、`fs.azure.read.request.size` などの特定のプロパティに依存し、読み取りモード (シーケンシャルとランダム) と判断した内容に基づいてブロックでデータをフェッチするため、読み取りの待機時間が長いインタンスが存在し続ける可能性があります。 経験的な実験により、読み取り要求のブロック サイズ (`fs.azure.read.request.size`) を 512 KB に設定し、HBase テーブルのブロック サイズを同じサイズに一致させると、実践で最良の結果が得られることがわかりました。

ほとんどの大規模なノード クラスターの場合、HDInsight HBase は、`regionservers` を実行する、仮想マシンに接続されたローカル Premium SSD のファイルとして `bucketcache` を提供します。 代わりにオフ ヒープ キャッシュを使用すると、いくらか改善されることがあります。 この回避策には、使用可能なメモリを使用し、ファイルベースのキャッシュよりも小さくなる可能性があるという制限があるため、必ずしも最良の選択であるとは限りません。

その他のいくつかの特定のパラメーターを以下に示します。これらは、Microsoft がチューニングしたもので、程度の差はあれ、役に立つと考えられます。

- `memstore` のサイズを既定の 128 MB から 256 MB に増やします。 通常、この設定は、大量の書き込みを行う場合に推奨されます。

- 圧縮専用のスレッド数を、既定の設定である **1** から **4** に増やします。 この設定は、軽微な圧縮が頻繁に見られる場合に適切です。

- ストアに制限があるため、`memstore` のフラッシュをブロックするのは避けます。 このバッファーを提供するには、`Hbase.hstore.blockingStoreFiles` の設定を **100** に増やします。

- フラッシュを制御するには、次の設定を使用します。

    - `Hbase.regionserver.maxlogs`:**140** (WAL の制限のためフラッシュを回避)

    - `Hbase.regionserver.global.memstore.lowerLimit`:**0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`:**0.60**

- スレッド プール チューニングのための Phoenix 固有の構成:

    - `Phoenix.query.queuesize`:**10000**

    - `Phoenix.query.threadpoolsize`:**512**

- その他の Phoenix 固有の構成:

    - `Phoenix.rpc.index.handler.count`:**50** (大規模な、または多くのインデックス参照がある場合)

    - `Phoenix.stats.updateFrequency`:**1 時間**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`:**1 時間**

    - `Phoenix.coprocessor.maxmetadatacachesize`:**50 MB**

- RPC タイムアウト:**3 分**

   - RPC タイムアウトには、HBase RPC タイムアウト、HBase クライアント スキャナー タイムアウト、および Phoenix クエリ タイムアウトが含まれます。 
   - `hbase.client.scanner.caching` パラメーターが、サーバー側とクライアント側の両方で同じ値に設定されていることを確認します。 同じでない場合、この設定により、`OutOfOrderScannerException` に関連したクライアント側のエラーが発生します。 大規模なスキャンの場合、この設定は小さい値に設定する必要があります。 この値は、**100** に設定します。

## <a name="other-considerations"></a>その他の考慮事項

チューニングを検討すべき追加パラメーターを次に示します。

- `Hbase.rs.cacheblocksonwrite` - HDI では既定で、この設定は **true** に設定されます。

- 後で行うために軽微な圧縮を延期できる設定。

- 読み取り要求および書き込み要求用に予約するキューの割合を調整するなどの試験的な設定。

## <a name="next-steps"></a>次のステップ

問題が解決しない場合は、次のいずれかのチャネルにアクセスして追加のサポートを受けてください。

- [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる。 これは、カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウントです。 Azure コミュニティを適切なリソース (回答、サポート、エキスパート) と結び付けます。

- さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 Microsoft Azure サブスクリプションには、サブスクリプション管理と課金サポートへのアクセスが含まれています。テクニカル サポートは、いずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)によって提供されます。