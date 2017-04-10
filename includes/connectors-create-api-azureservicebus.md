トリガーを追加すると、それによって生成されるデータと関係ができます。 次の手順を従って、 **[SharePoint Online - Create file (SharePoint Online - ファイルの作成)]** の操作を追加してください。 この操作により新しい項目のトリガーが起動するたびに、SharePoint Online にファイルが作成されます。 

この操作を設定するためには、次の情報を提供する必要があります。 新しいファイルのプロパティの一部の情報として、トリガーによって生成されたデータを簡単に使用できることを確認します。

| ファイルプロパティの作成 | 説明 |
| --- | --- |
| サイトの URL |これは、新しいファイルを作成する SharePoint Online サイトの URL です。 表示されるリストから、サイトを選択します。 |
| フォルダー パス |これは、新しいファイルを配置するフォルダー (サイトの URL) です。 フォルダーを参照して、選択します。 |
| ファイル名 |これは、作成されるファイルの名前です。 |
| ファイルのコンテンツ |コンテンツは、ファイルに書き込まれます。 |

1. **[+ New step (+ 新しいステップ)]** を選択して 、操作を追加します。  
   ![](./media/connectors-create-api-sharepointonline/action-1.png)  
2. **[アクションの追加]** のリンクを選択します。 これにより検索ボックスが開き、行う操作について検索できます。 この例では、SharePoint の操作が示されています。    
   ![](./media/connectors-create-api-sharepointonline/action-2.png)    
3. 「 *sharepoint* 」と入力して、 SharePoint に関連する操作を検索します。
4. 実行する操作として、 **[SharePoint Online - Create file (SharePoint Online - ファイルの作成)]** を選択します。   **注**: これまでにその操作を行っていない場合、SharePoint のアカウントにアクセスするロジック アプリを承認するように求められます。    
   ![](./media/connectors-create-api-sharepointonline/action-3.png)    
5. **[ファイルの作成]** コントロールを開きます。   
   ![](./media/connectors-create-api-sharepointonline/action-4.png)     
6. **[サイトの URL]** を選択し、ファイルを作成したいサイトを検索します。     
   ![](./media/connectors-create-api-sharepointonline/action-5.png)  
7. **[フォルダー パス]** を選択し、新しいファイルを配置するフォルダーを検索します。  
   ![](./media/connectors-create-api-sharepointonline/action-6.png)  
8. **[ファイル名]** 制御を選択し、作成するファイルの名前を入力します。 ファイル名は、表示されるリストから単純に選択して以前に作成したトリガーのプロパティのいずれかを使用できることに留意してください。     
   ![](./media/connectors-create-api-sharepointonline/action-7.png)  
9. **[File content (ファイルのコンテンツ)]** 制御を選択し、作成されるファイルに書き込まれるコンテンツを入力します。 ファイルの内容は、以前に作成したトリガーのプロパティのいずれかを使用できることに留意してください。 表示されるリストからプロパティを単純に選択します。 また、コントロールに直接 **[File content (ファイルのコンテンツ)]** のテキストを入力できます。 この例ではいくつかのプロパティを選択し、スペースや各プロパティ間のハイフンを追加します。        
   ![](./media/connectors-create-api-sharepointonline/action-8.png)  
10. ワークフローへの変更を保存します。  



<!--HONumber=Jan17_HO3-->


