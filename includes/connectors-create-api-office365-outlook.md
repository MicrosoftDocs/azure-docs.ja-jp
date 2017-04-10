#### <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* [Office 365](https://office365.com) アカウント  

ロジック アプリで Office 365 アカウントを使用するには、Office 365 アカウントに接続するロジック アプリを承認してください。 これは、Azure ポータルのロジック アプリ内で簡単に実行できます。  

次の手順に従って、Office 365 アカウントに接続するロジック アプリを承認します。

1. ロジック アプリを作成します。 Logic Apps デザイナーで、ドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「Office 365」と入力します。 トリガーまたはアクションの 1 つを選択します。  
    ![Office 365 の接続の作成手順](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. これまでに Office 365 への接続を作成したことがない場合は、Office 365 の資格情報を使用してサインインするよう求められます。  
    ![Office 365 の接続の作成手順](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. **[サインイン]** を選択し、ユーザー名とパスワードを入力します。 **[サインイン]** をクリックします。  
    ![Office 365 の接続の作成手順](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    これらの資格情報を使用して、接続するロジック アプリの承認と、Office 365 アカウントへのアクセスが行われます。 
4. 接続が作成されたことを確認します。 これで、ロジック アプリで他の手順に進むことができます。   
    ![Office 365 の接続の作成手順](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  



<!--HONumber=Nov16_HO3-->


