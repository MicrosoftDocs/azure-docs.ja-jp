---
title: "StorSimple デバイス コントローラーを管理する | Microsoft Docs"
description: "StorSimple デバイス コントローラーの停止、再起動、シャットダウン、リセットの方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4ee989d0-956f-4c14-951e-fd4e490ea09d
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 815ada25a548ddf419ac073f57a5153f60a40a58


---
# <a name="manage-your-storsimple-device-controllers"></a>StorSimple デバイス コントローラーを管理する
## <a name="overview"></a>Overview
このチュートリアルでは、StorSimple デバイス コントローラーに対して実行できる各種の操作について説明します。 StorSimple デバイスでは、ピア コントローラーがアクティブ/パッシブ構成で冗長化されています。 特定の時点でアクティブなコントローラーは 1 台だけで、そのコントローラーがすべてのディスク操作とネットワーク操作を処理します。 その他のコントローラーはパッシブ モードになります。 アクティブなコントローラーに障害が発生すると、パッシブ コントローラーが自動的にアクティブになります。

このチュートリアルでは、以下の手段を使ってデバイス コントローラーを管理するための手順を取り上げています。

* StorSimple Manager サービスの **[メンテナンス]** ページの **[コントローラー]** セクション
* StorSimple 用 Windows PowerShell。

デバイス コントローラーは、StorSimple Manager サービスを介して管理することをお勧めします。 StorSimple 用 Windows PowerShell を使用しないと実行できない操作については、チュートリアルの中で記載しています。

このチュートリアルを読むと、次のことができるようになります。

* StorSimple デバイス コントローラーを再起動またはシャットダウンする
* StorSimple デバイスをシャットダウンする
* StorSimple デバイスを出荷時の設定にリセットする

## <a name="restart-or-shut-down-a-single-controller"></a>単一のコントローラーの再起動とシャットダウン
通常のシステム運用の中でコントローラーの再起動またはシャットダウンが必要になることはありません。 単一デバイス コントローラーのシャットダウン操作が一般的に行われるのは、障害の発生したデバイスのハードウェア コンポーネントを交換しなければならないケースです。 また、過剰なメモリの使用やコントローラーの不調などでパフォーマンスに影響が生じている状況でコントローラーの再起動が必要となる場合もあります。 無事交換したコントローラーを有効にしてテストする場合にも、コントローラーを再起動する必要があります。

デバイスを再起動しても、パッシブ コントローラーが利用できる状態にあれば、接続されているイニシエーターに悪影響は生じません。 パッシブ コントローラーが利用できない、またはオフになっている場合は、アクティブ コントローラーを再起動すると、サービスの機能不全やダウンタイムが生じます。

> [!IMPORTANT]
> * **実行中のコントローラーを物理的に取り外すことは絶対に避けてください。冗長性が失われ、ダウンタイムのリスクが増大します。**
> * 以下の手順は、StorSimple の物理デバイスにのみ当てはまります。 仮想デバイスの起動、停止、再起動については、[仮想デバイスの作業](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device)に関するページをご覧ください。
> 
> 

StorSimple Manager サービスの Azure クラシック ポータルまたは StorSimple 用 Windows PowerShell を使用して、1 つのデバイス コント ローラーを再起動またはシャットダウンすることができます。

Azure クラシック ポータルからデバイス コントローラーを管理するには、次の手順を実行します。

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>クラシック ポータルでコントローラーを再起動またはシャットダウンするには
1. **[デバイス]、[メンテナンス]** の順にクリックします。
2. **[ハードウェアの状態]** に移動し、デバイスの両方のコントローラーのステータスが **[正常]** であることを確認します。
   
    ![StorSimple デバイス コントローラーが正常であることを確認する](./media/storsimple-manage-device-controller/IC766017.png)
3. **[メンテナンス]** ページの一番下にある **[コントローラーの管理]** をクリックします。
   
    ![StorSimple デバイス コントローラーを管理する](./media/storsimple-manage-device-controller/IC766018.png)</br>
   
   > [!NOTE]
   > **[コントローラーの管理]**が表示されない場合、更新プログラムをインストールする必要があります。 詳細については、「 [StorSimple デバイスの更新](storsimple-update-device.md)」をご覧ください。
   > 
   > 
