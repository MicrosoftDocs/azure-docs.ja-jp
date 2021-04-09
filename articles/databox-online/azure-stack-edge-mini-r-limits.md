---
title: Azure Stack Edge Mini R の制限 | Microsoft Docs
description: Azure Stack Edge Mini R のシステム制限および推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465746"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack Edge Mini R 制限


Azure Stack Edge Mini R ソリューションを配置および操作するときは、以下の制限事項を考慮してください。

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge サービスの制限

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Azure Stack Edge Mini R デバイスの制限

次の表で、Azure Stack Edge Mini R デバイスの制限について説明します。

| 説明 | 制限|
|---|---:|
|いいえ。 デバイスあたりのファイル数 | 1 億 <!--check with devs-->|
|いいえ。 コンテナーあたりの共有数 | 1|
|最大の デバイスあたり共有エンドポイントと REST エンドポイントの数| 24 |
|最大の デバイスあたり階層化ストレージ アカウント数| 24|
|1 つの共有に書き込まれる最大サイズ ファイル| 500 GB|
|最大のデバイスあたりリソース グループ数| 800|

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge の配置を準備する](azure-stack-edge-gpu-deploy-prep.md)
