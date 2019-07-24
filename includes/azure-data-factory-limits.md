---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 42c1856f30484532e1ace2e84187bcaaacdf4c72
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553838"
---
Azure Data Factory は、お客様のサブスクリプションが互いのワークロードから保護されるように、次の既定の制限が設定されているマルチテナント サービスです。 サブスクリプションの上限まで制限を引き上げるには、サポートにお問い合わせください。

### <a name="version-2"></a>Version 2

| リソース | 既定の制限 | 上限 |
| -------- | ------------- | ------------- |
| Azure サブスクリプション内の Data Factory | 50 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| データ ファクトリ内のエンティティ (パイプライン、データセット、トリガー、リンクされたサービス、統合ランタイムなど) の合計数 | 5,000 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 1 つのサブスクリプションでの Azure-SSIS Integration Runtime の CPU コア数の合計 | 256 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| データ ファクトリあたりの同時実行パイプラインの実行数 (ファクトリ内のすべてのパイプライン間で共有) | 10,000  | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| パイプラインあたりの最大アクティビティ数 (コンテナーの内部アクティビティを含む) | 40 | 40 |
| 単一のセルフホステッド統合ランタイムに対して作成できる、リンクされた統合ランタイムの最大数 | 20 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| パイプラインあたりの最大パラメーター数 | 50 | 50 |
| ForEach 項目数 | 100,000 | 100,000 |
| ForEach 並列処理 | 20 | 50 |
| 式ごとの文字数 | 8,192 | 8,192 |
| 最小タンブリング ウィンドウ トリガー間隔 | 15 分 | 15 分 |
| パイプラインのアクティビティ実行の最大タイムアウト | 7 日 | 7 日 |
| パイプライン オブジェクトのオブジェクトあたりのバイト数<sup>1</sup> | 200 KB | 200 KB |
| データセットとリンクされたサービス オブジェクトのオブジェクトあたりのバイト数<sup>1</sup> | 100 KB | 2,000 KB |
| コピー アクティビティの実行 1 回あたりのデータ統合単位<sup>3</sup> | 256 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| API 呼び出しの書き込み | 2,500/時<br/><br/> この制限は、Azure Data Factory ではなく、Azure Resource Manager によって課せられます。 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| API 呼び出しの読み取り | 12,500/時<br/><br/> この制限は、Azure Data Factory ではなく、Azure Resource Manager によって課せられます。 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 1 分あたりの監視クエリ | 1,000 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 1 分あたりのエンティティ CRUD 操作 | 50 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |


### <a name="version-1"></a>Version 1

| **リソース** | **既定の制限** | **上限** |
| --- | --- | --- |
| Azure サブスクリプション内の Data Factory |50 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 1 つの Data Factory 内のパイプライン数 |2,500 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 1 つのデータ ファクトリ内のデータセット数 |5,000 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| データセットあたりの同時実行のスライス数 |10 |10 |
| パイプライン オブジェクトのオブジェクトあたりのバイト数<sup>1</sup> |200 KB |200 KB |
| データセットとリンクされたサービス オブジェクトのオブジェクトあたりのバイト数<sup>1</sup> |100 KB |2,000 KB |
| サブスクリプション内の Azure HDInsight オンデマンドのクラスター コア数<sup>2</sup> |60 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| コピー アクティビティの実行 1 回あたりのクラウド データ移動単位<sup>3</sup> |32 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| パイプラインのアクティビティ実行の再試行カウント |1,000 |MaxInt (32 ビット) |

<sup>1</sup>パイプライン、データセット、リンクされたサービス オブジェクトは、ワークロードの論理グループを表します。 これらのオブジェクトの制限は、Azure Data Factory で移動したり処理したりできるデータ量には関係ありません。 Data Factory は、数ペタバイトのデータ処理までスケーリングできるように設計されています。

<sup>2</sup>オンデマンド HDInsight コアは、Data Factory を含むサブスクリプションから割り当てられます。 そのため、上記の制限は、オンデマンド HDInsight コアに対して Data Factory で適用されるコアの制限です。 Azure サブスクリプションに関連するコアの制限とは異なります。

<sup>3</sup>バージョン 2 のデータ統合単位 (DIU) またはバージョン 1 のクラウド データ移動単位 (DMU) は、クラウド間のコピー操作で使用されます。 これは、Data Factory の 1 つの単位の能力を表す尺度です。 能力は、CPU、メモリ、ネットワーク リソース割り当ての組み合わせです。 一部のシナリオで多くの DMU を使用することで、より高いコピー スループットを実現できます。 詳細については、[データ統合単位 (バージョン 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) および[クラウド データ移動単位 (バージョン 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) に関するセクションをご覧ください。 課金については、「[Azure Data Factory の価格](https://azure.microsoft.com/pricing/details/data-factory/)」をご覧ください。

<sup>4</sup>Integration Runtime (IR) は、さまざまなネットワーク環境間でデータ統合機能 (データ移動、コンピューティング サービスへのアクティビティのディスパッチ、SSIS パッケージの実行など) を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。 詳細については、[Integration Runtime の概要](../articles/data-factory/concepts-integration-runtime.md)に関する記事をご覧ください。

| **リソース** | **既定値の下限** | **上限** |
| --- | --- | --- |
| スケジュールの間隔 |約 15 分 |約 15 分 |
| 再試行の間隔 |1 秒 |1 秒 |
| 再試行のタイムアウト値 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web サービス呼び出しの制限
Azure Resource Manager では、API 呼び出しの制限があります。 API の呼び出しは、 [Azure リソース マネージャーの API 制限](../articles/azure-subscription-service-limits.md#resource-group-limits)内の割合で実行できます。
