---
title: ゲスト構成ポリシーを作成する方法
description: Windows VM または Linux VM に対する Azure Policy のゲスト構成ポリシーを作成する方法について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 131d6865c47a32bbefbfbd397a5f0f88dedc9c35
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543514"
---
# <a name="how-to-create-guest-configuration-policies"></a>ゲスト構成ポリシーを作成する方法

ゲスト構成では [Desired State Configuration](/powershell/dsc) (DSC) リソース モジュールを使って、Azure 仮想マシンの監査用の構成を作成します。 DSC 構成では、仮想マシンが満たす必要のある条件を定義します。 構成の評価が失敗した場合、ポリシー効果の **audit** がトリガーされて、仮想マシンは**非準拠**と見なされます。

[Azure Policy のゲスト構成](/azure/governance/policy/concepts/guest-configuration)は、仮想マシン内の設定を監査するためにのみ使用できます。 仮想マシン内の設定の修復はまだ利用できません。

Azure 仮想マシンの状態を検証するための独自の構成を作成するには、次のアクションを使用します。

> [!IMPORTANT]
> ゲスト構成でのカスタム ポリシーは、プレビュー機能です。

## <a name="add-the-guestconfiguration-resource-module"></a>GuestConfiguration リソース モジュールを追加する

ゲスト構成ポリシーを作成するには、リソース モジュールを追加する必要があります。 このリソース モジュールは、ローカルにインストールされた PowerShell、[Azure Cloud Shell](https://shell.azure.com)、または [Azure PowerShell Docker イメージ](https://hub.docker.com/rsdk-powershell/)で使用できます。

### <a name="base-requirements"></a>基本要件

ゲスト構成のリソース モジュールには、次のソフトウェアが必要です。

- PowerShell。 インストールされていない場合は、こちらの[手順](/powershell/scripting/install/installing-powershell)に従ってください。
- Azure PowerShell 1.5.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-az-ps)に従ってください。

### <a name="install-the-module"></a>モジュールのインストール

ゲスト構成では、DSC 構成を作成して Azure Policy に発行するには、**GuestConfiguration** リソース モジュールを使います。

1. PowerShell プロンプトから、次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. モジュールがインポートされていることを確認します。

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration"></a>カスタム ゲスト構成の構成を作成する

ゲスト構成のカスタム ポリシーを作成する最初のステップは、DSC 構成の作成です。 DSC の概念と用語の概要については、[PowerShell DSC の概要](/powershell/dsc/overview/overview)に関する記事をご覧ください。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux でのカスタム ゲスト構成の構成

