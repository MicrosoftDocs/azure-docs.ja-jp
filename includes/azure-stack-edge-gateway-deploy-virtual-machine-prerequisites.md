---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 71d5a910e36762d096763c4f45a13cbdad47414d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730627"
---
Azure Stack Edge デバイスに VM を展開する前に、Azure PowerShell 経由で Azure Resource Manager を介してデバイスに接続するようにクライアントを構成する必要があります。 詳細な手順については、「[Azure Stack Edge デバイスで Azure Resource Manager に接続する](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)」を参照してください。

次の手順を使用して、クライアントからデバイスにアクセスできることを確認します。 Azure Resource Manager に接続したときに既にこの構成は完了しているため、次はその構成が正常に完了したことを確認します。 

1. 次のコマンドを実行して、Azure Resource Manager の通信が機能していることを確認します。     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. ローカル デバイス API を呼び出して認証を行うには、次のように入力します。 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Azure Resource Manager を介して接続するには、ユーザー名 *EdgeARMuser* とパスワードを入力します。

1. Kubernetes でコンピューティングを構成した場合は、この手順を省略できます。 それ以外の場合は、次の手順に従って、コンピューティング用にネットワーク インターフェイスを有効にしていることを確認します。 

   a. ローカル ユーザー インターフェイスで、 **[Compute]\(コンピューティング\)** 設定に移動します。  
   b. 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。 作成する VM は、このポートおよび関連付けられているネットワークに接続されている仮想スイッチに接続されます。 VM に使用する IP アドレスと一致するネットワークを選択してください。  

    ![[Compute]\(コンピューティング\) の [Network setting]\(ネットワーク設定\) ペインのスクリーンショット。](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. ネットワーク インターフェイスの **[Enable for compute]\(コンピューティングに対して有効にする\)** で、 **[はい]** を選択します。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge によって作成および管理されます。 現時点では、Kubernetes に特定の IP を入力しないでください。 数分でコンピューティングが有効になります。

    > [!NOTE]
    > GPU VM を作成する場合は、インターネットに接続されているネットワーク インターフェイスを選択します。 これにより、デバイスに GPU 拡張機能をインストールできます。


