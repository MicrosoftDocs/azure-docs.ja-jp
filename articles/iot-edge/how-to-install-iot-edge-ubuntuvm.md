---
title: Ubuntu 仮想マシン上で Azure IoT Edge を実行する |Microsoft Docs
description: Ubuntu 16.04 Azure Marketplace 仮想マシンでの Azure IoT Edge のセットアップ手順について説明します
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 8275bceca1a18f49eb7eeece66a3866d77c47635
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796175"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu 仮想マシン上で Azure IoT Edge を実行する

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。

IoT Edge ランタイムの動作とランタイムに含まれるコンポーネントについては、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、あらかじめ構成された [Azure IoT Edge on Ubuntu Azure Marketplace オファー](https://aka.ms/azure-iot-edge-ubuntuvm)を使用して、Ubuntu 16.04 仮想マシン上で Azure IoT Edge ランタイムを実行する手順を示します。 

最初の起動時、Azure IoT Edge on Ubuntu VM には、Azure IoT Edge ランタイムの最新バージョンがプレインストールされています。 また、接続文字列を設定し、ランタイムを再起動するためのスクリプトも含まれています。これは、Azure VM ポータルまたは Azure コマンド ラインを通じてリモートでトリガーできます。これにより、SSH やリモート デスクトップ セッションを起動することなく、IoT Edge デバイスを簡単に構成し、接続することができます。 このスクリプトは、IoT Edge クライアントが完全にインストールされるまで、接続文字列の設定を待機します。そのため、その操作を自動化に組み込む必要はありません。

## <a name="deploy-from-the-azure-marketplace"></a>Azure Marketplace からデプロイする
1.  [Azure IoT Edge on Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace オファーに移動するか、[Azure Marketplace](https://azuremarketplace.microsoft.com/) で "Azure IoT Edge on Ubuntu" を検索します
2.  **[今すぐ入手する]** を選択し、次のダイアログで **[続行]** を選択します。
3.  Azure portal に移動したら、 **[作成]** を選択し、ウィザードに従って VM をデプロイします。 
    *   VM を初めて試す場合は、パスワードを使用し、パブリック受信ポート メニューで SSH を有効にするのが最も簡単です。 
    *   リソース消費の激しいワークロードがある場合は、CPU やメモリを追加して、仮想マシンのサイズをアップグレードしてください。
4.  仮想マシンがデプロイされたら、IoT Hub に接続するように構成します。
    1.  IoT Hub で作成された IoT Edge デバイスから、デバイス接続文字列をコピーします (このプロセスに詳しくない場合は、[Azure portal から新しい Azure IoT Edge デバイスを登録する](how-to-register-device-portal.md)方法のハウツー ガイドをご覧ください)
    1.  Azure portal から新しく作成された仮想マシン リソースを選択し、 **[実行コマンド]** オプションを開きます
    1.  **[RunShellScript]** オプションを選択します
    1.  コマンド ウィンドウから、デバイス接続文字列を使用して下記のスクリプトを実行します。`/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  **[実行]** を選択します
    1.  しばらく待つと、接続文字列が正常に設定されたことを示すメッセージが画面に表示されます。


## <a name="deploy-from-the-azure-portal"></a>Azure portal からデプロイする
Azure portal から、"Azure IoT Edge" を検索し、"**Ubuntu Server 16.04 LTS + Azure IoT Edge runtime**" を選択して VM の作成ワークフローを開始します。 そこから、上記の「Azure Marketplace からデプロイする」にある手順 3 と 4 を完了します。

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
    
   1. 使用するサブスクリプションの SubscriptionID フィールドをコピーします。

   1. コピーした ID で作業中のサブスクリプションを設定します。
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. 新しいリソース グループを作成します (または、次の手順で既存のリソース グループを指定します)。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 仮想マシンの利用規約に同意します。 先に利用規約を確認したい場合は、「[Azure Marketplace からデプロイする](#deploy-from-the-azure-marketplace)」の手順に従ってください。

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. 新しい仮想マシンを作成します。

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. デバイス接続文字列を設定します (このプロセスに詳しくない場合は、[Azure CLI を使用して新しい Azure IoT Edge デバイスを登録する](how-to-register-device-cli.md)方法のハウツー ガイドをご覧ください)。

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

セットアップ後にこの VM に SSH する場合は、次のコマンドで publicIpAddress を使用します。`ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>次の手順

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
