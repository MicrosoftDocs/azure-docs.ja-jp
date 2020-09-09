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
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414168"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド

> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [現在のバージョン](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ レイクまたはエンタープライズ データ ウェアハウス (EDW) から Azure への大規模なデータ移行を実行する場合でも、ビッグ データ分析のためにさまざまなソースから Azure にデータを大規模に取り込む場合でも、最適なパフォーマンスとスケーラビリティを実現することが重要です。  Azure Data Factory には、データを大規模に取り込むためのパフォーマンス、回復性、およびコスト効率に優れたメカニズムが備わっており、パフォーマンスとスケーラビリティの高いデータ インジェスト パイプラインを構築しようとしているデータ エンジニアに最適です。

この記事を読むと、次の質問に回答できるようになります。

- データ移行シナリオとデータ インジェスト シナリオで ADF のコピー アクティビティを使用すると、どのレベルのパフォーマンスとスケーラビリティを実現できますか。

- ADF のコピー アクティビティのパフォーマンスを調整するには、どのような手順を実行する必要がありますか。
- 1 回のコピー アクティビティの実行でパフォーマンスを最適化するために、どのような ADF のパフォーマンス最適化のヒントを利用できますか。
- コピーのパフォーマンスを最適化するときに考慮する ADF 以外の要因には何がありますか。

> [!NOTE]
> コピー アクティビティ全般に慣れていない場合は、この記事を読む前に、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF を使用して実現可能なコピー パフォーマンスとスケーラビリティ

ADF には、さまざまなレベルで並列処理を可能にするサーバーレス アーキテクチャが用意されています。そのため、開発者は、ネットワーク帯域幅だけでなくストレージの IOPS と帯域幅を利用して、データ移動のスループットが環境に合わせて最大限になるパイプラインを構築できます。  これは、ソース データ ストア、ターゲット データ ストア、およびソースとターゲット ストア間のネットワーク帯域幅によって提供される最小スループットを測定することで、実現できるスループットを推定できることを意味します。  次の表では、データ サイズと環境の帯域幅制限に基づいて、コピー期間を計算しています。 

| データ サイズ/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分    | 1.4 分   | 0.3 分   | 0.1 分  | 0.03 分 | 0.01 分 | 0.0 分   |
| **10 GB**                   | 27.3 分   | 13.7 分  | 2.7 分   | 1.3 分  | 0.3 分  | 0.1 分  | 0.03 分  |
| **100 GB**                  | 4.6 時間    | 2.3 時間   | 0.5 時間   | 0.2 時間  | 0.05 時間 | 0.02 時間 | 0.0 時間   |
| **1 TB**                    | 46.6 時間   | 23.3 時間  | 4.7 時間   | 2.3 時間  | 0.5 時間  | 0.2 時間  | 0.05 時間  |
| **10 TB**                   | 19.4 日  | 9.7 日  | 1.9 日  | 0.9 日 | 0.2 日 | 0.1 日 | 0.02 日 |
| **100 TB**                  | 194.2 日 | 97.1 日 | 19.4 日 | 9.7 日 | 1.9 日 | 1 日    | 0.2 日  |
| **1 PB**                    | 64.7 か月    | 32.4 か月   | 6.5 か月    | 3.2 か月   | 0.6 か月   | 0.3 か月   | 0.06 か月   |
| **10 PB**                   | 647.3 か月   | 323.6 か月  | 64.7 か月   | 31.6 か月  | 6.5 か月   | 3.2 か月   | 0.6 か月    |

ADF コピーはさまざまなレベルでスケーラブルです。

![ADF のコピー スケールのしくみ](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 制御フローでは、複数のコピー アクティビティを並列して開始できます。たとえば、[For Each ループ](control-flow-for-each-activity.md)を使用します。
- 1 回のコピー アクティビティで、スケーラブルなコンピューティング リソースを利用できます。Azure Integration Runtime を使用する場合は、サーバーレス方式で各コピー アクティビティに対して[最大 256 DIU](#data-integration-units) を指定できます。セルフホステッド統合ランタイムを使用する場合は、手動でマシンをスケールアップするか、複数のマシン ([最大 4 ノード](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) にスケールアウトすることができます。また、1 回のコピー アクティビティによって、すべてのノードでファイル セットがパーティション分割されます。
- 1 回のコピー アクティビティで、複数のスレッドを使用したデータ ストアの読み取りと書き込みが[並列](#parallel-copy)で行われます。

## <a name="performance-tuning-steps"></a>パフォーマンス チューニングの手順

Azure Data Factory サービスとコピー アクティビティのパフォーマンスをチューニングするには、次の手順を実行します。

1. **テスト データセットを選択し、ベースラインを確立します。** 開発フェーズでは、代表的なデータ サンプルに対してコピー アクティビティを使用して、パイプラインをテストします。 選択するデータセットは、一般的なデータ パターン (フォルダー構造、ファイル パターン、データ スキーマなど) を表し、コピーのパフォーマンスを評価するのに十分なサイズ (たとえば、コピー アクティビティが完了するまで 10 分以上かかるサイズ) である必要があります。 [コピー アクティビティの監視](copy-activity-monitoring.md)の後に、実行の詳細とパフォーマンス特性を収集します。

2. **1 回のコピー アクティビティのパフォーマンスを最大化する方法**:

   まず第一に、1 回のコピー アクティビティを使用するパフォーマンスを最大化することをお勧めします。

   - **コピー アクティビティが Azure Integration Runtime に対して実行される場合:** [データ統合単位 (DIU)](#data-integration-units) と[並列コピー](#parallel-copy)の設定の既定値で開始します。 

   - **コピー アクティビティがセルフホステッド統合ランタイムに対して実行される場合:** データ ストアをホストするサーバーとは別の専用のコンピューターを使用して統合ランタイムをホストすることをお勧めします。 [並列コピー](#parallel-copy)設定の既定値とセルフホステッド IR 用の単一のノードの使用から始めます。  

   パフォーマンス テストを実行し、達成されたパフォーマンスと、実際に使用された DIU や並列コピーなどの値を書き留めます。 実行結果と使用されたパフォーマンス設定を収集する方法については「[コピー アクティビティの監視](copy-activity-monitoring.md)」を、ボトルネックを特定して解決するには「[コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)」を参照してください。 

   トラブルシューティングとチューニング ガイダンスに従って、追加のパフォーマンス テストの実行を繰り返します。 1 回のコピー アクティビティの実行でスループットの向上を実現できない場合は、手順 3 を参照して複数のコピーを同時に実行することで合計スループットを最大化することを検討してください。


3. **複数のコピーを同時に実行することで合計スループットを最大化する方法:**

   これで、1 回のコピー アクティビティのパフォーマンスが最大になったので、環境 (ネットワーク、ソース データ ストア、およびターゲット データ ストア) のスループットの上限にまだ達していなければ、[For Each ループ](control-flow-for-each-activity.md)などの ADF 制御フロー コンストラクトを使用して、複数のコピー アクティビティを並行して実行できます。 詳細については、「[複数のコンテナーからのファイルのコピー](solution-template-copy-files-multiple-containers.md)」、「[Amazon S3 から ADLS Gen2 にデータを移行する](solution-template-migration-s3-azure.md)」、または「一般例として[管理テーブル ソリューションテンプレートを使用して一括コピーする](solution-template-bulk-copy-with-control-table.md)」を参照してください。

5. **構成をデータセット全体まで拡張します。** 実行の結果とパフォーマンスに問題がなければ、データセット全体を網羅するよう、定義とパイプラインを拡張できます。

## <a name="troubleshoot-copy-activity-performance"></a>コピー アクティビティのパフォーマンスのトラブルシューティング

[パフォーマンス チューニングの手順](#performance-tuning-steps) に従って、シナリオのパフォーマンステストを計画および実施します。 また、「[コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)」で、Azure Data Factory での各回のコピー アクティビティの実行のパフォーマンスに関する問題をトラブルシューティングする方法を確認します。

## <a name="copy-performance-optimization-features"></a>コピー パフォーマンス最適化機能

Azure Data Factory には、次のパフォーマンス最適化機能があります。

- [データ統合単位](#data-integration-units)
- [セルフホステッド統合ランタイムのスケーラビリティ](#self-hosted-integration-runtime-scalability)
- [並列コピー](#parallel-copy)
- [ステージング コピー](#staged-copy)

### <a name="data-integration-units"></a>データ統合単位

データ統合単位は、Azure Data Factory の 1 つの単位の能力 (CPU、メモリ、ネットワーク リソース割り当ての組み合わせ) を表す尺度です。 データ統合単位は [Azure 統合ランタイム](concepts-integration-runtime.md#azure-integration-runtime)にのみ適用され、[セルフホステッド統合ランタイム](concepts-integration-runtime.md#self-hosted-integration-runtime)には適用されません。 [詳細については、こちらを参照してください](copy-activity-performance-features.md#data-integration-units)。

### <a name="self-hosted-integration-runtime-scalability"></a>セルフホステッド統合ランタイムのスケーラビリティ

同時実行ワークロードの増加をホストしたりパフォーマンスを向上させたりするために、セルフホステッド統合ランタイムをスケールアップまたはスケールアウトできます。 [詳細については、こちらを参照してください](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)。

### <a name="parallel-copy"></a>並列コピー

並列コピーを設定して、コピー アクティビティで並列処理を使用することを指示できます。 このプロパティは、並列でソースから読み取る、またはシンク データ ストアに書き込むコピー アクティビティ内の最大スレッド数と見なすことができます。 [詳細については、こちらを参照してください](copy-activity-performance-features.md#parallel-copy)。

### <a name="staged-copy"></a>ステージング コピー

ソース データ ストアからシンク データ ストアにデータをコピーする場合、中間のステージング ストアとして Blob Storage を使用できます。 [詳細については、こちらを参照してください](copy-activity-performance-features.md#staged-copy)。

## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)
- [コピー アクティビティ パフォーマンス最適化機能](copy-activity-performance-features.md)
- [Azure Data Factory を使用してデータ レイクまたはデータ ウェアハウスから Azure にデータを移行する](data-migration-guidance-overview.md)
- [Amazon S3 から Azure Storage にデータを移行する](data-migration-guidance-s3-azure-storage.md)
