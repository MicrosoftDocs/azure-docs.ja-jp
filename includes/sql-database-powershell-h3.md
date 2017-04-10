
### <a name="start-your-powershell-session"></a>PowerShell セッションの開始
まず、最新の [Azure PowerShell](https://msdn.microsoft.com/library/mt619274\(v=azure.300\).aspx) をインストールして実行する必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

> [!NOTE]
> SQL Database の新機能の多くは、[Azure Resource Manager デプロイ モデル](../articles/azure-resource-manager/resource-group-overview.md)を使用している場合にのみサポートされます。したがって、各例では、Resource Manager の [Azure SQL Database PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)を使用しています。 サービス管理 (クラシック) デプロイ モデルの [Azure SQL Database サービス管理コマンドレット](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) は下位互換性を確保するためにサポートされていますが、Resource Manager のコマンドレットを使用することをお勧めします。
> 
> 

[**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) コマンドレットを実行すると、資格情報を入力するサインイン画面が表示されます。 Azure ポータルにサインインするときと同じ資格情報を使用してください。

    Add-AzureRmAccount

複数のサブスクリプションがある場合は、[**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) コマンドレットを使用して、PowerShell セッションで使用するサブスクリプションを選択します。 PowerShell セッションが現在使用しているサブスクリプションを確認するには、[**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx) を実行します。 すべてのサブスクリプションを表示するには、[**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx) を実行します。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Dec16_HO2-->


