---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200319"
---
Data Box デバイスでサポートされるストレージ アカウントとストレージの種類の一覧を次に示します。 ストレージ アカウントのすべての種類とそれらの完全な機能のすべての一覧については、「[ストレージ アカウントの種類](/azure/storage/common/storage-account-overview#types-of-storage-accounts)」を参照してください。

インポート注文については、サポートされているストレージ アカウントを次の表に示します。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB*** |**Azure Files** |**メモ**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y |
| 汎用 v1 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard  | Y | Y | Y | ホットとクールの両方がサポートされます。|
| 汎用 v2 Premium  |  |Y | | |
| Blob ストレージ Standard |Y | | |ホットとクールの両方がサポートされます。 |

\* *- ページ BLOB にアップロードされるデータは、VHD などの 512 バイトに揃えられている必要があります。*

エクスポートの順序については、サポートされているストレージ アカウントを次の表に示します。

| **ストレージ アカウント/サポートされるストレージの種類** | **ブロック BLOB** |**ページ BLOB*** |**Azure Files** |**サポートされているアクセス層**|
| --- | --- | -- | -- | -- |
| クラシック Standard | Y | Y | Y | |
| 汎用 v1 Standard  | Y | Y | Y | ホット、クール|
| 汎用 v1 Premium  |  | Y| | |
| 汎用 v2 Standard  | Y | Y | Y | ホット、クール|
| 汎用 v2 Premium  |  |Y | | |
| Blob ストレージ Standard |Y | | |ホット、クール |
| ブロック BLOB ストレージ Premium |Y | | |ホット、クール |
| ページ BLOB ストレージ Premium | |Y | | |

> [!IMPORTANT]
> - 汎用アカウントの場合、Data Box では、Queue Storage、Table Storage、Disk Storage、Azure Data Lake Gen 2 Storage の各種類がサポートされていません。
> - Data Box では、Blob Storage アカウントとブロック BLOB ストレージ アカウントの追加 BLOB がサポートされていません。
> - Data Box では、Premium File Storage アカウントがサポートされていません。
> - ページ BLOB にアップロードされるデータは、VHD など、512 バイトに揃えられている必要があります。
> - 最大 80 TB をエクスポートできます。
> - ファイル履歴と BLOB スナップショットはエクスポートされません。


