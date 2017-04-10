トリガーを追加すると、それによって生成されるデータと関係ができます。 次の手順を従って、 **[SharePoint Online - Create file (SharePoint Online - ファイルの作成)]** の操作を追加してください。 この操作により新しい項目のトリガーが起動するたびに、SharePoint Online にファイルが作成されます。 

この操作を設定するためには、次の情報を提供する必要があります。 これを提供すると、新しいファイルのプロパティの一部の情報として、トリガーによって生成されたデータを簡単に使用できることを確認します。

| ファイルプロパティの作成 | 説明 |
| --- | --- |
| サイトの URL |これは、新しいファイルを作成する SharePoint Online サイトの URL です。 表示されるリストから、サイトを選択します。 |
| フォルダー パス |これは、新しいファイルを配置するフォルダー (以前のステップで選択したサイトの URL) です。 フォルダーを参照して、選択します。 |
| ファイル名 |これは、作成されるファイルの名前です。 |
| ファイルのコンテンツ |コンテンツは、ファイルに書き込まれます。 |

1. **[+ New step (+ 新しいステップ)]** を選択して 、操作を追加します。  
   ![SharePoint オンライン アクション イメージ 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. **[アクションの追加]** のリンクを選択します。 これにより検索ボックスが開き、行う操作について検索できます。 この例では、SharePoint の操作が示されています。    
   ![SharePoint オンライン アクション イメージ 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. 「 *sharepoint* 」と入力して、 SharePoint に関連する操作を検索します。
4. 実行する操作として、 **[SharePoint Online - Create file (SharePoint Online - ファイルの作成)]** を選択します。   **注**: これまでに SharePoint Online への接続を作成していない場合、SharePoint のアカウントにアクセスするロジック アプリを承認するように求められます。    
   ![SharePoint オンライン アクション イメージ 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. **[ファイルの作成]** コントロールを開きます。   
   ![SharePoint オンライン アクション イメージ 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. **[サイトの URL]** を選択し、ファイルを作成したいサイトを検索します。     
   ![SharePoint オンライン アクション イメージ 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. **[フォルダー パス]** を選択し、新しいファイルを配置するフォルダーを検索します。  
   ![SharePoint オンライン アクション イメージ 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. **[ファイル名]** 制御を選択し、作成するファイルの名前を入力します。 これで、直接ファイル名を入力できたり、以前に作成したトリガーのプロパティのいずれかを使用できます。 **[Outputs from When a new item is created (新しい項目の作成時に出力)]**の一覧からプロパティを選択してこれを行います。 この一覧は、 **[ファイル名]** コントロールを選択後にのみ、表示されます。 このチュートリアルでは、 **[SharePoint Online - Create file (SharePoint Online - ファイルを作成)]** アクションで作成されるファイル名として、ID (新しいリスト アイテムの ID)を選択します。    
   ![SharePoint オンライン アクション イメージ 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. **[File content (ファイルのコンテンツ)]** 制御を選択し、作成されるファイルに書き込まれるコンテンツを入力します。 ファイルの内容は、以前に作成したトリガーのプロパティのいずれかを使用できることに留意してください。 表示されるリストからプロパティを単純に選択します。 また、コントロールに直接 **[File content (ファイルのコンテンツ)]** のテキストを入力できます。 この例ではいくつかのプロパティを選択し、スペースや各プロパティ間のハイフンを追加します。        
   ![SharePoint オンライン アクション イメージ 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. ワークフローへの変更を保存します。  
11. 新しい項目が SharePoint Online の一覧に追加されたときにトリガーされる、完全に機能するロジック アプリができました。 アプリでは、新しいリスト項目からプロパティを使用して、ファイルが作成されます。  SharePoint の一覧に新しい項目を作成して今すぐテストできます。 



<!--HONumber=Nov16_HO3-->


