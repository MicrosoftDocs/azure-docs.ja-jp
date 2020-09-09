---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88655147"
---
ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | 既定の制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | ~ 4.75 TiB                                                 |
| ページ BLOB         | 8 TiB <br> ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトでアラインされます。 |
| Azure Files        | 1 TiB                                                      |
| マネージド ディスク     | 4 TiB <br> サイズと制限の詳細については、以下をご覧ください。 <li>[Standard SSD のスケーラビリティの目標](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Premium SSD のスケーラビリティの目標](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[Standard HDD のスケーラビリティの目標](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[マネージド ディスクの価格と課金](../articles/virtual-machines/disks-types.md#billing)</li>  
