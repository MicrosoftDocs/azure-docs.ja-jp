## <a name="prepare-for-akv-integration"></a>AKV 統合の準備
Azure Key Vault 統合を使用し、SQL Server VM を構成するには、いくつかの前提条件があります。 

1. [Azure PowerShell をインストールする](#install-azure-powershell)
2. [Azure Active Directory を作成する](#create-an-azure-active-directory)
3. [Key Vault を作成します](#create-a-key-vault)

次のセクションでは、これらの前提条件と、後に PowerShell コマンドレットを実行するために必要な情報について説明します。

### <a name="install-azure-powershell"></a>Azure PowerShell をインストールする
最新の Azure PowerShell SDK がインストールされていることを確認します。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

### <a name="create-an-azure-active-directory"></a>Azure Active Directory を作成する
最初に、サブスクリプションに [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) を追加する必要があります。 特定のユーザーやアプリケーションが Key Vault にアクセスするための許可が与えられるなど、さまざまな利点があります。

次に、アプリケーションを AAD に登録します。 これで VM に必要な Key Vault へのアクセス許可を持つサービス プリンシパル アカウントが与えられます。 Azure Key Vault の記事の「[Azure Active Directory にアプリケーションを登録する](../articles/key-vault/key-vault-get-started.md#register)」セクションにこれらの手順があります。あるいは、[このブログ投稿](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)の「**Get an identity for the application (アプリケーションの ID を取得する)**」セクションのスクリーンショットで手順を確認できます。 これらの手順を完了する前に、後で SQL VM で Azure Key Vault 統合を有効にするときに必要になる次の情報をこの登録中に集める必要があります。

* アプリケーションが追加されたら、**[構成]** タブで **[クライアント ID]** を探します。 
    ![Azure Active Directory クライアント ID](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
  
    クライアント ID は後に PowerShell スクリプトの **$spName** (サービス プリンシパル名) パラメーターに割り当てられ、Azure Key Vault 統合を有効にします。 
* また、これらの手順で、鍵を作成するとき、次のスクリーンショットのように、鍵のシークレットをコピーします。 この鍵シークレットは後に PowerShell スクリプトの **$spSecret** (サービス プリンシパル シークレット) パラメーターに割り当てられます。  
    ![Azure Active Directory シークレット](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
* この新しいクライアント ID に権限を与え、アクセス許可 (**encrypt**、**decrypt**、**wrapKey**、**unwrapKey**、**sign**、**verify**) を与える必要があります。 これは [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) コマンドレットで行われます。 詳細については、「 [キーまたはシークレットを使用してアプリケーションを承認する](../articles/key-vault/key-vault-get-started.md#authorize)」を参照してください。

### <a name="create-a-key-vault"></a>Key Vault を作成します
Azure Key Vault を使用して VM の暗号化に使用する鍵を保存するには、Key Vault へのアクセス許可が必要です。 Key Vault をまだ設定していない場合、「 [Azure Key Vault の概要](../articles/key-vault/key-vault-get-started.md) 」トピックの手順で作成します。 これらの手順を完了する前に、後で SQL VM で Azure Key Vault 統合を有効にするときに必要になるいくつかの情報をこの設定中に集める必要があります。

"Key Vault を作成する" 手順に入ったら、返された **vaultUri** プロパティをメモします。これは Key Vault の URL です。 この手順の例では、下のように、Key Vault の名前は ContosoKeyVault であり、Key Vault URL は https://contosokeyvault.vault.azure.net/ になります。

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Key Vault ID は後に PowerShell スクリプトの **$akvURL** パラメーターに割り当てられ、Azure Key Vault 統合を有効にします。



<!--HONumber=Dec16_HO1-->


