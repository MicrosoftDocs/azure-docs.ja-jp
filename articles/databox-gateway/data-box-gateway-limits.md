---
title: Azure Data Box Gateway の制限 | Microsoft Docs
description: Microsoft Azure Data Box Gateway のシステム制限と推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581200"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway の制限

Microsoft Azure Data Box Gateway ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。

## <a name="data-box-gateway-service-limits"></a>Data Box Gateway サービスの制限

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway デバイスの制限

次の表で、Data Box Gateway デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 <br> 2500 万個のファイルが追加されるたびに (上限は 1 億個)、2 TB のディスク領域、8 GB の RAM、4 コアの CPU を追加してください。 |
|いいえ。 デバイスあたりの共有数 |24 |
|いいえ。 数 (Azure Storage コンテナーあたり) |1 |
|1 つの共有に書き込まれる最大サイズ ファイル|2 TB の仮想デバイスの場合、最大ファイル サイズは 500 GB です。 <br> 最大ファイル サイズは、最大 5 TB に達するまで、前の比率でデータ ディスク のサイズと増加します。 |

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)