4. **[コントローラー設定の変更]** ダイアログ ボックスで、次の手順に従います。
   
   1. **[コントローラーの選択]** ドロップダウン リストから、管理するコントローラーを選択します。 選択肢は、[コントローラー 0] と [コントローラー 1] です。 これらのコントローラーがアクティブであるか、パッシブであるかも表示されます。
      
      > [!NOTE]
      > 利用できない、またはオフになっていて管理できないコントローラーは、ドロップダウン リストに表示されません。
      > 
      > 
   2. **[アクションの選択]** ドロップダウン リストで、**[コントローラーの再起動]** または **[コントローラーのシャットダウン]** を選択します。
      
       ![StorSimple  デバイス パッシブ コントローラーを再起動する](./media/storsimple-manage-device-controller/IC766020.png)
   3. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-manage-device-controller/IC740895.png)に関するページを参照してください。

これでコントローラーが再起動またはシャットダウンされます。 以下の表は、 **[コントローラー設定の変更]** ダイアログ ボックスで選択した操作の内容とその結果をまとめたものです。  

| 選択肢番号 # | 操作の内容 | 想定される結果 |
| --- | --- | --- |
| 1. |パッシブ コントローラーを再起動する。 |コントローラーを再起動するためのジョブが作成されます。ジョブが正常に作成されると通知されます。 このジョブによってコントローラーの再起動が開始されます。 **[サービス]、[ダッシュボード]、[操作ログの表示]** の順に選択し、対象のサービスに固有のパラメーターでフィルタリングすることによって再起動プロセスを監視できます。 |
| 2. |アクティブ コントローラーを再起動する。 |"アクティブ コントローラーを再起動すると、デバイスがパッシブ コントローラーにフェールオーバーされます。 続行してもよろしいですか?" という警告が表示されます。 </br>この操作を続行するように選択した場合、その後の手順は、パッシブ コントローラーを再起動する手順 (「選択肢 1」を参照) と同じです。 |
| 3. |パッシブ コントローラーをシャットダウンする。 |"シャットダウンが完了した後で、コントローラーの電源ボタンを押して電源をオンにする必要があります。 このコントローラーをシャットダウンしてもよろしいですか?" というメッセージが表示されます。 </br>この操作を続行するように選択した場合、その後の手順は、パッシブ コントローラーを再起動する手順 (「選択肢 1」を参照) と同じです。 |
| 4. |アクティブ コントローラーをシャットダウンする。 |"シャットダウンが完了した後で、コントローラーの電源ボタンを押して電源をオンにする必要があります。 このコントローラーをシャットダウンしてもよろしいですか?" というメッセージが表示されます。 </br>この操作を続行するように選択した場合、その後の手順は、パッシブ コントローラーを再起動する手順 (「選択肢 1」を参照) と同じです。 |

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でコントローラーを再起動またはシャットダウンするには
StorSimple デバイスの単一のコントローラーを Azure クラシック ポータルからシャットダウンまたは再起動するには、次の手順を実行します。

1. リモート コンピューターからシリアル コンソールまたは Telnet セッションを使ってデバイスにアクセスします。 「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)」の手順に従い、コントローラー 0 またはコントローラー 1 に接続します。
2. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。
3. バナー メッセージで、接続先のコントローラーがどれであるか (コントローラー 0 またはコントローラー 1)、また、それがアクティブ コントローラーであるかパッシブ (スタンバイ) コントローラーであるかを書き留めます。
   
   * 単一のコントローラーをシャットダウンするには、プロンプトで次のように入力します。
     
       `Stop-HcsController`
     
       これで接続先のコントローラーがシャットダウンされます。 アクティブ コントローラーを停止した場合、パッシブ コントローラーにフェールオーバーした後でアクティブ コントローラーがシャットダウンされます。
   * コントローラーを再起動するには、プロンプトで次のように入力します。
     
       `Restart-HcsController`
     
       これで接続先のコントローラーが再起動されます。 アクティブ コントローラーを再起動した場合、パッシブ コントローラーにフェールオーバーした後でアクティブ コントローラーが再起動されます。

