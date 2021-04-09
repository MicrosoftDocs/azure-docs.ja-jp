---
title: マルチテナント ホスティング権限を使用して Azure 上で Windows 10 をデプロイする方法
description: Windows ソフトウェア アシュアランスの特典を最大限利用して、マルチテナント ホスティング権限でオンプレミスのライセンスを Azure で使用する方法について説明します。
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: bb86ba6867ad796ef0f5eeb1357a6df9e93e9f9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555773"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>マルチテナント ホスティング権限を使用して Azure 上で Windows 10 をデプロイする方法 
接続ユーザーごとに Windows 10 Enterprise E3/E5 または Windows Virtual Desktop Access (ユーザー サブスクリプション ライセンスまたはアドオン ユーザー サブスクリプション ライセンス) をご利用中のお客様は、Windows 10 のマルチテナント ホスティング権限により、他のライセンスを購入することなく、Windows 10 ライセンスをクラウドに移行し、Azure 上で Windows 10 Virtual Machines を実行することができます。 マルチテナント ホスティング権限は、Windows 10 (バージョン 1703 以降) でのみ使用できます。

詳細については、[Windows 10 のマルチテナント ホスティング](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)に関するページを参照してください。

> [!NOTE]
> - 開発またはテストのために Windows 7、8.1、10のイメージを使用する方法については、[開発/テスト シナリオのための Azure での Windows クライアント](client-images.md)に関するページを参照してください。
> - Windows Server のライセンス特典については、[Windows Server イメージの Azure ハイブリッド使用特典](hybrid-use-benefit-licensing.md)に関するページをご覧ください。

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>マルチテナント ホスティング権限の対象となるサブスクリプション ライセンス

[Microsoft 管理センター](/microsoft-365/admin/admin-overview/about-the-admin-center)を使用して、ユーザーに Windows 10 のサポートされているライセンスが割り当てられているかどうかを確認できます。

> [!IMPORTANT]
> Azure で Windows 10 のイメージを使用するには、ユーザーに以下のいずれかのサブスクリプション ライセンスが必要です。 これらのいずれかのサブスクリプション ライセンスがない場合は、[クラウド サービス パートナー](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)を通じて、または [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1) から直接購入できます。

**対象となるサブスクリプション ライセンス:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Windows 10 Enterprise E3/E5
-   Windows 10 Education A3/A5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Azure Marketplace から Windows 10 イメージをデプロイする 
PowerShell、CLI、および Azure Resource Manager テンプレートでのデプロイについては、Windows 10 イメージを `PublisherName: MicrosoftWindowsDesktop` と `Offer: Windows-10` で見つけることができます。 マルチテナント ホスティング権限では、Windows 10 Creators Update (バージョン 1809) 以降がサポートされています。 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

使用可能なイメージの詳細については、「[Azure PowerShell を使用して Azure Marketplace VM イメージを検索して使用する](./cli-ps-findimage.md)」を参照してください。

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
[最新の Azure PowerShell がインストールおよび構成](/powershell/azure/)されていることを確認します。 VHD が準備できたら、次のように `Add-AzVhd` コマンドレットを使用して、その VHD を Azure Storage アカウントにアップロードします。

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Azure Resource Manager テンプレートを使用してデプロイする** Resource Manager テンプレートでは、`licenseType` の追加パラメーターを指定できます。 [Azure Resource Manager テンプレートの作成](../../azure-resource-manager/templates/template-syntax.md)で詳細を確認できます。 VHD を Azure にアップロードした後、コンピューティング プロバイダーの一部としてライセンスの種類を含めるように Resource Manager テンプレートを編集し、テンプレートを通常どおりデプロイします。
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
Azure では、ビルトイン Administrator アカウントですべての Windows VM をプロビジョニングしますが、このアカウントは AAD への参加に使用できません。 たとえば、 *[設定] > [アカウント] > [職場または学校にアクセスする] > [+ 接続]* は機能しません。 手動で Azure AD に参加するには、2 つ目の Administrator アカウントを作成し、そのアカウントでログオンする必要があります。 また、プロビジョニング パッケージを使用して Azure AD を構成することもできます。詳細については、「*次のステップ*」セクションのリンクをご使用ください。

## <a name="next-steps"></a>次の手順
- [Windows 10 の VDAの構成](/windows/deployment/vda-subscription-activation)に関する詳細情報を確認します
- [Windows 10 のマルチテナント ホスティング](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)に関する詳細情報を確認します