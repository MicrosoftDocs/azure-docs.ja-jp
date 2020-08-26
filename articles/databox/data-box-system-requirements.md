---
title: Microsoft Azure Data Box のシステム要件 | Microsoft Docs
description: Azure Data Box と Data Box に接続するクライアントの重要なシステム要件について学習します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 71f499e1cdd7ccf22d90b21ce04299798978a828
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926605"
---
# <a name="azure-data-box-system-requirements"></a>Data Box のシステム要件

この記事では、Microsoft Azure Data Box と Data Box に接続するクライアントの重要なシステム要件について説明します。 この情報は、Data Box をデプロイする前によく確認し、デプロイ時やそれ以降の操作時にも、必要に応じて繰り返し参照することをお勧めします。

システム要件は次のとおりです。

* **Data Box に接続するホストのソフトウェア要件** - サポートされているプラットフォーム、ローカル Web UI 用のブラウザー、SMB クライアント、および Data Box に接続するホストのその他の要件についての情報を示します。
* **Data Box のネットワーク要件** - Data Box の最適な操作のためのネットワーク要件についての情報を示します。


## <a name="software-requirements"></a>ソフトウェア要件

ソフトウェア要件には、サポートされるオペレーティング システム、ローカル Web UI でサポートされるブラウザー、および SMB クライアントに関する情報が含まれます。

### <a name="supported-operating-systems-for-clients"></a>クライアントでサポートされるオペレーティング システム

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-filesystems-for-linux-clients"></a>Linux クライアントでサポートされるファイル システム

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

お客様のデータセンターには、高速ネットワークが必要です。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用してデータをコピーできますが、コピーの速度が影響を受けます。

### <a name="port-requirements"></a>ポートの要件

SMB または NFS トラフィックを許可するためにファイアウォールで開く必要があるポートを次の表に示します。 この表では、"*イン*" ("*受信*") は、着信クライアント要求がデバイスにアクセスする方向を意味します。 "*アウト*" ("*送信*") は Data Box デバイスがデプロイを超えて外部に (たとえば、インターネットに) データを送信する方向を意味します。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>次のステップ

* [Azure Data Box をデプロイする](data-box-deploy-ordered.md)
