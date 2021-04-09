---
title: Azure Data Lake Storage Gen1 のディザスター リカバリーのガイダンス | Microsoft Docs
description: Azure Data Lake Storage Gen1 のローカル冗長ストレージを超える範囲で、ローカル全体の障害や偶発的な削除からデータを保護する方法について説明します。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: 48136f8d9172c3674e849e24efca4ae5070f83ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109121"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1 の高可用性とディザスター リカバリーのガイダンス

Data Lake Storage Gen1 によって、ローカル冗長ストレージ (LRS) が提供されます。 そのため、Data Lake Storage Gen1 アカウントのデータは、データセンター内で発生する一時的なハードウェア障害に対して、自動レプリカによる回復性を備えています。 これにより、持続性と高可用性を確保し、Data Lake Storage Gen1 の SLA を達成しています。 この記事では、まれに発生するリージョン全体の障害や誤削除からデータをさらに保護する方法に関するガイダンスを示します。

## <a name="disaster-recovery-guidance"></a>ディザスター リカバリーの ガイダンス

ディザスター リカバリー プランを準備することは重要です。 この記事の情報と以下の追加リソースを確認して、独自のプランの作成に役立ててください。

* [Azure アプリケーションのディザスター リカバリーと高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Azure の回復性技術ガイダンス](/azure/architecture/framework/resiliency/overview)

### <a name="best-practice-recommendations"></a>ベスト プラクティスの推奨事項

ディザスター リカバリー計画のニーズに合った頻度で、別のリージョンにある別の Data Lake Storage Gen1 アカウントに重要なデータをコピーすることをお勧めします。 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md)、[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) など、データをコピーするさまざまな方法があります。 Azure Data Factory は、データ移動パイプラインを作成して定期的にデプロイできる便利なサービスです。

地域的な障害が発生した場合は、データのコピー先のリージョン内のデータにアクセスできます。 [Azure Service Health ダッシュボード](https://azure.microsoft.com/status/)を監視して、世界中の Azure サービスの状態を確認できます。

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>データの破損または誤削除からの復旧に関するガイダンス

Data Lake Storage Gen1 は自動レプリカによるデータの回復性を備えていますが、アプリケーション (または開発者やユーザー) によるデータの破損や誤った削除を防ぐことはできません。

誤削除を防ぐために、まず、Data Lake Storage Gen1 アカウントの適切なアクセス ポリシーを設定することをお勧めします。 これには、重要なリソースをロックダウンするための [Azure リソースのロック](../azure-resource-manager/management/lock-resources.md)の適用と、利用可能な [Data Lake Storage Gen1 セキュリティ機能](data-lake-store-security-overview.md)を使用したアカウントおよびファイル レベルのアクセス制御の適用が含まれます。 また、別の Data Lake Storage Gen1 アカウント、フォルダー、または Azure サブスクリプションで、[ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md)、または [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) を使用して、重要なデータのコピーを定期的に作成することもお勧めします。 これを使用して、データの破損や削除から復旧できます。 Azure Data Factory は、データ移動パイプラインを作成して定期的にデプロイできる便利なサービスです。

Data Lake Storage Gen1 アカウントの[診断ログ](data-lake-store-diagnostic-logs.md)を有効にして、データ アクセスの監査証跡を収集することもできます。 監査証跡は、ファイルを削除または更新した可能性のあるユーザーに関する情報を提供します。

## <a name="next-steps"></a>次のステップ

* [Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)