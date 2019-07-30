---
title: 前提条件 - IaaS VM の Azure Disk Encryption | Microsoft Docs
description: この記事では、IaaS VM 用に Microsoft Azure Disk Encryption を使用する場合の前提条件について説明します。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: 8e01815cee0d6e39f6f773e9838b2a8b60638ab1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672296"
---
# <a name="azure-disk-encryption-prerequisites"></a>Azure Disk Encryption の前提条件

この記事「Azure Disk Encryption の前提条件」では、Azure Disk Encryption を使用する前に用意する必要がある項目について説明します。 Azure Disk Encryption は、暗号化キーを管理できるように [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) と統合されています。 [Azure PowerShell](/powershell/azure/overview)、[Azure CLI](/cli/azure/)、または [Azure portal](https://portal.azure.com) を使用して、Azure Disk Encryption を構成できます。

[Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)に関する記事で説明されているサポート対象シナリオの Azure IaaS VM で Azure Disk Encryption を有効にする前に、以下の前提条件を満たしていることを確認してください。 

> [!WARNING]
> - これまで [Azure AD アプリで Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) を使用してこの VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 この暗号化された VM に対して [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) を使用することはできません。それはサポートされていないシナリオであり、暗号化された VM 用の AAD アプリケーションからの切り替えはまだサポートされていないことを意味します。
> - 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。 サポートされているリージョンにおいて Azure でリソースを作成するための有効なアクティブ Azure サブスクリプションが必要です。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

Azure Disk Encryption は、次の最小メモリ要件を満たした仮想マシンで利用できます。

| 仮想マシン | 最小メモリ要件 |
|--|--|
| Windows VM | 2 GB |
| Linux VM (データ ボリュームのみを暗号化する場合)| 2 GB |
| Linux VM (データ ボリュームと OS ボリュームの両方を暗号化し、なおかつルート (/) ファイル システムの使用量が 4 GB 以下の場合) | 8 GB |
| Linux VM (データ ボリュームと OS ボリュームの両方を暗号化し、なおかつルート (/) ファイル システムの使用量が 4 GB を超える場合) | ルート ファイル システムの使用量 * 2。 たとえば、ルート ファイル システムの使用量が 16 GB の場合、32 GB 以上の RAM が必要となります。 |

Linux 仮想マシンの OS ディスクの暗号化プロセスが完了すると、より少ないメモリで VM を実行するように構成できます。 

> [!NOTE]
> [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) では、Linux OS ディスクの暗号化が利用できません。

Azure Disk Encryption は、Premium Storage を使用した VM でも利用できます。 

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

### <a name="windows"></a>Windows

- Windows クライアント: Windows 8 以降
- Windows Server: Windows Server 2008 R2 以降  
 
> [!NOTE]
> Windows Server 2008 R2 には、暗号化用に .NET Framework 4.5 をインストールする必要があります。Windows Update から、オプションの更新プログラムである Windows Server 2008 R2 x64 ベース システム用の Microsoft .NET Framework 4.5.2 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)) を使用してインストールすることができます。  
>  
> Windows Server 2012 R2 Core と Windows Server 2016 Core には、暗号化用に VM 上に bdehdcfg コンポーネントをインストールする必要があります。


### <a name="linux"></a>Linux 

Azure Disk Encryption は [Azure での動作が保証された一部の Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)でサポートされています。Azure での動作が保証された Linux ディストリビューションはそれ自体があらゆる Linux サーバー ディストリビューションの一部となります。

