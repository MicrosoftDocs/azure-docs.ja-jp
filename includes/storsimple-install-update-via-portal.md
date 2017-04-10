<!--author=SharS last changed: 01/15/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Azure クラシック ポータルから Update 1.2 をインストールするには
1. StorSimple サービス ページでデバイスを選択します。 **[デバイス]** > **[メンテナンス]** の順に移動します。
2. ページの下部にある **[更新プログラムのスキャン]**をクリックします。 利用可能な更新プログラムをスキャンするジョブが作成されます。 このジョブが正常に完了すると、通知されます。
3. 同じページの **[ソフトウェア更新プログラム]** セクションで、新しいソフトウェア更新プログラムを利用できることがわかります。 Update 1.2 をデバイスに適用する前に、リリース ノートを確認することをお勧めします。
   
    ![Install software updates](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)
4. ページの下部にある **[更新プログラムのインストール]**をクリックします。
5. 確認を求められます。 **[OK]**をクリックします。
6. **[更新プログラムのインストール]** ダイアログ ボックスが表示されます。 デバイスは、このダイアログ ボックスに示されているチェック項目を満たしている必要があります。 これらの手順は、更新に先立って完了しています。 **[上記の要件を理解し、デバイスを更新する準備ができました]**を選択します。 チェック マーク アイコンをクリックします。
   
    ![Confirmation message](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)
7. 一連の事前チェックが自動的に開始されます。 チェックの内容は次のとおりです
   
   * **コントローラーの正常性チェック** では、両方のデバイス コントローラーが正常であり、オンラインであることを確認します。
   * **ハードウェア コンポーネントの正常性チェック** では、StorSimple デバイスのすべてのハードウェア コンポーネントが正常であることを確認します。
   * **DATA 0 チェック** では、デバイスで DATA 0 が有効であることを確認します。 このインターフェイスが有効でない場合は、有効にしてから再試行する必要があります。
   * **DATA 2 と DATA 3 のチェック** では、DATA 2 と DATA 3 のネットワーク インターフェイスが有効でないことを確認します。 これらのインターフェイスが有効になっている場合、無効にしてからデバイスを更新する必要があります。 このチェックは、GA ソフトウェアを実行するデバイスから更新する場合にのみ実行されます。 バージョン 0.1、0.2、または 0.3 を実行しているデバイスでは、このチェックは必要ありません。
   * **ゲートウェイ チェック** は、Update 1 より前のバージョンを実行しているデバイスで行われます。 このチェックは、Update 1 より前のソフトウェアを実行中のすべてのデバイスで実行されますが、ゲートウェイが DATA 0 以外のネットワーク インターフェイス用に構成されているデバイスでは失敗します。
     
     Update 1.2 は、上記の更新前のチェックがすべて正常に完了している場合にのみ適用されます。 更新前のチェックを実行中であることが通知されます。
     
     ![Pre-check notification](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)
     
     アップグレード前のチェックが失敗した例を次に示します。 この場合、両方のデバイス コントローラーが正常であり、オンラインであることを確認する必要があります。 また、ハードウェア コンポーネントの正常性も確認する必要があります。 この例では、コント ローラー 0 とコント ローラー 1 の各コンポーネントに対処する必要があります。 これらの問題に自分で対処できない場合、Microsoft サポートに連絡することが必要になる場合があります。
     
       ![Pre-check failed](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)
     
     > [!NOTE]
     > StorSimple デバイスで Update 1.2 を適用すると、今後の更新では、DATA 2 と DATA 3 のチェックもゲートウェイのチェックも不要になります。 それ以外の事前チェックは依然として必要です。
     > 
     > 
8. アップグレード前のチェックが正常に完了したら、更新ジョブが作成されます。 更新ジョブが正常に作成されると、通知されます。
   
    ![Update job creation](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)
   
    その後、更新プログラムがデバイスに適用されます。
9. 更新ジョブの進行状況を監視するには、 **[ジョブの表示]**をクリックします。 **[ジョブ]** ページで、更新の進行状況を確認できます。 
   
    ![Update job progress](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)
10. 更新が完了するまでに数時間かかります。 ジョブの詳細はいつでも表示できます。
    
    ![Update job details](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)
11. ジョブが完了したら、**[メンテナンス]** ページに移動し、**[ソフトウェア更新プログラム]** まで下へスクロールします。
12. デバイスで **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**が実行されていることを確認します。 **[最終更新日]** も変更されています。
    
    ![Maintenance page](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)
13. メンテナンス モードの更新プログラムが使用できるようになったことがわかります。 これらの更新プログラムは、デバイスのダウンタイムを発生させる更新プログラムであり、デバイスの Windows PowerShell インターフェイス経由でのみ適用できます。 これらの更新プログラムを StorSimple 用 Windows PowerShell を使用してインストールするには、[メンテナンス モードの更新プログラムのインストール](../articles/storsimple/storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)に関するセクションの手順に従ってください。

> [!NOTE]
> 場合によっては、メンテナンス モードの更新プログラムを使用できることを示すメッセージが、そのプログラムのデバイスへの適用が正常に終了した後も、最大 24 時間表示されることがあります。  
> 
> 



<!--HONumber=Jan17_HO3-->