Linux でのゲスト構成の DSC 構成では、`ChefInSpecResource` リソースを使って、エンジンに [Chef InSpec](https://www.chef.io/inspec/) 定義の名前を設定します。 **Name** は、唯一必要なリソース プロパティです。

次の例では、**baseline** という名前の構成を作成し、**GuestConfiguration** リソース モジュールをインポートし、`ChefInSpecResource` リソースを使って InSpec 定義の名前を **linux-patch-baseline** に設定しています。

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

詳細については、[構成の作成、コンパイル、適用](/powershell/dsc/configurations/write-compile-apply-configuration)に関する記事をご覧ください。

### <a name="custom-guest-configuration-configuration-on-windows"></a>Windows でのカスタム ゲスト構成の構成

Azure Policy ゲスト構成用の DSC 構成はゲスト構成エージェントによってのみ使われ、Windows PowerShell の Desired State Configuration とは競合しません。

次の例では、**AuditBitLocker** という名前の構成を作成し、**GuestConfiguration** リソース モジュールをインポートし、`Service` リソースを使って実行中のサービスを監査しています。

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

詳細については、[構成の作成、コンパイル、適用](/powershell/dsc/configurations/write-compile-apply-configuration)に関する記事をご覧ください。

## <a name="create-guest-configuration-custom-policy-package"></a>ゲスト構成のカスタム ポリシー パッケージを作成する

MOF をコンパイルしたら、サポート ファイルをまとめてパッケージ化する必要があります。 完成したパッケージは、Azure Policy の定義を作成するためにゲスト構成によって使われます。 パッケージは次のもので構成されます。

- MOF としてのコンパイル済み DSC 構成
- モジュール フォルダー
  - GuestConfiguration モジュール
  - DscNativeResources モジュール
  - (Linux) Chef InSpec の定義と追加のコンテンツが含まれるフォルダー
  - (Windows) 組み込まれていない DSC リソース モジュール

`New-GuestConfigurationPackage` コマンドレットでパッケージを作成します。 カスタム パッケージの作成には、次の形式を使います。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

`New-GuestConfigurationPackage` コマンドレットのパラメーター:

- **[名前]** :ゲスト構成のパッケージ名。
- **構成**:コンパイル済み DSC 構成ドキュメントの完全なパス。
- **パス**:出力フォルダーのパス。 このパラメーターは省略可能です。 指定しないと、パッケージは現在のディレクトリに作成されます。
- **ChefProfilePath**: InSpec プロファイルへの完全なパス。 このパラメーターは、Linux を監査するコンテンツを作成する場合にのみサポートされます。

完成したパッケージは、管理対象の仮想マシンからアクセスできる場所に保存されている必要があります。 たとえば、GitHub リポジトリ、Azure リポジトリ、Azure Storage などです。 パッケージを公開したくない場合は、URL に [SAS トークン](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)を含めることができます。 また、仮想マシンの[サービス エンドポイント](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)をプライベート ネットワークに実装することもできますが、この構成はパッケージへのアクセスにのみ適用され、サービスとの通信には適用されません。

### <a name="working-with-secrets-in-guest-configuration-packages"></a>ゲスト構成パッケージでのシークレットの使用

Azure Policy のゲスト構成で、実行時に使われるシークレットを管理する最適な方法は、Azure Key Vault に保存することです。 この設計は、カスタム DSC リソース内に実装されます。

最初に、Azure でユーザー割り当てマネージド ID を作成します。 その ID は、Key Vault に格納されているシークレットにアクセスするために、仮想マシンによって使われます。 詳しい手順については、「[Azure PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)」をご覧ください。

次に、Key Vault のインスタンスを作成します。 詳しい手順については、[PowerShell を使用してシークレットの設定と取得を行う](../../../key-vault/quick-create-powershell.md)に関する記事をご覧ください。
インスタンスにアクセス許可を割り当てて、Key Vault に格納されているシークレットにユーザー割り当て ID でアクセスできるようにします。 詳しい手順については、[.NET を使用してシークレットの設定と取得を行う](../../../key-vault/quick-create-net.md#assign-permissions-to-your-application-to-read-secrets-from-key-vault)に関する記事をご覧ください。

次に、ユーザー割り当て ID を仮想マシンに割り当てます。 詳しい手順については、[PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity)に関する記事をご覧ください。
大規模な場合は、Azure Resource Manager を使って Azure Policy でこの ID を割り当てます。 詳しい手順については、[テンプレートを使用して Azure VM で Azure リソースのマネージド ID を構成する](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm)に関する記事をご覧ください。

最後に、カスタム リソース内で、上で生成されたクライアント ID を使って、コンピューターから利用可能なトークンを使って Key Vault にアクセスします。 `client_id` と Key Vault インスタンスへの URL は、[プロパティ](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema)としてリソースに渡すことができるため、複数の環境の場合、または値を変更する必要がある場合でも、リソースを更新する必要はありません。

カスタム リソースで次のコード サンプルを使うことで、ユーザー割り当て ID を使って Key Vault からシークレットを取得できます。 Key Vault への要求から返される値はプレーンテキストです。 ベスト プラクティスとしては、それを資格情報オブジェクト内に格納します。

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>ゲスト構成パッケージをテストする

構成パッケージを作成したら、Azure に発行する前に、ワークステーションまたは CI/CD 環境からパッケージの機能をテストすることができます。 GuestConfiguration モジュールに含まれる `Test-GuestConfigurationPackage` コマンドレットでは、Azure 仮想マシンで使われるのと同じエージェントが開発環境に読み込まれます。 このソリューションを使って、有料のテスト/QA/運用環境にリリースする前に、ローカル環境で統合テストを実行できます。

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

`Test-GuestConfigurationPackage` コマンドレットのパラメーター:

- **[名前]** :ゲスト構成ポリシーの名前。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。
- **パス**:ゲスト構成パッケージの完全なパス。

コマンドレットでは、PowerShell パイプラインからの入力もサポートされています。 `New-GuestConfigurationPackage` コマンドレットの出力を `Test-GuestConfigurationPackage` コマンドレットにパイプします。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

パラメーターでのテスト方法の詳細については、後述の「[カスタム ゲスト構成ポリシーでのパラメーターの使用](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies)」のセクションを参照してください。

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Azure Policy 定義とイニシアティブ デプロイ ファイルを作成する

ゲスト構成のカスタム ポリシー パッケージを作成し、仮想マシンからアクセス可能な場所にアップロードした後、Azure Policy のゲスト構成ポリシー定義を作成します。 `New-GuestConfigurationPolicy` コマンドレットにパブリックにアクセス可能なゲスト構成カスタム ポリシー パッケージを渡すと、**auditIfNotExists** および **deployIfNotExists** ポリシー定義が作成されます。 両方のポリシー定義を含むポリシー イニシアティブ定義も作成されます。

次の例では、Windows 用のゲスト構成カスタム ポリシー パッケージから指定したパスにポリシーとイニシアティブ定義を作成し、名前、説明、およびバージョンを設定します。

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

`New-GuestConfigurationPolicy` コマンドレットのパラメーター:

- **ContentUri**: ゲスト構成コンテンツ パッケージのパブリック HTTP(S) URI。
- **DisplayName**: ポリシーの表示名。
- **説明**:ポリシーの説明。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。
- **バージョン**:ポリシーのバージョン。
- **パス**:ポリシー定義が作成されるターゲット パス。
- **Platform**: ゲスト構成ポリシーとコンテンツ パッケージのターゲット プラットフォーム (Windows/Linux)。

`New-GuestConfigurationPolicy` により、次のファイルが作成されます。

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

コマンドレットの出力では、イニシアティブの表示名とポリシー ファイルのパスが含まれるオブジェクトが返されます。

このコマンドを使ってカスタム ポリシー プロジェクトをスキャフォールディングする場合は、これらのファイルを変更できます。 たとえば、特定のタグが仮想マシンに存在するかどうかを評価するように "If" セクションを変更します。 ポリシーの作成の詳細については、「[ポリシーをプログラムで作成してコンプライアンス データを表示する](./programmatically-create.md)」を参照してください。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>カスタム ゲスト構成ポリシーでのパラメーターの使用

ゲスト構成では、実行時に構成のプロパティをオーバーライドすることができます。 この機能は、パッケージの MOF ファイル内の値を静的と見なす必要がないことを意味します。 オーバーライドする値は Azure Policy を通じて提供され、構成の作成方法またはコンパイル方法には影響しません。

`New-GuestConfigurationPolicy` と `Test-GuestConfigurationPolicyPackage` のコマンドレットには、**Parameters** という名前のパラメーターが含まれています。
このパラメーターは、各パラメーターの詳細をすべて含むハッシュテーブル定義を受け取り、各 Azure Policy 定義の作成に使用されるファイルのすべての必要なセクションが自動的に作成されます。

次の例では、サービスを監査するための Azure Policy を作成します。ポリシーの割り当て時にサービスの一覧からユーザーが選択します。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Linux ポリシーの場合は、構成に `AttributesYmlContent` プロパティを含め、それに従って値を上書きします。 ゲスト構成エージェントでは、属性を格納するために InSpec によって使われる YaML ファイルが自動的に作成されます。 次の例を見てください。

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

追加パラメーターごとに、配列にハッシュテーブルを追加します。 ポリシー ファイルでは、リソースの種類、名前、プロパティ、値を示すゲスト構成の configurationName に追加されたプロパティを確認できます。

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Azure Policy に発行する

**GuestConfiguration** リソース モジュールでは、`Publish-GuestConfigurationPolicy` コマンドレットから 1 ステップで Azure でポリシー定義とイニシアティブ定義の両方を作成する方法が提供されています。
コマンドレットのパラメーターは、`New-GuestConfigurationPolicy` によって作成される 3 つの JSON ファイルの場所を指し示す **Path** だけです。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

`Publish-GuestConfigurationPolicy` コマンドレットは、PowerShell パイプラインからパスを受け取ります。 この機能では、パイプされたコマンドの 1 つのセットで、ポリシー ファイルを作成して発行できます。

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Azure で作成されるポリシー定義とイニシアティブ定義に関する最後のステップでは、イニシアティブを割り当てます。 [ポータル](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md)、および [Azure PowerShell](../assign-policy-powershell.md) でイニシアティブを割り当てる方法について確認できます。

> [!IMPORTANT]
> ゲスト構成ポリシーは**常に**、"_AuditIfNotExists_" ポリシーと "_DeployIfNotExists_" ポリシーを組み合わせたイニシアティブを使って割り当てる必要があります。 "_AuditIfNotExists_" ポリシーのみを割り当てた場合は、前提条件がデプロイされず、ポリシーでは、準拠しているサーバーが常に "0" と示されます。

## <a name="policy-lifecycle"></a>ポリシーのライフサイクル

カスタム コンテンツ パッケージを使ってカスタム Azure Policy を発行した後、新しいリリースを発行する場合に更新する必要があるフィールドが 2 つあります。

- **バージョン**:`New-GuestConfigurationPolicy` コマンドレットを実行するときは、現在発行されているバージョンより大きいバージョン番号を指定する必要があります。  これにより、新しいポリシー ファイルのゲスト構成割り当てのバージョンが更新され、パッケージが更新されたことを拡張機能で認識できるようになります。
- **contentHash**: これは、`New-GuestConfigurationPolicy` コマンドレットによって自動的に更新されます。  `New-GuestConfigurationPackage` によって作成されるパッケージのハッシュ値です。  これは、発行する `.zip` ファイルに対して適切なものである必要があります。  ユーザーがポータルからポリシー定義を手動で変更した場合など、`contentUri` プロパティのみが更新された場合、拡張機能ではコンテンツ パッケージが受け入れられません。

更新されたパッケージをリリースする最も簡単な方法は、この記事で説明されているプロセスを繰り返し、更新されたバージョン番号を指定することです。
このようにすると、すべてのプロパティが正しく更新されることが保証されます。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows グループ ポリシー コンテンツから Azure Policy ゲスト構成への変換

Windows マシンを監査する場合のゲスト構成は、PowerShell Desired State Configuration 構文の実装です。
DSC コミュニティでは、エクスポートしたグループ ポリシー テンプレートを DSC 形式に変換するためのツールが公開されています。
このツールを前述のゲスト構成コマンドレットと共に使用することで、Windows グループ ポリシーのコンテンツを変換し、Azure Policy 用にパッケージ化および公開して監査することができます。
ツールの使用の詳細については、[クイックスタート: グループ ポリシーを DSC に変換する](/powershell/dsc/quickstarts/gpo-quickstart)」という記事を参照してください。
コンテンツの変換後は、パッケージを作成して Azure Policy として公開する上記の手順は、他の DSC コンテンツと同じです。

## <a name="optional-signing-guest-configuration-packages"></a>省略可能:ゲスト構成パッケージに署名する

既定では、ゲスト構成カスタム ポリシーは SHA256 ハッシュを使って、発行されてから監査対象のサーバーによって読み込まれるまでに、ポリシー パッケージが変更されていないことが検証されます。
必要に応じて、お客様は証明書を使ってパッケージに署名し、署名されたコンテンツのみを許可するようにゲスト構成拡張機能を強制することもできます。

このシナリオを有効にするには、2 つのステップを実行する必要があります。 コマンドレットを実行してコンテンツ パッケージに署名し、コードが署名されていることを要求する必要があるように仮想マシンにタグを追加します。

署名検証機能を使用するには、`Protect-GuestConfigurationPackage` コマンドレットを実行して、発行前にパッケージに署名します。 このコマンドレットには "コード署名" 証明書が必要です。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` コマンドレットのパラメーター:

- **パス**:ゲスト構成パッケージの完全なパス。
- **Certificate**: パッケージに署名するためのコード署名証明書。 このパラメーターは、Windows 用のコンテンツに署名する場合にのみサポートされます。
- **PrivateGpgKeyPath**: プライベート GPG キーのパス。 このパラメーターは、Linux 用のコンテンツに署名する場合にのみサポートされます。
- **PublicGpgKeyPath**: パブリック GPG キーのパス。 このパラメーターは、Linux 用のコンテンツに署名する場合にのみサポートされます。

GuestConfiguration エージェントにより、Windows マシンの場合は "信頼されたルート証明機関" に、Linux マシンの場合はパス `/usr/local/share/ca-certificates/extra` に、証明書の公開キーが存在していることが求められます。 署名されたコンテンツをノードで検証するには、カスタム ポリシーを適用する前に、仮想マシンに証明書の公開キーをインストールします。 このプロセスは、VM 内で任意の方法を使うか、Azure Policy を使って、行うことができます。 テンプレートの例は、[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)で提供されています。
Key Vault のアクセス ポリシーでは、デプロイ中にコンピューティング リソース プロバイダーが証明書にアクセスできるようにする必要があります。 詳しい手順については、[Azure Resource Manager の仮想マシンの Key Vault を設定する](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)に関する記事をご覧ください。

署名証明書から公開キーをエクスポートして仮想マシンにインポートする例を次に示します。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Linux 仮想マシンで使用する GPG キーの作成については、GitHub の[新しい GPG キーの生成](https://help.github.com/en/articles/generating-a-new-gpg-key)に関する記事に優れたリファレンスが提供されています。

コンテンツを発行した後、コード署名が必要なすべての仮想マシンに、名前が `GuestConfigPolicyCertificateValidation` で値が `enabled` のタグを追加します。 このタグは、Azure Policy を使って大規模に配信できます。 「[サンプル - タグとその既定値の適用](../samples/apply-tag-default-value.md)」をご覧ください。
このタグを配置すると、`New-GuestConfigurationPolicy` コマンドレットを使って生成されるポリシー定義では、ゲスト構成拡張による要件が有効になります。

## <a name="next-steps"></a>次の手順

- [ゲスト構成](../concepts/guest-configuration.md)による VM の監査について学習します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](getting-compliance-data.md)方法を学習します。
