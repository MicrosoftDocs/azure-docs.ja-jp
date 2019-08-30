---
title: マルチテナント ホスティング権限を使用して Azure 上で Windows 10 をデプロイする方法
description: Windows ソフトウェア アシュアランスの特典を最大限利用してオンプレミスのライセンスを Azure で使用する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 9ff8cc64266375a2d439763b222870843136f67a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101506"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>マルチテナント ホスティング権限を使用して Azure 上で Windows 10 をデプロイする方法 
接続ユーザーごとに Windows 10 Enterprise E3/E5 または Windows Virtual Desktop Access (ユーザー サブスクリプション ライセンスまたはアドオン ユーザー サブスクリプション ライセンス) をご利用中のお客様は、Windows 10 のマルチテナント ホスティング権限により、他のライセンスを購入することなく、Windows 10 ライセンスをクラウドに移行し、Azure 上で Windows 10 Virtual Machines を実行することができます。 詳細については、[Windows 10 のマルチテナント ホスティング](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)に関するページを参照してください。

> [!NOTE]
> この記事では、Azure Marketplace の Windows 10 Pro デスクトップ イメージのライセンス特典を実装する方法について説明します。
> - MSDN サブスクリプションを対象とした Azure Marketplace の Windows 7、8.1、10 Enterprise (x64) イメージについては、[Azure での Windows クライアントを用いた開発およびテスト シナリオ](client-images.md)に関するページをご覧ください。
> - Windows Server のライセンス特典については、[Windows Server イメージの Azure ハイブリッド使用特典](hybrid-use-benefit-licensing.md)に関するページをご覧ください。
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Azure Marketplace から Windows 10 イメージをデプロイする 
PowerShell、CLI、および Azure Resource Manager テンプレートでのデプロイについては、Windows 10 イメージを次の発行元、プラン、SKU で見つけることができます。

| OS  |      発行元      |  プラン | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 VHD を Azure にアップロードする
汎用化された Windows 10 VHD をアップロードする場合、Windows 10 ではビルトイン Administrator アカウントが既定で有効になっていないことにご注意ください。 ビルトイン Administrator アカウントを有効にするには、カスタム スクリプト拡張機能の一部として次のコマンドを含めます。

```powershell
Net user <username> /active:yes
```

次の PowerShell スニペットでは、ビルトイン Administrator を含む、すべての Administrator アカウントをアクティブとしてマークします。 この例は、ビルトイン Administrator のユーザー名が不明の場合に便利です。
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
詳細: 
* [VHD を Azure に アップロードする方法](upload-generalized-managed.md)
* [Azure にアップロードする Windows VHD を準備する方法](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>マルチテナント ホスティング権限で Windows 10 をデプロイする
[最新の Azure PowerShell がインストールおよび構成](/powershell/azure/overview)されていることを確認します。 VHD が準備できたら、次のように `Add-AzVhd` コマンドレットを使用して、その VHD を Azure Storage アカウントにアップロードします。

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Azure Resource Manager テンプレートを使用してデプロイする** Resource Manager テンプレートでは、`licenseType` の追加パラメーターを指定できます。 [Azure Resource Manager テンプレートの作成](../../resource-group-authoring-templates.md)で詳細を確認できます。 VHD を Azure にアップロードした後、コンピューティング プロバイダーの一部としてライセンスの種類を含めるように Resource Manager テンプレートを編集し、テンプレートを通常どおりデプロイします。
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**PowerShell を使用してデプロイする** Windows Server VM を PowerShell を使用してデプロイする場合、`-LicenseType` の追加パラメーターを指定できます。 VHD を Azure にアップロードした後、`New-AzVM` を使って VM を作成し、ライセンスの種類を次のように指定します。
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM がライセンスの特典を利用していることを確認する
VM を PowerShell または Resource Manager のいずれかのデプロイ方法によってデプロイした後、次のように `Get-AzVM` を使用してライセンスの種類を確認します。
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

適切なライセンス タイプが指定された Windows 10 の場合、出力は次の例のようになります。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

この出力を、Azure Hybrid Use Benefit ライセンスを使わずにデプロイされた次の VM (Azure Gallery から直接デプロイされた VM など) と比べてください。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Azure AD への参加に関する追加情報
>[!NOTE]
>Azure では、ビルトイン Administrator アカウントですべての Windows VM をプロビジョニングしますが、このアカウントは AAD への参加に使用できません。 たとえば、 *[設定] > [アカウント] > [職場または学校にアクセスする] > [+ 接続]* は機能しません。 手動で Azure AD に参加するには、2 つ目の Administrator アカウントを作成し、そのアカウントでログオンする必要があります。 また、プロビジョニング パッケージを使用して Azure AD を構成することもできます。詳細については、「*次のステップ*」セクションのリンクをご使用ください。
>
>

## <a name="next-steps"></a>次の手順
- [Windows 10 の VDAの構成](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)に関する詳細情報を確認します
- [Windows 10 のマルチテナント ホスティング](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)に関する詳細情報を確認します


