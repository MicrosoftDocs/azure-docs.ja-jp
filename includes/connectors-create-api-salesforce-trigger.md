このチュートリアルでは、Salesforce で新しい潜在顧客が作成される場合、ロジック アプリ ワークフローを開始するために、 **[Salesforce - When an object is created (Salesforce - オブジェクトが作成される場合)]** トリガーがどのように使用されるのかを学習します。

> [!NOTE]
> まだ Salesforce に *"接続"* を作成していない場合は、Salesforce アカウントにサインインするよう求められます。  
> 
> 

1. ロジック アプリ デザイナーの検索ボックスに「*salesforce*」と入力し、**[Salesforce - When an object is created (Salesforce - オブジェクトが作成される場合)]** トリガーを選択します。  
   ![Salesforce トリガー イメージ 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. **[When an object is created (オブジェクトが作成される場合)]** コントロールが表示されます。  
   ![Salesforce トリガー イメージ 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. **[オブジェクトの種類]** を選択し、オブジェクトのリストから *[潜在顧客]* を選択します。 この手順では、Salesforce で新しい潜在顧客が作成されるたびに、ロジック アプリを通知するトリガーを作成することが示されています。   
   ![Salesforce トリガー イメージ 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. これで終了です。 トリガーを作成しました。 ただし、これに有効なロジック アプリを作成するために、少なくとも 1 つのアクションを作成する必要があります。    
   ![Salesforce トリガー イメージ 4](./media/connectors-create-api-salesforce/trigger-4.png)   

これで、Salesforce で新しい項目が作成されるときに、ワークフローでその他のトリガーとアクションの実行を開始するトリガーのあるロジック アプリが構成されました。  



<!--HONumber=Nov16_HO3-->