![Azure Disk Encryption をサポートする Linux サーバー ディストリビューションのベン図](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Azure での動作が保証されていない Linux サーバー ディストリビューションでは Azure Disk Encryption がサポートされておらず、動作が保証されているディストリビューションの中でも、次のディストリビューションとバージョンだけで Azure Disk Encryption がサポートされています。

| Linux ディストリビューション | Version | 暗号化がサポートされているボリュームの種類|
| --- | --- |--- |
| Ubuntu | 18.04| OS とデータ ディスク |
| Ubuntu | 16.04| OS とデータ ディスク |
| Ubuntu | 14.04.5</br>[カーネルが 4.15 以降に調整されている Azure](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | OS とデータ ディスク |
| RHEL | 7.6 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.5 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.4 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.3 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.2 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 6.8 | データ ディスク (後述する注を参照してください) |
| RHEL | 6.7 | データ ディスク (後述する注を参照してください) |
| CentOS | 7.6 | OS とデータ ディスク |
| CentOS | 7.5 | OS とデータ ディスク |
| CentOS | 7.4 | OS とデータ ディスク |
| CentOS | 7.3 | OS とデータ ディスク |
| CentOS | 7.2n | OS とデータ ディスク |
| CentOS | 6.8 | データ ディスク |
| openSUSE | 42.3 | データ ディスク |
| SLES | 12-SP4 | データ ディスク |
| SLES | 12-SP3 | データ ディスク |

> [!NOTE]
> RHEL7 の従量課金制イメージについては、RHEL OS とデータ ディスクに新しい ADE の実装がサポートされます。 RHEL の BYOS (Bring-Your-Own-Subscription) イメージについては、現在 ADE はサポートされません。 詳細については、[Linux 用の Azure Disk Encryption](azure-security-disk-encryption-linux.md) に関するページを参照してください。

- Azure Disk Encryption では、Key Vault と VM が同じ Azure リージョンおよびサブスクリプションに属している必要があります。 リソースをそれぞれ別のリージョンで構成すると、Azure Disk Encryption 機能を有効にする場合にエラーが発生します。

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Linux IaaS VM のその他の前提条件 

- Azure Disk Encryption では、dm-crypt モジュールと vfat モジュールがシステムに存在している必要があります。 vfat を既定のイメージから削除したり無効にしたりすると、システムはキー ボリュームを読み取って、その後のリブートでディスクのロックを解除するために必要なキーを取得できなくなります。 vfat モジュールをシステムから削除するシステム強化手順は、Azure Disk Encryption とは互換性がありません。 
- 暗号化を有効にする前に、暗号化するデータ ディスクを /etc/fstab に正しく登録する必要があります。 "/dev/sdX" 形式のデバイス名は、再起動後 (特に暗号化が適用された後) に同じディスクに関連付けられるとは限らないため、このエントリに永続的なブロック デバイス名を使用してください。 この動作の詳細については、次を参照してください:[Linux VM デバイス名の変更トラブルシューティング](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- /etc/fstab 設定がマウントに合わせて正しく構成されていることを確認します。 これらの設定を構成するには、mount -a コマンドを実行するか、VM を再起動してその方法での再マウントをトリガーします。 完了したら、lsblk コマンドの出力を調べて、ドライブがまだマウントされていることを確認します。 
  - 暗号化を有効にする前に /etc/fstab ファイルによってドライブが適切にマウントされない場合、Azure Disk Encryption Azure でそれを適切にマウントできません。
  - Azure Disk Encryption プロセスは、暗号化プロセスの一部として、/etc/fstab から独自の構成ファイルにマウント情報を移動します。 データ ドライブの暗号化が完了した後、/etc/fstab からそのエントリがなくなっても気にする必要はありません。
  - マウントされたデータ ディスクに書き込みを行う可能性のあるサービスとプロセスは、暗号化を開始する前にすべて停止し、それらを無効にして、再起動後に自動的に再開することのないようにしてください。 それが原因でそれらのパーティション上のファイルが開いたままになっていると、暗号化手順で再マウントが阻害され、暗号化に失敗します。 
  - 再起動後、新しく暗号化されたディスクが Azure Disk Encryption プロセスによってマウントされる処理には時間がかかります。 再起動後すぐには利用できません。 このプロセスでは、他のプロセスがアクセスできるようになる前に、暗号化されたドライブを起動し、ロックを解除し、マウントする時間が必要です。 システムの特性によっては、再起動後に 1 分以上かかることがあります。

データ ディスクをマウントし、必要な /etc/fstab エントリを作成するために使用できるコマンドの例は、[このスクリプト ファイルの行 244 から 248](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248) にあります。 

## <a name="bkmk_GPO"></a> ネットワークとグループ ポリシー

**Azure Disk Encryption 機能を有効にするには、IaaS VM が次のネットワーク エンドポイントの構成要件を満たす必要があります。**
  - Key Vault に接続するためのトークンを取得するには、IaaS VM が Azure Active Directory エンドポイント \[login.microsoftonline.com\] に接続できる必要があります。
  - 暗号化キーを Key Vault に書き込むには、IaaS VM が Key Vault エンドポイントに接続できる必要があります。
  - IaaS VM は、Azure 拡張リポジトリをホストする Azure ストレージ エンドポイントと、VHD ファイルをホストする Azure ストレージ アカウントに接続できる必要があります。
  -  セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。 詳細については、「[ファイアウォールの内側にある Azure Key Vault へのアクセス](../key-vault/key-vault-access-behind-firewall.md)」を参照してください。    


**グループ ポリシー:**
 - Azure Disk Encryption ソリューションでは、Windows IaaS VM に対して BitLocker 外部キー保護機能を使用します。 ドメインに参加している VM の場合は、TPM 保護機能を適用するグループ ポリシーをプッシュしないでください。 "互換性のある TPM が装備されていない BitLocker を許可する" のグループ ポリシーについては、「[BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)」(BitLocker グループ ポリシー リファレンス) をご覧ください。

-  ドメインに参加済みであり、カスタム グループ ポリシーを使用する仮想マシンでの BitLocker ポリシーには、次の設定を含める必要があります。[[BitLocker 回復情報のユーザー記憶域を構成する] -> [256 ビットの回復キーを許可する]](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 BitLocker のカスタム グループ ポリシー設定に互換性がない場合、Azure Disk Encryption は失敗します。 正しいポリシー設定がないマシンでは、新しいポリシーを適用し、新しいポリシーを強制的に更新して (gpupdate.exe /force)、再起動する処理が必要になる可能性があります。

- BitLocker によって使用される AES-CBC アルゴリズムがドメイン レベル グループ ポリシーでブロックされると、Azure Disk Encryption は失敗します。


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) には、Azure リソースの管理に [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) モデルを使う一連のコマンドレットが用意されています。 これは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用してブラウザーで使用するか、以下の手順でローカル コンピューターにインストールして PowerShell セッションで使用することができます。 既にローカルにインストールされている場合、Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用します。 [Azure PowerShell リリース](https://github.com/Azure/azure-powershell/releases)の最新バージョンをダウンロードします。

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Azure PowerShell をインストールしてローカル コンピューターで使用する (省略可能): 
1. お使いのオペレーティング システムのリンク先に記載されている指示を実行してから、以下の残りの手順を続行します。      
   - [Azure PowerShell をインストールして構成](/powershell/azure/install-az-ps)します。 
     - PowerShellGet、Azure PowerShell をインストールし、Az モジュールを読み込みます。 

2. Az モジュールのインストール済みバージョンを確認します。 必要な場合は、[Azure PowerShell モジュールを更新](/powershell/azure/install-az-ps#update-the-azure-powershell-module)します。
    最新バージョンの Az モジュールを使用することをお勧めします。

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure にサインインします。
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  必要な場合は、「[Azure PowerShell を使ってみる](/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="bkmk_CLI"></a> ローカル コンピューターで使用する Azure CLI をインストールする (省略可能)

[Azure CLI 2.0](/cli/azure) は、Azure リソースを管理するためのコマンドライン ツールです。 CLI は、データのクエリを柔軟に実行し、長時間実行される操作を非ブロッキング プロセスとしてサポートし、スクリプトが簡単になるように設計されています。 これは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用してブラウザーで使用することも、ローカル コンピューターにインストールして PowerShell セッションで使用することもできます。

1. ローカル コンピューターで使用する [Azure CLI をインストールします](/cli/azure/install-azure-cli) (省略可能)。

2. インストールされているバージョンを確認します。
     
     ```azurecli-interactive
     az --version
     ``` 

3. [az login](/cli/azure/authenticate-azure-cli) を使用して Azure にサインインします。
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. 必要に応じて、「[Azure CLI 2.0 を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。 


## <a name="prerequisite-workflow-for-key-vault"></a>Key Vault の前提条件ワークフロー
Azure Disk Encryption に関する Key Vault と Azure AD の前提条件に既に精通している場合は、[Azure Disk Encryption の前提条件となる PowerShell スクリプト](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )を使用できます。 前提条件のスクリプトの使用について詳しくは、[VM の暗号化クイック スタート](quick-encrypt-vm-powershell.md)および[Azure Disk Encryption の付録](azure-security-disk-encryption-appendix.md#bkmk_prereq-script)に関するページをご覧ください。 

1. 必要な場合は、リソース グループを作成します。
2. Key Vault を作成します。 
3. キー コンテナーに高度なアクセス ポリシーを設定します。

>[!WARNING]
>キー コンテナーを削除する前に、そのキー コンテナーを使用して既存の VM を暗号化していないかどうかを確認します。 コンテナーが誤って削除されないようにするには、コンテナーで[論理的な削除](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete)と[リソースのロック](../azure-resource-manager/resource-group-lock-resources.md)を有効にしてください。 
 
## <a name="bkmk_KeyVault"></a> キー コンテナーを作成する 
Azure Disk Encryption は [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 Azure Disk Encryption ではキー コンテナーを作成するか、既存のコンテナーを使用することができます。 キー コンテナーの詳細については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」と「[キー コンテナーのセキュリティ保護](../key-vault/key-vault-secure-your-key-vault.md)」を参照してください。 Resource Manager テンプレート、Azure PowerShell、または Azure CLI を使用してキー コンテナーを作成できます。 


>[!WARNING]
>暗号化シークレットがリージョンの境界を越えないようにするため、Azure Disk Encryption では Key Vault と VM を同じリージョンに併置する必要があります。 暗号化する VM と同じリージョン内に Key Vault を作成し、使用します。 


### <a name="bkmk_KVPSH"></a> PowerShell を使用してキー コンテナーを作成する

Azure PowerShell で [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) コマンドレットを使用してキー コンテナーを作成できます。 Key Vault のその他のコマンドレットについては、[Az.KeyVault](/powershell/module/az.keyvault/) を参照してください。 

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。 
2. 必要に応じて、[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して新しいリソース グループを作成します。  データセンターの場所を一覧表示するには、[Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用します。 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) を使用して、新しいキー コンテナーを作成します。
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 返される**コンテナー名**、**リソース グループ名**、**リソース ID**、**コンテナーの URI**、および**オブジェクト ID** をメモします。これらの情報は、後でディスクを暗号化するときに使用します。 


### <a name="bkmk_KVCLI"></a> Azure CLI を使用してキー コンテナーを作成する
[az keyvault](/cli/azure/keyvault#commands) コマンドを使用して Azure CLI でキー コンテナーを管理できます。 キー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用します。

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 必要に応じて、[az group create](/cli/azure/group#az-group-create) を使用して新しいリソース グループを作成します。 場所を一覧表示するには、[az account list-locations](/cli/azure/account#az-account-list) を使用します。 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. [az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用して、新しいキー コンテナーを作成します。
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. 返される**コンテナー名** (名前)、**リソース グループ名**、**リソース ID** (ID)、**コンテナーの URI**、および**オブジェクト ID** をメモします。これらの情報は後で使用します。 

### <a name="bkmk_KVRM"></a> Resource Manager テンプレートでキー コンテナーを作成する

[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)を使用してキー コンテナーを作成できます。

1. Azure クイックスタート テンプレートで、 **[Azure に配置する]** をクリックします。
2. サブスクリプション、リソース グループ、リソース グループの場所、キー コンテナー名、オブジェクト ID、法律条項および契約を選択し、 **[購入]** をクリックします。 


## <a name="bkmk_KVper"></a>キー コンテナーに高度なアクセス ポリシーを設定する
Azure プラットフォームには、Key Vault 内の暗号化キーまたはシークレットへのアクセス権を付与する必要があります。これにより、ボリュームをブートして暗号化する際に、それらの情報を VM に提供できるようになります。 キー コンテナーのディスク暗号化を有効にします。そうしないと、デプロイは失敗します。  

### <a name="bkmk_KVperPSH"></a> Azure PowerShell を使用してキー コンテナーに高度なアクセス ポリシーを設定する
 キー コンテナーの PowerShell コマンドレット [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を使用してキー コンテナーのディスク暗号化を有効にします。

  - **Key Vault のディスク暗号化を有効にする:** Azure Disk Encryption には EnabledForDiskEncryption が必要です。
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **必要に応じて Key Vault のデプロイを有効にする:** 仮想マシンの作成時など、このキー コンテナーがリソース作成時に参照される場合、Microsoft.Compute リソース プロバイダーがこのキー コンテナーからシークレットを取得できるようにします。

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **必要に応じて Key Vault のテンプレートのデプロイを有効にする:** テンプレートのデプロイでこのキー コンテナーが参照される場合、Azure Resource Manager がこのキー コンテナーからシークレットを取得できるようにします。

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Azure CLI を使用してキー コンテナーに高度なアクセス ポリシーを設定する
[az keyvault update](/cli/azure/keyvault#az-keyvault-update) を使用し、キー コンテナーのディスク暗号化を有効にします。 

 - **Key Vault のディスク暗号化を有効にする:** Enabled-for-disk-encryption が必要です。 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **必要に応じて Key Vault のデプロイを有効にする:** 仮想マシンの作成時など、このキー コンテナーがリソース作成時に参照される場合、Microsoft.Compute リソース プロバイダーがこのキー コンテナーからシークレットを取得できるようにします。

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **必要に応じて Key Vault のテンプレートのデプロイを有効にする:** Resource Manager がコンテナーからシークレットを取得することを許可します。
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Azure portal を使用してキー コンテナーに高度なアクセス ポリシーを設定する

1. キー コンテナーを選択し、 **[アクセス ポリシー]** に移動し、 **[クリックして高度なアクセス ポリシーを表示する]** を選択します。
2. **[ボリューム暗号化に対して Azure Disk Encryption へのアクセスを有効にする]** というボックスをオンにします。
3. 必要に応じて、 **[展開に対して Azure Virtual Machines へのアクセスを有効にする]** と **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** の一方または両方をオンにします。 
4. **[Save]** をクリックします。

    ![Azure Key Vault の高度なアクセス ポリシー](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> キー暗号化キーを設定する (省略可能)
暗号化キーのセキュリティに対する追加レイヤーとしてキー暗号化キー (KEK) を使用する場合は、キー コンテナーに KEK を追加します。 キー コンテナーにキー暗号化キーを作成するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) コマンドレットを使用します。 オンプレミスのキー管理 HSM から KEK をインポートすることもできます。 詳細については、[Key Vault](../key-vault/key-vault-hsm-protected-keys.md) のドキュメントを参照してください。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault への書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。

* キーを生成するときは、RSA のキーの種類を使用します。 Azure Disk Encryption では、楕円曲線キーの使用はまだサポートされていません。

* Key Vault シークレットおよび KEK URL のバージョン管理が必要です。 Azure では、このバージョン管理制限が適用されます。 有効なシークレットと KEK URI については、次の例をご覧ください。

  * 有効なシークレット URL の例: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有効な KEK URL の例: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption では、ポート番号をキー コンテナー シークレットおよび KEK URL の一部として指定することはサポートされません。 サポートされない Key Vault URL とサポートされる Key Vault URL の例については、以下の例を参照してください。

  * 許容できないキー コンテナー URL *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 許容されるキー コンテナー URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Azure PowerShell でキー暗号化キーを設定する 
PowerShell スクリプトを使用する前に、Azure Disk Encryption の前提条件をよく読み、スクリプトの手順を理解しておく必要があります。 サンプル スクリプトは、実際の環境に合わせて変更が必要な場合があります。 このスクリプトで、すべての Azure Disk Encryption の前提条件が作成され、既存の IaaS VM が暗号化され、キー暗号化キーを使用してディスク暗号化キーがラップされます。 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Windows で Azure Disk Encryption を有効にする](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Linux で Azure Disk Encryption を有効にする](azure-security-disk-encryption-linux.md)

