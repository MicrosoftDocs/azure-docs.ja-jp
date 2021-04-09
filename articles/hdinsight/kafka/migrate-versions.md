---
title: Apache Kafka のワークロードを Azure HDInsight 4.0 に移行する
description: HDInsight 3.6 上の Apache Kafka のワークロードを HDInsight 4.0 に移行する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: e15ebb13aee0e5dd814688ae77edaded667d54ac
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864127"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Apache Kafka のワークロードを Azure HDInsight 4.0 に移行する

Azure HDInsight 4.0 では、パフォーマンス、接続性、およびセキュリティが大幅に向上した最新のオープン ソース コンポーネントが提供されています。 このドキュメントでは、HDInsight 3.6 上の Apache Kafka のワークロードを HDInsight 4.0 に移行する方法について説明します。 ワークロードを HDInsight 4.0 に移行すると、HDInsight 3.6 では提供されていない新機能を多数使用できます。

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka の移行パス

HDInsight 3.6 では、次の 2 つのバージョンの Kafka がサポートされています:1.0.0 および 1.1.0。 HDInsight 4.0 では、バージョン 1.1.0 と 2.1.0 がサポートされています。 実行する予定の Kafka のバージョンと HDInsight のバージョンに応じて、複数の移行パスがサポートされています。 これらのパスについて、以下で説明し、その後に図を示します。

* **最新バージョンで Kafka と HDInsight の両方を実行する (推奨)** :HDInsight 3.6 と Kafka 1.0.0 または 1.1.0 アプリケーションを、Kafka 2.1.0 を使用する HDInsight 4.0 に移行します (下のパス D および E)。
* **HDInsight は最新バージョンで実行するが、Kafka はより新しいバージョンでのみ実行する**:HDInsight 3.6 と Kafka 1.0.0 アプリケーションを、Kafka 1.1.0 を使用する HDInsight 4.0 に移行します (下のパス B)。
* **HDInsight は最新バージョンで実行し、Kafka のバージョンは維持する**:HDInsight 3.6 と Kafka 1.1.0 アプリケーションを、Kafka 1.1.0 を使用する HDInsight 4.0 に移行します (下のパス C)。
* **Kafka はより新しいバージョンで実行し、HDInsight のバージョンは維持する**:Kafka 1.0.0 アプリケーションを 1.1.0 に移行し、HDInsight 3.6 にとどまります (下のパス A)。 このオプションでは、新しいクラスターをデプロイする必要があることに注意してください。 既存クラスターでの Kafka バージョンのアップグレードはサポートされていません。 必要なバージョンのクラスター作成後に、新しいクラスターを使用するように Kafka クライアントを移行します。

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png" alt-text="3\.6 での Apache Kafka のアップグレード パス" border="false":::

## <a name="apache-kafka-versions"></a>Apache Kafka のバージョン

### <a name="kafka-110"></a>Kafka 1.1.0
  
Kafka 1.0.0 から 1.1.0 に移行する場合は、以下の新機能を利用できます。

