---
title: Azure HDInsight IO キャッシュ (プレビュー) を使用した場合の Apache Spark のワークロード パフォーマンス
description: Azure HDInsight IO キャッシュについて、およびそれを使用して Apache Spark のパフォーマンスを向上させる方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: f3f171d4dfd3642dc71724afbe084c3fcbf8beaa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091067"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Azure HDInsight IO キャッシュを使用して Apache Spark のワークロードのパフォーマンスを改善する 

IO キャッシュは、Apache Spark のジョブのパフォーマンスを改善する、Azure HDInsight 用のデータ キャッシュ サービスです。 IO キャッシュは [Apache TEZ](https://tez.apache.org/) や [Apache Hive](https://hive.apache.org/) のワークロードでも機能し、[Apache Spark](https://spark.apache.org/) のクラスター上で実行できます。 IO キャッシュは RubiX と呼ばれるオープン ソースのキャッシュ コンポーネントを使用します。 RubiX はビッグ データ分析エンジンで使用されるローカル ディスク キャッシュで、クラウド ストレージ システムからデータにアクセスします。 RubiX は、キャッシュ目的でオペレーティング メモリを予約するのではなくソリッド ステート ドライブ (SSD) を使用するため、キャッシュ システム間で一意です。 IO キャッシュ サービスはクラスターの各ワーカー ノード上に RubiX メタデータ サーバーを起動して管理します。 また、RubiX キャッシュの透過的な使用のためにクラスターのすべてのサービスを構成します。

ほとんどの SSD の帯域幅は 1 秒につき 1 GB 以上です。 この帯域幅はオペレーティング システムのインメモリ ファイル キャッシュによって補完され、Apache Spark などのビッグ データ演算処理エンジンを読み込むのに十分な帯域幅を提供します。 オペレーティング メモリは、シャッフルなどのメモリに大きく依存するタスクを処理するために Apache Spark が利用できるように残されています。 オペレーティング メモリを排他的に使用できることにより、Apache Spark が最適なリソース使用量を達成できます。  

>[!Note]  
>現在、IO キャッシュは RubiX をキャッシュ コンポーネントとして使用しますが、将来のバージョンのサービスでは変わる可能性があります。 IO キャッシュのインターフェイスを使用し、RubiX の実装に直接依存することはしないでください。

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Azure HDInsight IO キャッシュの利点

IO キャッシュを使用すると、Azure Blob Storage からデータを読み取るジョブのパフォーマンスを改善します。

IO キャッシュを使用すると、Spark のジョブに変更を加えなくてもパフォーマンスの改善が見られます。 IO キャッシュを無効にすると、Spark コード `spark.read.load('wasbs:///myfolder/data.parquet').count()` は Azure Blob Storage からデータをリモートで読み取ります。 IO キャッシュをアクティブにすると、IO キャッシュによってそのコードの同じ行で キャッシュされた読み取りが行われます。 続く読み取りでは、データが SSD からローカルで読み取られます。 HDInsight クラスター上のワーカー ノードにはローカルで接続された専用の SSD ドライブが備わっています。 HDInsight IO キャッシュはこれらのローカル SSD を使用してキャッシュを行います。これにより、最小レベルの待ち時間を実現し、帯域幅を最大化します。

## <a name="getting-started"></a>使用の開始

Azure HDInsight IO キャッシュはプレビューでは既定で非アクティブ化されます。 IO キャッシュは Azure HDInsight 3.6 以上の Spark クラスターで利用可能で、Apache Spark 2.3 を実行します。  IO キャッシュをアクティブにするには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) で HDInsight クラスターを選択します。

1. **[概要]** ページ (クラスターを選択すると既定で開かれるページ) の **[クラスター ダッシュボード]** で **[Ambari ホーム]** を選択します。

1. 左側の **[IO キャッシュ]** を選択します。

1. **[アクション]** を選択して **[アクティブ化]** を選択します。

    ![Ambari で IO キャッシュ サービスを有効にする](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Ambari で IO キャッシュ サービスを有効にする")

1. 影響を受けるクラスター上のすべてのサービスの再起動を確認します。

>[!NOTE]  
> 進行状況バーにアクティブ化と表示されていても、IO キャッシュは実際には 影響を受ける他のサービスを再起動するまで有効になりません。

## <a name="troubleshooting"></a>トラブルシューティング
  
IO キャッシュを有効にした後に Spark のジョブを実行するとディスク領域エラーが発生することがあります。 これらのエラーは、Spark がシャッフル操作中にデータの格納にもローカル ディスク ストレージを使用することが原因で発生します。 IO キャッシュが有効になり、Spark のストレージ領域が減ると、Spark で SSD 領域が不足する場合があります。 IO キャッシュで使用されるディスク領域は既定で SSD の合計領域の半分になります。 IO キャッシュのディスク領域使用量は Ambari で構成できます。 ディスク領域のエラーが発生した場合は、IO キャッシュで使用されるSSD 領域を減らし、サービスを再起動してください。 IO キャッシュに設定されている領域を変更するには、次の手順を実行します。

1. Apache Ambari で、左側にある **[HDFS]** サービスを選択します。

1. **[Configs]\(構成\)** タブを選択し、 **[Advanced]\(詳細\)** タブを選択します。

    ![HDFS の詳細構成を編集する](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "HDFS の詳細構成を編集する")

1. 下にスクロールし、 **[Custom core-site]\(カスタム コアサイト\)** 領域を展開します。

1. プロパティ **hadoop.cache.data.fullness.percentage** を探します。

1. ボックスの値を変更します。

    ![IO キャッシュの最大割合を編集する](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "IO キャッシュの最大割合を編集する")

1. 右上の **[Save]\(保存\)** を選択します。

1. **[Restart]\(再起動\)**  >  **[Restart All Affected]\(影響を受けるすべてを再起動\)** を選択します。

    ![影響を受けるすべてを再起動する](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "影響を受けるすべてを再起動する")

1. **[Confirm Restart All]\(すべて再起動\)** を選択します。

機能しない場合は、IO キャッシュを無効にします。

## <a name="next-steps"></a>次の手順

- このブログ投稿のパフォーマンス ベンチマークなど、IO キャッシュに関する詳細をお読みください。[Apache Spark ジョブが HDInsight IO キャッシュで最大 9 倍のスピードアップ](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
