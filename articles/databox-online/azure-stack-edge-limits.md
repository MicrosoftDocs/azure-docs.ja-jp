---
title: Azure Stack Edge Pro の制限 | Microsoft Docs
description: Azure Stack Edge Pro を配置して操作するときの制限 (サービスの制限、デバイスの制限、記憶域の制限など) と推奨されるサイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992766"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro の制限

Microsoft Azure Stack Edge Pro ソリューションを配置および操作するときは、以下の制限事項を考慮してください。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge サービスの制限

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge デバイスの制限

次の表では、Azure Stack Edge Pro デバイスの制限について説明します。 

次の表では、Azure Stack Edge デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 |
|いいえ。 コンテナーあたりの共有数 |1 |
|最大の デバイスあたり共有エンドポイントと REST エンドポイントの数| 24 |
|最大の デバイスあたり階層化ストレージ アカウント数| 24|
|1 つの共有に書き込まれる最大サイズ ファイル| 5 TB |
|最大のデバイスあたりリソース グループ数| 800 |

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro の配置を準備する](azure-stack-edge-deploy-prep.md)
