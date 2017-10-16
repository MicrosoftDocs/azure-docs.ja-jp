<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>StorSimple の最小デバイス セットアップを完了するには
1. デバイスを選択し、 **[クイック スタート]**をクリックします。 **[デバイス セットアップを完了する]** をクリックして、デバイスの構成ウィザードを起動します。
2. デバイスの構成ウィザードの **[基本設定]** ダイアログ ボックスで、次の手順を実行します。
   
   1. デバイスの **[表示名]** を指定します。 既定のデバイス名は、デバイスのモデルやシリアル番号などの情報を反映します。 デバイスを管理するために、最大 64 文字の表示名を割り当てることができます。
   2. デバイスをデプロイする地理的な場所に基づいて **タイム ゾーン** を設定します。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
   3. **[DNS 設定]** で **[セカンダリ DNS サーバー]** のアドレスを指定します。 IPv6 を使用する場合、このフィールドには、Windows PowerShell インターフェイスに提供される IPv6 プレフィックスに基づいて値が入力されます。 
      セカンダリ DNS サーバーが構成されていない場合、デバイスの構成を保存することはできません。
   4. [iSCSI 対応インターフェイス] で、iSCSI 用に少なくとも 1 つのネットワークを有効にします。 1 つ以上のネットワーク インターフェイスをクラウド対応とし、1 つのインターフェイスを iSCSI 対応とする必要があります。 DATA 0 は自動的にクラウド対応となります。
      
      ![StorSimple の最小限のデバイス セットアップ基本設定](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)
3. 矢印アイコンをクリックします。 ![StorSimple の矢印アイコン](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
4. **[ネットワーク インターフェイス]** ダイアログ ボックスで、コントローラー 0 およびコントローラー 1 の固定 IP アドレスを指定します。 **コントローラーの固定 IP アドレスは、デバイス の IP アドレスによってアクセス可能なサブネット内で空き IP となっている必要があります。** DATA 0 インターフェイスが IPv4 に対して構成されている場合、固定 IP アドレスは IPv4 形式で指定する必要があります。 IPv6 構成でプレフィックスを指定した場合、これらのフィールドには固定 IP アドレスが自動的に入力されます。

    ![StorSimple の最小限のデバイス セットアップのネットワーク インターフェイス](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    コントローラーの固定 IP アドレスはデバイスに更新プログラムを提供するために使用されるため、ルーティング可能でインターネットに接続可能である必要があります。 [Test-HcsmConnection][Test] コマンドレットを使用して、コントローラーの固定 IP がルーティング可能であることを確認できます。 次の例に、コントローラーの固定 IP がインターネットにルーティングされ、Microsoft Update サーバーにアクセスできることを示します。 

     ![Test-HcsmConnection showing routable IPs](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

1. チェック マーク アイコン ![StorSimple のチェック マーク アイコン](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png)をクリックします。
   デバイスの **[クイック スタート]** ページが再び表示されます。
   
   > [!NOTE]
   > **[構成]** ページが再び表示されます。
   > 
   > 

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx