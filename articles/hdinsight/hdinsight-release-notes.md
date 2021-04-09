---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 3ea5168641f233f2e9580612a2f245fd8fef860f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699353"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近** の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

リリース ノートをサブスクライブする場合は、[この GitHub リポジトリ](https://github.com/hdinsight/release-notes/releases)のリリースをご覧ください。

## <a name="release-date-02052021"></a>リリース日: 2021 年 2 月 5 日

このリリースは HDInsight 3.6 と HDInsight 4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

## <a name="new-features"></a>新機能
### <a name="dav4-series-support"></a>Dav4 シリーズのサポート
HDInsight のこのリリースでは、Dav4 シリーズのサポートが追加されました。 [Dav4 シリーズの詳細についてはこちら](/azure/virtual-machines/dav4-dasv4-series)を参照してください。

### <a name="kafka-rest-proxy-ga"></a>Kafka REST プロキシ GA 
Kafka REST プロキシを使用すると、HTTPS 経由の REST API を使用して Kafka クラスターを操作することができます。 Kafka Rest プロキシは、このリリース以降、一般提供となります。 [Kafka REST プロキシの詳細についてはこちら](/azure/hdinsight/kafka/rest-proxy)を参照してください。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 このサービスは [Azure 仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)に段階的に移行しています。 このプロセス全体に数か月かかる可能性があります。 リージョンとサブスクリプションが移行された後は、新しく作成された HDInsight クラスターは、お客様が操作することなく、仮想マシン スケール セット上で動作するようになります。 破壊的変更は想定されていません。

## <a name="deprecation"></a>非推奨
### <a name="disabled-vm-sizes"></a>無効な VM サイズ
2021 年 1 月 9 日以降は、すべてのお客様が HDInsight で standand_A8、standand_A9、standand_A10、および standand_A11 の VM サイズを使用してクラスターを作成することができなくなります。 既存のクラスターはそのまま実行されます。 システムやサポートが中断される可能性を回避するため、HDInsight 4.0 への移行を検討してください。

## <a name="behavior-changes"></a>動作の変更
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>既定のクラスター VM サイズが Ev3 シリーズに変更 
既定のクラスター VM のサイズは、D シリーズから Ev3 シリーズに変更されます。 この変更は、ヘッド ノードとワーカー ノードに適用されます。 この変更がテスト済みのワークフローに影響を与えることを回避するには、使用する VM サイズを ARM テンプレートで指定します。

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットで実行されているクラスターでのネットワーク インターフェイス リソースの非表示
HDInsight は Azure 仮想マシン スケール セットに段階的に移行しています。 仮想マシンのネットワーク インターフェイスは、Azure 仮想マシン スケール セットを使用するクラスターの顧客に対しては表示されなくなりました。


### <a name="breaking-change-for-net-for-apache-spark-100"></a>.NET for Apache Spark 1.0.0 の破壊的変更
最新リリースでは、HDInsight では、 [".NET for Apache Spark"](https://github.com/dotnet/spark) ライブラリの最初の公式バージョン v1.0.0 が導入されています。 これにより、多くの[他の機能](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)と併せて、Spark 2.4.x と Spark 3.0.x 用の完全な DataFrame API が提供されます。 このメジャー バージョンでは破壊的変更が行われます。コードとパイプラインの更新に必要な手順を理解するには、[.NET for Apache Spark 移行ガイド](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)を参照してください。 詳細については、この [Azure HDInsight 上の .NET for Apache Spark v1.0 ガイド](/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight)を参照してください。


## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。

### <a name="default-cluster-version-will-be-changed-to-40"></a>既定のクラスター バージョンは 4.0 に変更されます
2021 年 2 月以降、HDInsight クラスターの既定のバージョンは 3.6 から 4.0 に変更されます。 使用可能なバージョンの詳細については、「[使用可能なバージョン](./hdinsight-component-versioning.md)」を参照してください。 [HDInsight 4.0](./hdinsight-version-release.md) の新機能については、こちらを参照してください。

### <a name="os-version-upgrade"></a>OS バージョンのアップグレード
HDInsight では、OS バージョンが Ubuntu 16.04 から 18.04 にアップグレードされます。 アップグレードは、2021 年 4 月までに完了する予定です。

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 は 2021 年 6 月 30 日にサポート終了
HDInsight 3.6 のサポートが終了します。 2021 年 6 月 30 日以降は、お客様が新しい HDInsight 3.6 クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断される可能性を回避するため、HDInsight 4.0 への移行を検討してください。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、コンポーネントのバージョン変更はありません。 HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちらのドキュメント](./hdinsight-component-versioning.md)を参照してください。