## <a name="shut-down-a-storsimple-device"></a>StorSimple デバイスをシャットダウンする
このセクションでは、実行中の StorSimple デバイスまたは障害の発生した StorSimple デバイスをリモート コンピューターからシャットダウンする方法について説明します。 両方のデバイス コントローラーをシャットダウンすると、デバイスはオフになります。 デバイスのシャットダウンは、そのデバイスを物理的に移動するか、または運用から外すときに行います。

> [!IMPORTANT]
> デバイスをシャットダウンする前に、デバイスのコンポーネントの正常性を確認してください。 **[デバイス]、[メンテナンス]、[ハードウェアの状態]** の順に移動し、すべてのコンポーネントの LED ステータスが緑色になっていることを確認します。 正常なデバイスのみ、緑色のステータスになります。 デバイスをシャットダウンする場合や、不調なコンポーネントを交換する場合、コンポーネントごとにエラー (赤色) または機能低下 (黄色) ステータスが表示されます。
> 
> 

#### <a name="to-shut-down-a-storsimple-device"></a>StorSimple デバイスをシャットダウンするには
1. [コントローラーの再起動とシャットダウン](#restart-or-shut-down-a-single-controller) の手順に従って、デバイス上のパッシブ コントローラーを特定し、シャットダウンします。 この操作は、Azure クラシック ポータルまたは StorSimple 用 Windows PowerShell でのみ実行できます。
2. 上の手順を繰り返してアクティブ コントローラーをシャットダウンします。
3. ここでデバイスのバックプレーンを確認する必要があります。 2 つのコントローラーが完全にシャットダウンされた後、両方のコントローラーのステータス LED が赤色に点滅しています。 この時点でデバイスの電源を完全にオフにする必要がある場合は、両方の電源/冷却モジュール (PCM) の電源スイッチをオフの位置に設定してください。 これでデバイスの電源が切れます。

<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>デバイスを出荷時の設定にリセットする
> [!IMPORTANT]
> デバイスを出荷時の設定にリセットする必要がある場合は、Microsoft サポートにお問い合わせください。 次に示す手順は、Microsoft サポートと共に実行する場合にのみ使用してください。
> 
> 

ここでは、StorSimple 用 Windows PowerShell を使用して、Microsoft Azure StorSimple デバイスを出荷時の既定の設定にリセットする方法について説明します。
デバイスをリセットすると、既定でクラスター全体からすべてのデータと設定が削除されます。

Microsoft Azure StorSimple デバイスを出荷時の設定にリセットするには、次の手順に従います。

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でデバイスを既定の設定にリセットするには
1. シリアル コンソールを使ってデバイスにアクセスします。 アクティブ コントローラーに接続されていることをバナー メッセージで確認してください。
2. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。
3. プロンプトで次のコマンドを入力して、クラスター全体をリセットし、すべてのデータ、メタデータ、コントローラー設定を削除します。
   
    `Reset-HcsFactoryDefault`
   
    代わりに、1 つのコントローラーだけをリセットするには、`-scope` パラメーターを指定して [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) コマンドレットを使用します。
   
    システムが何度か再起動されます。 リセットが正常に完了すると、その旨通知されます。 このプロセスの所要時間はシステムのモデルによって異なります。8100 デバイスの場合は 45 ～ 60 分、8600 の場合は 60 ～ 90 分かかります。
   
   > [!TIP]
   > * Update 1.2 以前を使用している場合は、`–SkipFirmwareVersionCheck` パラメーターを使用してファームウェアのバージョン チェックをスキップしてください (そうしないと、"ファームウェアのバージョンが一致しないため、出荷時設定へのリセットを続行できません" というファームウェア不一致エラーが発生します)。 バージョンを参照)。
   > * ファクトリ リセットの手順は、政府ポータルの Update 1 または 1.1 を実行し、シングルまたはデュアル コントローラー交換 (Update 1 より前のソフトウェアに付属の交換用コントローラー) を正常に実行した StorSimple デバイスで失敗する可能性があります。 これは、コントローラー上の SHA1 ファイルの有無を確認するためにファクトリ リセットのイメージが検証され、Update 1 より前のソフトウェアに存在しないときに発生します。 このファクトリ リセット エラーが発生した場合は、次ステップについて Microsoft サポートにお問い合わせください。 この問題は、工場から出荷された Update 1 またはそれ以降のソフトウェアが付属した交換用コントローラーでは発生しません。
   > 
   > 