* Kafka コントローラーの改善により、制御されたシャットダウンが高速化されているため、より迅速に、ブローカーを再起動して問題から復旧できます。 
* [FetchRequests ロジック](https://issues.apache.org/jira/browse/KAFKA-6254)の改善により、クラスター上でより多くのパーティション (したがってより多くのトピック) を使用できるようになっています。 
* Kafka Connect では、トピックに対して[レコード ヘッダー](https://issues.apache.org/jira/browse/KAFKA-5142)と[正規表現](https://issues.apache.org/jira/browse/KAFKA-3073)がサポートされます。 

更新項目の完全な一覧については、[Apache Kafka 1.1 のリリース ノート](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)を参照してください。

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Kafka 2.1 に移行する場合は、以下の機能を利用できます。

* レプリケーション プロトコルの改善により、ブローカーの回復性が向上しています。
* KafkaAdminClient API の新機能。
* 構成可能なクォータ管理。
* Zstandard 圧縮のサポート。

更新項目の完全な一覧については、[Apache Kafka 2.0 のリリース ノート](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html)と [Apache Kafka 2.1 のリリース ノート](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)を参照してください。

## <a name="kafka-client-compatibility"></a>Kafka クライアントの互換性

新しい Kafka ブローカーでは古いクライアントがサポートされています。 [KIP-35 - Retrieving protocol version](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) では、Kafka ブローカーの機能を動的に決定するためのメカニズムが導入されました。また、[KIP-97:Improved Kafka Client RPC Compatibility Policy](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) では、Java クライアントについての新しい互換性ポリシーと保証が導入されました。 以前、Kafka クライアントは、同一バージョンまたはより新しいバージョンのブローカーとやり取りする必要がありました。 今では、新しいバージョンの Java クライアントや、`librdkafka` などの KIP-35 をサポートするその他のクライアントは、古い要求の種類にフォールバックすることや、機能を使用できない場合には適切なエラーをスローしたりすることが可能です。

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png" alt-text="Kafka クライアントの互換性をアップグレードする" border="false":::

クライアントが古いブローカーをサポートしていのではないことに注意してください。  詳細については、「[Compatibility Matrix](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)」 (互換性マトリックス) を参照してください。

## <a name="general-migration-process"></a>一般的な移行プロセス

次の移行ガイダンスでは、1 つの仮想ネットワーク内に、HDInsight 3.6 にデプロイされた Apache Kafka 1.0.0 または 1.1.0 クラスターがあると想定しています。 既存のブローカーにはトピックがいくつかあり、ブローカーはプロデューサーとコンシューマーによって積極的に使用されています。

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png" alt-text="想定された現在の Kafka の環境" border="false":::

移行を完了するには、以下の手順を実行します。

1. **新しい HDInsight 4.0 クラスターとテスト用のクライアントをデプロイします。** 新しい HDInsight 4.0 Kafka クラスターをデプロイします。 複数の Kafka クラスター バージョンを選択できる場合は、最新バージョンを選択することをお勧めします。 デプロイ後、必要に応じていくつかのパラメーターを設定し、既存の環境と同じ名前を使用してトピックを作成します。 また、必要に応じて TLS と Bring Your Own Key (BYOK) 暗号化を設定します。 次に、新しいクラスターでそれが正しく機能するかどうかを確認します。

    :::image type="content" source="./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png" alt-text="新しい HDInsight 4.0 クラスターをデプロイする" border="false":::

1. **プロデューサー アプリケーション用のクラスターを切り替えて、すべてのキュー データが現在のコンシューマーによって使用されるまで待機します。** 新しい HDInsight 4.0 Kafka クラスターの準備ができたら、既存のプロデューサーの対象を新しいクラスターに切り替えます。 既存のコンシューマー アプリが既存クラスターのすべてのデータを使用するまで、そのままにしておきます。

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-producer-app.png" alt-text="プロデューサー アプリ用のクラスターを切り替える" border="false":::

1. **コンシューマー アプリケーションに関するクラスターを切り替えます。** 既存のコンシューマー アプリケーションが、既存クラスターのすべてのデータを使い終えたことを確認したら、接続を新しいクラスターに切り替えます。

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png" alt-text="コンシューマー アプリに関するクラスターを切り替える" border="false":::

1. **必要に応じて、古いクラスターを削除し、アプリケーションをテストします。** 切り替えが完了して正常に動作しているようになったら、必要に応じて、古い HDInsight 3.6 Kafka クラスターと、テストで使用したプロデューサーおよびコンシューマーを削除します。

## <a name="next-steps"></a>次のステップ

* [Apache Kafka HDInsight クラスターのパフォーマンスの最適化](apache-kafka-performance-tuning.md)
* [クイック スタート: Azure portal を使用して Azure HDInsight 内に Apache Kafka クラスターを作成する](apache-kafka-get-started.md)
