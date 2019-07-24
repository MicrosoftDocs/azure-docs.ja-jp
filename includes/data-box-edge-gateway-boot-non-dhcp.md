---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556584"
---
非 DHCP 環境内で起動する場合は、次の手順に従って、ご使用の Data Box Gateway の仮想マシンをデプロイします。

1. [デバイスの Windows PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。
2. `Get-HcsIpAddress` コマンドレットを使用して、ご使用の仮想デバイス上で有効なネットワーク インターフェイスの一覧を表示します。 デバイスで単一のネットワーク インターフェイスが有効になっている場合、このインターフェイスに割り当てられる既定の名前は `Ethernet`です。

    このコマンドレットの使用例を次に示します。

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. `Set-HcsIpAddress` コマンドレットを使用してネットワークを構成します。 次の例を参照してください。

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

