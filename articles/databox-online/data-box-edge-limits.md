---
title: Azure Data Box Edge の制限 | Microsoft Docs
description: Azure Data Box Edge のシステム制限および推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: b454b563cdb870ca8f07a45b796dc6b1e272502d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924593"
---
# <a name="azure-data-box-edge-limits"></a>Azure Data Box Edge の制限

Microsoft Azure Data Box Edge ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。

## <a name="data-box-edge-service-limits"></a>Data Box Edge サービスの制限

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Data Box Edge デバイスの制限

次の表で、Data Box Edge デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|デバイスあたりのファイル数 |1 億 |
|デバイスあたりの共有数 |24 |
|コンテナーあたりの共有数 |1 |
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
