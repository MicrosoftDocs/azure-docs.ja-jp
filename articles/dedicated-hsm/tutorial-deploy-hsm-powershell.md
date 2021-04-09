---
title: 'チュートリアル: PowerShell を使用して既存の仮想ネットワークにデプロイする - Azure Dedicated HSM | Microsoft Docs'
description: PowerShell を使用して専用 HSM を既存の仮想ネットワークにデプロイする方法について示すチュートリアル
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 52b62e463edc51b3d93d7af69623a88abd9cc6be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108604"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>チュートリアル - PowerShell を使用して既存の仮想ネットワークに HSM をデプロイする

Azure Dedicated HSM サービスでは、完全な管理制御と完全な管理責任が備わった、お客様専用の物理デバイスが提供されます。 物理ハードウェアを提供するので、Microsoft は確実に容量が効果的に管理されるよう、これらのデバイスの割り当て方法を制御する必要があります。 そのため、Dedicated HSM サービスは通常、Azure サブスクリプション内でリソース プロビジョニング用には表示されません。 Dedicated HSM サービスへのアクセスを必要とする Azure のお客様は、まず、Microsoft アカウント担当者に連絡して、Dedicated HSM サービスへの登録を依頼しなければなりません。 このプロセスが正常に完了した場合にのみ、プロビジョニングが可能になります。
このチュートリアルの目的は、次の場合の一般的なプロビジョニング プロセスを示すことです。

- お客様の元に既に仮想ネットワークがある
- 仮想マシンがある
- その既存の環境に HSM リソースを追加する必要がある。

高可用性の複数リージョン デプロイの一般的なアーキテクチャは、次のようになります。

![複数リージョン デプロイ](media/tutorial-deploy-hsm-powershell/high-availability.png)

このチュートリアルでは、既存の仮想ネットワーク (上の VNET 1 を参照) に対する、HSM のペアと必須の ExpressRoute ゲートウェイ (上の Subnet 1 を参照) の統合を中心に説明しています。  他のすべてのリソースは、標準の Azure リソースです。 同じ統合プロセスを、上の VNET 3 における Subnet 4 の HSM に使用できます。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

Azure Dedicated HSM は、現時点では Azure portal で使用できません。そのため、サービスに対するすべての操作は、コマンドラインまたは PowerShell を使用して行います。 このチュートリアルでは、Azure Cloud Shell で PowerShell を使用します。 PowerShell を使用するのが初めての場合は、こちらの [Azure PowerShell の開始](/powershell/azure/get-started-azureps)に関するページの開始手順に従います。

想定:

- Azure Dedicated HSM の登録プロセスが完了しており、サービスの使用が承認されている。 そうでない場合は、お客様の Microsoft アカウント担当者に詳細をお問い合わせください。 
- これらのリソース用にリソース グループが作成してあり、このチュートリアルでデプロイされる新しいものはそのグループに加えられる。
- 上の図のとおりに、必要な仮想ネットワーク、サブネット、仮想マシンが既に作成してあり、そのデプロイに 2 つの HSM を統合しようとしている。

以下のすべての手順では、お客様が既に Azure portal に移動して Cloud Shell を開いていることが想定されています (ポータルの右上の [\>\_] を選択します)。

## <a name="provisioning-a-dedicated-hsm"></a>専用 HSM のプロビジョニング

HSM のプロビジョニングと、ExpressRoute ゲートウェイを介した既存の仮想ネットワークへの統合は、追加の構成アクティビティに備えて HSM デバイスの到達可能性と基本的な可用性を確保するために、SSH コマンドライン ツールを使用して検証されます。 以下のコマンドでは、HSM リソースと関連ネットワーク リソースを作成するために Resource Manager テンプレートを使用します。

### <a name="validating-feature-registration"></a>機能登録の検証

前述のように、プロビジョニング アクティビティでは、Dedicated HSM サービスがお客様のサブスクリプションに登録されている必要があります。 それを検証するには、Azure portal の Cloud Shell で次の PowerShell コマンドを実行します。 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

このコマンドで "Registered" の状態が返されてから先に進む必要があります (以下を参照)。  このサービスへの登録が済んでいない方は、Microsoft アカウント担当者にお問い合わせください。

