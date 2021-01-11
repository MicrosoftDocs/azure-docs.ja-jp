---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0587a179b98a410cdba46b7817d86567f275f25d
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826822"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="release-date-08092020"></a>リリース日: 2020 年 8 月 9 日

このリリースは、HDInsight 4.0 にのみ適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

## <a name="new-features"></a>新機能
### <a name="support-for-sparkcruise"></a>SparkCruise のサポート
SparkCruise は、Spark 用の自動計算再利用システムです。 これは、過去のクエリ ワークロードに基づいて具体化する共通の部分式を選択します。 SparkCruise は、これらの部分式をクエリ処理の一部として具体化します。計算の再利用は、バックグラウンドで自動的に適用されます。 Spark コードを変更しなくても、SparkCruise の機能を使用できます。
 
### <a name="support-hive-view-for-hdinsight-40"></a>HDInsight 4.0 での Hive ビューのサポート
Apache Ambari Hive ビューは、Web ブラウザーから Hive クエリを作成、最適化、および実行する際に役立つように設計されています。 Hive ビューは、このリリース以降の HDInsight 4.0 クラスターでネイティブにサポートされています。 既存のクラスターには適用されません。 組み込みの Hive ビューを取得するには、クラスターを削除して再作成する必要があります。
 
### <a name="support-tez-view-for-hdinsight-40"></a>HDInsight 4.0 での Tez ビューのサポート
Apache Tez ビューは、Hive Tez ジョブの実行を追跡およびデバッグする際に使用されます。 Tez ビューは、このリリース以降の HDInsight 4.0 でネイティブにサポートされています。 既存のクラスターには適用されません。 組み込みの Tez ビューを取得するには、クラスターを削除して再作成する必要があります。

## <a name="deprecation"></a>非推奨
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 3.6 Spark クラスターでの Spark 2.1 と 2.2 の非推奨化
2020 年 7 月 1 日以降、HDInsight 3.6 で Spark 2.1 と 2.2 を使用して新しい Spark クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断する可能性を回避するため、2020 年 6月 30 日までに HDInight 3.6 で Spark 2.3 に移行することを検討してください。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark クラスターでの Spark 2.3 の非推奨化
2020 年 7 月 1 日以降、HDInsight 4.0 で Spark 2.3 を使用して新しい Spark クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断する可能性を回避するため、2020 年 6月 30 日までに HDInight 4.0 で Spark 2.4 に移行することを検討してください。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka クラスターの Kafka 1.1 の廃止
2020 年 7 月 1 日以降、HDInsight 4.0 で Kafka 1.1 を使用して新しい Kafka クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断される可能性を回避するため、2020 年 6 月 30 日までに HDInsight 4.0 で Kafka 2.1 に移行することを検討してください。

## <a name="behavior-changes"></a>動作の変更
### <a name="ambari-stack-version-change"></a>Ambari スタック バージョンの変更
このリリース以降、Ambari のバージョンが 2.x.x.x から 4.1 に変更されます。 Ambari のバージョンは、Ambari の UI > [バージョン変更] で確認できます。

## <a name="upcoming-changes"></a>今後の変更
注意を払う必要があり、近く予定されている破壊的変更はありません。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

Hive の場合、以下の JIRA が移植されます。
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

HBase の場合、以下の JIRA が移植されます。
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、コンポーネントのバージョン変更はありません。 HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちらのドキュメント](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)を参照してください。

## <a name="known-issues"></a>既知の問題

Azure portal で、SSH 認証の種類の公開キーを使用して Azure HDInsight クラスターを作成するときにエラーが発生する問題が修正されました。 ユーザーが **[確認と作成]** をクリックすると、"SSH ユーザー名に含まれる 3 文字以上連続する文字列を使用することはできません" というエラーを受け取ります。 この問題は修正されましたが、CTRL + F5 キーを押してブラウザーのキャッシュを更新し、修正されたビューを読み込む必要がある場合があります。 この問題の回避策は、ARM テンプレートを使用してクラスターを作成することでした。 
