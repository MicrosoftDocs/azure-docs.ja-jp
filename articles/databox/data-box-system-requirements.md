---
title: Microsoft Azure Data Box のシステム要件 | Microsoft Docs
description: Azure Data Box と、Data Box に接続するクライアントの重要なシステム要件について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 85a0ce20f1d8e5a7e943efc088c19a8ad1912fc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706038"
---
# <a name="azure-data-box-system-requirements"></a>Data Box のシステム要件

この記事では、Microsoft Azure Data Box と、Data Box に接続するクライアント用の重要なシステム要件について説明します。 この情報は、Data Box をデプロイする前によく確認し、デプロイおよび操作中に必要に応じて参照することをお勧めします。

システム要件は次のとおりです。

* **ソフトウェア要件:** Data Box に接続するホストについては、サポートされているオペレーティング システム、ファイル転送プロトコル、ストレージ アカウント、ストレージの種類、およびローカル Web UI 用のブラウザーについて説明します。
* **ネットワーク要件:** Data Box については、Data Box を最適に運用するためのネットワーク接続とポートの要件について説明します。


## <a name="software-requirements"></a>ソフトウェア要件

ソフトウェア要件としては、サポートされているオペレーティング システム、ファイル転送プロトコル、ストレージ アカウント、ストレージの種類、ローカル Web UI 用のブラウザーなどがあります。

### <a name="supported-operating-systems-for-clients"></a>クライアントでサポートされるオペレーティング システム

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>クライアントでサポートされるファイル転送プロトコル

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> エクスポート注文については、Data Box 共有への接続は REST 経由でサポートされていません。 

### <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>サポートされているストレージの種類

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>サポートされている Web ブラウザー

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>ネットワーク要件

お客様のデータセンターには、高速ネットワークが必要です。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用してデータをコピーできます。ただし、コピー速度に影響があります。

### <a name="port-requirements"></a>ポートの要件

SMB または NFS トラフィックを許可するためにファイアウォールで開く必要があるポートを次の表に示します。 この表では、"*イン*" ("*受信*") は、着信クライアント要求がデバイスにアクセスする方向を意味します。 "*アウト*" (または "*送信*") は Data Box デバイスがデプロイを超えて外部にデータを送信する方向を意味します。 たとえば、データがインターネットに送信される場合があります。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>次のステップ

* [Azure Data Box をデプロイする](data-box-deploy-ordered.md)
