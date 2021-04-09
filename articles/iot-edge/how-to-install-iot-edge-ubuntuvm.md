---
title: Ubuntu 仮想マシン上で Azure IoT Edge を実行する |Microsoft Docs
description: Ubuntu 18.04 LTS 仮想マシンへの Azure IoT Edge のセットアップ手順
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1cd89f3f772effce4997fb69b37858ce2077c1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201087"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu 仮想マシン上で Azure IoT Edge を実行する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。

IoT Edge ランタイムの動作とランタイムに含まれるコンポーネントについては、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、事前に指定されたデバイス接続文字列でインストールおよび構成された Azure IoT Edge ランタイムを使用して、 Ubuntu 18.04 LTS 仮想マシンをデプロイする手順について一覧で説明します。 このデプロイは、[iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) プロジェクト リポジトリに保持されている [cloud-init](../virtual-machines/linux/using-cloud-init.md
) ベースの[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を使用して行われます。

最初の起動時に、Ubuntu 18.04 LTS 仮想マシンは、[cloud-init 経由で最新バージョンの Azure IoT Edge ランタイムをインストールします](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt)。 また、ランタイムが開始される前に、指定された接続文字列を設定します。これにより、SSH またはリモート デスクトップ セッションを開始することなしに、IoT Edge デバイスを簡単に構成して接続することができます。

>[!NOTE]
>この記事で使用するテンプレートでは IoT Edge バージョン 1.1 がインストールされます。

## <a name="deploy-using-deploy-to-azure-button"></a>[Deploy to Azure] ボタンを使用してデプロイする

[[Deploy to Azure] ボタン](../azure-resource-manager/templates/deploy-to-azure-button.md)を使用すると、GitHub に保持されている [Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)をスムーズにデプロイできます。  このセクションでは、[iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) プロジェクト リポジトリに含まれる [Deploy to Azure] ボタンの使用方法を示します。  


1. ここでは、iotedge-vm-deploy の Azure Resource Manager テンプレートを使用して、Azure IoT Edge 対応の Linux VM をデプロイします。  開始するには、以下のボタンをクリックします。

    [![iotedge-vm-deploy の [Deploy to Azure] ボタン](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 新しく起動されたウィンドウで、使用可能なフォームフィールドに入力します。

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm-deploy テンプレートを示すスクリーンショット](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **サブスクリプション**:仮想マシンをデプロイするためのアクティブな Azure サブスクリプション。

    **[リソース グループ]** :仮想マシンとそれに関連付けられたリソースを格納する、既存または新しく作成されたリソース グループ。

    **[DNS ラベル プレフィックス]** :仮想マシンのホスト名のプレフィックスとして使用される、あなたが選択した必要値。

    **管理ユーザー名**: ユーザー名。デプロイ時にルート権限が与えられます。

    **[デバイス接続文字列]** :目的の [IoT Hub](../iot-hub/about-iot-hub.md) 内に作成された、デバイス向けの [デバイス接続文字列](./how-to-register-device.md)。

    **[VM サイズ]** :デプロイする仮想マシンの [サイズ](../cloud-services/cloud-services-sizes-specs.md)

    **[Ubuntu OS バージョン]** :ベース仮想マシンにインストールする Ubuntu OS のバージョン。

    **[場所]** :仮想マシンをデプロイする [地理的リージョン](https://azure.microsoft.com/global-infrastructure/locations/)。選択したリソース グループの場所が規定値となります。

    **[認証タイプ]** :**sshPublicKey** または **パスワード** のどちらか好きな方を選択します。

    **[管理パスワードまたはキー]** :認証タイプの選択に応じて、SSH 公開キーの値、またはパスワードの値。

    すべてのフィールドへの入力を終えたら、ページの下部にあるチェックボックスをオンにして使用条件に同意し、 **[購入]** を選択してデプロイを開始します。

1. デプロイが正常に完了したことを確認します。  仮想マシン リソースが、選択したリソース グループにデプロイされている必要があります。  マシン名をメモしておきます。この名前は `vm-0000000000000` の形式である必要があります。 また、関連付けられている **DNS 名** をメモしておきます。この名前は `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com の形式である必要があります。

    **DNS 名** は、Azure portal 内に新しく展開された仮想マシンの **概要** セクションから取得できます。

    > [!div class="mx-imgBorder"]
    > [![Iotedge VM の DNS 名を示すスクリーンショット](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. セットアップ後にこの VM に SSH する場合は、以下のコマンドで関連付けられている **DNS 名** を使用します。`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Azure CLI からデプロイする

1. 以下の Azure CLI iot 拡張をインストールしていることを確認します。
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. 次に、デスクトップで Azure CLI を使用している場合は、まずログインします。

   ```azurecli-interactive
   az login
   ```

1. 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。
   1. サブスクリプションを一覧表示します。

      ```azurecli-interactive
      az account list --output table
      ```

   1. 使用するサブスクリプションの SubscriptionID フィールドをコピーします。

   1. コピーした ID で、作業中のサブスクリプションを設定します。

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 新しいリソース グループを作成します (または、次の手順で既存のリソース グループを指定します)。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 新しい仮想マシンを作成します。

    `password` の **authenticationType** を使用するには、以下の例を参照してください。

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    SSH キーを使用して認証するには、`sshPublicKey` の **authenticationType** を指定し、次に **adminPasswordOrKey** パラメーター中で SSH キーの値を指定します。  次に例を示します。

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. デプロイが正常に完了したことを確認します。  仮想マシン リソースが、選択したリソース グループにデプロイされている必要があります。  マシン名をメモしておきます。この名前は `vm-0000000000000` の形式である必要があります。 また、関連付けられている **DNS 名** をメモしておきます。この名前は `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com の形式である必要があります。

    **DNS 名** は、前の手順の JSON 形式の出力から取得できます。具体的には、 **[パブリック SSH]** エントリの一部をなす **[出力]** セクション内から。  このエントリの値を使用して、新しくデプロイされたマシンに SSH できます。

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS 名** は、Azure portal 内に新しくデプロイされた仮想マシンの **[概要]** セクションから取得できます。

    > [!div class="mx-imgBorder"]
    > [![Iotedge VM の DNS 名を示すスクリーンショット](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. セットアップ後にこの VM に SSH する場合は、以下のコマンドで関連付けられている **DNS 名** を使用します。`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>次のステップ

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。

SSH またはその他の受信接続を使用して VM にアクセスするポートを開く場合は、Azure Virtual Machine のドキュメントの「[Linux VM へのポートとエンドポイントを開く](../virtual-machines/linux/nsg-quickstart.md)」のページを参照してください。