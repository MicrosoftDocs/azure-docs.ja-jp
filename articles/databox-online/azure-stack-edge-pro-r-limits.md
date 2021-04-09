---
title: Azure Stack Edge Pro R の制限 | Microsoft Docs
description: Azure Stack Edge Pro R のシステム制限および推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465679"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack Edge Pro R の制限

Azure Stack Edge Pro R ソリューションをデプロイおよび操作するときは、以下の制限事項を考慮してください。

## <a name="azure-stack-edge-pro-r-service-limits"></a>Azure Stack Edge Pro R サービスの制限

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Azure Stack Edge Pro R デバイスの制限

次の表で、Azure Stack Edge Pro R デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 |
|いいえ。 コンテナーあたりの共有数 |1 |
|最大の デバイスあたり共有エンドポイントと REST エンドポイントの数| 24 |
|最大の デバイスあたり階層化ストレージ アカウント数| 24|
|1 つの共有に書き込まれる最大サイズ ファイル| 5 TB |
|最大のデバイスあたりリソース グループ数| 800 |

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro R のデプロイを準備する](azure-stack-edge-pro-r-deploy-prep.md)
