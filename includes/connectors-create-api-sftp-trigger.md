トリガーを追加します。

1. ロジック アプリ デザイナーの検索ボックスに「*sftp*」と入力し、**[SFTP - When a file is added or modified (SFTP - ファイルが追加または変更されたとき)]** トリガーを選択します。   
   ![SFTP トリガー イメージ 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. **[When a file is added or modified (ファイルが追加または変更されたとき)]** コントロールを開きます。  
   ![SFTP トリガー イメージ 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. コントロールの右側にある **[...]** を選択します。 これによりフォルダー ピッカー コントロールが開きます。  
   ![SFTP トリガー イメージ 3](./media/connectors-create-api-sftp/action-1.png)  
4. **[SFTP]** を選択して、新規のまたは変更されたファイルを監視するフォルダーとしてルート フォルダーを選択します。 **[フォルダー]** コントロールにルート フォルダーが表示されていることに注意してください。  
   ![SFTP トリガー イメージ 4](./media/connectors-create-api-sftp/action-2.png)   

これで、ファイルが特定の SFTP フォルダーに変更または作成されるときに、ワークフローでその他のトリガーとアクションの実行を開始するトリガーのあるロジック アプリが構成されました。 

> [!NOTE]
> ロジック アプリを機能させるためには、少なくとも 1 つのトリガーとアクションを含まなければなりません。 アクションを追加するには、次のセクションの手順に従います。  
> 
> 



<!--HONumber=Nov16_HO3-->


