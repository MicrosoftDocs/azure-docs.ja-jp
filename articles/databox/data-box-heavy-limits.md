---
title: Azure Data Box Heavy の制限 | Microsoft Docs
description: Microsoft Azure Data Box Heavy のコンポーネントと接続に関するシステム制限および推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 76e543a5bec749db55eb77649d2f6e0526f2cb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84705561"
---
# <a name="azure-data-box-heavy-limits"></a>Azure Data Box Heavy の制限

Azure Data Box Heavy デバイスをデプロイおよび運用する際には、以下の制限事項を考慮してください。 次の表で、Data Box の制限について説明します。


## <a name="data-box-heavy-service-limits"></a>Data Box Heavy サービスの制限

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-heavy-limits"></a>Data Box Heavy の制限

- Data Box Heavy では、ノードあたり最大 10 億ファイルを格納できます。
- Data Box Heavy は、クラウドでノードあたり最大 512 個のコンテナーまたは共有をサポートしています。 ユーザー共有内の最上位レベルのディレクトリは、クラウド内のコンテナーまたは Azure ファイル共有になります。

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="azure-storage-account-size-limits"></a>Azure ストレージ アカウントのサイズ制限

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure ブロック BLOB、ページ BLOB およびファイルの名前付け規則

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
