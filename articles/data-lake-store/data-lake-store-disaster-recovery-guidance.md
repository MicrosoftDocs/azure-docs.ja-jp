---
title: "Azure Data Lake Store のディザスター リカバリーのガイダンス | Microsoft Docs"
description: "Azure Data Lake Store のディザスター リカバリーのガイダンス"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/30/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: f9ad730688433912a46d6e22bfc82063a9a60be3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>Data Lake Store 内のデータに対するディザスター リカバリーのガイダンス

Azure Data Lake Store アカウントのデータは、リージョン内で発生する一時的なハードウェア障害に対して、自動レプリカによる回復性を備えています。 これにより、持続性と高可用性を確保し、Azure Data Lake Store の SLA を達成しています。 この記事では、まれに発生するリージョン全体の障害や誤削除からデータをさらに保護する方法に関するガイダンスを示します。

## <a name="disaster-recovery-guidance"></a>ディザスター リカバリーの ガイダンス
すべての顧客が独自のディザスター リカバリー計画を準備することが重要です。 ディザスター リカバリー計画を作成する場合は、次の Azure ドキュメントをご覧ください。 独自の計画を作成する際に役立つリソースがあります。

* [Azure アプリケーションのディザスター リカバリーと高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure の回復性技術ガイダンス](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>ベスト プラクティス
ディザスター リカバリー計画のニーズに合った頻度で、別のリージョンにある別の Data Lake Store アカウントに重要なデータをコピーすることをお勧めします。 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md)、[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) など、データをコピーするさまざまな方法があります。 Azure Data Factory は、データ移動パイプラインを作成して定期的にデプロイできる便利なサービスです。

リージョンの障害が発生しても、データがコピーされているリージョンのデータにアクセスできます。また、[Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)を監視することで、世界中の Azure サービスの状態を確認できます。

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>データの破損または誤削除からの復旧に関するガイダンス
Azure Data Lake Store は自動レプリカによるデータの回復性を備えていますが、アプリケーション (または開発者やユーザー) によるデータの破損や誤った削除を防ぐことはできません。

### <a name="best-practices"></a>ベスト プラクティス
誤削除を防ぐために、まず、Data Lake Store アカウントの適切なアクセス ポリシーを設定することをお勧めします。  これには、重要なリソースをロックダウンするための [Azure リソースのロック](../azure-resource-manager/resource-group-lock-resources.md)の適用と、利用可能な [Data Lake Store セキュリティ機能](data-lake-store-security-overview.md)を使用したアカウントおよびファイル レベルのアクセス制御の適用が含まれます。 また、別の Data Lake Store アカウント、フォルダー、または Azure サブスクリプションで、[ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)[Azure PowerShell](data-lake-store-get-started-powershell.md)、または [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) を使用して、重要なデータのコピーを定期的に作成することもお勧めします。  これを使用して、データの破損や削除から復旧できます。 Azure Data Factory は、データ移動パイプラインを作成して定期的にデプロイできる便利なサービスです。

また、組織で Azure Data Lake Store アカウントの[診断ログ](data-lake-store-diagnostic-logs.md)を有効にすることもできます。これにより、ファイルを削除または更新した可能性のあるユーザーに関する情報を提供する、データ アクセスの監査証跡を収集できます。

## <a name="next-steps"></a>次のステップ
* [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)


