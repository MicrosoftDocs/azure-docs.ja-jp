
1. [Azure Portal](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

2. **[+新規]** > **[Compute]** > **[Function App]** をクリックし、**[サブスクリプション]** を選択します。Function App を識別する一意の**アプリ名**を入力し、次の設定を指定します。
   
   * **[[リソース グループ]](../articles/azure-resource-manager/resource-group-overview.md)**: **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 
   * **[[ホスティング プラン](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)]**: 次のいずれかになります。 
     * **従量課金プラン**: Azure Functions の既定のプランの種類です。 従量課金プランを選択した場合は、**[場所]** の選択と **[メモリ割り当て]** (MB 単位) の設定も行う必要があります。 メモリ割り当てのコストへの影響については、 [Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページをご覧ください。 
     * **App Service プラン**: App Service プランの場合、**App Service プラン/場所**を作成するか、既存のものを選択する必要があります。 これらの設定により、アプリに関連付けられる [場所、機能、コスト、コンピューティング リソース](https://azure.microsoft.com/pricing/details/app-service/) が決まります。  
   * **[ストレージ アカウント]**: 各 Function App には、ストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、[ストレージ アカウントを作成する](../articles/storage/storage-create-storage-account.md#create-a-storage-account)こともできます。 
     
    ![Azure Portal での Function App の作成](./media/functions-create-function-app-portal/function-app-create-flow.png)

3. **[作成]** をクリックして、新しい Function App をプロビジョニングし、デプロイします。  