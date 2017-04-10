## <a name="connect-to-outlookcom"></a>Outlook.com に接続する
### <a name="prerequisites"></a>前提条件
* Outlook.com アカウント

ロジック アプリで Outlook.com アカウントを使用するには、Outlook.com アカウントに接続するロジック アプリを承認する必要があります。 幸い、Azure ポータルのロジック アプリ内から簡単に実行できます。 

次に、Outlook.com アカウントに接続するロジック アプリを承認する手順を示します。

1. すべてのロジック アプリはトリガーによって起動する必要があるため、ロジック アプリを作成すると、デザイナーが開いて、ロジック アプリの起動に使用できるトリガーの一覧が表示されます。
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. 検索ボックスに「outlook」と入力します。 一覧にフィルターが適用されて、名前に "Outlook" が含まれているトリガーがすべて表示されます。![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. **[Office 365 Outlook - On new email (Office 365 Outlook - 新着メール)]** を選択します。   
   これまでに Outlook への接続を作成していない場合は、Outlook.com の資格情報を指定するよう求められます。 この資格情報を使用して、接続するロジック アプリの承認と、Outlook.com アカウントのデータへのアクセスが行われます。![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Outlook の資格情報を入力して、サインインします。![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   これで終了です。 Outlook への接続が作成されました。 この接続は、作成するロジック アプリで使用可能になります。



<!--HONumber=Nov16_HO3-->


