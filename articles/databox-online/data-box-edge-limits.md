---
title: Azure Data Box Edge の制限 | Microsoft Docs
description: Microsoft Azure Data Box Edge のシステム制限と推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: a687398032adffe5e787ef1469abe0b2627a38fe
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403546"
---
# <a name="azure-data-box-edge-limits"></a>Azure Data Box Edge の制限

Microsoft Azure Data Box Edge ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。

## <a name="data-box-edge-service-limits"></a>Data Box Edge サービスの制限

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Data Box Edge デバイスの制限

次の表で、Data Box Edge デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 |
|いいえ。 デバイスあたりの共有数 |24 |
|いいえ。 コンテナーあたりの共有数 |1 |
|1 つの共有に書き込まれる最大サイズ ファイル| 5 TB |

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>次の手順

- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)
