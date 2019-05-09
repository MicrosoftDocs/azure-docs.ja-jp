---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556764"
---
デバイスをリセットするには、データ ディスクのすべてのデータおよびデバイスのブート ディスクを安全に消去する必要があります。 

データ ディスクとブート ディスクの両方を消去する、またはデータ ディスクのみを消去するには、`Reset-HcsAppliance` コマンドレットを使用します。 `ClearData` および `BootDisk` スイッチによってデータ ディスクとブート ディスクをそれぞれ消去できます。

ローカル Web UI でデバイス リセットを使用する場合、データ ディスクのみが安全に消去されますが、ブート ディスクはそのまま維持されます。 ブート ディスクはデバイス構成を含みます。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. コマンド プロンプトに、次のコマンドを入力します。

    `Reset-HcsAppliance -ClearData -BootDisk`

    次の例は、コマンドレットの使用方法を示しています。
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
