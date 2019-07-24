---
title: Azure Data Box Gateway の制限 | Microsoft Docs
description: Microsoft Azure Data Box Gateway のシステム制限と推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401590"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway の制限

Microsoft Azure Data Box Gateway ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway サービスの制限

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway デバイスの制限

次の表で、Data Box Gateway デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 <br> 制限は、2 TB のディスク領域ごとに最大 2500 万ファイルであり、上限は 1 億です |
|いいえ。 デバイスあたりの共有数 |24 |
|いいえ。 数 (Azure Storage コンテナーあたり) |1 |
|1 つの共有に書き込まれる最大サイズ ファイル|2 TB の仮想デバイスの場合、最大ファイル サイズは 500 GB です。 <br> 最大ファイル サイズは、最大 5 TB に達するまで、前の比率でデータ ディスク のサイズと増加します。 |

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
