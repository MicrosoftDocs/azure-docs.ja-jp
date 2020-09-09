---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378449"
---
ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限に関する最新情報については、「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../articles/storage/blobs/scalability-targets.md)」と「[Azure Files のスケーラビリティとパフォーマンスのターゲット](../articles/storage/files/storage-files-scale-targets.md)」を参照してください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | 上限は [Azure サブスクリプションに対して定義されているストレージの上限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)と同じであり、Data Box を含むすべてのソースのデータが含まれています。|
| Azure Files                                                          | Standard ファイル共有の最大サイズは 5 TB です。 <br> Premium ファイル共有の最大サイズは、共有あたり 100 TiB です。<br> *StorageAccount_AzureFiles* の下のすべてのフォルダーはこの制限に従う必要があります。       |
