---
title: Apache Hadoop コンポーネントおよびバージョン - Azure HDInsight
description: Azure HDInsight 内の Apache Hadoop コンポーネントおよびバージョンについて説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 5c3586e9ba405cb5a7338b8f7f22858a238b9271
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717253"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Azure HDInsight で使用可能な Apache コンポーネントおよびバージョン

この記事では、Azure HDInsight の [Apache Hadoop](https://hadoop.apache.org/) 環境のコンポーネントおよびバージョンと、Enterprise セキュリティ パッケージについて説明します。 また、HDInsight で Hadoop コンポーネントのバージョンを確認する方法についても説明します。

## <a name="apache-components-available-with-different-hdinsight-versions"></a>HDInsight の各バージョンで使用できる Apache コンポーネント

Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。 2017 年 4 月 4 日時点の Azure HDInsight で使用される既定のクラスター バージョンは 3.6 でした。

HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。

> [!NOTE]
> HDInsight サービスの既定のバージョンは、予告なく変更される場合があります。 バージョンの依存関係がある場合は、Azure PowerShell や Azure クラシック CLI を含む .NET SDK を使用してクラスターを作成する際に HDInsight バージョンを指定してください。

| コンポーネント              | HDInsight 4.0 | HDInsight 3.6 (既定)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop と YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (ESP Interactive Query では 2.1.0) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0.5           | 0.4.                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4.0:Spark 2.4 と Kafka 2.1 は完全にサポートされています。 ただし、Spark 2.3 と Kafka 1.1 クラスターの種類には対応していません。 HDInsight 3.6:Spark 2.3 と Kafka 1.1 は完全にサポートされています。  

## <a name="check-for-current-apache-component-version-information"></a>現在の Apache コンポーネントのバージョン情報の確認

HDInsight クラスターのバージョンに関連付けられた Hadoop 環境コンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。 Hadoop コンポーネントを調べて、どのバージョンがクラスターに使用されているかを確認するには、Ambari REST API を使用します。 **GetComponentInformation** コマンドによって、サービス コンポーネントに関する情報を取得します。 詳細については、[Apache Ambari のドキュメント](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)を参照してください。

### <a name="release-notes"></a>リリース ノート

HDInsight の最新バージョンに関する追加のリリース ノートは、[HDInsight リリース ノート](hdinsight-release-notes.md)を参照してください。

## <a name="supported-hdinsight-versions"></a>サポートされる HDInsight のバージョン

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>HDInsight バージョンのサポートの有効期限と提供終了

**サポートの有効期限**は、特定の HDInsight バージョンに対して Microsoft によるサポートが提供されなくなることを意味します。 また、クラスターを作成するために Azure portal を介して使用することができなくなります。 これらのバージョンは、Azure CLI またはさまざまな SDK を使用して、引き続き作成することができます。

**提供終了**とは、ある HDInsight バージョンの既存のクラスターが引き続きそのまま実行されることを意味します。 このバージョンの新しいクラスターは、CLI や SDK など、どのような方法でも作成することはできません。 手動によるスケーリングや自動スケールなど、その他のコントロール プレーン機能は、バージョンの提供終了後に機能しなくなる場合があります。 提供が終了したバージョンでサポートを利用することはできません。

次の表は、HDInsight のバージョンの一覧を示しています。 サポート有効期限と提供終了日については、既知の場合、それらも記載されます。

### <a name="available-versions"></a>使用可能なバージョン

この表は、Azure portal と、PowerShell や .NET SDK などの他のデプロイ方法で使用可能な HDInsight のバージョンをまとめたものです。

| HDInsight のバージョン | VM の OS | リリース日 | サポート有効期限 | 提供終了日 | 高可用性 |  Azure portal での可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |はい |はい |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日      | \* 2021 年 6 月 31 日 |2021 年 6 月 31 日 |はい |はい |

\* 特定のクラスターの種類 HDInsight 3.6 のサポート期間を延長しています

| クラスターの種類                    | Framework のバージョン | 現在のサポート有効期限        | 新しいサポート有効期限 |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Spark             | 2.3               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Spark             | 2.2               | 2020 年 6 月 30 日に廃止          |                             |
| HDInsight 3.6 Spark             | 2.1               | 2020 年 6 月 30 日に廃止          |                             |
| HDInsight 3.6 Kafka             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Kafka             | 1.0               | 2020 年 6 月 30 日に廃止         |                             |
| HDInsight 3.6 HBase             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Interactive Query | 2.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Storm             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6  ML Services      | 9.3               | 2020 年 12 月 31 日                      | 2020 年 12 月 31 日                |

> [!NOTE]
> クラスター バージョンのサポートが期限切れになると、Azure Portal で使用できなくなります。 一部のケースにおいて、クラスター バージョンは、Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) コマンドの **Version** パラメーターと .NET SDK を使用することで、バージョンの提供終了日まで利用できる場合があります。

