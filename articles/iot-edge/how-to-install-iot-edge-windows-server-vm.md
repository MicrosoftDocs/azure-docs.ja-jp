---
title: Windows Server 仮想マシンで Azure IoT Edge を実行する | Microsoft Docs
description: Windows Server Marketplace 仮想マシンでの Azure IoT Edge のセットアップ手順
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494062"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Windows Server 仮想マシンで Azure IoT Edge を実行する

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。

IoT Edge ランタイムの動作とランタイムに含まれるコンポーネントについては、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、[Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace オファーを使用して Windows Server 2019 仮想マシンで Azure IoT Edge ランタイムを実行する手順を紹介します。 他のバージョンで使用する場合は、Windows に [Azure IoT Edge ランタイムをインストール](how-to-install-iot-edge-windows.md)する手順に従ってください。

## <a name="deploy-from-the-azure-marketplace"></a>Azure Marketplace からデプロイする

1. [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace オファーに移動するか、[Azure Marketplace](https://azuremarketplace.microsoft.com/) で “Windows Server” を検索します。
2. **[今すぐ入手する]** を選択します。
3. **ソフトウェア プラン**で、「Windows Server 2019 Datacenter Server Core with Containers」を検索し、次のダイアログで **[続行]** を選択します。
    * 他のバージョンの Windows Server with Containers でこれらの手順を使用することもできます。
4. Azure portal に移動したら、 **[作成]** を選択し、ウィザードに従って VM をデプロイします。
    * VM を初めて試す場合は、パスワードを使用し、パブリック受信ポート メニューで RDP と SSH を有効にするのが最も簡単です。
    * リソース消費の激しいワークロードがある場合は、CPU やメモリを追加して、仮想マシンのサイズをアップグレードしてください。
5. 仮想マシンがデプロイされたら、IoT Hub に接続するように構成します。
    1. IoT Hub で作成された IoT Edge デバイスからデバイス接続文字列をコピーします。 手順については、「[Azure portal で接続文字列を取得する](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)」を参照してください。
    1. Azure portal から新しく作成された仮想マシン リソースを選択し、 **[実行コマンド]** オプションを開きます
    1. **[RunPowerShellScript]** オプションを選択します
    1. 使用するデバイスの接続文字列を使用して、下記のスクリプトをコマンド ウィンドウにコピーします。

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. **[Run]\(実行\)** を選択してスクリプトを実行し、IoT Edge ランタイムをインストールして接続文字列を設定します。
    1. 1、2 分すると、Edge ランタイムがインストールされ、正常にプロビジョニングされたことを示すメッセージが表示されます。

## <a name="deploy-from-the-azure-portal"></a>Azure portal からデプロイする

1. Azure portal で "Windows Server" を検索し、**Windows Server 2019 Datacenter** を選択して VM の作成ワークフローを開始します。
2. **ソフトウェア プランの選択**で、"Windows Server 2019 Datacenter Server Core with Containers" を選択してから、 **[作成]** を選択します。
3. 上記の説明の「Azure Marketplace からデプロイする」にある手順 5 を実行します。

## <a name="deploy-from-azure-cli"></a>Azure CLI からデプロイする

1. デスクトップで Azure CLI を使用している場合は、まずログインします。

   ```azurecli-interactive
   az login
   ```

1. 複数のサブスクリプションがある場合は、次のようにして、使用するサブスクリプションを選択します。
   1. サブスクリプションを一覧表示します。

      ```azurecli-interactive
      az account list --output table
      ```

   1. 使用するサブスクリプションの SubscriptionID フィールドをコピーします
   1. コピーした ID を使用して、次のコマンドを実行します。

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. 新しいリソース グループを作成します (または、次の手順で既存のリソース グループを指定します)。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 新しい仮想マシンを作成します。

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * このコマンドはパスワードを求めるプロンプトを表示しますが、オプション `--admin-password` を追加 することで、スクリプトにより簡単にこれを設定できます
   * Windows Server Core イメージは、リモート デスクトップでのみコマンド ラインをサポートするため、完全なデスクトップ エクスペリエンスが必要な場合は、イメージとして `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` を指定します

1. デバイス接続文字列を設定します (このプロセスに詳しくない場合は、「[Azure CLI を使用して接続文字列を取得する](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli)」の手順に従ってください)。

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>次のステップ

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。

Windows 仮想マシンの使用に関する詳細を [Windows Virtual Machines のドキュメント](https://docs.microsoft.com/azure/virtual-machines/windows/)で参照してください。

セットアップ後にこの VM に SSH で接続する場合は、リモート デスクトップまたはリモート PowerShell を使用して、[Windows Server 用の OpenSSH のインストール](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) ガイドに従ってください。
