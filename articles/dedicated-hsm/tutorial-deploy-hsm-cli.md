---
title: 'チュートリアル: Azure CLI を使用して既存の仮想ネットワークにデプロイする - Azure Dedicated HSM | Microsoft Docs'
description: CLI を使用して専用 HSM を既存の仮想ネットワークにデプロイする方法について示すチュートリアル
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 1ca8bc5c82540b0dc02959d26b452554ef294368
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200531"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>チュートリアル:Azure CLI を使用して既存の仮想ネットワークに HSM をデプロイする

Azure Dedicated HSM では、完全な管理制御と完全な管理責任が備わった、お客様専用の物理デバイスが提供されます。 物理デバイスを使用するので、確実に容量が効果的に管理されるよう、Microsoft がデバイスの割り当てを制御する必要があります。 そのため、Dedicated HSM サービスは通常、Azure サブスクリプション内でリソース プロビジョニング用には表示されません。 Dedicated HSM サービスへのアクセスを必要とする Azure のお客様は、まず、Microsoft アカウント担当者に連絡して、Dedicated HSM サービスへの登録を依頼しなければなりません。 このプロセスが正常に完了した場合にのみ、プロビジョニングが可能になります。 

このチュートリアルでは、次の場合の一般的なプロビジョニング プロセスを示します。

- お客様の元に既に仮想ネットワークがある
- 仮想マシンがある
- その既存の環境に HSM リソースを追加する必要がある。

高可用性の複数リージョン デプロイの一般的なアーキテクチャは、次のようになります。

