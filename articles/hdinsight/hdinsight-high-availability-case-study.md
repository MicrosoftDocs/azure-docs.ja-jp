---
title: Azure HDInsight の高可用性ソリューション アーキテクチャのケース スタディ
description: この記事は、考えられる Azure HDInsight の高可用性ソリューション アーキテクチャの架空のケース スタディです。
keywords: Hadoop の高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6b995e2ab5ba663f6e33b009062859eb32928cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508593"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Azure HDInsight の高可用性ソリューション アーキテクチャのケース スタディ

Azure HDInsight のレプリケーション メカニズムを高可用性ソリューション アーキテクチャに統合できます。 この記事では、Contoso Retail の架空のケース スタディを使用して、考えられる高可用性ディザスター リカバリー方法、コストに関する考慮事項、および対応する設計について説明します。

高可用性ディザスター リカバリーの推奨事項には、多数の順列組み合わせが考えられます。 各オプションの長所と短所を熟慮した後で、これらのソリューションに到達しています。 この記事では、考えられる 1 つのソリューションのみについて説明します。

## <a name="customer-architecture"></a>顧客のアーキテクチャ

次の図は、Contoso Retail のプライマリ アーキテクチャを示しています。 このアーキテクチャは、ストリーミング ワークロード、バッチ ワークロード、サービス レイヤー、消費レイヤー、ストレージ レイヤー、およびバージョン管理で構成されています。

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Contoso Retail のアーキテクチャ":::

### <a name="streaming-workload"></a>ストリーミング ワークロード

デバイスとセンサーによって、メッセージング フレームワークを構成する HDInsight Kafka に送信されるデータが生成されます。 An HDInsight Spark コンシューマーによって、Kafka トピックからの読み取りが行われます。 Spark によって、受信メッセージが変換され、サービス レイヤーの HDInsight HBase クラスターに書き込まれます。

### <a name="batch-workload"></a>バッチ ワークロード

Hive と MapReduce 取り込みデータを実行している HDInsight Hadoop クラスターで、オンプレミスのトランザクション システムからデータが取り込まれます。 Hive と MapReduce によって変換された生データは、Azure Data Lake Storage Gen2 によってバックアップされるデータ レイクの論理パーティション上の Hive テーブルに格納されます。 Hive テーブルに格納されたデータは、Spark SQL でも使用可能であり、選別されたデータをサービスのために HBase に格納する前にバッチ変換が行われます。

### <a name="serving-layer"></a>サービス レイヤー

Apache Phoenix を使用する HDInsight HBase クラスターを使用して、Web アプリケーションと視覚化ダッシュボードにデータが提供されます。 内部レポート要件に対応するために、An HDInsight LLAP クラスターが使用されます。

### <a name="consumption-layer"></a>消費レイヤー

Azure API Apps と API Management レイヤーによって、パブリックに公開される Web ページがバックアップされます。 内部レポート要件は Power BI によって対応されます。

### <a name="storage-layer"></a>ストレージ レイヤー

エンタープライズ データ レイクとして、論理的にパーティション分割された Azure Data Lake Storage Gen2 が使用されます。 Azure SQL DB によって、HDInsight メタストアがバックアップされます。

### <a name="version-control-system"></a>バージョン管理システム

Azure Pipelines に統合されたバージョン管理システムは、Azure の外部でホストされます。

## <a name="customer-business-continuity-requirements"></a>顧客のビジネス継続性要件

災害が発生した場合に必要とする最小限のビジネス機能を決定することが重要です。

### <a name="contoso-retails-business-continuity-requirements"></a>Contoso Retail のビジネス継続性要件

* リージョンでの障害またはサービスの正常性の問題から保護される必要がある。
* お客様に 404 エラーが表示されないようにする必要がある。 パブリック コンテンツを常に提供する必要がある。 (RTO = 0)  
* ほぼ一年中、5 時間前の古いパブリック コンテンツを表示できる。 (RPO = 5 時間)
* ホリデー シーズン中は、パブリックに公開されているコンテンツを常に最新の状態にする必要がある。 (RPO = 0)
* ビジネス継続性にとって、社内レポート要件は重要ではないとみなされている。
* ビジネス継続性にかかるコストを最適化する。

## <a name="proposed-solution"></a>提案されるソリューション

次の図に、Contoso Retail の高可用性ディザスター リカバリー アーキテクチャを示します。

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso のソリューション":::

**Kafka** では、[アクティブ/パッシブ](hdinsight-business-continuity-architecture.md#apache-kafka) レプリケーションを使用して、プライマリ リージョンからセカンダリ リージョンに Kafka トピックがミラー化されます。 Kafka のレプリケーションに対する代替手段として、両方のリージョンに Kafka を生成することができます。

**Hive と Spark** では、平常時は [アクティブ プライマリ/オンデマンド セカンダリ](hdinsight-business-continuity-architecture.md#apache-spark) レプリケーション モデルが使用されます。 Hive レプリケーション プロセスが定期的に実行され、Hive Azure SQL メタストアと Hive ストレージ アカウントのレプリケーションが同時に実行されます。 ADF DistCP を使用して、Spark ストレージ アカウントが定期的にレプリケートされます。 これらのクラスターの一時的な性質は、コストを最適化するのに役立ちます。 RPO に到達するためにレプリケーションは 4 時間ごとにスケジュールされ、5 時間という要件に十分対応します。

**HBase** レプリケーションでは、平常時は [リーダー/フォロワー](hdinsight-business-continuity-architecture.md#apache-hbase) モデルを使用して、リージョンに関係なくデータが常に確実に提供され、RPO が非常に低くなります。

プライマリ リージョンでリージョン障害が発生した場合、ある程度古くなった 5 時間前の Web ページとバックエンドのコンテンツがセカンダリ リージョンから提供されます。 Azure サービス正常性ダッシュボードに、5 時間のリカバリ ETA が示されない場合、Contoso Retail では、セカンダリ リージョンに Hive および Spark 変換レイヤーを作成した後、すべてのアップストリーム データ ソースをセカンダリ リージョンに向けます。 セカンダリ リージョンを書き込み可能にすると、プライマリへのレプリケーションを伴うフェールバック プロセスが発生します。

ショッピング シーズンのピーク時は、セカンダリ パイプライン全体が常にアクティブになり、実行されます。 Kafka プロデューサーによって、両方のリージョンに対する生成が行われ、HBase レプリケーションはリーダー/フォロワーからリーダー/リーダーに変更され、パブリックに公開されているコンテンツが常に最新の状態になります。

ビジネス継続性にとって重要ではないため、内部レポート用のフェールオーバー ソリューションを設計する必要はありません。

## <a name="next-steps"></a>次のステップ

この記事で説明した項目の詳細については、次を参照してください。

* [Azure HDInsight のビジネス継続性](./hdinsight-business-continuity.md)
* [Azure HDInsight のビジネス継続性アーキテクチャ](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight における Apache Hive と HiveQL](./hadoop/hdinsight-use-hive.md)