![サブスクリプションの状態](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM リソースの作成

HSM デバイスは、お客様の仮想ネットワークにプロビジョニングされます。 これは、サブネットが必要であることを意味します。 仮想ネットワークと物理デバイスの間の通信を可能にするうえで、HSM は ExpressRoute ゲートウェイに依存しています。最後に、Gemalto クライアント ソフトウェアを使用して HSM デバイスにアクセスするために、仮想マシンが必要です。 使いやすいように、これらのリソースは対応するパラメーター ファイルと共にテンプレート ファイルに集められています。 ファイルは、HSMrequest@Microsoft.com で直接 Microsoft に問い合わせて入手できます。

ファイルを入手したら、パラメーター ファイルを編集して、お客様にとって望ましい名前をリソースに指定する必要があります。 つまり、"value": "" のある行を編集します。

- `namingInfix` HSM リソースの名前のプレフィックス
- `ExistingVirtualNetworkName` HSM のために使用される仮想ネットワークの名前
- `DedicatedHsmResourceName1` データセンター スタンプ 1 内の HSM リソースの名前
- `DedicatedHsmResourceName2` データセンター スタンプ 2 内の HSM リソースの名前
- `hsmSubnetRange` HSM のサブネット IP アドレス範囲
- `ERSubnetRange` VNET ゲートウェイのサブネット IP アドレス範囲

これらの変更の例は、次のとおりです。

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

関連付けられている Resource Manager テンプレート ファイルでは、この情報を使用して 6 つのリソースが作成されます。

- 指定された VNET 内の HSM のサブネット
- 仮想ネットワーク ゲートウェイのサブネット 
- VNET を HSM デバイスに接続する仮想ネットワーク ゲートウェイ
- ゲートウェイのパブリック IP アドレス
- スタンプ 1 内の HSM
- スタンプ 2 内の HSM

ファイルは、パラメーター値の設定後、使用するために Azure portal の Cloud Shell ファイル共有にアップロードする必要があります。 Azure portal で、右上の Cloud Shell 記号 ([\>\_]) をクリックします。これによって、画面の下部がコマンド環境になります。 これには [BASH] と [PowerShell] のオプションがあり、まだ設定されていない場合は [BASH] を選択する必要があります。

コマンド シェルのツール バーにはアップロード/ダウンロードのオプションがあり、これを選択して、お客様のファイル共有にテンプレート ファイルとパラメーター ファイルをアップロードする必要があります。

![ファイル共有](media/tutorial-deploy-hsm-powershell/file-share.png)

ファイルがアップロードされたら、リソースを作成する準備が整います。
新しい HSM リソースを作成する前に、前提条件となるいくつかのリソースが揃っていることを確認する必要があります。 コンピューティング、HSM、およびゲートウェイのためのサブネット範囲が含まれた仮想ネットワークが必要です。 以下のコマンドは、このような仮想ネットワークを作成するコマンドの例です。

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>仮想ネットワークで注意する必要がある最も重要な構成は、HSM デバイス用のサブネットで委任を "Microsoft.HardwareSecurityModules/dedicatedHSMs" に設定することです。  HSM のプロビジョニングは、これがないと機能しません。

すべての前提条件が満たされたら、次のコマンドを実行して Resource Manager テンプレートを使用します。その際に、一意の名前を使用して値を更新したことを確認します (少なくともリソース グループ名について)。

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

このコマンドは、完了までに約 20 分かかるはずです。 "-verbose" オプションを使用すれば、状態が継続的に表示されます。

![プロビジョニング状態](media/tutorial-deploy-hsm-powershell/progress-status.png)

"provisioningState": "Succeeded" と表示されて正常に完了したら、お客様の既存の仮想マシンにサインインし、SSH を使用して HSM デバイスの可用性を確認できます。

## <a name="verifying-the-deployment"></a>デプロイの確認

デバイスがプロビジョニングされたことを確認し、デバイスの属性を表示するには、次のコマンド セットを実行します。 リソース グループが適切に設定され、リソース名がパラメーター ファイル内のものとまったく同じであることを確認します。

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![プロビジョニングの状態](media/tutorial-deploy-hsm-powershell/progress-status2.png)

また、[Azure Resource Explorer](https://resources.azure.com/) を使用してリソースを表示できるようになりました。   エクスプローラーで、左側の [サブスクリプション]、Dedicated HSM 用の特定のサブスクリプション、[リソース グループ]、お客様が使用したリソース グループの順に展開し、最後に [リソース] 項目を選択します。

## <a name="testing-the-deployment"></a>デプロイのテスト

デプロイのテストでは、HSM にアクセスできる仮想マシンに接続し、その後、HSM デバイスに直接接続します。 これらの操作によって、HSM に到達可能であることを確認します。
仮想マシンへの接続には SSH ツールが使用されます。 コマンドは次のようになりますが、管理者名と DNS 名はお客様がパラメーターで指定したものになります。

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

使用するパスワードは、パラメーター ファイルにあるものです。
Linux VM へのログオン後、ポータルでリソース \<prefix>hsm_vnic に表示されるプライベート IP アドレスを使用して、HSM にログインできます。

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
IP アドレスを取得したら、次のコマンドを実行します。

`ssh tenantadmin@<ip address of HSM>`

成功の場合、パスワードの入力を求められます。 既定のパスワードは PASSWORD です。 お客様のパスワードの変更を求めるメッセージが HSM によって表示されるので、強力なパスワードを設定し、お客様の組織にとって望ましい何らかのメカニズムを使用してパスワードを保存し、紛失を防ぎます。  

>[!IMPORTANT]
>このパスワードを紛失した場合、HSM のリセットが必要になり、お客様のキーが失われることになります。

SSH を使用して HSM デバイスに接続されている場合、HSM が動作していることを確認するには、次のコマンドを実行します。

`hsm show`

出力は次の画像のようになります。

![hsm show コマンドの出力を示すスクリーンショット。](media/tutorial-deploy-hsm-powershell/output.png)

この時点で、高可用性のためのリソースをすべて割り当て、2 つの HSM をデプロイし、アクセスと動作状態を検証しました。 以降の構成やテストでは、HSM デバイス自体の操作が多くなります。 そのため、Gemalto Luna Network HSM 7 の管理ガイド第 7 章の指示に従って、HSM を初期化し、パーティションを作成する必要があります。 Gemalto のカスタマー サポート ポータルで登録し、利用者 ID を取得すると、すべてのドキュメントとソフトウェアを Gemalto から直接ダウンロードして入手できます。 すべての必須コンポーネントを入手するには、クライアント ソフトウェア バージョン 7.2 をダウンロードします。

## <a name="delete-or-clean-up-resources"></a>リソースの削除またはクリーンアップ

HSM デバイスだけでの作業を完了したら、それをリソースとして削除し、空きプールに戻すことができます。 これを行う際の明らかな問題は、デバイス上にあるお客様の機密データです。 デバイスを "ゼロにする" 最善の方法は、HSM 管理者パスワードを 3 回間違えることです (注: これはアプライアンス管理者ではなく、実際の HSM 管理者です)。 キー マテリアルを保護するための安全対策として、デバイスがゼロの状態になるまでは、そのデバイスを Azure リソースとして削除することはできません。

> [!NOTE]
> Gemalto デバイスの構成に問題がある場合は、[Gemalto カスタマー サポート](https://safenet.gemalto.com/technical-support/)に問い合わせる必要があります。

Azure 内で HSM リソースを削除する場合は、次のコマンドを使用できます。"$" 変数を一意のパラメーターで置き換えてください。

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>次のステップ

チュートリアルの手順を完了した後、Dedicated HSM リソースはお客様の仮想ネットワークにプロビジョニングされ、利用可能になります。 これで、お客様にとって望ましいデプロイ アーキテクチャに必要なリソースを追加し、このデプロイを補完できるようになりました。 お客様のデプロイの計画に役立つ詳細情報については、概念に関する各ドキュメントを参照してください。 プライマリ リージョンの 2 つの HSM によってラック レベルの可用性に対処し、セカンダリ リージョンの 2 つの HSM によってリージョンの可用性に対処する設計をお勧めします。 このチュートリアルで使用されたテンプレート ファイルは、2 つの HSM のデプロイに土台として簡単に使用できますが、お客様の要件に合わせてパラメーターを変更する必要があります。

* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [Monitoring](monitoring.md)
* [サポート可能性](supportability.md)