### <a name="retired-versions"></a>廃止されたバージョン

この表は、Azure portal で使用できない HDInsight のバージョンをまとめたものです。

| HDInsight のバージョン | HDP のバージョン | VM の OS | リリース日 | サポート有効期限 | 提供終了日 | 高可用性 |  Azure portal での可用性 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016 年 9 月 30 日 |2017 年 9 月 5 日 |2018 年 6 月 28 日 |はい |いいえ |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016 年 3 月 29 日 |2016 年 12 月 29 日 |2018 年 1 月 9 日 |はい |いいえ |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2018 年 7 月 31日 |はい |いいえ |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2017 年 7 月 31 日 |はい |いいえ |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS または Windows Server 2012 R2 |2015 年 2 月 18 日 |2016 年 3 月 1 日 |2017 年 4 月 1 日 |はい |いいえ |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014 年 6 月 24 日 |2015 年 5 月 18 日 |2016 年 6 月 30 日 |はい |いいえ |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014 年 2 月 11 日 |2014 年 9 月 17 日 |2015 年 6 月 30 日 |はい |いいえ |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013 年 10 月 28 日 |2014 年 5 月 12 日 |2015 年 5 月 31 日 |はい |いいえ |
| HDInsight 1.6 |HDP 1.1 | |2013 年 10 月 28 日 |2014 年 4 月 26 日 |2015 年 5 月 31 日 |いいえ |いいえ |

> [!NOTE]
> ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight バージョン 2.1 以降では既定でデプロイされています。 HDInsight バージョン 1.6 クラスターでは利用できません。

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight クラスター バージョンのサービス レベル アグリーメント

サービス レベル アグリーメントは、"_サポート ウィンドウ_" として定義されます。 サポート ウィンドウとは、HDInsight のバージョンが Microsoft カスタマー サービスおよびサポートによってサポートされる期間です。 バージョンの "_サポート有効期限_" が過ぎている場合、HDInsight クラスターはサポート ウィンドウ外となります。 HDInsight バージョン X のサポート有効期限は (新しい X+1 バージョンが利用可能になった後)、次の日付のうち、遅い方です。

- **数式 1:** HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
- **数式 2:** HDInsight クラスター バージョン X+1 が Azure portal で使用可能になった日付に 90 日を加える。

"_提供終了日_" とは、その日を過ぎると HDInsight でクラスター バージョンを作成できなくなる日付です。 2017 年 7 月 31 日以降は、提供終了日より後に HDInsight クラスターのサイズを変更できません。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>クラスターの既定のノード構成と仮想マシン サイズ

クラスター用に選択する仮想マシンの SKU の詳細については、[Azure HDInsight のクラスター構成の詳細](hdinsight-supported-node-configuration.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [HDInsight で Apache Hadoop、Spark、その他のクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows PC から HDInsight の Apache Hadoop で作業する](hdinsight-hadoop-windows-tools.md)
- [Azure HDInsight バージョンに対応する Hortonworks リリース ノート](./hortonworks-release-notes.md)
- [Enterprise セキュリティ パッケージ](./enterprise-security-package.md)
