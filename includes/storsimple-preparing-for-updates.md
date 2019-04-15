---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891009"
---
## <a name="preparing-for-updates"></a>更新プログラムの準備
更新プログラムをスキャンして適用する前に、次の手順を実行する必要があります。

1. デバイス データのクラウド スナップショットを取得します。
2. コントローラーの固定 IP アドレスがルーティング可能でインターネットに接続できることを確認します。 これらの固定 IP は、デバイスに更新プログラムを提供するために使用されます。 デバイスの Windows PowerShell インターフェイスから、各コントローラーで次のコマンドレットを実行してこれをテストできます。
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **固定 IP でインターネットに接続できるときの Test-Connection のサンプル出力**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

これらの手動の事前チェックが正常に完了した後で、更新プログラムのスキャンとインストールを開始できます。

