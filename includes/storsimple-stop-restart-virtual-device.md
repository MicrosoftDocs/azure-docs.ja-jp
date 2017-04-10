#### <a name="to-stop-and-start-a-virtual-device"></a>仮想デバイスを停止および開始するには
仮想デバイスを停止するには、名前をクリックしてから **[シャットダウン]**をクリックします。 仮想デバイスのシャットダウン中、状態は **[停止中]**です。 仮想デバイスが停止した後、状態は **[停止]**になります。

仮想デバイスを停止および開始するには、以下のコマンドレットを使用します。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-virtual-device"></a>仮想デバイスを再起動するには
実行中の仮想デバイスを再起動する場合は、名前をクリックしてから **[再起動]**をクリックします。 仮想デバイスの再起動中の状態は **[再開中]**になります。 仮想デバイスを使用する準備ができると、その状態は **[実行中]**になります。

仮想デバイスを再起動するには、以下のコマンドレットを使用します。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`



<!--HONumber=Jan17_HO1-->


