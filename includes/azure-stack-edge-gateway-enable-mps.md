---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517568"
---
1. 開始する前に次の点を確認します。

    1. Azure の Azure Stack Edge リソースを使用して、[Azure Stack Edge Pro デバイスの構成とアクティブ化](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md)が完了していること。
    1. [Azure portal でこのデバイスのコンピューティングが構成済み](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute)であること。
    
1. [PowerShell インターフェイスに接続する](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
1. 次のコマンドを使用して、お使いのデバイスで MPS を有効にします。

    ```powershell
    Start-HcsGpuMPS
    ```