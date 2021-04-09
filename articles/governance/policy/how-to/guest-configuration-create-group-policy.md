---
title: Windows のグループ ポリシー ベースラインからゲスト構成ポリシー定義を作成する方法
description: Windows Server 2019 セキュリティ ベースラインからグループ ポリシーをポリシー定義に変換する方法について説明します。
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7f7e2af70efa6771d94d7ceaa14d1408175b1d12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93348646"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Windows のグループ ポリシー ベースラインからゲスト構成ポリシー定義を作成する方法

カスタム ポリシー定義を作成する前に、[Azure Policy ゲスト構成](../concepts/guest-configuration.md)に関するページで、概念上の概要情報を読むことをお勧めします。 Linux のカスタムのゲスト構成ポリシー定義を作成する方法の詳細については、「[Linux 用のゲスト構成ポリシーを作成する方法](./guest-configuration-create-linux.md)」を参照してください。 Windows のカスタムのゲスト構成ポリシー定義を作成する方法の詳細については、「[Windows 用のゲスト構成ポリシーを作成する方法](./guest-configuration-create.md)」を参照してください。

Windows の監査時に、ゲスト構成では [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) リソース モジュールを使用して構成ファイルが作成されます。 DSC 構成では、マシンが満たす必要のある条件を定義します。 構成の評価が **準拠していない** 場合、ポリシー効果 *auditIfNotExists* がトリガーされます。
[Azure Policy ゲスト構成](../concepts/guest-configuration.md)では、マシン内の設定の監査のみが行われます。

> [!IMPORTANT]
> Azure の仮想マシンで監査を実行するには、ゲスト構成拡張機能が必要です。 すべての Windows マシンに拡張機能を大規模にデプロイするには、次のポリシー定義を割り当てます。
> - [Windows VM でゲスト構成ポリシーを有効にするための前提条件をデプロイする。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> カスタム コンテンツ パッケージでは、秘密や機密情報を使用しないでください。

DSC コミュニティでは、エクスポートされたグループ ポリシー テンプレートを DSC 形式に変換できる [BaselineManagement モジュール](https://github.com/microsoft/BaselineManagement)を公開しています。 BaselineManagement モジュールと GuestConfiguration コマンドレットを組み合わせて使用すると、グループ ポリシー コンテンツから Windows 用の Azure Policy ゲスト構成パッケージを作成できます。 BaselineManagement モジュールの使用方法の詳細については、「[クイックスタート: グループ ポリシーを DSC に変換する](/powershell/scripting/dsc/quickstarts/gpo-quickstart)」という記事を参照してください。

このガイドでは、グループ ポリシー オブジェクト (GPO) から Azure Policy ゲスト構成パッケージを作成するプロセスについて説明します。 このチュートリアルでは、Windows Server 2019 セキュリティ ベースラインの変換の概要を説明していますが、同じプロセスを他の GPO に適用することもできます。  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Windows Server 2019 セキュリティ ベースラインをダウンロードし、関連する PowerShell モジュールをインストールする

PowerShell で **DSC**、**GuestConfiguration**、**Baseline Management**、および関連する Azure モジュールをインストールするには:

1. PowerShell プロンプトから、次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Windows Security Compliance ツールキットから Windows Server 2019 セキュリティ ベースラインのディレクトリを作成してダウンロードします。

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. ダウンロードした Server 2019 ベースラインのブロックを解除して展開します。

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. **MapGuidsToGpoNames.ps1** を使用して、Server 2019 ベースラインの内容を検証します。

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>グループ ポリシーから Azure Policy ゲスト構成に変換する

次に、ゲスト構成モジュールとベースライン管理モジュールを使用して、ダウンロードした Server 2019 ベースラインをゲスト構成パッケージに変換します。

1. ベースライン管理モジュールを使用して、グループ ポリシーを Desired State Configuration に変換します。

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. ポリシー コンテンツ パッケージを作成する前に、変換されたスクリプトの名前を変更し、再フォーマットし、実行します。

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Azure Policy ゲスト構成コンテンツ パッケージを作成します。

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Azure Policy ゲスト構成を作成する

1. 次の手順はファイルを Azure Blob Storage に発行することです。 コマンド `Publish-GuestConfigurationPackage` には `Az.Storage` モジュールが必要です。

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. ゲスト構成のカスタム ポリシー パッケージを作成してアップロードした後、ゲスト構成ポリシー定義を作成します。 `New-GuestConfigurationPolicy` コマンドレットを使用してゲスト構成を作成します。

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. `Publish-GuestConfigurationPolicy` コマンドレットを使用してポリシー定義を発行します。 コマンドレットのパラメーターは、`New-GuestConfigurationPolicy` によって作成される JSON ファイルの場所を指し示す **Path** だけです。 Publish コマンドを実行するには、Azure でポリシー定義を作成できるアクセス権が必要です。 特定の承認要件については、[Azure Policy の概要](../overview.md#getting-started)に関するページに記載されています。 最適な組み込みロールは、**リソース ポリシーの共同作成者** です。

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>ゲスト構成ポリシー定義を割り当てる

Azure で作成されるポリシーに関する最後のステップでは、イニシアティブを割り当てます。 [ポータル](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md)、および [Azure PowerShell](../assign-policy-powershell.md) でイニシアティブを割り当てる方法について確認できます。

> [!IMPORTANT]
> ゲスト構成ポリシー定義は **常に**、"_AuditIfNotExists_" ポリシーと "_DeployIfNotExists_" ポリシーを組み合わせたイニシアティブを使って割り当てる必要があります。 "_AuditIfNotExists_" ポリシーのみを割り当てた場合は、前提条件がデプロイされず、ポリシーでは、準拠しているサーバーが常に "0" と示されます。

_DeployIfNotExists_ 効果でポリシー定義を割り当てるには、追加のレベルのアクセス権が必要です。 最小限の権限を付与するために、**リソース ポリシーの共同作成者** を拡張するカスタム ロール定義を作成できます。 次の例では、追加のアクセス許可 _Microsoft.Authorization/roleAssignments/write_ を持つ **リソース ポリシーの共同作成者 DINE** という名前のロールを作成します。

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>次のステップ

- [ゲスト構成](../concepts/guest-configuration.md)による VM の監査について学習します。
- [プログラムによってポリシーを作成する](./programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](./get-compliance-data.md)方法を学習します。
