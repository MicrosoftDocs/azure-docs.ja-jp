---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684435"
---
移動中のデータの場合:

- デバイスと Azure 間で移動するデータの場合、標準 TLS 1.2 を使用します。 TLS 1.1 以前へのフォールバックはありません。 TLS 1.2 がサポートされていない場合は、エージェント通信はブロックされます。 ポータルや SDK の管理操作にも TLS 1.2 が必要です。
- クライアントがブラウザーのローカル Web UI を介してデバイスにアクセスする場合、標準 TLS 1.2 が既定の安全なプロトコルとして使用されます。

    - ベスト プラクティスとして、TLS 1.2 を使用するようにブラウザーを構成することをお勧めします。
    - お使いのブラウザーが TLS 1.2 をサポートしていない場合、TLS 1.1 または TLS 1.0 を使用できます。
- データ サーバーからデータをコピーするときに、そのデータを保護するには、暗号化に SMB 3.0 を使用することをお勧めします。
