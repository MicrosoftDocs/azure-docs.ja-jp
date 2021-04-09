---
title: Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)
description: この記事では、Linux VM 用に Microsoft Azure Disk Encryption を使用する場合の前提条件について説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 20cb94dd8bfca6adeba151d2169b1896cc7ff5a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557881"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release-for-linux-vms"></a>Linux VM 用 Azure AD (以前のリリース) を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成

**Azure Disk Encryption の新しいリリースでは、VM ディスク暗号化を有効にするために Azure AD アプリケーション パラメーターを指定する必要はありません。新しいリリースでは、暗号化を有効にする手順の途中で、Azure AD の資格情報を指定する必要がなくなりました。すべての新しい VM は、新しいリリースを使用して、Azure AD アプリケーション パラメーターを指定せずに暗号化する必要があります。新しいリリースを使用して VM のディスク暗号化を有効にする手順を表示するには、[Azure Disk Encryption](disk-encryption-overview.md) に関するページを参照してください。Azure AD アプリケーション パラメーターで既に暗号化された VM はまだサポートされていますが、AAD 構文を使用して保持し続ける必要があります。**

Azure Disk Encryption では、Azure Key Vault を使用して、ディスク暗号化キーとシークレットを制御および管理します。  キー コンテナーの詳細については、「[Azure Key Vault の概要](../../key-vault/general/overview.md)」と「[キー コンテナーのセキュリティ保護](../../key-vault/general/secure-your-key-vault.md)」を参照してください。 

Azure AD (以前のリリース) を使用して Azure Disk Encryption で使用するためのキー コンテナーの作成と構成には、次の 3 つの手順が含まれます。

1. Key Vault を作成します。 
2. Azure AD アプリケーションとサービス プリンシパルを設定します。
3. Azure AD アプリのキー コンテナー アクセス ポリシーを設定します。
4. キー コンテナーに高度なアクセス ポリシーを設定します。
 
また、必要に応じて、キー暗号化キー (KEK) を生成またはインポートすることもできます。

[ツールをインストールして Azure に接続する](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)手順については、メイン記事「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」を参照してください。

> [!Note]
> この記事の手順は、[Azure Disk Encryption の前提条件となる CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started)および [Azure Disk Encryption の前提条件となる PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)に関するページで自動化されています。


