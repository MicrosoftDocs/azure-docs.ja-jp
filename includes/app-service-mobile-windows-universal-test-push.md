
1. Windows ストア プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]**をクリックしてから、F5 キーを押して Windows ストア アプリを実行します。
   
    アプリが起動すると、デバイスがプッシュ通知に登録されます。
2. Windows ストア アプリを停止し、Windows Phone ストア アプリに対して前の手順を繰り返します。
   
    この時点で、両方のデバイスがプッシュ通知を受信するように登録されています。
3. Windows ストア アプリを再度実行し、**[Insert a TodoItem (TodoItem の挿入)]** にテキストを入力してから、**[保存]** をクリックします。
   
       Note that after the insert completes, both the Windows Store and the Windows Phone apps receive a push notification from WNS. The notification is displayed on Windows Phone even when the app isn't running.
   
       ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)



<!--HONumber=Nov16_HO3-->


