## <a name="set-up-azure-powershell-for-azure-dns"></a>Azure DNS 用に Azure PowerShell をセットアップする

### <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下がそろっていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレットの最新版をインストールする必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

PowerShell コンソールを開き、アカウントに接続します。 詳細については、[Resource Manager での PowerShell の使用](../articles/azure-resource-manager/powershell-azure-resource-manager.md)に関するページを参照してください。

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>サブスクリプションの選択
 
アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

使用する Azure サブスクリプションを選択します。

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>リソース プロバイダーの登録

Azure DNS サービスは Microsoft.Network リソース プロバイダーによって管理されます。 Azure DNS を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。 この操作は、サブスクリプションごとに 1 回だけ実行します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

<!--HONumber=Dec16_HO3-->


