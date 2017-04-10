<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用して通常の更新プログラムをインストールするには
1. デバイスのシリアル コンソールを開き、オプション 1 の **[フル アクセスによるログイン]**を選択します。 パスワードを入力します。 既定のパスワードは *Password1*です。 
2. コマンド プロンプトに、次のコマンドを入力します。
   
     `Get-HcsUpdateAvailability`
   
    更新プログラムが利用可能かどうかと、更新プログラムが中断を伴うものであるかどうかが通知されます。
3. コマンド プロンプトに、次のコマンドを入力します。
   
     `Start-HcsUpdate`
   
    更新プロセスが開始します。

> [!IMPORTANT]
> * このコマンドは通常の更新プログラムのみに適用されます。 このコマンドは 1 つのコントローラーでのみ実行しますが、両方のコントローラーが更新されます。 
> * 更新プロセス中にコントローラーのフェールオーバーが行われても、システムの可用性または処理には影響しません。
> 
> 



<!--HONumber=Nov16_HO3-->


