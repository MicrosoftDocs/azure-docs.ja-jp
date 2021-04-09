---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 10aa9b06af439fe701c53ef736ec691167560f95
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109122"
---
Azure Data Factory は、お客様のサブスクリプションが互いのワークロードから保護されるように、次の既定の制限が設定されているマルチテナント サービスです。 サブスクリプションの上限まで制限を引き上げるには、サポートにお問い合わせください。

### <a name="version-2"></a>Version 2

| リソース | 既定の制限 | 上限 |
| -------- | ------------- | ------------- |
| Azure サブスクリプション内の Data Factory | 800 | 800 |
| データ ファクトリ内のエンティティ (パイプライン、データセット、トリガー、リンクされたサービス、プライベート エンドポイント、統合ランタイムなど) の合計数 | 5,000 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 1 つのサブスクリプションでの Azure-SSIS Integration Runtime の CPU コア数の合計 | 256 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| データ ファクトリあたりの同時実行パイプラインの実行数 (ファクトリ内のすべてのパイプライン間で共有) | 10,000  | 10,000 |
| [Azure Integration Runtime リージョン](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)あたり、サブスクリプションあたりの同時実行外部アクティビティの実行数<br><small>外部アクティビティは統合ランタイムで管理されますが、リンクされたサービスで実行されます (Databricks、ストアド プロシージャ、HDInsight、Web などを含む)。この制限は、セルフホステッド IR には適用されません。</small> | 3,000 | 3,000 |
| [Azure Integration Runtime リージョン](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)あたり、サブスクリプションあたりの同時実行パイプライン アクティビティの実行数 <br><small>パイプライン アクティビティは統合ランタイムで実行されます (Lookup、GetMetadata、Delete を含む)。この制限は、セルフホステッド IR には適用されません。</small> | 1,000 | 1,000                                                        |
| [Azure Integration Runtime リージョン](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)あたり、サブスクリプションあたりの同時実行オーサリング操作数<br><small>接続のテスト、フォルダー一覧とテーブル一覧の参照、データのプレビューを含みます。この制限は、セルフホステッド IR には適用されません。</small> | 200 | 200                                                          |
| [Azure Integration Runtime リージョン](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)あたり、サブスクリプションあたりの同時実行データ統合単位<sup>1</sup>の消費| リージョン グループ 1<sup>2</sup>: 6,000<br>リージョン グループ 2<sup>2</sup>: 3,000<br>リージョン グループ 3<sup>2</sup>: 1,500 | リージョン グループ 1<sup>2</sup>: 6,000<br/>リージョン グループ 2<sup>2</sup>: 3,000<br/>リージョン グループ 3<sup>2</sup>: 1,500 |
| パイプラインあたりの最大アクティビティ数 (コンテナーの内部アクティビティを含む) | 40 | 40 |
| 単一のセルフホステッド統合ランタイムに対して作成できる、リンクされた統合ランタイムの最大数 | 100 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| パイプラインあたりの最大パラメーター数 | 50 | 50 |
| ForEach 項目数 | 100,000 | 100,000 |
| ForEach 並列処理 | 20 | 50 |
| パイプラインあたりのキューに入れられた実行の最大数 | 100 | 100 |
| 式ごとの文字数 | 8,192 | 8,192 |
| 最小タンブリング ウィンドウ トリガー間隔 | 15 分 | 15 分 |
| パイプラインのアクティビティ実行の最大タイムアウト | 7 日 | 7 日 |
| パイプライン オブジェクトのオブジェクトあたりのバイト数<sup>3</sup> | 200 KB | 200 KB |
| データセットおよびリンクされたサービス オブジェクトのオブジェクトあたりのバイト数<sup>3</sup> | 100 KB | 2,000 KB |
| 各アクティビティの実行のペイロードあたりのバイト数<sup>4</sup> | 896 KB | 896 KB |
| コピー アクティビティの実行あたりのデータ統合単位<sup>1</sup> | 256 | 256 |
| API 呼び出しの書き込み | 1,200/h | 1,200/h<br/><br/> この制限は、Azure Data Factory ではなく、Azure Resource Manager によって課せられます。 |
| API 呼び出しの読み取り | 12,500/時 | 12,500/時<br/><br/> この制限は、Azure Data Factory ではなく、Azure Resource Manager によって課せられます。 |
| 1 分あたりの監視クエリ | 1,000 | 1,000 |
| データ フロー デバッグ セッションの最大時間 | 8 時間 | 8 時間 |
| 統合ランタイムごとのデータ フロー同時実行数 | 50 | [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| ファクトリごと、かつユーザーごとのデータ フロー デバッグ セッションの同時実行数 | 3 | 3 |
| Data Flow の Azure IR の TTL 制限 | 4 時間 |  4 時間 |

<sup>1</sup> データ統合単位 (DIU) は、クラウド間のコピー操作で使用されます。詳細については、[データ統合単位 (バージョン 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) に関するページを参照してください。 課金については、「[Azure Data Factory の価格](https://azure.microsoft.com/pricing/details/data-factory/)」をご覧ください。

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) は、データ コンプライアンス、効率、およびネットワーク エグレス コストの削減を実現するために[グローバルに使用できます](https://azure.microsoft.com/global-infrastructure/services/)。 

| リージョン グループ | リージョン |
| -------- | ------ |
| リージョン グループ 1 | 米国中部、米国東部、米国東部 2、北ヨーロッパ、西ヨーロッパ、米国西部、米国西部 2 |
| リージョン グループ 2 | オーストラリア東部、オーストラリア南東部、ブラジル南部、インド中部、東日本、米国中北部、米国中南部、東南アジア、米国中西部 |
| リージョン グループ 3 | 他の地域 |

<sup>3</sup> パイプライン、データ セット、およびリンクされたサービス オブジェクトは、ワークロードの論理的なグループ化を表します。 これらのオブジェクトの制限は、Azure Data Factory で移動したり処理したりできるデータ量には関係ありません。 Data Factory は、数ペタバイトのデータ処理までスケーリングできるように設計されています。

<sup>4</sup> 各アクティビティの実行のペイロードには、アクティビティの構成、関連付けられているデータセットとリンクされたサービスの構成 (ある場合)、アクティビティの種類ごとに生成されるシステム プロパティの一部が含まれます。 このペイロード サイズの制限は、Azure Data Factory で移動したり処理したりできるデータ量には関係ありません。 この制限に達した場合の[現象と推奨事項](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large)について確認してください。

### <a name="version-1"></a>Version 1

| **リソース** | **既定の制限** | **上限** |
| --- | --- | --- |
| 1 つの Data Factory 内のパイプライン数 |2,500 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 1 つのデータ ファクトリ内のデータセット数 |5,000 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| データセットあたりの同時実行のスライス数 |10 |10 |
| パイプライン オブジェクトのオブジェクトあたりのバイト数<sup>1</sup> |200 KB |200 KB |
| データセットとリンクされたサービス オブジェクトのオブジェクトあたりのバイト数<sup>1</sup> |100 KB |2,000 KB |
| サブスクリプション内の Azure HDInsight オンデマンドのクラスター コア数<sup>2</sup> |60 |[サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| コピー アクティビティの実行 1 回あたりのクラウド データ移動単位<sup>3</sup> |32 |32 |
| パイプラインのアクティビティ実行の再試行カウント |1,000 |MaxInt (32 ビット) |

<sup>1</sup> パイプライン、データ セット、およびリンクされたサービス オブジェクトは、ワークロードの論理的なグループ化を表します。 これらのオブジェクトの制限は、Azure Data Factory で移動したり処理したりできるデータ量には関係ありません。 Data Factory は、数ペタバイトのデータ処理までスケーリングできるように設計されています。

<sup>2</sup> オンデマンド HDInsight コアは、Data Factory を含むサブスクリプションから割り当てられます。 そのため、上記の制限は、オンデマンド HDInsight コアに対して Data Factory で適用されるコアの制限です。 Azure サブスクリプションに関連するコアの制限とは異なります。

<sup>3</sup> バージョン 1 のクラウド データ移動単位 (DMU) は、クラウド間のコピー操作で使用されます。詳細については、[クラウド データ移動単位 (バージョン 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) に関するページを参照してください。 課金については、「[Azure Data Factory の価格](https://azure.microsoft.com/pricing/details/data-factory/)」をご覧ください。

| **リソース** | **既定値の下限** | **上限** |
| --- | --- | --- |
| スケジュールの間隔 |約 15 分 |約 15 分 |
| 再試行の間隔 |1 秒 |1 秒 |
| 再試行のタイムアウト値 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web サービス呼び出しの制限
Azure Resource Manager では、API 呼び出しの制限があります。 API の呼び出しは、 [Azure リソース マネージャーの API 制限](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)内の割合で実行できます。
