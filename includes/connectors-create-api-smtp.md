### <a name="prerequisites"></a>前提条件
* [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) アカウント  

ロジック アプリで SMTP アカウントを使用するには、SMTP アカウントに接続するロジック アプリを承認しておく必要があります。これは、Azure ポータルのロジック アプリ内から簡単に実行できます。  

SMTP アカウントに接続するロジック アプリを承認する手順を次に示します。  

1. SMTP への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「*SMTP*」と入力します。 使用するトリガーまたはアクションを選択します。  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. これまでに SMTP への接続を作成したことがない場合は、SMTP の資格情報の入力を求められます。 この資格情報を使用して、接続するロジック アプリの承認と、SMTP アカウントのデータへのアクセスが行われます。  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  



<!--HONumber=Nov16_HO3-->


