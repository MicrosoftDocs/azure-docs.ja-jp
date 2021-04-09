---
title: 定期的なデータ転送用の Azure ソリューションを選択する | Microsoft Docs
description: 定期的にデータを転送する場合にデータ転送用の Azure ソリューションを選択する方法について説明します。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573920"
---
# <a name="solutions-for-periodic-data-transfer"></a>定期的なデータの転送用のソリューション
 
この記事では、定期的にデータを転送する場合のデータ転送ソリューションの概要を示します。 ネットワーク経由での定期的なデータ転送は、一定間隔での繰り返しまたは継続的なデータ移動として分類できます。 また、この記事では、推奨されるデータ転送と、このシナリオのそれぞれの主要な機能のマトリックスについても説明します。

利用可能なすべてのデータ転送オプションの概要を理解するには、[Azure データ転送ソリューションの選択](storage-choose-data-transfer-solution.md)に関するページに移動します。

## <a name="recommended-options"></a>推奨されるオプション

定期的なデータ転送用の推奨されるオプションは、転送が定期的であるか継続的であるかに応じて 2 つのカテゴリに分類されます。

- **スクリプト/プログラム ツール**– 一定間隔で発生するデータ転送では、AzCopy や Azure Storage REST API などのスクリプトおよびプログラム ツールを使用します。 これらのツールは、IT プロフェッショナルおよび開発者を対象としています。

    - **AzCopy** - このコマンドライン ツールを使用すると、最適なパフォーマンスで Azure BLOB、Files、および Table ストレージとの間で相互に簡単にデータをコピーできます。 AzCopy はコンカレンシーと並列処理をサポートし、中断された場合にコピー操作を再開することができます。
    - **Azure Storage REST API/SDK** – アプリケーションの構築時に、Azure Storage REST API に対するアプリケーションを開発し、複数の言語で提供される Azure SDK を使用できます。 REST API では、Azure との間で相互に高パフォーマンスのデータ コピーを行うことができるように特別に設計された、Azure Storage Data Movement Library を利用することもできます。

- **継続的なデータ インジェスト ツール** – 持続的かつ継続的なデータ インジェストには、Data Box オンライン転送デバイスの 1 つまたは Azure Data Factory を選択できます。 これらのツールは IT プロフェッショナルによって設定され、データ転送を透過的に自動化できます。

    - **Azure Data Factory** – 転送操作をスケールアウトする場合や、オーケストレーションとエンタープライズ レベルの監視機能が必要な場合は、Data Factory を使用する必要があります。 Azure Data Factory を使用すると、クラウド パイプラインを設定して、複数の Azure サービス間またはオンプレミスで、あるいは 2 つを組み合わせて、ファイルを定期的に転送することができます。 Azure Data Factory では、さまざまなデータ ストアからデータをインジェストし、データ移動とデータ転送を自動化する、データ駆動型ワークフローを調整することができます。
    - **オンライン転送用の Azure Data Box** - Data Box Edge と Data Box Gateway はオンラインのネットワーク デバイスであり、Azure の内外へのデータの移動が可能です。 Data Box Edge は、人工知能 (AI) 対応 Edge コンピューティングを使用して、アップロードの前にデータを前処理します。 Data Box Gateway は、同じデータ転送機能を備えた、デバイスの仮想バージョンです。


## <a name="comparison-of-key-capabilities"></a>主な機能の比較

次の表は、主な機能における相違点をまとめたものです。

### <a name="scriptedprogrammatic-network-data-transfer"></a>スクリプト/プログラムによるネットワーク データ転送

| 機能                  | AzCopy                                 | Azure Storage REST API       |
|-----------------------------|----------------------------------------|-------------------------------|
| フォーム ファクター                 | Microsoft からのコマンドライン ツール       | 顧客による Storage に対する開発 <br> Azure クライアント ライブラリを使用した REST API |
| 最初の 1 回限りのセットアップ     | Minimal                                | 多少の変数開発作業    |
| データ形式                 | Azure BLOB、Azure Files、Azure Tables | Azure BLOB、Azure Files、Azure Tables   |
| パフォーマンス                 | 既に最適化済み                      | 開発する際に最適化                  |
| 価格                     | 無料、データ エグレス料金を適用      | 無料、データ エグレス料金を適用        |

### <a name="continuous-data-ingestion-over-network"></a>ネットワーク経由での継続的なデータ インジェスト

| 機能                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| フォーム ファクター                                   | 仮想デバイス             | 物理デバイス          | Azure portal のサービス、オンプレミス エージェント                                                            |
| ハードウェア                                      | ハイパーバイザー            | Microsoft により提供    | NA                                                            |
| 初期セットアップ作業                          | 短時間 (< 30 分)            | 中程度 (から数時間) | 長時間 (から数日間)                                                 |
| データ形式                                   | Azure BLOB、Azure Files   | Azure BLOB、Azure Files | [データ ストアと形式について、70 個を超えるデータ コネクタがサポートされます](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| データの前処理                           | いいえ                         | はい、Edge コンピューティング経由    | はい                                                           |
| ローカル キャッシュ<br>(オンプレミス データを格納)    | はい                        | はい                      | いいえ                                                            |
| その他のクラウドからの転送                    | いいえ                         | いいえ                       | はい                                                           |
| 価格                                       | [料金](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [料金](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [料金](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>次のステップ

- [AzCopy を使用してデータを転送する](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。
- [Storage REST API を使用したデータ転送の詳細](/dotnet/api/overview/azure/storage)。
- 以下の方法を理解します。
    - [Data Box Gateway を使用してデータを転送する](../../databox-gateway/data-box-gateway-deploy-add-shares.md)します。
    - [Azure に送信する前に Data Box Edge を使用してデータを変換する](../../databox-online/azure-stack-edge-deploy-configure-compute.md)。
- [Azure Data Factory を使用してデータを転送する方法を学習します](../../data-factory/tutorial-bulk-copy-portal.md)。