---
title: コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド
description: コピー アクティビティを使用する場合に、Azure Data Factory でのデータ移動のパフォーマンスに影響する主な要因について説明します。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: cba248d3f254c9bb97c66ff7a3d39275b4b912c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616079"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド

> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [現在のバージョン](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ レイクまたはエンタープライズ データ ウェアハウス (EDW) から Azure への大規模なデータ移行を実行することが必要な場合があります。 また、ビッグ データ分析用に各種ソースから Azure に大量のデータを取り込むことが必要な場合があります。 いずれの場合も、最適なパフォーマンスとスケーラビリティを実現することが不可欠です。

Azure Data Factory (ADF) には、データを取り込むメカニズムが備わっています。 ADF には次の利点があります。

* 大量のデータを処理する
* パフォーマンスが高い
* コスト効率に優れている

これらの利点により、ADF は、パフォーマンスの高いスケーラブルなデータ インジェスト パイプラインを構築したいデータ エンジニアに最適です。

この記事を読むと、次の質問に回答できるようになります。

* データ移行シナリオとデータ インジェスト シナリオで ADF のコピー アクティビティを使用すると、どのレベルのパフォーマンスとスケーラビリティを実現できますか。
* ADF のコピー アクティビティのパフォーマンスを調整するには、どのような手順を実行する必要がありますか。
* 1 回のコピー アクティビティの実行でパフォーマンスを最適化するために、どのような ADF のパフォーマンス最適化のヒントを利用できますか。
* コピーのパフォーマンスを最適化するときに考慮する ADF 以外の要因には何がありますか。

> [!NOTE]
> コピー アクティビティ全般に慣れていない場合は、この記事を読む前に、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF を使用して実現可能なコピー パフォーマンスとスケーラビリティ

ADF は、さまざまなレベルで並列処理を可能にするサーバーレス アーキテクチャを提供します。

このアーキテクチャを使用すると、お使いの環境のデータ移動スループットを最大化するパイプラインを開発できます。 これらのパイプラインは、次のリソースを完全に利用します。

* ソースとコピー先のデータ ストア間のネットワーク帯域幅
* ソースとコピー先のデータストアの 1 秒あたりの入力/出力操作数 (IOPS) と帯域幅

この完全な利用は、次のリソースで使用可能な最小スループットを測定することで、全体のスループットを推定できることを意味します。

* ソース データ ストア
* コピー先データ ストア
* ソースとコピー先のデータストア間のネットワーク帯域幅

次の表は、コピー時間を計算したものです。 時間は、データ サイズとお使いの環境のネットワーク/データ ストアの帯域幅制限に基づいています。

&nbsp;

| データ サイズ/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分    | 1.4 分   | 0.3 分   | 0.1 分  | 0.03 分 | 0.01 分 | 0.0 分   |
| **10 GB**                   | 27.3 分   | 13.7 分  | 2.7 分   | 1.3 分  | 0.3 分  | 0.1 分  | 0.03 分  |
| **100 GB**                  | 4.6 時間    | 2.3 時間   | 0.5 時間   | 0.2 時間  | 0.05 時間 | 0.02 時間 | 0.0 時間   |
| **1 TB (テラバイト)**                    | 46.6 時間   | 23.3 時間  | 4.7 時間   | 2.3 時間  | 0.5 時間  | 0.2 時間  | 0.05 時間  |
| **10 TB**                   | 19.4 日  | 9.7 日  | 1.9 日  | 0.9 日 | 0.2 日 | 0.1 日 | 0.02 日 |
| **100 TB**                  | 194.2 日 | 97.1 日 | 19.4 日 | 9.7 日 | 1.9 日 | 1 日    | 0.2 日  |
| **1 PB**                    | 64.7 か月    | 32.4 か月   | 6.5 か月    | 3.2 か月   | 0.6 か月   | 0.3 か月   | 0.06 か月   |
| **10 PB**                   | 647.3 か月   | 323.6 か月  | 64.7 か月   | 31.6 か月  | 6.5 か月   | 3.2 か月   | 0.6 か月    |
| | |  | | |  | | |

ADF コピーはさまざまなレベルでスケーラブルです。

![ADF のコピー スケールのしくみ](media/copy-activity-performance/adf-copy-scalability.png)

* ADF 制御フローでは、複数のコピー アクティビティを並列して開始できます。たとえば、[For Each ループ](control-flow-for-each-activity.md)を使用します。

* 1 回のコピー アクティビティで、スケーラブルなコンピューティング リソースを利用できます。
  * Azure 統合ランタイム (IR) を使用すると、各コピー アクティビティに対して[最大 256 データ統合単位 (DIU)](#data-integration-units) をサーバーレス方式で指定できます。
  * セルフホステッド IR を使用する場合は、次のいずれかの方法を使用できます。
    * マシンを手動でスケールアップします。
    * 複数のマシン ([最大 4 ノード](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) にスケールアウトします。すると、1 回のコピー アクティビティによって、そのファイル セットがすべてのノードにわたってパーティション分割されます。

* 1 回のコピー アクティビティで、複数のスレッドを使用したデータ ストアの読み取りと書き込みが[並列](#parallel-copy)で行われます。

## <a name="performance-tuning-steps"></a>パフォーマンス チューニングの手順

コピー アクティビティを伴う Azure Data Factory サービスのパフォーマンスをチューニングするには、次の手順を実行します。

1. **テスト データセットを選択し、ベースラインを確立します。**

    開発中は、代表的なデータ サンプルに対してコピー アクティビティを使用して、パイプラインをテストします。 選択するデータセットは、次の属性に従って一般的なデータ パターンを表す必要があります。

    * フォルダー構造
    * ファイル パターン
    * データ スキーマ

    また、データセットは、コピーのパフォーマンス評価に十分な大きさである必要があります。 十分なサイズにすると、コピー アクティビティの完了に少なくとも 10 分かかります。 [コピー アクティビティの監視](copy-activity-monitoring.md)の後に、実行の詳細とパフォーマンス特性を収集します。

2. **1 回のコピー アクティビティのパフォーマンスを最大化する方法**:

    まず、1 回のコピー アクティビティの使用におけるパフォーマンスを最大化することをお勧めします。

    * **コピー アクティビティが _Azure_ 統合ランタイムで実行される場合:**

        [データ統合単位 (DIU)](#data-integration-units) と[並列コピー](#parallel-copy)の設定の既定値から始めます。

    * **コピー アクティビティが "_セルフホステッド_" 統合ランタイムで実行される場合:**

        専用のマシンを使用して IR をホストすることをお勧めします。 このマシンは、データ ストアをホストするサーバーとは別にする必要があります。 [並列コピー](#parallel-copy)設定の既定値とセルフホステッド IR 用の単一のノードの使用から始めます。

    パフォーマンス テストの実行を行います。 達成されたパフォーマンスをメモしておきます。 使用した実際の値 (DIU や 並列コピーなど) を含めます。 実行結果と使用されたパフォーマンス設定を収集する方法については、[コピー アクティビティの監視](copy-activity-monitoring.md)に関する記事を参照してください。 ボトルネックを特定して解決するために、[コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)を行う方法を学習します。

    トラブルシューティングとチューニング ガイダンスに従って、追加のパフォーマンス テストの実行を繰り返します。 1 回のコピー アクティビティの実行でスループットの向上を実現できなければ、複数のコピーを同時に実行することで合計スループットを最大化できるかどうか検討してください。 このオプションについては、次の番号付きの箇条書きで説明します。

3. **複数のコピーを同時に実行することで合計スループットを最大化する方法:**

    ここまでで、1 回のコピー アクティビティのパフォーマンスを最大化しました。 お使いの環境のスループットの上限にまだ達していない場合は、複数のコピー アクティビティを並行して実行できます。 ADF 制御フロー コンストラクトを使用することで、並列で実行できます。 このようなコンストラクトの 1 つは、[For Each ループ](control-flow-for-each-activity.md)です。 詳細については、ソリューション テンプレートに関する次の記事を参照してください。

    * [複数のコンテナーからのファイルのコピー](solution-template-copy-files-multiple-containers.md)
    * [Amazon S3 から ADLS Gen2 にデータを移行する](solution-template-migration-s3-azure.md)
    * [制御テーブルを使用した一括コピー](solution-template-bulk-copy-with-control-table.md)

4. **構成をデータセット全体まで拡張します。**

    実行の結果とパフォーマンスに問題がなければ、データセット全体を網羅するよう、定義とパイプラインを拡張できます。

## <a name="troubleshoot-copy-activity-performance"></a>コピー アクティビティのパフォーマンスのトラブルシューティング

[パフォーマンス チューニングの手順](#performance-tuning-steps) に従って、シナリオのパフォーマンステストを計画および実施します。 また、「[コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)」で、Azure Data Factory での各回のコピー アクティビティの実行のパフォーマンスに関する問題をトラブルシューティングする方法を確認します。

## <a name="copy-performance-optimization-features"></a>コピー パフォーマンス最適化機能

Azure Data Factory には、次のパフォーマンス最適化機能があります。

* [データ統合単位](#data-integration-units)
* [セルフホステッド統合ランタイムのスケーラビリティ](#self-hosted-integration-runtime-scalability)
* [並列コピー](#parallel-copy)
* [ステージング コピー](#staged-copy)

### <a name="data-integration-units"></a>データ統合単位

データ統合単位 (DIU) は、Azure Data Factory で 1 つの単位の能力を表す尺度です。 能力は、CPU、メモリ、およびネットワーク リソース割り当てを組み合わせたものです。 DIU は [Azure 統合ランタイム](concepts-integration-runtime.md#azure-integration-runtime)にのみ適用されます。 DIU は[セルフホステッド統合ランタイム](concepts-integration-runtime.md#self-hosted-integration-runtime)には適用されません。 [こちら](copy-activity-performance-features.md#data-integration-units)を参照してください。

### <a name="self-hosted-integration-runtime-scalability"></a>セルフホステッド統合ランタイムのスケーラビリティ

増加する同時実行ワークロードをホストすることもできます。 または、現在のワークロード レベルでパフォーマンスを向上させることもできます。 次の方法で、処理のスケーリングを強化できます。

* 1 つのノードで実行できる [同時実行ジョブ](create-self-hosted-integration-runtime.md#scale-up)の数を増やすことで、セルフホステッド IR をスケール "_アップ_" できます。  
スケールアップは、ノードのプロセッサとメモリが完全に使用されていない場合にのみ機能します。
* ノード (マシン) をさらに追加することで、セルフホステッド IR をスケール "_アウト_" できます。

詳細については、次を参照してください。

* [コピー アクティビティ パフォーマンス最適化機能:セルフホステッド統合ランタイムのスケーラビリティ](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [セルフホステッド統合ランタイムを作成して構成する:スケールに関する考慮事項](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>並列コピー

`parallelCopies` プロパティを設定して、コピー アクティビティで使用する並列処理を指定できます。 このプロパティは、コピー アクティビティ内のスレッドの最大数と考えてください。 スレッドは並行して動作します。 スレッドは、ソースから読み取るか、シンク データ ストアに書き込みます。 [詳細については、こちらを参照してください](copy-activity-performance-features.md#parallel-copy)。

### <a name="staged-copy"></a>ステージング コピー

データ コピー操作では、データを "_直接_" シンク データ ストアに送信できます。 または、BLOB ストレージを "_中間ステージング_" ストアとして使用することもできます。 [詳細については、こちらを参照してください](copy-activity-performance-features.md#staged-copy)。

## <a name="next-steps"></a>次のステップ

コピー アクティビティの他の記事を参照してください。

* [コピー アクティビティの概要](copy-activity-overview.md)
* [コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)
* [コピー アクティビティ パフォーマンス最適化機能](copy-activity-performance-features.md)
* [Azure Data Factory を使用してデータ レイクまたはデータ ウェアハウスから Azure にデータを移行する](data-migration-guidance-overview.md)
* [Amazon S3 から Azure Storage にデータを移行する](data-migration-guidance-s3-azure-storage.md)
