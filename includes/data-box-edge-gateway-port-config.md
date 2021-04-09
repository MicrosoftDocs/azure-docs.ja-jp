---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86218451"
---
| ポート番号| インまたはアウト | ポート範囲| 必須|   Notes |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|アウト|WAN |いいえ|送信ポートは、更新プログラムを取得するためのインターネット アクセスに使用します。 <br>送信 Web プロキシは、ユーザーが構成できます。 |
| TCP 443 (HTTPS)|アウト|WAN|はい|送信ポートは、クラウドのデータへのアクセスに使用します。<br>送信 Web プロキシは、ユーザーが構成できます。|
| UDP 123 (NTP)|アウト|WAN|場合により必須<br>メモを参照|このポートは、インターネット ベースの NTP サーバーを使用する場合にのみ必要です。  |   
| UDP 53 (DNS)|アウト|WAN|場合により必須<br>メモを参照|このポートは、インターネット ベースの DNS サーバーを使用する場合にのみ必要です。<br>ローカル DNS サーバーを使用することをお勧めします。 |
| TCP 5985 (WinRM)|アウト/イン|LAN|場合により必須<br>メモを参照|HTTP 上のリモート PowerShell を介してデバイスに接続するには、このポートが必要です。  |
| UDP 67 (DHCP)|アウト|LAN|場合により必須<br>メモを参照|このポートは、ローカル DHCP サーバーを使用する場合にのみ必要です。  |
| TCP 80 (HTTP)|アウト/イン|LAN|はい|このポートは、ローカル管理に使用するデバイスのローカル UI 用の受信ポートです。 <br>HTTP 経由でのローカル UI へのアクセスは、自動的に HTTPS にリダイレクトされます。  |
| TCP 443 (HTTPS)|アウト/イン|LAN|はい|このポートは、ローカル管理に使用するデバイスのローカル UI 用の受信ポートです。 |
| TCP 445 (SMB)|/|LAN|場合により必須<br>メモを参照|このポートは、SMB を介して接続する場合にのみ必要です。 |
| TCP 2049 (NFS)|/|LAN|場合により必須<br>メモを参照|このポートは、NFS を介して接続する場合にのみ必要です。 |
