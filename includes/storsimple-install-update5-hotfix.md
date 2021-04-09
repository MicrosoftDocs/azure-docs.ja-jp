---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cbc7c82803115f71db233be94d62a857ba050b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050210"
---
#### <a name="to-download-hotfixes"></a>修正プログラムをダウンロードするには

次の手順を実行して、Microsoft Update カタログからソフトウェア更新プログラムをダウンロードします。

1. Internet Explorer を起動し、[http://catalog.update.microsoft.com](https://catalog.update.microsoft.com) に移動します。
2. このコンピューターで Microsoft Update カタログを初めて使用する場合は、Microsoft Update カタログ アドオンのインストールを求められたら、 **[インストール]** をクリックします。

    ![カタログのインストール](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号 (**4037264** など) を入力して、**[検索]** をクリックします。
   
    "**Cumulative Software Bundle Update 5.0 for StorSimple 8000 Series**" のような修正プログラムの一覧が表示されます。
   
    ![カタログの検索](./media/storsimple-install-update5-hotfix/update-catalog-search.png)

4. **[Download]** をクリックします。 ダウンロード先となるローカルの場所を指定または **参照** します。 指定した場所やフォルダーにダウンロードするファイルをクリックします。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。
5. 上の表に記載されているその他の修正プログラム (**4037266**) を探し、対応するファイルを、前の表に記載された特定のフォルダーにダウンロードします。

> [!NOTE]
> 修正プログラムは、ピア コントローラーから潜在的なエラー メッセージをすべて検出するために両方のコントローラーからアクセス可能である必要があります。
>
> 修正プログラムは、3 つの異なるフォルダーにコピーする必要があります。 たとえば、デバイス ソフトウェア/Cis/MDS エージェントの更新プログラムは _FirstOrderUpdate_ フォルダーに、その他すべての中断なしの更新プログラムは _SecondOrderUpdate_ フォルダーに、メンテナンス モードの更新プログラムは _ThirdOrderUpdate_ フォルダーにコピーできます。

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>通常モードの修正プログラムをインストールして確認するには

通常モードの修正プログラムをインストールして確認するには、次の手順を実行します。 Azure Portal を使用して既にインストールしてある場合は、[メンテナンス モードの修正プログラムのインストールと確認](#to-install-and-verify-maintenance-mode-hotfixes)に進みます。

1. 修正プログラムをインストールするには、StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイスにアクセスします。 詳細については、[PuTTy を使用してシリアル コンソールに接続する方法](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)に関するセクションを参照してください。 コマンド プロンプトで **Enter** キーを押します。
2. オプション 1 の **[Log in with full access]\(フル アクセスによるログイン\)** を選択します。 まず、パッシブ コントローラーに修正プログラムをインストールすることをお勧めします。
3. 修正プログラムをインストールするには、コマンド プロンプトで次のように入力します。
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    上記のコマンドの共有パスでは、DNS ではなく IP を使用します。 Credential パラメーターは、認証済みの共有にアクセスする場合にのみ使用されます。
   
    共有にアクセスする場合は、Credential パラメーターを使用することをお勧めします。 "すべてのユーザー" に解放されている共有であっても、通常は、非認証ユーザーには解放されません。
   
4. パスワードの入力を求められたら、入力します。 最初の注文の更新プログラムをインストールするためのサンプル出力を次に示します。 最初の注文の更新プログラムについては、特定のファイルを指定する必要があります。

    >[!NOTE] 
    > まず、_HcsSoftwareUpdate.exe_ をインストールする必要があります。 このインストールが完了した後、_CisMdsAgentUpdate.exe_ をインストールします。
   
    ```output
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
    Confirm
   
    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
   
    ```
5. 修正プログラムのインストールの確認を求められたら、「 **Y** 」と入力します。
6. `Get-HcsUpdateStatus` コマンドレットを使用して、更新プログラムを監視します。 更新プログラムはまずパッシブ コントローラーで完了します。 パッシブ コントローラーが更新されると、フェイル オーバーが発生し、もう一方のコントローラーに更新プログラムが適用されます。 更新プログラムは両方のコントローラーが更新されると完了します。
   
    次のサンプル出力は、インストール中の更新プログラムを示しています。 更新が進行中の場合、`RunInprogress` は `True` になります。

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 07/28/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     次のサンプル出力は、更新が完了したことを示しています。 更新が完了した場合、`RunInProgress` は `False` になります。
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 07/28/2017 9:15:55 AM
    LastUpdateTimestamp : 07/28/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > 場合によっては、更新がまだ進行中であっても、コマンドレットは `False` とレポートします。 修正プログラムが完了したことを確認するには、数分待ってから、このコマンドを再実行し、`RunInProgress` が `False` になっていることを確認します。 False の場合、修正プログラムは完了しています。

7. ソフトウェアの更新が完了したら、システムのソフトウェア バージョンを確認します。 型:
   
    `Get-HcsSystem`
   
    次のバージョンが表示されます。
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 5.0`
   * `HcsSoftwareVersion: 6.3.9600.17845`
   
     更新プログラムの適用後にバージョン番号が変わらない場合は、修正プログラムの適用に失敗したことを示します。 そのような場合は、[Microsoft サポート](../articles/storsimple/storsimple-8000-contact-microsoft-support.md)にお問い合わせください。
     
     > [!IMPORTANT]
     > 次の更新プログラムを適用する前に、`Restart-HcsController` コマンドレットを使用してアクティブ コントローラーを再起動する必要があります。
     
8. 手順 3. ～ 6. を繰り返して、_FirstOrderUpdate_ フォルダーにダウンロードした _CisMDSAgentupdate.exe_ エージェントをインストールします。
8. 2 番目の順位の更新プログラムをインストールするには、手順 3. ～ 6. を繰り返します。 

    > [!NOTE] 
    > 2 番目の順位の更新プログラムについては、`Start-HcsHotfix cmdlet` を実行し、2 番目の順位の更新プログラムが含まれているフォルダーを指定するだけで、複数の更新プログラムをインストールすることができます。 コマンドレットでは、フォルダー内で利用可能なすべての更新プログラムが実行されます。 更新プログラムが既にインストールされている場合、更新ロジックはそれを検出し、その更新プログラムを適用しません。

    すべての修正プログラムがインストールされたら、`Get-HcsSystem` コマンドレットを使用します。 バージョンは次のようになります。
    
    * `CisAgentVersion:  1.0.9724.0`
    * `MdsAgentVersion: 35.2.2.0`
    * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>メンテナンス モードの修正プログラムをインストールして確認するには

KB4037263 を使用して、ディスク ファームウェアの更新プログラムをインストールします。 これらは中断を伴う更新プログラムであり、完了まで約 30 分かかります。 デバイスのシリアル コンソールに接続することで、計画されたメンテナンス期間にこれらをインストールするよう選択できます。

> [!NOTE] 
> ディスク ファームウェアが既に最新の状態になっている場合は、これらの更新プログラムをインストールする必要はありません。 デバイスのシリアル コンソールから `Get-HcsUpdateAvailability` コマンドレットを実行して、更新プログラムが利用可能かどうか、および更新プログラムが中断を伴うか (メンテナンス モード)、伴わないか (通常モード) を確認します。

ディスク ファームウェアの更新プログラムをインストールするには、次の指示に従います。

1. デバイスをメンテナンス モードにします。 

    > [!NOTE] 
    > デバイスにメンテナンス モードで接続するときは、Windows PowerShell リモート処理を使用しないでください。 デバイスのシリアル コンソールを通して接続したときは、代わりにデバイス コントローラーでこのコマンドレットを実行します。

    コントローラーをメンテナンス モードにするには、次のように入力します。
   
    `Enter-HcsMaintenanceMode`
   
    サンプル出力を次に示します。

    ```output
    Controller0>Enter-HcsMaintenanceMode
    Checking device state...
   
    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y
   
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8600
    Name: Update4-8600-mystorsimple
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------
   
    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
    ```

    両方のコントローラーがメンテナンス モードで再起動します。
2. ディスク ファームウェアの更新プログラムをインストールするには、次のように入力します。
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    サンプル出力を次に示します。

    ```output
    Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
    Enter Password:
    WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    Confirm
    This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
    [Y] Yes [N] No (Default is "Y"): Y
    WARNING: Installation is currently in progress. This operation can take several minutes to complete.
    ```

3. `Get-HcsUpdateStatus` コマンドを使用して、インストールの進行状況を監視します。 更新が完了すると、`RunInProgress` が `False` に変わります。
4. インストールが完了すると、メンテナンス モードの修正プログラムがインストールされたコントローラーが再起動されます。 オプション 1 の **[Log in with full access]\(フル アクセスによるログイン\)** を選択し、ディスクのファームウェアのバージョンを確認します。 型:
   
   `Get-HcsFirmwareVersion`
   
   予想されるディスク ファームウェアのバージョンは次のとおりです。
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`
   
   サンプル出力を次に示します。

    ```output
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17845
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
        ActiveBIOS:0.45.0010
           BackupBIOS:0.45.0006
           MainCPLD:17.0.000b
           ActiveBMCRoot:2.0.001F
           BackupBMCRoot:2.0.001F
           BMCBoot:2.0.0002
           LsiFirmware:20.00.04.00
           LsiBios:07.37.00.00
           Battery1Firmware:06.2C
           Battery2Firmware:06.2C
           DomFirmware:X231600
           CanisterFirmware:3.5.0.56
           CanisterBootloader:5.03
           CanisterConfigCRC:0x9134777A
           CanisterVPDStructure:0x06
           CanisterGEMCPLD:0x19
           CanisterVPDCRC:0x142F7DC2
           MidplaneVPDStructure:0x0C
           MidplaneVPDCRC:0xA6BD4F64
           MidplaneCPLD:0x10
           PCM1Firmware:1.00|1.05
           PCM1VPDStructure:0x05
           PCM1VPDCRC:0x41BEF99C
           PCM2Firmware:1.00|1.05
           PCM2VPDStructure:0x05
           PCM2VPDCRC:0x41BEF99C

        EbodFirmware
           CanisterFirmware:3.5.0.56
           CanisterBootloader:5.03
           CanisterConfigCRC:0xB23150F8
           CanisterVPDStructure:0x06
           CanisterGEMCPLD:0x14
           CanisterVPDCRC:0xBAA55828
           MidplaneVPDStructure:0x0C
           MidplaneVPDCRC:0xA6BD4F64
           MidplaneCPLD:0x10
           PCM1Firmware:3.11
           PCM1VPDStructure:0x03
           PCM1VPDCRC:0x6B58AD13
           PCM2Firmware:3.11
           PCM2VPDStructure:0x03
           PCM2VPDCRC:0x6B58AD13

        DisksFirmware
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
    ```
   
    2 番目のコントローラーで `Get-HcsFirmwareVersion` コマンドを実行して、ソフトウェアのバージョンが更新されたことを確認します。 この後、メンテナンス モードを終了できます。 そのためには、各デバイス コントローラーに対して次のコマンドを入力します。
   
   `Exit-HcsMaintenanceMode`

5. メンテナンス モードを終了すると、コントローラーが再起動します。 ディスク ファームウェアの更新プログラムが正常に適用され、デバイスがメンテナンス モードを終了したら、Azure Portal に戻ります。 メンテナンス モードの更新プログラムがインストールされたことがポータルに 24 時間表示されない可能性があることにご注意ください。

