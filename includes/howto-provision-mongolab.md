Azure ストアでは、Azure にホストされ、完全に管理された MongoDB データベースにサブスクライブできます。 これを行うには、次のステップに従います。

1. Azure 管理ポータルにログインします。
2. **[新規]**をクリックします。  
   ![[新規]][button-new]
3. **[ストア]**を選択します。  
   ![[ストア]][button-store]
4. [MongoLab] を選択します。 **[App Services]** カテゴリだけでなく、**[すべて]** の下にもあります。  
   ![MongoLab][entry-mongolab]
5. **[次へ]**をクリックします。  
   ![[次へ]][button-next]  
   MongoLab ストア エントリが表示されます。  
   ![NewMongoLab][screen-newmongolab]
6. 必要な **[サブスクリプション]** オプションを選択します。
7. **[名前]** にデータベースの名前を入力します。 名前には、英数字、ダッシュ、ドット、アンダースコアのみ使用できます。 MongoLab では、この名前を一意にする必要もあるため、名前が使用されている場合は要求を再送信するよう求められることがあります。
8. 目的の **[リージョン]**を選択します。
9. **[次へ]**をクリックします。  
   ![[次へ]][button-next]
10. ストアの購入情報を確認し、 **[購入]** をクリックして確認します。  
    ![[次へ]][button-purchase]  
11. ツール バーの進捗状況ボタンにプロビジョニングの状態が表示されます。  
    ![ProgressButton][button-progress]  
    プロビジョニングが完了すると、成功メッセージが表示されます。  
    ![SuccessMessage][message-success]

ご利用ありがとうございます。 MongoLab により、選択した Azure のリージョンで MongoDB データベースがプロビジョニングされました。 管理 UI と 24 時間 365 日のサポートをご利用いただけます。

[button-new]: ./media/howto-provision-mongolab/button-new.png
[button-store]: ./media/howto-provision-mongolab/button-store.png
[button-next]: ./media/howto-provision-mongolab/button-next.png
[button-purchase]: ./media/howto-provision-mongolab/button-purchase.png
[button-progress]: ./media/howto-provision-mongolab/button-progress.png
[entry-mongolab]: ./media/howto-provision-mongolab/entry-mongolab.png 
[screen-newmongolab]: ./media/howto-provision-mongolab/screen-newmongolab.png 
[message-success]: ./media/howto-provision-mongolab/message-provisionsuccess.png


<!--HONumber=Jan17_HO3-->


