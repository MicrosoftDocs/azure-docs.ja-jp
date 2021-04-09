---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98225226"
---
ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限に関する最新情報については、「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../articles/storage/blobs/scalability-targets.md)」と「[Azure Files のスケーラビリティとパフォーマンスのターゲット](../articles/storage/files/storage-files-scale-targets.md)」を参照してください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | 上限は [Azure サブスクリプションに対して定義されているストレージの上限](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)と同じであり、Data Box を含むすべてのソースのデータが含まれています。   |
| Azure Files                                                          | Data Box で、Data Box の注文を作成する前に有効にした場合、大きなファイル共有 (100 TiB) がサポートされます。  <br> 順序の作成の前に有効にしなかった場合、サポートされるファイル共有の最大サイズは 5 TiB です。 <br> Data Box では Azure Premium ファイル共有がサポートされます。これにより、ストレージ アカウント内のすべての共有で合計 100 TiB が許可されます。 <br> コピー ログと監査ログで使用される領域があるため、使用可能な最大容量は若干少なくなります。 コピー ログと監査ログ用に、それぞれ少なくとも 100 GiB が予約されています。 詳細については、[Azure Data Box、Azure Data Box Heavy の監査ログ](../articles/databox/data-box-audit-logs.md)に関する記事を参照してください。 <br> *StorageAccount_AzureFiles* の下のすべてのフォルダーはこの制限に従う必要があります。 <br> 詳しくは、「[大きなファイル共有の有効化と作成](../articles/storage/files/storage-files-how-to-create-large-file-share.md)」を参照してください。      |
