### <a name="prerequisites"></a>前提条件
* [SendGrid](https://www.SendGrid.com/) アカウント 

ロジック アプリで SendGrid アカウントを使用するには、SendGrid アカウントに接続するロジック アプリを承認する必要があります。 幸い、Azure ポータルのロジック アプリ内から簡単に実行できます。 

次に、SendGrid アカウントに接続するロジック アプリを承認する手順を示します。

1. SendGrid への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「*SendGrid*」と入力します。 使用するトリガーまたはアクションを選択します。  
   ![SendGrid 手順 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. これまでに SendGrid への接続を作成していない場合は、SendGrid の資格情報を指定するよう求められます。 この資格情報を使用して、接続するロジック アプリの承認と、SendGrid アカウントのデータへのアクセスが行われます。  
   ![SendGrid 手順 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。  
   ![SendGrid 手順 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   



<!--HONumber=Nov16_HO3-->


