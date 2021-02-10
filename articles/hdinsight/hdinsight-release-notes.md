---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 88e2161cfddf95f7f250b8b76c067d045f1529da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092236"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近** の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

リリース ノートをサブスクライブする場合は、[この GitHub リポジトリ](https://github.com/hdinsight/release-notes/releases)のリリースをご覧ください。

## <a name="release-date-11182020"></a>リリース日: 2020 年 11 月 18 日

このリリースは HDInsight 3.6 と HDInsight 4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

## <a name="new-features"></a>新機能
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>保存時におけるカスタマー マネージド キー暗号化の自動キー ローテーション
このリリース以降、お客様はカスタマー マネージド キーの暗号化に、バージョンレスの Azure KeyValut 暗号化キー URL を使用できます。 HDInsight は、有効期限が切れたとき、または新しいバージョンに置き換えられたときに、自動的にキーをローテーションします。 詳細については[ここ](./disk-encryption.md)を参照してください。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Spark、Hadoop、および ML Services 用のさまざまな Zookeeper 仮想マシン サイズを選択する機能
これまで、HDInsight では、Spark、Hadoop、ML Services のクラスターの種類に対して Zookeeper ノード サイズのカスタマイズはサポートされていませんでした。 既定値は A2_v2 および A2 の仮想マシン サイズであり、無料で提供されます。 このリリース以降、お客様のシナリオに最も適した Zookeeper 仮想マシン サイズを選択できます。 A2_v2 および A2 以外の仮想マシン サイズの Zookeeper ノードには課金されます。 A2_v2 および A2 の仮想マシンは引き続き無料で提供されます。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 このリリース以降、サービスは徐々に [Azure 仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)に移行されます。 このプロセス全体に数か月かかる可能性があります。 リージョンとサブスクリプションが移行された後は、新しく作成された HDInsight クラスターは、お客様が操作することなく、仮想マシン スケール セット上で動作するようになります。 破壊的変更は想定されていません。

## <a name="deprecation"></a>非推奨
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>HDInsight 3.6 ML Services クラスターの非推奨
HDInsight 3.6 ML Services クラスターの種類は、2020 年 12 月 31 日でサポート終了となります。 2021 年 1 月 1 日以降は新しい 3.6 ML Services クラスターを作成できなくなります。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 HDInsight のバージョンとクラスターの種類に関するサポートの有効期限については、[こちら](./hdinsight-component-versioning.md#available-versions)で確認してください。

### <a name="disabled-vm-sizes"></a>無効な VM サイズ
2020 年 11 月 16 日以降、新規のお客様が HDInsight で standand_A8、standand_A9、standand_A10、および standand_A11 の VM サイズを使用してクラスターを作成することができなくなります。 過去 3 か月間、これらの VM サイズを使用していた既存のお客様は、影響を受けません。 2021 年 1 月 9 日以降は、すべてのお客様が HDInsight で standand_A8、standand_A9、standand_A10、および standand_A11 の VM サイズを使用してクラスターを作成することができなくなります。 既存のクラスターはそのまま実行されます。 システムやサポートが中断される可能性を回避するため、HDInsight 4.0 への移行を検討してください。

## <a name="behavior-changes"></a>動作の変更
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>スケーリング操作の前に NSG ルール チェックを追加する
HDInsight では、ネットワーク セキュリティ グループ (NSG) とユーザー定義ルート (UDR) のチェックがスケーリング操作で追加されました。 クラスターの作成に加えて、クラスターのスケーリングについても同じ検証が行われます。 この検証により、予測できないエラーを防ぐことができます。 検証が成功しなかった場合、スケーリングは失敗します。 NSG と UDR を正しく構成する方法の詳細については、「[HDInsight 管理 IP アドレス](./hdinsight-management-ip-addresses.md)」を参照してください。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。

### <a name="breaking-change-for-net-for-apache-spark-100"></a>.NET for Apache Spark 1.0.0 の破壊的変更
HDInsight では、次のリリースで .NET for Apache Spark の最初の公式なメジャー リリースが導入されます。 これにより、他の機能と併せて、Spark 2.4. x と Spark 3.0. x 用の完全な DataFrame API が提供されます。 このメジャー バージョンでは破壊的変更が行われます。コードとパイプラインの更新に必要な手順を理解するには、[こちらの移行ガイド](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)を参照してください。 [こちら](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight)をご覧ください。

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>クラスターの既定の VM サイズが Ev3 ファミリに変更される
次のリリース (1 月末) 以降、クラスターの既定の VM サイズが D ファミリから Ev3 ファミリに変更されます。 この変更は、ヘッド ノードとワーカー ノードに適用されます。 この変更を回避するには、使用する VM サイズを ARM テンプレートで指定します。

### <a name="default-cluster-version-will-be-changed-to-40"></a>既定のクラスター バージョンは 4.0 に変更されます
2021 年 2 月以降、HDInsight クラスターの既定のバージョンは 3.6 から 4.0 に変更されます。 使用可能なバージョンの詳細については、「[使用可能なバージョン](./hdinsight-component-versioning.md#available-versions)」を参照してください。 HDInsight 4.0 の新機能については、[こちら](./hdinsight-version-release.md)を参照してください

### <a name="os-version-upgrade"></a>OS バージョンのアップグレード
HDInsight では、OS バージョンが 16.04 から 18.04 にアップグレードされます。 アップグレードは、2021 年 4 月までに完了する予定です。

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 は 2021 年 6 月 30 日にサポート終了
HDInsight 3.6 のサポートが終了します。 2021 年 6 月 30 日以降は、お客様が新しい HDInsight 3.6 クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断される可能性を回避するため、HDInsight 4.0 への移行を検討してください。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、コンポーネントのバージョン変更はありません。 HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちらのドキュメント](./hdinsight-component-versioning.md)を参照してください。

## <a name="known-issues"></a>既知の問題
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>HDInsight クラスター VM が定期的に再起動しないようにする

2020 年 11月中旬から、HDInsight クラスター VM が定期的に再起動されることがあります。 これには次の原因が考えられます。

1.  クラスターで Clamav が有効になっています。 新しい azsec-clamav パッケージが大量のメモリを消費し、それによってノードの再起動がトリガーされます。 
2.  CRON ジョブは、Azure サービスで使用される証明機関 (CA) の一覧の変更を監視するように、日単位でスケジュールされます。 新しい CA 証明書が利用可能になると、スクリプトによってその証明書が JDK 信頼ストアに追加され、再起動がスケジュールされます。

HDInsight は、両方の問題に対して、実行中のすべてのクラスターに修正プログラムをデプロイし、パッチを適用しています。 修正プログラムをすぐに適用し、予期しない VM の再起動を回避するには、すべてのクラスター ノードで次のスクリプト アクションを永続的なスクリプト アクションとして実行します。 修正プログラムとパッチの適用が完了すると、HDInsight から別の通知が送信されます。
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```