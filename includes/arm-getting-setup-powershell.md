## <a name="setting-up-powershell-for-resource-manager-templates"></a>リソース マネージャー テンプレート向けの PowerShell の設定
リソース マネージャーで Azure PowerShell を使用する前に、適切なバージョンの Windows PowerShell と Azure PowerShell を用意する必要があります。

### <a name="verify-powershell-versions"></a>PowerShell のバージョンを確認する
Windows PowerShell Version 3.0 または 4.0 があることを確認します。 Windows PowerShell のバージョンを確認するには、Windows PowerShell コマンド プロンプトで次のコマンドを入力します。

    $PSVersionTable

次の種類の情報が表示されます。

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


**PSVersion** の値が 3.0 または 4.0 であることを確認します。 3.0 または 4.0 でない場合は、[Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)のダウンロード ページを参照してください。

### <a name="set-your-azure-account-and-subscription"></a>Azure アカウントとサブスクリプションを設定する
Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

Azure PowerShell コマンド プロンプトを開き、次のコマンドで Azure にログオンします。

    Login-AzureRmAccount

Azure サブスクリプションが複数ある場合は、次のコマンドで、Azure サブスクリプションの一覧を表示できます。

    Get-AzureRmSubscription

次の種類の情報が表示されます。

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Azure PowerShell コマンド プロンプトで次のコマンドを実行して、現在の Azure サブスクリプションを設定します。 引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Azure サブスクリプションとアカウントの詳細については、[サブスクリプションへの接続方法](/powershell/azureps-cmdlets-docs#step-3-connect)に関するトピックを参照してください。



<!--HONumber=Dec16_HO1-->