## <a name="create-a-key-vault"></a>Key Vault を作成します 
Azure Disk Encryption は [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 Azure Disk Encryption ではキー コンテナーを作成するか、既存のコンテナーを使用することができます。 キー コンテナーの詳細については、「[Azure Key Vault の概要](../../key-vault/general/overview.md)」と「[キー コンテナーのセキュリティ保護](../../key-vault/general/secure-your-key-vault.md)」を参照してください。 Resource Manager テンプレート、Azure PowerShell、または Azure CLI を使用してキー コンテナーを作成できます。 


>[!WARNING]
>暗号化シークレットがリージョンの境界を超えないようにするため、Azure Disk Encryption では Key Vault と VM を同じリージョンに併置する必要があります。 暗号化する VM と同じリージョン内に Key Vault を作成し、使用します。 


### <a name="create-a-key-vault-with-powershell"></a><a name="bkmk_KVPSH"></a> PowerShell を使用してキー コンテナーを作成する

Azure PowerShell で [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) コマンドレットを使用してキー コンテナーを作成できます。 Key Vault のその他のコマンドレットについては、[Az.KeyVault](/powershell/module/az.keyvault/) を参照してください。 

1. 必要に応じて、[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して新しいリソース グループを作成します。  データセンターの場所を一覧表示するには、[Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用します。 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) を使用して、新しいキー コンテナーを作成します。
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 返される **コンテナー名**、**リソース グループ名**、**リソース ID**、**コンテナーの URI**、および **オブジェクト ID** をメモします。これらの情報は、後でディスクを暗号化するときに使用します。 


### <a name="create-a-key-vault-with-azure-cli"></a><a name="bkmk_KVCLI"></a> Azure CLI を使用してキー コンテナーを作成する
[az keyvault](/cli/azure/keyvault#commands) コマンドを使用して Azure CLI でキー コンテナーを管理できます。 キー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用します。

1. 必要に応じて、[az group create](/cli/azure/group#az-group-create) を使用して新しいリソース グループを作成します。 場所を一覧表示するには、[az account list-locations](/cli/azure/account#az-account-list) を使用します。 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. [az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用して、新しいキー コンテナーを作成します。
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. 返される **コンテナー名** (名前)、**リソース グループ名**、**リソース ID** (ID)、**コンテナーの URI**、および **オブジェクト ID** をメモします。これらの情報は後で使用します。 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a><a name="bkmk_KVRM"></a> Resource Manager テンプレートでキー コンテナーを作成する

[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)を使用してキー コンテナーを作成できます。

1. Azure クイックスタート テンプレートで、 **[Azure に配置する]** をクリックします。
2. サブスクリプション、リソース グループ、リソース グループの場所、キー コンテナー名、オブジェクト ID、法律条項および契約を選択し、 **[購入]** をクリックします。 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a><a name="bkmk_ADapp"></a> Azure AD アプリとサービス プリンシパルを設定する 
Azure で実行中の VM に対して暗号化を有効にする必要がある場合、Azure Disk Encryption により、暗号化キーが生成され、Key Vault に書き込まれます。 Key Vault の暗号化キーを管理するには、Azure AD 認証が必要です。 この目的で Azure AD アプリケーションを作成します。 認証には、クライアント シークレット ベースの認証か、[クライアント証明書ベースの Azure AD 認証](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)を使用できます。


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a><a name="bkmk_ADappPSH"></a> Azure PowerShell で Azure AD アプリとサービス プリンシパルを設定する 
次のコマンドを実行するには、[Azure AD PowerShell モジュール](/powershell/azure/active-directory/install-adv2)を取得して使用します。 

1. [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell コマンドレットを使用して Azure AD アプリケーションを作成します。 MyApplicationHomePage と MyApplicationUri には、任意の値を指定できます。

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId は Azure AD ClientID、$aadClientSecret はクライアント シークレットです。これらは、Azure Disk Encryption を有効にするために後で使用します。 Azure AD のクライアント シークレットは適切に保護してください。 `$azureAdApplication.ApplicationId` を実行すると、ApplicationID が表示されます。


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a><a name="bkmk_ADappCLI"></a> Azure CLI で Azure AD アプリとサービス プリンシパルを設定する

Azure CLI で [az ad sp](/cli/azure/ad/sp) コマンドを使用してサービス プリンシパルを管理できます。 詳細については、[Azure サービス プリンシパルの作成](/cli/azure/create-an-azure-service-principal-azure-cli)に関するページを参照してください。

1. 新しいサービス プリンシパルを作成します。
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  返される appId は、他のコマンドで使用される Azure AD ClientID です。 これは、az keyvault set-policy に使用する SPN でもあります。 パスワードは、あとで Azure Disk Encryption を有効にするために使用するクライアント シークレットです。 Azure AD のクライアント シークレットは適切に保護してください。
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a><a name="bkmk_ADappRM"></a> Azure portal を使用して Azure AD アプリとサービス プリンシパルを設定する
[ポータルを使用した、リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関する記事の手順を使用して、Azure AD アプリケーションを作成します。 以下の各手順で、完了する必要がある記事のセクションに直接進むことができます。 

1. [必要なアクセス許可を確認する](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Azure Active Directory アプリケーションを作成する](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - アプリケーションの作成時には任意の名前とサインオン URL を使用できます。
3. [アプリケーション ID と認証キーを取得する](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 
     - 認証キーはクライアント シークレットであり、Set-AzVMDiskEncryptionExtension の AadClientSecret として使用されます。 
        - 認証キーは、Azure AD にサインインするための資格情報としてアプリケーションによって使用されます。 Azure portal では、このシークレットはキーと呼ばれますが、キー コンテナーとは関係がありません。 このシークレットはセキュリティで適切に保護してください。 
     - アプリケーション ID は、あとで Set-AzVMDiskEncryptionExtension の AadClientId と Set-AzKeyVaultAccessPolicy の ServicePrincipalName として使用されます。 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a><a name="bkmk_KVAP"></a> Azure AD アプリのキー コンテナー アクセス ポリシーを設定する
指定した Key Vault に暗号化シークレット情報を書き込むには、シークレットを Key Vault に書き込むアクセス許可を持つ Azure Active Directory アプリケーションのクライアント ID とクライアント シークレットが Azure Disk Encryption に必要です。 

> [!NOTE]
> Azure Disk Encryption では、Azure AD のクライアント アプリケーションに対して、次のアクセス ポリシーを構成する必要があります:_WrapKey_ および _Set_ アクセス許可。

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a><a name="bkmk_KVAPPSH"></a> Azure PowerShell を使用して Azure AD アプリのキー コンテナー アクセス ポリシーを設定する
Azure AD アプリケーションには、Vault 内のキーまたはシークレットへのアクセス権が必要です。 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) コマンドレットで、 _–ServicePrincipalName_ パラメーター値としてクライアント ID (アプリケーションの登録時に生成されたもの) を使用して、アプリケーションに権限を付与します。 詳しくは、ブログ投稿「[Azure Key Vault - Step by Step](/archive/blogs/kv/azure-key-vault-step-by-step)」(Azure Key Vault - 手順) をご覧ください。 

1. PowerShell を使用して AD アプリケーションのキー コンテナー アクセス ポリシーを設定します。

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a><a name="bkmk_KVAPCLI"></a> Azure CLI を使用して Azure AD アプリのキー コンテナー アクセス ポリシーを設定する
[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) を使用してアクセス ポリシーを設定します。 詳細については、[CLI 2.0 を使用した Key Vault の管理](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)に関するページを参照してください。

次のコマンドを使用して、Azure CLI アクセス経由で作成したサービス プリンシパルにシークレット キーとラップ キーを渡します。

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a><a name="bkmk_KVAPRM"></a> ポータルを使用して Azure AD アプリのキー コンテナー アクセス ポリシーを設定する

1. キー コンテナーを使用してリソース グループを開きます。
2. キー コンテナーを選択し、 **[アクセス ポリシー]** に移動し、 **[新規追加]** をクリックします。
3. **[プリンシパルの選択]** で、作成した Azure AD アプリケーションを検索して選択します。 
4. **[キーのアクセス許可]** で、 **[暗号化操作]** の **[キーを折り返す]** をオンにします。
5. **[シークレットのアクセス許可]** で、 **[シークレットの管理操作]** の **[設定]** をオンにします。
6. **[OK]** をクリックしてアクセス ポリシーを保存します。 

![Azure Key Vault の [暗号化操作] - [キーを折り返す]](./media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault の [シークレットのアクセス許可] - [設定]](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a><a name="bkmk_KVper"></a>キー コンテナーに高度なアクセス ポリシーを設定する
Azure プラットフォームには、Key Vault 内の暗号化キーまたはシークレットへのアクセス権を付与する必要があります。これにより、ボリュームをブートして暗号化する際に、それらの情報を VM に提供できるようになります。 キー コンテナーのディスク暗号化を有効にします。そうしないと、デプロイは失敗します。  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a><a name="bkmk_KVperPSH"></a> Azure PowerShell を使用してキー コンテナーに高度なアクセス ポリシーを設定する
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

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a><a name="bkmk_KVperCLI"></a> Azure CLI を使用してキー コンテナーに高度なアクセス ポリシーを設定する
[az keyvault update](/cli/azure/keyvault#az-keyvault-update) を使用し、キー コンテナーのディスク暗号化を有効にします。 

 - **Key Vault のディスク暗号化を有効にする:** Enabled-for-disk-encryption が必要です。 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **必要に応じて Key Vault のデプロイを有効にする:** 資格情報コンテナーからシークレットとして保存されている証明書を取得することを Virtual Machines に許可します。
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **必要に応じて Key Vault のテンプレートのデプロイを有効にする:** Resource Manager がコンテナーからシークレットを取得することを許可します。
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a><a name="bkmk_KVperrm"></a> Azure portal を使用してキー コンテナーに高度なアクセス ポリシーを設定する

1. キー コンテナーを選択し、 **[アクセス ポリシー]** に移動し、 **[クリックして高度なアクセス ポリシーを表示する]** を選択します。
2. **[ボリューム暗号化に対して Azure Disk Encryption へのアクセスを有効にする]** というボックスをオンにします。
3. 必要に応じて、 **[展開に対して Azure Virtual Machines へのアクセスを有効にする]** と **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** の一方または両方をオンにします。 
4. **[保存]** をクリックします。

![Azure Key Vault の高度なアクセス ポリシー](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a><a name="bkmk_KEK"></a> キー暗号化キーを設定する (省略可能)
暗号化キーのセキュリティに対する追加レイヤーとしてキー暗号化キー (KEK) を使用する場合は、キー コンテナーに KEK を追加します。 キー コンテナーにキー暗号化キーを作成するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) コマンドレットを使用します。 オンプレミスのキー管理 HSM から KEK をインポートすることもできます。 詳細については、[Key Vault](../../key-vault/keys/hsm-protected-keys.md) のドキュメントを参照してください。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault への書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。 

* キーを生成するときは、RSA のキーの種類を使用します。 Azure Disk Encryption では、楕円曲線キーの使用はまだサポートされていません。

* Key Vault シークレットおよび KEK URL のバージョン管理が必要です。 Azure では、このバージョン管理制限が適用されます。 有効なシークレットと KEK URI については、次の例をご覧ください。

  * 有効なシークレット URL の例: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有効な KEK URL の例: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption では、ポート番号をキー コンテナー シークレットおよび KEK URL の一部として指定することはサポートされません。 サポートされない Key Vault URL とサポートされる Key Vault URL の例については、以下の例を参照してください。

  * 許容できないキー コンテナー URL *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 許容されるキー コンテナー URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a><a name="bkmk_KEKPSH"></a> Azure PowerShell でキー暗号化キーを設定する 
PowerShell スクリプトを使用する前に、Azure Disk Encryption の前提条件をよく読み、スクリプトの手順を理解しておく必要があります。 サンプル スクリプトは、実際の環境に合わせて変更が必要な場合があります。 このスクリプトで、すべての Azure Disk Encryption の前提条件が作成され、既存の IaaS VM が暗号化され、キー暗号化キーを使用してディスク暗号化キーがラップされます。 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a><a name="bkmk_Cert"></a> 証明書ベースの認証 (省略可能)
証明書認証を使用する場合は、キー コンテナーにアップロードして、それをクライアントに展開することができます。 PowerShell スクリプトを使用する前に、Azure Disk Encryption の前提条件をよく読み、スクリプトの手順を理解しておく必要があります。 サンプル スクリプトは、実際の環境に合わせて変更が必要な場合があります。

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a><a name="bkmk_CertKEK"></a> 証明書ベースの認証と KEK (省略可能)

証明書認証を使用し、暗号化キーを KEK でラップする場合は、例として以下のスクリプトを使用できます。 PowerShell スクリプトを使用する前に、前述の Azure Disk Encryption の前提条件をすべてよく読み、スクリプトの手順を理解しておく必要があります。 サンプル スクリプトは、実際の環境に合わせて変更が必要な場合があります。

> [!IMPORTANT]
> Azure AD の証明書ベースの認証は、現時点では Linux VM ではサポートされていません。



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>次のステップ

[Linux VM で Azure AD を使用して Azure Disk Encryption を有効にする (以前のリリース)](disk-encryption-linux-aad.md)