## <a name="questions-and-answers-about-managing-device-controllers"></a>デバイス コントローラーの管理に関する FAQ
このセクションでは、StorSimple デバイス コントローラーの管理に関して、よく寄せられる質問とその答えを掲載しています。

**Q.**  デバイスの両方のコントローラーが正常で電源が投入されているときに、アクティブ コントローラーを再起動またはシャットダウンした場合、どうなりますか。

**A.**  デバイスの両方のコントローラーが正常で電源が投入されている場合、確認のメッセージが表示されます。 次の操作を選択できます。

* **アクティブ コントローラーを再起動する** – アクティブ コントローラーを再起動するとデバイスがパッシブ コントローラーにフェールオーバーされる、という内容の通知が表示されます。 コントローラーが再起動されます。
* **アクティブ コントローラーをシャットダウンする** – アクティブ コントローラーをシャットダウンするとダウンタイムが発生する、という内容の通知が表示されます。 この場合、コントローラーの電源を投入するには、デバイスの電源ボタンを押す必要があります。

**Q.**  デバイスのパッシブ コントローラーが利用できない、または電源が切れているときに、アクティブ コントローラーを再起動またはシャットダウンした場合はどうなりますか。

**A.**  デバイスのパッシブ コントローラーが利用できない、または電源が切れている場合、次の操作を選択できます。

* **アクティブ コントローラーを再起動する** – 操作を続行すると一時的にサービスが中断される、という内容の通知が表示され、確認を求められます。
* **アクティブ コントローラーをシャットダウンする** – 操作を続行するとダウンタイムが発生し、デバイスの電源を投入するには、一方または両方のコントローラーを有効にするには電源ボタンを押す必要がある、という内容の通知が表示されます。 確認を求められます。

**Q.**  コントローラーの再起動またはシャットダウンに失敗するのはどのようなときでしょうか。

**A.**  次の場合、コントローラーの再起動またはシャットダウンに失敗します。

* デバイスの更新が進行中であるとき。
* コントローラーの再起動が既に進行中であるとき。
* コントローラーのシャットダウンが既に進行中であるとき。

**Q.**  コントローラーが再起動されたかどうかやシャットダウンされたかどうかは、どのようにして確認できるでしょうか。

**A.**  コントローラーのステータスは、[メンテナンス] ページで確認できます。 コントローラーが再起動またはシャットダウンされたことは、コントローラーのステータスで把握できます。 コントローラーが再起動またはシャットダウンされた場合、[アラート] ページにも情報案内アラートが表示されます。 また操作ログにも、コントローラーの再起動操作とシャットダウン操作が記録されます。 操作ログの詳細については、「 [操作ログの表示](storsimple-service-dashboard.md#view-the-operations-logs)」をご覧ください。

**Q.**  コントローラーのフェールオーバーは I/O に影響しますか。

**A.**  コントローラーがフェールオーバーされるとイニシエーターとアクティブ コントローラー間の TCP 接続がリセットされますが、パッシブ コントローラーが操作を引き継ぐときに、接続が再度確立されます。 この操作の過程でイニシエーターとデバイス間の I/O アクティビティが一時的 (30 秒未満) に停止する場合があります。

**Q.**  シャットダウンして運用から除外したコントローラーを再び運用状態に戻すにはどうすればよいでしょうか。

**A.** コントローラーを運用状態に戻すには、「 [StorSimple デバイスのコントローラー モジュールを交換する](storsimple-controller-replacement.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* このチュートリアルで紹介した手順では解決できないような問題が StorSimple デバイス コントローラーで発生した場合は、 [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md)ください。
* StorSimple Manager サービスを使用する方法については、「 [StorSimple Manager サービスを使用した StorSimple デバイスの管理](storsimple-manager-service-administration.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