![複数リージョン デプロイ](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

このチュートリアルでは、既存の仮想ネットワーク (上の VNET 1 を参照) に対する、HSM のペアと必須の ExpressRoute ゲートウェイ (上の Subnet 1 を参照) の統合を中心に説明しています。  他のすべてのリソースは、標準の Azure リソースです。 同じ統合プロセスを、上の VNET 3 における Subnet 4 の HSM に使用できます。

## <a name="prerequisites"></a>前提条件

Azure Dedicated HSM は現在、Azure portal では使用できません。 サービスに対するすべての操作は、コマンドラインまたは PowerShell を使用して行います。 このチュートリアルでは、Azure Cloud Shell でコマンドライン インターフェイス (CLI) を使用します。 Azure CLI を使用するのが初めての場合は、こちらの [Azure CLI 2.0 の開始](/cli/azure/get-started-with-azure-cli)に関するページの開始手順に従います。

想定:

- Azure Dedicated HSM の登録プロセスが完了している
- サービスの使用が承認されている。 そうでない場合は、お客様の Microsoft アカウント担当者に詳細を問い合わせます。
- これらのリソース用にリソース グループが作成してあり、このチュートリアルでデプロイされる新しいものは、そのグループに加えられる。
- 上の図のとおりに、必要な仮想ネットワーク、サブネット、仮想マシンが既に作成してあり、そのデプロイに 2 つの HSM を統合しようとしている。

以下のすべての手順では、お客様が既に Azure portal に移動して Cloud Shell を開いていることが想定されています (ポータルの右上の [\>\_] を選択します)。

## <a name="provisioning-a-dedicated-hsm"></a>専用 HSM のプロビジョニング

HSM のプロビジョニングと、ExpressRoute ゲートウェイを介した既存の仮想ネットワークへの統合は、SSH を使用して検証されます。 この検証は、追加の構成アクティビティに備えて、HSM デバイスの到達可能性と基本的な可用性を確保するために役立ちます。

### <a name="validating-feature-registration"></a>機能登録の検証

前述のように、プロビジョニング アクティビティでは、Dedicated HSM サービスがお客様のサブスクリプションに登録されている必要があります。 それを検証するには、Azure portal の Cloud Shell で以下のコマンドを実行します。

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

コマンドで "Registered" の状態が返される必要があります (以下を参照)。 コマンドによって "Registered" が返されない場合は、このサービスへの登録が必要です。お客様の Microsoft アカウント担当者にお問い合わせください。

![サブスクリプションの状態](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM リソースの作成

HSM リソースを作成する前に、必要な前提条件リソースがいくつかあります。 コンピューティング、HSM、およびゲートウェイのためのサブネット範囲が含まれた仮想ネットワークが必要です。 以下のコマンドは、このような仮想ネットワークを作成するコマンドの例です。

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>仮想ネットワークで注意する必要がある最も重要な構成は、HSM デバイス用のサブネットで委任を "Microsoft.HardwareSecurityModules/dedicatedHSMs" に設定することです。  HSM のプロビジョニングは、このオプションが設定されていないと機能しません。

ネットワークを構成したら、次の Azure CLI コマンドを使用して HSM をプロビジョニングします。

1. [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) コマンドを使用して、最初の HSM をプロビジョニングします。 HSM には hsm1 という名前が付けられます。 ご自分のサブスクリプションに置き換えます。

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   このデプロイには、完了までに 25 - 30 分ほどかかります。その時間の多くは、HSM デバイスに費やされます。

1. 現在の HSM を表示するには、[az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) コマンドを実行します。

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. 次のコマンドを使用して、2 つ目の HSM をプロビジョニングします。

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) コマンドを実行して、現在の HSM に関する詳細を表示します。

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

他にも便利なコマンドがいくつかあります。 HSM を更新するには、[az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) コマンドを使用します。

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

HSM を削除するには、[az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) コマンドを使用します。

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>デプロイの確認

デバイスがプロビジョニングされたことを確認し、デバイスの属性を表示するには、次のコマンド セットを実行します。 リソース グループが適切に設定され、リソース名がパラメーター ファイル内のものとまったく同じであることを確認します。

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

出力は次の出力のようになります。

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

また、[Azure Resource Explorer](https://resources.azure.com/) を使用してリソースを表示できるようになりました。   エクスプローラーで、左側の [サブスクリプション]、Dedicated HSM 用の特定のサブスクリプション、[リソース グループ]、お客様が使用したリソース グループの順に展開し、最後に [リソース] 項目を選択します。

## <a name="testing-the-deployment"></a>デプロイのテスト

デプロイのテストでは、HSM にアクセスできる仮想マシンに接続し、その後、HSM デバイスに直接接続します。 これらの操作によって、HSM に到達可能であることを確認します。
仮想マシンへの接続には SSH ツールが使用されます。 コマンドは次のようになりますが、管理者名と DNS 名はお客様がパラメーターで指定したものになります。

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

VM の IP アドレスは、上のコマンドで DNS 名の代わりに使用することもできます。 コマンドが成功した場合は、パスワードの入力を求めるメッセージが表示されるので、入力する必要があります。 仮想マシンへのログオン後、HSM に関連付けられているネットワーク インターフェイス リソースについてポータルに表示されるプライベート IP アドレスを使用して、HSM にサインインできます。

![コンポーネント一覧](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>[非表示の型の表示] チェック ボックスに注意してください。これをオンにした場合は、HSM リソースが表示されます。

上のスクリーンショットでは、"HSM1_HSMnic" または "HSM2_HSMnic" をクリックすると、適切なプライベート IP アドレスが表示されます。 その他に、上で使用された `az resource show` コマンドを使って、適切な IP アドレスを特定することもできます。 

正しい IP アドレスを取得したら、そのアドレスに置換して次のコマンドを実行します。

`ssh tenantadmin@10.0.2.4`

成功の場合、パスワードの入力を求められます。 既定のパスワードは PASSWORD です。最初に、お客様のパスワードの変更を求めるメッセージが HSM によって表示されるので、強力なパスワードを設定し、お客様の組織にとって望ましい何らかのメカニズムを使用してパスワードを保存し、紛失を防ぎます。

>[!IMPORTANT]
>このパスワードを紛失した場合、HSM のリセットが必要になり、お客様のキーが失われることになります。

SSH を使用して HSM に接続されている場合、HSM が動作していることを確認するには、次のコマンドを実行します。

`hsm show`

出力は次の画像のようになります。

![PowerShell ウィンドウの出力を示すスクリーンショット。](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

この時点で、高可用性のためのリソースをすべて割り当て、2 つの HSM をデプロイし、アクセスと動作状態を検証しました。 以降の構成やテストでは、HSM デバイス自体の操作が多くなります。 そのため、Thales Luna Network HSM 7 の管理ガイド第 7 章の指示に従って、HSM を初期化し、パーティションを作成する必要があります。 Thales のカスタマー サポート ポータルで登録し、利用者 ID を取得すると、すべてのドキュメントとソフトウェアを Thales から直接ダウンロードして入手できます。 すべての必須コンポーネントを入手するには、クライアント ソフトウェア バージョン 7.2 をダウンロードします。

## <a name="delete-or-clean-up-resources"></a>リソースの削除またはクリーンアップ

HSM デバイスだけでの作業を完了したら、それをリソースとして削除し、空きプールに戻すことができます。 これを行う際の明らかな問題は、デバイス上にあるお客様の機密データです。 デバイスを "ゼロにする" 最善の方法は、HSM 管理者パスワードを 3 回間違えることです (注: これはアプライアンス管理者ではなく、実際の HSM 管理者です)。 キー マテリアルを保護するための安全対策として、デバイスがゼロの状態になるまでは、そのデバイスを Azure リソースとして削除することはできません。

> [!NOTE]
> Thales デバイスの構成に問題がある場合は、[Thales カスタマー サポート](https://safenet.gemalto.com/technical-support/)に問い合わせる必要があります。

このリソース グループ内のすべてのリソースでの作業が完了したら、次のコマンドでそれらをすべて削除できます。

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>次のステップ

チュートリアルの手順を完了した後、Dedicated HSM リソースがプロビジョニングされ、必要な HSM と、その HSM との通信を可能にするための追加のネットワーク コンポーネントを備えた仮想ネットワークが確立されます。  これで、お客様にとって望ましいデプロイ アーキテクチャに必要なリソースを追加し、このデプロイを補完できるようになりました。 お客様のデプロイの計画に役立つ詳細情報については、概念に関する各ドキュメントを参照してください。
プライマリ リージョンの 2 つの HSM によってラック レベルの可用性に対処し、セカンダリ リージョンの 2 つの HSM によってリージョンの可用性に対処する設計をお勧めします。 

* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [サポート可能性](supportability.md)
* [Monitoring](monitoring.md)
