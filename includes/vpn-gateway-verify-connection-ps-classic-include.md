`Get-AzureVNetConnection` を使用すると、クラシック仮想ネットワーク ゲートウェイの接続を確認できます。 

1. 次のコマンドレットを使用します。値は実際の値に置き換えてください。 仮想ネットワークの名前にスペースが含まれる場合は、名前を引用符で囲む必要があります。
   
        Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
2. コマンドレットの実行後、値を確認します。 以下の例では、接続状態は "Connected" と表示されており、受信バイトと送信バイトを確認できます。

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal


<!--HONumber=Jan17_HO3-->


