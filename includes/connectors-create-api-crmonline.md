#### <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) アカウント 

ロジック アプリで Dynamics アカウントを使用するには、CRM Online アカウントに接続するロジック アプリを承認してください。 これは、Azure ポータルのロジック アプリ内で簡単に実行できます。 

次の手順に従って、CRM Online アカウントに接続するロジック アプリを承認します。

1. ロジック アプリを作成します。 Logic Apps デザイナーで、ドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「dynamics」と入力します。 トリガーまたはアクションの 1 つを選択します。  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. これまでに Dynamics への接続を作成したことがない場合は、Dynamics の資格情報を使用してサインインするよう求められます。  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. **[サインイン]** を選択し、ユーザー名とパスワードを入力します。 **[サインイン]** をクリックします。 
   
    この資格情報は、接続するロジック アプリの承認と、Dynamics アカウントのデータへのアクセスに使用されます。 
4. 接続が作成されたことを確認します。 これで、ロジック アプリで他の手順に進むことができます。  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)



<!--HONumber=Nov16_HO3-->


