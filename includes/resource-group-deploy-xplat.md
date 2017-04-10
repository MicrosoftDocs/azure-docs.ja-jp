## <a name="how-to-deploy-with-azure-cli"></a>Azure CLI でデプロイする方法
1. Azure アカウントにログインします。
   
        azure login
   
   資格情報を提供すると、コマンドがログインの結果を返します。
   
        ...
        info:    login command OK
2. 複数のサブスクリプションがある場合、デプロイメントに使用するサブスクリプション ID を提供します。
   
        azure account set <YourSubscriptionNameOrId>
3. Azure リソース マネージャー モジュールに切り替える
   
        azure config mode arm
   
   新しいモードの確認が表示されます。
   
        info:     New mode is arm
4. 既存のリソース グループがない場合は、新しいリソース グループを作成します。 ソリューションに必要なリソース グループと場所の名前を指定します。
   
        azure group create -n ExampleResourceGroup -l "West US"
   
   新しいリソース グループの概要が返されます。
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK
5. リソース グループに新しいデプロイメントを作成するには、次のコマンドを実行して必要なパラメーターを指定します。 パラメーターにはデプロイメントの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。
   
   次のオプションを使用してパラメーターの値を提供できます。
   
   * インライン パラメーターとローカルのテンプレートを使用します。
     
             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment
   * インライン パラメーターとローカルのテンプレートへのリンクを使用します。
     
             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment
   * パラメーター ファイルを使用します。
     
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment
   
   リソース グループをデプロイすると、デプロイメントの概要が表示されます。
   
         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK
6. 最新のデプロイメントに関する情報を取得するには
   
         azure group log show -l ExampleResourceGroup
7. デプロイメント エラーに関する詳細情報を取得するには
   
         azure group log show -l -v ExampleResourceGroup



<!--HONumber=Jan17_HO3-->


