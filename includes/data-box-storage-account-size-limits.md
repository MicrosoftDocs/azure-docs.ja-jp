---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 0975df8ee9da4a239dbd72e468b967c88ab3feed
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277330"
---
ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限の最新情報については、「[Azure Blob Storage のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)」および「[Azure Files のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)」をご覧ください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | 米国およびヨーロッパでは 2 PB。<br>英国を含む他のすべてのリージョンでは 500 TB。  <br> これには、Data Box を含むすべてのソースのデータが含まれます。|
| Azure Files                                                          | 共有あたり 5 TB。<br> *StorageAccount_AzureFiles* の下のすべてのフォルダーはこの制限に従う必要があります。       |
