---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 67de9042af11a2b17c4b65f8225ecd0580b95466
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263477"
---
| ポート番号| インまたはアウト | ポート範囲| 必須|   メモ |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|アウト|WAN |いいえ |送信ポートは、更新プログラムを取得するためのインターネット アクセスに使用します。 <br>送信 Web プロキシは、ユーザーが構成できます。 |
| TCP 443 (HTTPS)|アウト|WAN|[はい]|送信ポートは、クラウドのデータへのアクセスに使用します。<br>送信 Web プロキシは、ユーザーが構成できます。|   
| UDP 53 (DNS)|アウト|WAN|場合により必須<br>メモを参照|このポートは、インターネット ベースの DNS サーバーを使用する場合にのみ必要です。<br>ローカル DNS サーバーを使用することをお勧めします。 |
| UDP 123 (NTP)|アウト|WAN|場合により必須<br>メモを参照|このポートは、インターネット ベースの NTP サーバーを使用する場合にのみ必要です。  |
| UDP 67 (DHCP)|アウト|WAN|場合により必須<br>メモを参照|このポートは、DHCP サーバーを使用する場合にのみ必要です。  |
| TCP 80 (HTTP)|イン|LAN|[はい]|このポートは、ローカル管理に使用するデバイスのローカル UI 用の受信ポートです。 <br>HTTP 経由でのローカル UI へのアクセスは、自動的に HTTPS にリダイレクトされます。  |
| TCP 443 (HTTPS)|イン|LAN|[はい]|このポートは、ローカル管理に使用するデバイスのローカル UI 用の受信ポートです。 |