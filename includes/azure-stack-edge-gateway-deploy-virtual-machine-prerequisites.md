---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500380"
---
Azure Stack Edge デバイスに VM を展開する前に、Azure PowerShell 経由で Azure Resource Manager を介してデバイスに接続するようにクライアントを構成する必要があります。 詳細な手順については、「[Azure Stack Edge デバイスで Azure Resource Manager に接続する](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)」を参照してください。

次の手順に従い、クライアントからデバイスにアクセスできることを確認します。 (Azure Resource Manager への接続時に、この構成は完了しています。 ここでは、構成が正常に完了したことを確認するのみです。) 

1. Azure Resource Manager の通信が機能していることを確認します。 次のように入力します。     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. ローカル デバイス API を呼び出して認証を行います。 次のように入力します。 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Azure Resource Manager を介して接続するには、ユーザー名 (*EdgeARMuser*) とパスワードを入力します。

1. Kubernetes で **コンピューティング** を構成した場合は、この手順を省略できます。 続行して、コンピューティング用ネットワークを有効にしたことを確認します。 ローカル ユーザー インターフェイスで、 **[Compute]** 設定に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。 作成する VM は、このポートおよび関連付けられているネットワークに接続されている仮想スイッチに接続されます。 VM に使用する IP アドレスと一致するネットワークを選択してください。  

    ![コンピューティング設定を有効にする方法を示すスクリーンショット。](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge によって作成および管理されます。 現時点では、Kubernetes に特定の IP を入力しないでください。 数分でコンピューティングが有効になります。

    > [!NOTE]
    > GPU VM を作成する場合、インターネットに接続されているネットワーク インターフェイスを選択します。 これにより、デバイスに GPU 拡張機能をインストールできます。


