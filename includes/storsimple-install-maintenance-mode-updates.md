<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用してメンテナンス モードの更新プログラムをインストールするには
1. デバイスのシリアル コンソールにアクセスし、オプション 1 の **[フル アクセスによるログイン]**を選択します (まだの場合)。 
2. パスワードを入力します。 既定のパスワードは **Password1**です。
3. コマンド プロンプトに、次のコマンドを入力します。
   
     `Get-HcsUpdateAvailability` 
4. 更新プログラムが利用可能かどうかと、更新プログラムが中断を伴うものであるかどうかが通知されます。 中断を伴う更新プログラムを適用するには、デバイスをメンテナンス モードにする必要があります。 手順については、「[手順 2: メンテナンス モードを開始する](../articles/storsimple/storsimple-update-device.md#step2)」を参照してください。
5. デバイスがメンテナンス モードになっている状態で、コマンド プロンプトで「 `Start-HcsUpdate`
6. 確認を求められます。 更新プログラムを確認すると、現在アクセスしているコントローラーにインストールされます。 更新プログラムがインストールされると、コントローラーが再起動されます。 
7. 更新プログラムの状態を監視します。 次のコマンドを入力します。
   
    `Get-HcsUpdateStatus`
   
    `RunInProgress` が `True` の場合は、更新が実行中です。 `RunInProgress` が `False` の場合は、更新が完了したことを示します。  
8. 更新プログラムを現在のコントローラーにインストールし、再起動が完了したら、別のコントローラーに接続して手順 1. から 6. を実行します。
9. 両方のコントローラーが更新されたら、メンテナンス モードを終了します。 手順については、「[手順 4: メンテナンス モードを終了する](../articles/storsimple/storsimple-update-device.md#step4)」を参照してください。



<!--HONumber=Nov16_HO3-->


