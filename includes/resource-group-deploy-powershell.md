## <a name="how-to-deploy-with-powershell"></a>PowerShell でデプロイする方法
1. Azure アカウントにログインします。
   
          Add-AzureAccount
   
   資格情報を提供すると、コマンドがアカウントの情報を返します。
   
          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   
2. 複数のサブスクリプションがある場合、デプロイに使用するサブスクリプション ID を提供します。 
   
          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>
3. Azure リソース マネージャー モジュールに切り替える
   
          Switch-AzureMode AzureResourceManager
4. 既存のリソース グループがない場合は、新しいリソース グループを作成します。 ソリューションに必要なリソース グループと場所の名前を指定します。
   
        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
   新しいリソース グループの概要が返されます。
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup
5. リソース グループに新しいデプロイメントを作成するには、 **New-AzureResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。 パラメーターにはデプロイメントの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。 
   
   次のオプションを使用してパラメーターの値を提供できます。 
   
   * インライン パラメーターを使用する
     
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"
   * パラメーター オブジェクトを使用する
     
            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters
   * パラメーター ファイルの使用
     
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>
   
   リソース グループをデプロイすると、デプロイメントの概要が表示されます。
   
             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...
6. デプロイメント エラーに関する情報を取得するには
   
        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed
7. デプロイメント エラーに関する詳細情報を取得するには
   
        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput



<!--HONumber=Jan17_HO3-->


