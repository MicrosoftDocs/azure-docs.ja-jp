
## <a name="start-your-powershell-session"></a>PowerShell セッションの開始
まず、最新の [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) をインストールして実行する必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

> [!NOTE]
> このトピックの例では、[Azure Resource Manager デプロイメント モデル](../articles/azure-resource-manager/resource-group-overview.md)を使用するため、例では [Azure Resource Manager コマンドレット](http://msdn.microsoft.com/library/azure/mt125356.aspx)を使用します。 
> 
> 

[**Add-AzureRmAccount**](http://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを実行すると、資格情報を入力するサインイン画面が表示されます。 Azure ポータルにサインインするときと同じ資格情報を使用してください。

    Add-AzureRmAccount

複数のサブスクリプションがある場合は、[**Set-AzureRmContext**](http://msdn.microsoft.com/library/mt619263.aspx) コマンドレットを使用して、PowerShell セッションで使用するサブスクリプションを選択します。 PowerShell セッションが現在使用しているサブスクリプションを確認するには、[**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx) を実行します。 すべてのサブスクリプションを表示するには、[**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx) を実行します。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'



<!--HONumber=Dec16_HO1-->


