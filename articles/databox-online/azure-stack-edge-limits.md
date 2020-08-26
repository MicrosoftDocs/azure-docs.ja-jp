---
title: Azure Stack Edge の制限 | Microsoft Docs
description: Azure Stack Edge をデプロイして操作する際の制限 (サービスの制限、デバイスの制限、記憶域の制限など) と推奨されるサイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 8bbcf3f61121813b91cb951809992d10977a640c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922712"
---
# <a name="azure-stack-edge-limits"></a>Azure Stack Edge の制限

Microsoft Azure Stack Edge ソリューションを配置および操作するときは、以下の制限事項を考慮してください。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge サービスの制限

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack デバイスの制限

次の表では、Azure Stack Edge デバイスの制限について説明します。 

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

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge の配置を準備する](azure-stack-edge-deploy-prep.md)
