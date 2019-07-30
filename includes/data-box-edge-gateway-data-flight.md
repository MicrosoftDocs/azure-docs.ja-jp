---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181812"
---
移動中のデータの場合:

- デバイスと Azure の間を移動するデータには標準 TLS 1.2 が使用されます。 TLS 1.1 以前へのフォールバックはありません。 TLS 1.2 がサポートされていない場合は、エージェント通信がブロックされます。 TLS 1.2 はまた、ポータルや SDK の管理にも必要です。
- クライアントがブラウザーのローカル Web UI 経由でデバイスにアクセスする場合は、標準 TLS 1.2 が既定のセキュリティで保護されたプロトコルとして使用されます。

    - ベスト プラクティスとして、TLS 1.2 を使用するようにブラウザーを構成することをお勧めします。
    - ブラウザーで TLS 1.2 がサポートされていない場合は、TLS 1.1 または TLS 1.0 を使用できます。
- データ サーバーからデータをコピーする場合は、そのデータを保護するために暗号化付き SMB 3.0 を使用することをお勧めします。
