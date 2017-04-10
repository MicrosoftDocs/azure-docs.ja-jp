ここでは、新しい項目が Service Bus キューに送信された場合、ロジック アプリ ワークフローを開始するために、 **[Service Bus - When a message is received in a queue (Service Bus - メッセージをキューで受信する場合)]** トリガーがどのように使用されるのかを示します。  

> [!NOTE]
> Service Bus に接続していない場合は、Service Bus 接続文字列でサインインするよう求められます。  
> 
> 

1. ロジック アプリ デザイナーの検索ボックスに、「 **サービス バス**」と入力します。 次に、 **[Service Bus - When a message is received in a queue (Service Bus - メッセージをキューで受信する場合)]** トリガーを選択します。  
   ![Service Bus トリガー イメージ 1](./media/connectors-create-api-servicebus/trigger-1.png)   
2. **[When a message is received in a queue (メッセージをキューで受信する場合)]** ダイアログ ボックスが表示されます。  
   ![Service Bus トリガー イメージ 2](./media/connectors-create-api-servicebus/trigger-2.png)   
3. 監視するトリガーの Service Bus キューの名前を入力します。   
   ![Service Bus トリガー イメージ 3](./media/connectors-create-api-servicebus/trigger-3.png)   

この時点で、ロジック アプリはトリガーで構成されています。 選択したキューで新しい項目を受信すると、そのトリガーは、ワークフローでその他のトリガーとアクションの実行を開始します。    



<!--HONumber=Nov16_HO3-->


