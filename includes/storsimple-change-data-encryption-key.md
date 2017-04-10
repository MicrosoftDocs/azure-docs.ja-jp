<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>手順 1. Azure クラシック ポータルでサービス データ暗号化キーを変更できるようにデバイスを承認する
通常、デバイスの管理者は、サービス データ暗号化キーを変更する場合、デバイスの承認をサービス管理者に依頼します。 その後、サービス管理者は、キーの変更をデバイスに承認します。

この手順は、Azure クラシック ポータルで実行されます。 サービス管理者は、承認の条件を満たすデバイスが表示された一覧から、デバイスを選択できます。 選択したデバイスは、サービス データ暗号化キー変更プロセスを開始できるようになります。

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>サービス データ暗号化キーの変更を許可できるデバイス
デバイスは、サービス データ暗号化キーの変更開始の承認に先立ち、次の条件を満たす必要があります。

* デバイスがサービス データ暗号化キーの変更の承認の対象となるようにオンラインである必要があります。
* キーの変更が開始されなかった場合は、30 分経過後にもう一度同じデバイスを承認できます。
* 別のデバイスを承認するには、既に承認済みのデバイスによってキーの変更が開始されていないことが条件となります。 新しいデバイスが承認された後に、以前のデバイスが変更を開始することはできません。
* サービス データ暗号化キーのロールオーバーの実行中に、デバイスを承認することはできません。
* サービスに登録されているデバイスの中に、暗号化をロールオーバーしたデバイスと、ロールオーバしていないデバイスがある場合、デバイスを承認できます。 このような場合に承認できるデバイスは、サービス データ暗号化キーの変更を完了したものです。

> [!NOTE]
> Azure クラシック ポータルでは、キー変更の開始を承認できるデバイスの一覧に StorSimple 仮想デバイスは表示されません。
> 
> 

サービス データ暗号化キーの変更を開始するデバイスを選択して承認するには、次の手順を実行します。

#### <a name="to-authorize-a-device-to-change-the-key"></a>キーを変更するデバイスを承認するには
1. サービスのダッシュボード ページで、 **[サービス データ暗号化キーの変更]**をクリックします。
   
    ![サービス暗号化キーの変更](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. **[サービス データ暗号化キーの変更]** ダイアログ ボックスで、サービス データ暗号化キーの変更を開始するデバイスを選択して承認します。 ドロップダウン リストに、承認可能なすべてのデバイスが表示されます。
3. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png) をクリックします。

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>手順 2. StorSimple 用 Windows PowerShell を使用してサービス データ暗号化キーの変更を開始する
この手順は、承認済みの StorSimple デバイスの StorSimple 用 Windows PowerShell インターフェイスで実行されます。

> [!NOTE]
> キーのロールオーバーが完了するまで、StorSimple Manager サービスの Azure クラシック ポータルでは操作を行うことができません。
> 
> 

デバイスのシリアル コンソールを使用して Windows PowerShell インターフェイスに接続している場合は、次の手順を実行します。

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>サービス データ暗号化キーの変更を開始するには
1. オプション 1 を選択して、フル アクセスでログオンします。
2. コマンド プロンプトに、次のコマンドを入力します。
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. コマンドレットが正常に完了すると、新しいサービス データ暗号化キーが表示されます。 このキーをコピーし、このプロセスの手順 3. で使用するために保存します。 このキーは、StorSimple Manager サービスに登録されている残りのすべてのデバイスの更新に使用されます。
   
   > [!NOTE]
   > このプロセスは、StorSimple デバイスを承認してから 4 時間以内に開始する必要があります。
   > 
   > 
   
   4 時間を経過すると、この新しいキーはサービスに送信され、サービスに登録されているすべてのデバイスにプッシュされます。 そのようになると、サービスのダッシュボードにアラートが表示されます。 このサービスにより、登録済みのデバイス上でのすべての操作が無効になります。その後、デバイスの管理者は、他のデバイスのサービス データ暗号化キーを更新する必要があります。 ただし、I/O (データをクラウドに送信するホスト) は中断されません。
   
   サービスに登録されているデバイスが 1 台の場合は、ロールオーバー プロセスが完了し、次の手順をスキップできます。 サービスに登録されているデバイスが複数ある場合は、手順 3. に進みます。

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>手順 3. 他の StorSimple デバイス上のサービス データ暗号化キーを更新する
StorSimple Manager サービスに登録されているデバイスが複数ある場合は、StorSimple デバイスの Windows PowerShell インターフェイスで次の手順を実行する必要があります。 「手順 2. StorSimple 用 Windows PowerShell を使用してサービス データ暗号化キーの変更を開始する」で取得したキーを使用して、StorSimple Manager サービスに登録されている残りのすべての StorSimple デバイスを更新する必要があります。

デバイスのサービス データ暗号化を更新するには、次の手順を実行します。

#### <a name="to-update-the-service-data-encryption-key"></a>サービス データ暗号化キーを更新するには
1. StorSimple 用 Windows PowerShell を使用して、コンソールに接続します。 オプション 1 を選択して、フル アクセスでログオンします。
2. コマンド プロンプトに、次のコマンドを入力します。
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. 「 [手順 2. StorSimple 用 Windows PowerShell を使用してサービス データ暗号化キーの変更を開始する](#to-initiate-the-service-data-encryption-key-change)」で取得したサービス データ暗号化キーを指定します。



<!--HONumber=Nov16_HO3-->


