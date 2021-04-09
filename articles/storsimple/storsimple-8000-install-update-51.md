---
title: StorSimple 8000 シリーズ デバイスへの Update 5.1 のインストール | Microsoft Docs
description: StorSimple 8000 シリーズ デバイスに StorSimple 8000 シリーズの Update 5.1 をインストールする方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91575964"
---
# <a name="install-update-51-on-your-storsimple-device"></a>StorSimple デバイスへの Update 5.1 のインストール

## <a name="overview"></a>概要

このチュートリアルでは、Update 5.1 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure portal 経由で Update 5.1 をインストールする方法について説明します。 <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Update 5.1 には、中断なしのセキュリティ更新プログラムが含まれています。 中断なしまたは通常の更新プログラムは、Azure portal を使用して適用できます <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Update 5.1 は必須の更新プログラムであり、すぐにインストールする必要があります。 詳細については、[Update 5.1 リリース ノート](storsimple-update51-release-notes.md)に関する記事を参照してください。
> * インストールの前に、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。 これらの事前チェックは、Azure portal から更新プログラムを適用する場合にのみ実行されます。
> * 修正プログラムによる方法を使用してインストールする場合は、[Microsoft サポート](mailto:support@microsoft.com)にお問い合わせください。

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Azure portal を使用して Update 5.1 をインストールする

デバイスを [Update 5.1](storsimple-update51-release-notes.md) に更新するには、次の手順を実行します。

> [!NOTE]
> Microsoft はデバイスから追加の診断情報を取得します。 その結果、Microsoft の運用チームが問題のあるデバイスを識別したときに、デバイスから情報を収集して問題を診断する能力が向上します。

#### <a name="to-install-an-update-from-the-azure-portal"></a>Azure ポータルから 更新プログラムをインストールするには

1. StorSimple サービス ページでデバイスを選択します。

    ![デバイスの削除](./media/storsimple-8000-install-update-51/update1.png)

2. **[デバイスの設定]**  >  **[デバイスの更新プログラム]** の順に移動します。

    ![[設定] ブレードのスクリーンショット。[デバイスの更新] オプションのコールアウトが示されています。](./media/storsimple-8000-install-update-51/update2.png)

3. 新しい更新プログラムが利用できる場合は、通知が表示されます。 または、 **[デバイスの更新プログラム]** ブレードで **[更新プログラムのスキャン]** をクリックします。 利用可能な更新プログラムをスキャンするジョブが作成されます。 ジョブが正常に完了すると、その旨が通知されます。

    ![[設定] ブレードのスクリーンショット。[デバイスの更新] オプションのコールアウトが示され、[デバイスの更新] ブレードでは [新しい通常の更新プログラムを利用できます] というメッセージがコールアウトされています。](./media/storsimple-8000-install-update-51/update3.png)

4. 更新プログラムをデバイスに適用する前に、リリース ノートを確認することをお勧めします。 **[更新プログラムのインストール]** をクリックすると、更新プログラムが適用されます。 **[定期更新プログラムの確認]** ブレードで、更新プログラムを適用する前に完了する必要のある前提条件を確認します。 デバイスを更新する準備ができたことを示すチェック ボックスをオンにし、 **[インストール]** をクリックします。

    ![[デバイスの更新] ブレードのスクリーンショット。[更新プログラムのインストール] オプションのコールアウトと、[定期的な更新プログラムの確認] の同意オプションと [インストール] オプションのコールアウトが示されています。](./media/storsimple-8000-install-update-51/update4.png)

5. 一連の前提条件のチェックが開始されます。 これらのチェックは次のとおりです。
   
   * **コントローラーの正常性チェック** では、両方のデバイス コントローラーが正常であり、オンラインであることを確認します。
   * **ハードウェア コンポーネントの正常性チェック** では、StorSimple デバイスのすべてのハードウェア コンポーネントが正常であることを確認します。
   * **DATA 0 チェック** では、デバイスで DATA 0 が有効であることを確認します。 このインターフェイスが有効でない場合は、有効にしてから再試行する必要があります。

     すべてのチェックが正常に完了した場合にのみ、更新プログラムがダウンロードされてインストールされます。 チェックが実行中のときは通知されます。 事前チェックに失敗した場合、失敗の理由が表示されます。 それらの問題を解決してから操作をやり直してください。 これらの問題に自分で対処できない場合、Microsoft サポートに連絡することが必要になる場合があります。

7. 事前チェックが正常に完了したら、更新ジョブが作成されます。 更新ジョブが正常に作成されると、通知されます。
   
    ![[ソフトウェアの更新ジョブを開始しています] という通知のスクリーンショット。](./media/storsimple-8000-install-update-51/update6.png)
   
    その後、更新プログラムがデバイスに適用されます。

9. 更新の完了には数時間かかります。 更新ジョブを選択し、 **[詳細]** をクリックすると、ジョブの詳細をいつでも表示できます。

    ![[デバイスの更新] ブレードのスクリーンショット。[ソフトウェア更新プログラムのダウンロードとインストールが進行中です] オプションのコールアウトと、[更新プログラムのインストール] ブレードが表示されています。](./media/storsimple-8000-install-update-51/update8.png)

     **[デバイスの設定]、[ジョブ]** の順に移動して、更新ジョブの進行状況を監視することもできます。 **[ジョブ]** ブレードで、更新の進行状況を確認できます。

     ![[設定] ブレードのスクリーンショット。[ジョブ] のコールアウトが表示され、[ジョブ] ブレードに更新の進行状況が示されています。](./media/storsimple-8000-install-update-51/update7.png)

10. ジョブが完了したら、 **[デバイスの設定]、[デバイスの更新プログラム]** の順に移動します。 ソフトウェアのバージョンが更新されています。


デバイスで **StorSimple 8000 Series Update 5.1 (6.3.9600.17885)** が実行されていることを確認します。 **[最終更新日]** が変更されています。
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>次のステップ

詳しくは、[Update 5.1 リリース](storsimple-update51-release-notes.md)に関するページをご覧ください。
