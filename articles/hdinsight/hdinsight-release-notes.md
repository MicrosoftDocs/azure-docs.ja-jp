---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 5d9ca8d0df3eb0186add5c40765c87a4409a5660
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926424"
---
# <a name="release-notes"></a>リリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープンソースの Apache Hadoop および Apache Spark の分析を行う、エンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="release-date-11072019"></a>リリース日: 2019/07/11

このリリースは、HDInsight 3.6 と4.0 の両方に適用されます。

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン管理に関する記事](hdinsight-component-versioning.md)に関するページを参照してください。


## <a name="new-features"></a>新機能

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight Identity Broker (HIB) (プレビュー)

HDInsight Identity Broker (HIB) を使用すると、ユーザーは多要素認証 (MFA) を使用して Apache Ambari にサインインし、Azure Active Directory Domain Services (AAD) でパスワードハッシュを必要とせずに必要な Kerberos チケットを取得できます。 現在、HIB は ARM テンプレートを使用してデプロイされたクラスターでのみ使用できます。

### <a name="kafka-rest-api-proxy-preview"></a>Kafka Rest API プロキシ (プレビュー)

Kafka Rest API プロキシでは、セキュリティで保護された AAD 認証と OAuth プロトコルを使用して、Kafka クラスターで高可用性 REST プロキシをワンクリックでデプロイできます。 

### <a name="auto-scale"></a>自動スケール

Apache Spark および Hadoop クラスターの種類向けに、すべてのリージョンで Azure HDInsight の自動スケールの一般提供が開始されました。 この機能により、よりコスト効率に優れた生産性の高い方法で、ビッグ データ分析のワークロードを管理できるようになります。 HDInsight クラスターの使用を最適化し、必要な分だけ支払うことができるようになりました。

要件に応じて、負荷ベースまたはスケジュール ベースの自動スケーリングを選択できます。 負荷ベースの自動スケールでは、現在のリソースのニーズに基づいてクラスター サイズをスケールアップ/スケールダウンできます。スケジュール ベースの自動スケールでは、事前定義されたスケジュールに基づいてクラスター サイズを変更できます。 

自動スケールでの HBase および LLAP ワークロードのサポートもパブリック プレビュー段階です。 詳細については、「[Azure HDInsight クラスターを自動的にスケール調整する](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters)」を参照してください。

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>HDInsight の Apache HBase 用書き込みアクセラレータ 

高速書き込みでは、Azure Premium SSD マネージド ディスクが使用されて、Apache HBase の先書きログ (WAL) のパフォーマンスが向上します。 詳細については、「[Azure HDInsight の Apache HBase 用書き込みアクセラレータ](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes)」を参照してください。

### <a name="custom-ambari-db"></a>カスタム Ambari DB

HDInsight では、お客様が独自の SQL DB for Ambari を使用できるようにするための新しい容量が提供されるようになりました。 お客様は、適切な SQL DB for Ambari を選択し、独自のビジネス拡張要件に基づいて簡単にアップグレードすることができます。 デプロイには、Azure Resource Manager テンプレートを使用します。 詳細については、「[カスタム Ambari DB を使用して HDInsight クラスターを設定する](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db)」を参照してください。

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>HDInsight で F シリーズの仮想マシンを使用できるようになりました

F シリーズの仮想マシン (VM) は、低負荷の処理要件を持つ HDInsight の使用を開始する場合に適しています。 F シリーズは時間あたりの料金が抑えられており、vCPU あたりの Azure コンピューティング ユニット (ACU) に基づく Azure ポートフォリオにおいて、最もコスト パフォーマンスに優れています。 詳細については、「[Azure HDInsight クラスターの適切な VM サイズの選択](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size)」を参照してください。

## <a name="deprecation"></a>非推奨

### <a name="g-series-virtual-machine-deprecation"></a>G シリーズの仮想マシンの非推奨
このリリースから、G シリーズの VM は HDInsight で提供されなくなりました。

### <a name="dv1-virtual-machine-deprecation"></a>Dv1 仮想マシンの非推奨
このリリースから、HDInsight での Dv1 VM の使用は非推奨とされます。 Dv1 のお客様の要求は、Dv2 で自動的に提供されます。 Dv1 VM と Dv2 VM の価格に違いはありません。

## <a name="behavior-changes"></a>動作の変更

### <a name="cluster-managed-disk-size-change"></a>クラスター マネージド ディスクのサイズ変更
HDInsight では、クラスターにマネージド ディスク領域が用意されています。 このリリースから、新しく作成されたクラスター内の各ノードのマネージド ディスクのサイズが 128 GB に変更されます。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 12 月以降、HDInsight では代わりに Azure 仮想マシン スケール セットが使用されます。 [Azure 仮想マシン スケール セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)についてご確認ください。

### <a name="hbase-20-to-21"></a>HBase 2.0 から 2.1
今後の HDInsight 4.0 リリースでは、HBase のバージョンがバージョン 2.0 から 2.1 にアップグレードされます。

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>ESP クラスターにおける A シリーズの仮想マシンの非推奨
A シリーズの VM では、CPU とメモリの容量が比較的少ないため、ESP クラスターの問題が発生する可能性があります。 今後のリリースでは、新しい ESP クラスターを作成するために A シリーズの VM が非推奨とされます。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、コンポーネントのバージョンの変更はありません。 HDInsight 4.0 および HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちら](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)を参照してください。
