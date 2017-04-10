### <a name="prerequisites"></a>前提条件
* Twilio アカウント
* SMS を受信できる確認済みの Twilio 電話番号
* SMS を送信できる確認済みの Twilio 電話番号

> [!NOTE]
> Twilio の試用アカウントを使用している場合は、**確認済み**の電話番号にのみ SMS を送信できます。  
> 
> 

ロジック アプリで Twilio アカウントを使用するには、Twilio アカウントに接続するロジック アプリを承認する必要があります。 幸い、Azure ポータルのロジック アプリ内から簡単に実行できます。 

次に、Twilio アカウントに接続するロジック アプリを承認する手順を示します。

1. Twilio への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「*Twilio*」と入力します。 使用するトリガーまたはアクションを選択します。  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. これまでに Twilio への接続を作成していない場合は、Twilio の資格情報を指定するよう求められます。 この資格情報を使用して、接続するロジック アプリの承認と、Twilio アカウントのデータへのアクセスが行われます。  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Twilio のダッシュボードから **Twilio アカウント ID** と **Twilio アクセス トークン**が必要になるため、ここで Twilio アカウントにログインし、これらの 2 つの情報を取得します。  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio とロジック アプリでは別の名前を使用して、これらの 2 つの情報を識別します。 次のようにして、これらの名前をロジック アプリのダイアログに対応付ける必要があります。![](./media/connectors-create-api-twilio/twilio-3.png)  
5. **[接続の作成]** ボタンを選択します。  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。  
   ![](./media/connectors-create-api-twilio/twilio-5.png)



<!--HONumber=Nov16_HO3-->


