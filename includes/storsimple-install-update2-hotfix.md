<!--author=alkohli last changed: 03/17/16-->

#### <a name="to-download-hotfixes"></a>修正プログラムをダウンロードするには
次の手順を実行して、Microsoft Update カタログからソフトウェア更新プログラムをダウンロードします。

1. Internet Explorer を起動し、 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)に移動します。
2. このコンピューターで Microsoft Update カタログを初めて使用する場合は、Microsoft Update カタログ アドオンのインストールを求められたら、[ **インストール** ] をクリックします。
    ![カタログのインストール](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号 (**3121901** など) を入力して、**[検索]** をクリックします。
   
    " **Cumulative Software Bundle Update 2.0 for StorSimple 8000 Series**" のような修正プログラムの一覧が表示されます。
   
    ![カタログの検索](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. **[追加]**をクリックします。 更新プログラムがバスケットに追加されます。
5. 上の表で示されている他の修正プログラム (**3121900**、**3080728**、**3090322**、**3121899**) を検索し、バスケットに追加します。
6. **[バスケットの表示]**をクリックします。
7. **[Download]**をクリックします。 ダウンロード先となるローカルの場所を指定または **参照** します。 更新プログラムが指定した場所にダウンロードされて、更新プログラムと同じ名前のサブフォルダーに配置されます。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。

> [!NOTE]
> 修正プログラムは、ピア コントローラーから潜在的なエラー メッセージをすべて検出するために両方のコントローラーからアクセス可能である必要があります。
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>通常モードの修正プログラムをインストールして確認するには
通常モードの修正プログラムをインストールして確認するには、次の手順を実行します。 Azure ポータルを使用して既にインストールしてある場合は、 [メンテナンス モードの修正プログラムのインストールと確認](#to-install-and-verify-maintenance-mode-hotfixes)に進みます。

1. 修正プログラムをインストールするには、StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイスにアクセスします。 詳細については、[PuTTy を使用してシリアル コンソールに接続する方法](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)に関するセクションを参照してください。 コマンド プロンプトで **Enter**キーを押します。
2. **[オプション 1]** を選択して、フル アクセスでデバイスにログオンします。
3. 修正プログラムをインストールするには、コマンド プロンプトで次のように入力します。
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    上記のコマンドの共有パスでは、DNS ではなく IP を使用します。 Credential パラメーターは、認証済みの共有にアクセスする場合にのみ使用されます。
   
    共有にアクセスする場合は、Credential パラメーターを使用することをお勧めします。 "すべてのユーザー" に解放されている共有であっても、通常は、非認証ユーザーには解放されません。
   
    サンプル出力を次に示します。
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```
4. 修正プログラムのインストールの確認を求められたら、「 **Y** 」と入力します。
5. `Get-HcsUpdateStatus` コマンドレットを使用して、更新プログラムを監視します。
   
    次のサンプル出力は、インストール中の更新プログラムを示しています。 更新が進行中の場合、`RunInprogress` は `True` になります。
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 12/21/2015 10:36:13 PM
    LastUpdateTimestamp : 12/21/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     次のサンプル出力は、更新が完了したことを示しています。 更新が完了した場合、`RunInProgress` は `False` になります。
   
    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 12/21/2015 10:59:13 PM
    LastUpdateTimestamp : 12/21/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > 場合によっては、更新がまだ進行中であっても、コマンドレットは `False` とレポートします。 修正プログラムが完了したことを確認するには、数分待ってから、このコマンドを再実行し、`RunInProgress` が `False` になっていることを確認します。 False の場合、修正プログラムは完了しています。

6. ソフトウェアの更新が完了した後、SaaS エージェントと MDS エージェントをインストールして監視するために手順 3 ～ 5 を繰り返します。 `all-cismdsagentupdatebundle_f98e62f4d56c79e2a6644d027af7a2393a93827a.exe` の前に `all-hcsmdssoftwareupdate_0b438ddf0d5b686aada2378b754fac8c7f2160e9.exe` がインストールされていることを確認します。
7. システム ソフトウェアのバージョンを確認します。 次のコマンドを入力します。
   
    `Get-HcsSystem`
   
    次のバージョンが表示されます。
   
   * HcsSoftwareVersion: 6.3.9600.17673
   * CisAgentVersion: 1.0.9150.0
   * MdsAgentVersion: 30.0.4698.13
     
     更新プログラムの適用後にバージョン番号が変わらない場合は、修正プログラムの適用に失敗したことを示します。 そのような場合は、[Microsoft サポート](../articles/storsimple/storsimple-contact-microsoft-support.md)にお問い合わせください。
8. 手順 3 ～ 5 を繰り返して、残りの通常モード修正プログラムをインストールします。
   
   * LSI ドライバー - KB3121900
   * Storport 更新プログラム - KB3080728
   * Spaceport 更新プログラム - KB3090322

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>メンテナンス モードの修正プログラムをインストールして確認するには
KB3121899 を使用して、ディスク ファームウェアの更新プログラムをインストールします。 これらは中断を伴う更新プログラムであり、完了まで約 30 分かかります。 デバイスのシリアル コンソールに接続することで、計画されたメンテナンス期間にこれらをインストールするよう選択できます。

ディスク ファームウェアが既に最新の状態になっている場合は、これらの更新プログラムをインストールする必要はありません。 デバイスのシリアル コンソールから `Get-HcsUpdateAvailability` コマンドレットを実行して、更新プログラムが利用可能かどうか、および更新プログラムが中断を伴うか (メンテナンス モード)、伴わないか (通常モード) を確認します。

ディスク ファームウェアの更新プログラムをインストールするには、次の指示に従います。

1. デバイスをメンテナンス モードにします。 デバイスにメンテナンス モードで接続するときは、Windows PowerShell リモート処理を使用しないでください。 デバイスのシリアル コンソールを通して接続したときは、代わりにデバイス コントローラーでこのコマンドレットを実行します。 次のコマンドを入力します。
   
    `Enter-HcsMaintenanceMode`
   
    サンプル出力を次に示します。
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17664
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    両方のコントローラーがメンテナンス モードで再起動します。
2. ディスク ファームウェアの更新プログラムをインストールするには、次のように入力します。
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    サンプル出力を次に示します。
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. `Get-HcsUpdateStatus` コマンドを使用して、インストールの進行状況を監視します。 更新が完了すると、`RunInProgress` が `False` に変わります。
4. インストールが完了すると、メンテナンス モードの修正プログラムがインストールされたコントローラーが再起動されます。 フル アクセスを持つオプション 1 としてログインし、ディスク ファームウェアのバージョンを確認します。 次のコマンドを入力します。
   
   `Get-HcsFirmwareVersion`
   
   予想されるディスク ファームウェアのバージョンは次のとおりです。
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   サンプル出力を次に示します。
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17664
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    2 番目のコントローラーで `Get-HcsFirmwareVersion` コマンドを実行して、ソフトウェアのバージョンが更新されたことを確認します。 この後、メンテナンス モードを終了できます。 そのためには、各デバイス コントローラーに対して次のコマンドを入力します。
   
   `Exit-HcsMaintenanceMode`
5. メンテナンス モードを終了すると、コントローラーが再起動します。 ディスク ファームウェアの更新プログラムが正常に適用され、デバイスがメンテナンス モードを終了したら、Azure クラシック ポータルに戻ります。 メンテナンス モードの更新プログラムがインストールされたことがポータルに 24 時間表示されない可能性があることに注意してください。



<!--HONumber=Nov16_HO3-->


