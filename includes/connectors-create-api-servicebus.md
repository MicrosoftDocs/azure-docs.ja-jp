### <a name="prerequisites"></a>前提条件
[Service Bus](https://azure.microsoft.com/services/service-bus/) アカウントが必要です。  

ロジック アプリで Azure Service Bus アカウントを使用するには、Service Bus アカウントに接続するロジック アプリを承認する必要があります。 これは、Azure ポータルのロジック アプリ内から簡単に実行できます。  

Service Bus アカウントに接続するロジック アプリを承認する手順を次に示します。  

1. Service Bus への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Microsoft が管理している API を表示]** を選択します。 検索ボックスに「**service bus**」と入力します。 使用するトリガーまたはアクションを選択します。  
    ![Service Bus の接続イメージ 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. これまでに Service Bus への接続を作成したことがない場合は、Service Bus の資格情報の入力を求められます。 この資格情報を使用して、接続するロジック アプリの承認と、Service Bus アカウントのデータへのアクセスが行われます。 Service Bus コネクタでは、Service Bus 名前空間に接続文字列が必要です。 また、**管理**アクセス許可も必要です。 接続文字列が名前空間を対象としているか、特定のエンティティを対象としているかを確認するには、その接続文字列に `EntityPath` パラメーターが含まれるかどうかを確認することをお勧めします。 含まれる場合、それはロジック アプリに適切な接続文字列ではありません。  
    ![Service Bus の接続文字列](./media/connectors-create-api-servicebus/connectionstring.png)
3. 名前空間の接続文字列を受け取ったら、Logic Apps で API 接続に使用できます。  
    ![Service Bus の接続イメージ 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。  
    ![Service Bus の接続イメージ 3](./media/connectors-create-api-servicebus/servicebus-3.png)   



<!--HONumber=Nov16_HO3-->


