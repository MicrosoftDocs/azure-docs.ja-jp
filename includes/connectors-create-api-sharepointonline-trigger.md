この例では、SharePoint Online の一覧で新しい項目が作成される場合、ロジック アプリ ワークフローを開始するために、 **[SharePoint Online - When a new item is created (SharePoint Online - 新しい項目が作成される場合)]** トリガーがどのように使用されるのかを示します。

> [!NOTE]
> まだ SharePoint Online に *"接続"* を作成していない場合は、SharePoint アカウントにサインインするよう求められます。  
> 
> 

1. ロジック アプリ デザイナーの検索ボックスに「*sharepoint*」と入力し、**[SharePoint Online - When a new item is created (SharePoint Online - 新しい項目が作成される場合)]** トリガーを選択します。  
   ![SharePoint オンライン トリガー イメージ ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. **[When a new item is created (新しい項目が作成される場合)]** コントロールが表示されます。  
   ![SharePoint オンライン トリガー イメージ 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. **[サイトの URL]**を選択します。 これは、ワークフローをトリガーするための新しい項目を監視する SharePoint online サイトです。  
   ![SharePoint オンライン トリガー イメージ 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. **[List name (リスト名)]**を選択します。 これは、ワークフローをトリガーする新しい項目を監視する SharePoint Online サイトにある一覧です。  
   ![SharePoint オンライン トリガー イメージ 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

これで、ワークフローでその他のトリガーとアクションの実行を開始するトリガーのあるロジック アプリが構成されました。 選択した SharePoint Online の一覧に新しい項目が作成されるたびにこれが行われます。  



<!--HONumber=Nov16_HO3-->


