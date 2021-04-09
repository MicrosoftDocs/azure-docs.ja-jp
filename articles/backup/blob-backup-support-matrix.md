---
title: Azure BLOB バックアップのサポート マトリックス
description: Azure BLOB をバックアップするときのサポート設定と制限の概要について説明します (プレビュー)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: ade43350bbe3fa1bcf58f47e93b948db3a5b21bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743765"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Azure BLOB バックアップのサポート マトリックス (プレビュー)

この記事では、利用できるリージョン、サポートされるシナリオ、BLOB の運用バックアップの制限事項について説明します。

## <a name="supported-regions"></a>サポートされているリージョン

現在、BLOB の運用バックアップは次のリージョンで利用できます: オーストラリア中部、オーストラリア東部、ブラジル南部、カナダ中部、インド中部、米国中部、東アジア、米国東部、米国東部 2、ドイツ中西部、東日本、西日本、韓国中部、韓国南部、北ヨーロッパ、米国中南部、東南アジア、スイス北部、アラブ首長国連邦北部、英国南部、英国西部、米国中西部、西ヨーロッパ、米国西部、米国西部 2

## <a name="limitations"></a>制限事項

BLOB の運用バックアップでは、ローカル バックアップソリューションを提供するために、BLOB のポイントインタイム リストア、BLOB のバージョン管理、BLOB の論理的な削除、BLOB の変更フィード、および削除ロックが使用されます。 そのため、これらの機能に適用される制限が、運用バックアップにも適用されます。

**サポートされているシナリオ:** 運用バックアップでサポートされているのは、Standard 汎用 v2 ストレージ アカウントのブロック BLOB のみです。 そのため、ADLS Gen2 アカウントはサポートされません。 また、ストレージ アカウントのページ BLOB、追加 BLOB、Premium BLOB は復元されず、ブロック BLOB のみが復元されます。

**その他の制限事項:**

- 保持期間中にコンテナーを削除した場合、そのコンテナーは、ポイントインタイム リストア操作では復元されません。 復元しようとしている BLOB 範囲に、削除されたコンテナー内の BLOB が含まれている場合、ポイントインタイム リストア操作は失敗します。 削除からのコンテナーの保護の詳細については、「[コンテナーの論理的な削除 (プレビュー)](https://docs.microsoft.com/azure/storage/blobs/soft-delete-container-overview)」を参照してください。
- 現在の時点から復元ポイントまでの間にホット層とクール層の間で BLOB が移動した場合、BLOB は以前の層に復元されます。 アーカイブ層でのブロック BLOB の復元はサポートされていません。 たとえば、ホット層の BLOB が 2 日前にアーカイブ層に移動され、復元操作によって 3 日前の時点に復元された場合、BLOB はホット層に復元されません。 アーカイブされた BLOB を復元するには、最初にアーカイブ層の外に移動します。 詳細については、「[アーカイブ層から BLOB データをリハイドレートする](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration)」を参照してください。
- [Put Block](https://docs.microsoft.com/rest/api/storageservices/put-block) または [Put Block from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) を使用してアップロードされたが、[Put Block List](https://docs.microsoft.com/rest/api/storageservices/put-block-list) を使用してコミットされていないブロックは、BLOB の一部ではないため、復元操作の一部としては復元されません。
- アクティブなリースを持つ BLOB は復元できません。 リースがアクティブになっている BLOB が、復元する BLOB の範囲に含まれる場合、復元操作は自動的に失敗します。 復元操作を始める前に、アクティブなリースをすべて中断してください。
- スナップショットは、復元操作の一環として作成または削除されません。 ベース BLOB のみが以前の状態に復元されます。

## <a name="next-steps"></a>次のステップ

- [Azure Blob の運用バックアップの概要 (プレビュー)](blob-backup-overview.md)
