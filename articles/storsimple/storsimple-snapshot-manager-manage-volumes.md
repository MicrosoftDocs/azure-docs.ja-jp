---
title: StorSimple Snapshot Manager とボリューム | Microsoft Docs
description: StorSimple Snapshot Manager MMC スナップインを使用して、ボリュームを表示および管理する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022940"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>StorSimple Snapshot Manager を使用したボリュームの表示と管理
## <a name="overview"></a>概要
StorSimple Snapshot Manager の **[ボリューム]** ノード (**スコープ** ウィンドウ) を使用してボリュームを選択し、それらのボリュームの情報を表示できます。 ボリュームは、ホストにマウントされているボリュームに対応するドライブとして表示されます。 **[ボリューム]** ノードには、ローカル ボリュームと StorSimple でサポートされているボリュームの種類が表示されます。これには、iSCSI とデバイスを使用して検出されたボリュームが含まれます。 

サポートされるボリュームの詳細については、「 [各種ボリュームのサポート](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)」をご覧ください。

![結果ウィンドウのボリューム リスト](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**[ボリューム]** ノードでは、StorSimple Snapshot Manager がボリュームを検出した後に、それらのボリュームの再スキャンや削除を実行することもできます。 

このチュートリアルでは、ボリュームをマウントし、初期化してフォーマットした後に、StorSimple Snapshot Manager を使用して、次の操作を実行する方法について説明します。

* ボリュームに関する情報を表示する 
* ボリュームを削除する
* ボリュームを再スキャンする 
* ベーシック ボリュームを構成し、バックアップする
* ダイナミック ミラー ボリュームを構成し、バックアップする

> [!NOTE]
> [**ボリューム**] ノードのすべての操作は [**アクション**] ウィンドウでも実行できます。
> 
> 

## <a name="mount-volumes"></a>ボリュームのマウント
StorSimple ボリュームをマウントし、初期化してフォーマットするには、次の手順に従います。 この手順で使用する "ディスクの管理" は、ハード ディスクおよび対応するボリュームまたはパーティションを管理するためのシステム ユーティリティです。 ディスクの管理の詳細については、Microsoft TechNet Web サイトの「 [Disk Management (ディスクの管理)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) 」をご覧ください。

#### <a name="to-mount-volumes"></a>ボリュームをマウントするには
1. ホスト コンピューターで、Microsoft iSCSI イニシエーターを開始します。
2. ターゲット ポータルとしてインターフェイス IP アドレスのいずれかを入力するか、検出 IP アドレスを入力し、デバイスに接続します。 デバイスに接続したら、ボリュームは Windows システムにアクセスできるようになります。 Microsoft iSCSI イニシエーターの使用方法の詳細については、「[Installing and Configuring Microsoft iSCSI Initiator (Microsoft iSCSI イニシエーターのインストールと構成)][1]」の「Connecting to an iSCSI target device (iSCSI ターゲット デバイスへの接続)」をご覧ください。
3. 次のいずれかの方法を使用して、ディスクの管理を起動します。
   
   * **[ファイル名を指定して実行]** ボックスに「Diskmgmt.msc」と入力します。
   * サーバー マネージャーを起動して、**[記憶域]** ノードを展開し、**[ディスクの管理]** を選択します。
   * **[管理ツール]** を起動し、**[コンピューターの管理]** ノードを展開して、**[ディスクの管理]** を選択します。 
     
     > [!NOTE]
     > ディスクの管理を実行するには、管理者特権を使用する必要があります。
     > 
     > 
4. ボリュームをオンラインにします。
   
   1. ディスク管理で、[**オフライン**] の印が付いているボリュームを右クリックします。
   2. [**ディスクの再アクティブ化]**] をクリックします。 ディスクが再度アクティブになると、[**オンライン**] の印が付きます。
5. ボリュームを初期化します。
   
   1. 検出されたボリュームを右クリックします。
   2. メニューで、[**ディスクの初期化**] を選択します。
   3. [**ディスクの初期化**] ダイアログ ボックスで、初期化するディスクを選択し、[**OK**] をクリックします。
6. シンプル ボリュームをフォーマットします。
   
   1. フォーマットするボリュームを右クリックします。
   2. メニューで、[**新しいシンプル ボリューム**] を選択します。
   3. 新しいシンプル ボリューム ウィザードを使用して、ボリュームをフォーマットします。
      
      * ボリュームのサイズを指定します。
      * ドライブ文字を指定します。
      * NTFS ファイル システムを選択します。
      * 64 KB アロケーション ユニット サイズを指定します。
      * クイック フォーマットを実行します。
7. 複数パーティション ボリュームをフォーマットします。 手順については、「 [Implementing Disk Management (ディスク管理の実装)](/previous-versions/tn-archive/dd163556(v=technet.10))」の「Partitions and Volumes (パーティションとボリューム)」をご覧ください。

## <a name="view-information-about-your-volumes"></a>ボリュームに関する情報の表示
ローカル ボリュームと Azure StorSimple ボリュームに関する情報を表示するには、次の手順に従います。

#### <a name="to-view-volume-information"></a>ボリューム情報を表示するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。 
2. **[スコープ]** ウィンドウで、**[ボリューム]** ノードをクリックします。 **結果** ウィンドウに、すべての Azure StorSimple ボリュームを含む、ローカル ボリュームとマウントされたボリュームのリストが表示されます。 (**結果** ウィンドウ内の列は構成可能です。 **[ボリューム]** ノードを右クリックして **[表示]** を選択し、 **[列の追加と削除]** を選択します)。
   
    ![列の構成](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | 結果の列 | 説明 |
   |:--- |:--- |
   |  名前 |[**名前**] 列には、検出された各ボリュームに割り当てられたドライブ文字が入ります。 |
   |  Device |**[デバイス]** 列には、ホスト コンピューターに接続されているデバイスの IP アドレスが表示されます。 |
   |  デバイスのボリューム名 |[**デバイスのボリューム名**] 列には、選択されているボリュームが属するデバイス ボリュームの名前が入ります。 これは、Azure Portal で定義されたそのボリュームのボリューム名です。 |
   |  アクセス パス |[**アクセス パス**] 列にはボリュームにアクセスするためのパスが入ります。 これは、ホスト コンピューターのボリュームにアクセスする際のドライブ文字またはマウント ポイントです。 |

## <a name="delete-a-volume"></a>ボリュームを削除する
StorSimple Snapshot Manager からボリュームを削除するには、次の手順に従います。

> [!NOTE]
> ボリューム グループに属するボリュームは削除できません  (ボリューム グループのメンバーであるボリュームでは、削除オプションは使用できません)。ボリュームを削除するには、ボリューム グループ全体を削除する必要があります。

#### <a name="to-delete-a-volume"></a>ボリュームを削除するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、**[ボリューム]** ノードをクリックします。 
3. [**結果**] ウィンドウで、削除するボリュームを右クリックします。
4. メニューで [**削除**] をクリックします。 
   
    ![ボリュームを削除する](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. [**ボリュームの削除**] ダイアログ ボックスが表示されます。 テキスト ボックスに「**確認**」と入力し、**[OK]** をクリックします。
6. 既定では、StorSimple Snapshot Manager はボリュームを削除する前にバックアップします。 この予防措置により、誤って削除してもデータが失われることはありません。 ボリュームのバックアップ中、StorSimple Snapshot Manager に **[自動スナップショット]** 進行状況メッセージが表示されます。 
   
    ![自動スナップショット メッセージ](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>ボリュームを再スキャンする
StorSimple Snapshot Manager に接続されているボリュームを再スキャンするには、次の手順に従います。

#### <a name="to-rescan-the-volumes"></a>ボリュームを再スキャンするには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. [**スコープ**] ウィンドウで、[**ボリューム**] を右クリックし、[**ボリュームの再スキャン**] をクリックします。
   
    ![ボリュームを再スキャンする](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    この手順により、ボリューム リストが StorSimple Snapshot Manager と同期されます。 新しいボリュームや削除されたボリュームなど、すべての変更が結果に反映されます。

## <a name="configure-and-back-up-a-basic-volume"></a>ベーシック ボリュームの構成とバックアップ
ベーシック ボリュームのバックアップを構成した後、バックアップをすぐに開始するか、スケジュールされたバックアップのポリシーを作成するには、次の手順に従います。

### <a name="prerequisites"></a>前提条件
作業を開始する前に、次のことを行います。

* StorSimple デバイスとホスト コンピューターが正しく構成されていることを確認します。 詳細については、「 [オンプレミスの StorSimple デバイスのデプロイ](./storsimple-8000-deployment-walkthrough-u2.md)」をご覧ください。
* StorSimple Snapshot Manager をインストールして構成します。 詳細については、 [Deploy StorSimple Snapshot Manager (StorSimple Snapshot Manager のデプロイ)](storsimple-snapshot-manager-deployment.md)」をご覧ください。

#### <a name="to-configure-backup-of-a-basic-volume"></a>ベーシック ボリュームのバックアップを構成するには
1. StorSimple デバイスにベーシック ボリュームを作成します。
2. 「 [ボリュームのマウント](#mount-volumes)」の説明に従って、ボリュームをマウントし、初期化してフォーマットします。 
3. デスクトップの [StorSimple Snapshot Manager] アイコンをクリックします。 [StorSimple Snapshot Manager] ウィンドウが表示されます。 
4. [**スコープ**] ウィンドウで、[**ボリューム**] ノードを右クリックし、[**ボリュームの再スキャン**] を選択します。 スキャンが終了すると、ボリュームの一覧が [**結果**] ウィンドウに表示されます。 
5. [**結果**] ウィンドウでボリュームを右クリックし、[**ボリューム グループの作成**] を選択します。 
   
    ![[ボリューム グループの作成]](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. [**ボリューム グループの作成**] ダイアログ ボックスで、ボリューム グループの名前を入力し、ボリュームをそれに割り当てて、[**OK**] をクリックします。
7. [**スコープ**] ウィンドウで、[**ボリューム グループ**] ノードを展開します。 **[ボリューム グループ]** ノードの下に新しいボリューム グループが表示されます。 
8. ボリューム グループ名を右クリックします。
   
   * 対話形式の (オンデマンド) バックアップ ジョブを開始するには、[**バックアップの作成**] をクリックします。 
   * 自動バックアップをスケジュールするには、[**バックアップ ポリシーの作成**] をクリックします。 [**全般**] ページで、一覧からボリューム グループを選択します。 [**スケジュール**] ページで、スケジュールの詳細を入力します。 操作が終了したら、 **[OK]** をクリックします。 
9. バックアップ ジョブが開始されたことを確認するには、[**スコープ**] ウィンドウで [**ジョブ**] ノードを展開し、[**実行中**] ノードをクリックします。 現在実行中のジョブの一覧が [**結果**] ウィンドウに表示されます。 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>ダイナミック ミラー ボリュームの構成とバックアップ
ダイナミック ミラー ボリュームのバックアップを構成するには、次の手順を実行します。

* 手順 1: ディスクの管理を使用してダイナミック ミラー ボリュームを作成する 
* 手順 2: StorSimple Snapshot Manager を使用してバックアップを構成する

### <a name="prerequisites"></a>前提条件
作業を開始する前に、次のことを行います。

* StorSimple デバイスとホスト コンピューターが正しく構成されていることを確認します。 詳細については、「 [オンプレミスの StorSimple デバイスのデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」をご覧ください。
* StorSimple Snapshot Manager をインストールして構成します。 詳細については、 [Deploy StorSimple Snapshot Manager (StorSimple Snapshot Manager のデプロイ)](storsimple-snapshot-manager-deployment.md)」をご覧ください。
* StorSimple デバイスに 2 つのボリュームを構成します  (この例では、使用できるボリュームは **Disk 1** と **Disk 2** です)。 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>手順 1: ディスクの管理を使用してダイナミック ミラー ボリュームを作成する
ディスクの管理は、ハード ディスクとハード ディスクに含まれるボリュームまたはパーティションを管理するためのシステム ユーティリティです。 ディスクの管理の詳細については、Microsoft TechNet Web サイトの「 [Disk Management (ディスクの管理)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) 」をご覧ください。

#### <a name="to-create-a-dynamic-mirrored-volume"></a>ダイナミック ミラー ボリュームを作成するには
1. 次のいずれかの方法を使用して、ディスクの管理を起動します。 
   
   * [**ファイル名を指定して実行**] ボックスを開き、「**Diskmgmt.msc**」と入力して、Enter キーを押します。
   * サーバー マネージャーを起動して、**[記憶域]** ノードを展開し、**[ディスクの管理]** を選択します。 
   * **[管理ツール]** を起動し、**[コンピューターの管理]** ノードを展開して、**[ディスクの管理]** を選択します。 
2. StorSimple デバイスに使用可能な 2 つのボリュームがあることを確認します  (この例では、使用できるボリュームは **Disk 1** と **Disk 2** です)。 
3. [ディスクの管理] ウィンドウの下部ウィンドウの右側の列で、[**Disk 1**] を右クリックし、[**新しいミラー ボリューム**] を選択します。 
   
    ![[新しいミラー ボリューム]](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. [**新しいミラー ボリューム**] ウィザード ページで、[**次へ**] をクリックします。
5. [**ディスクの選択**] ページの [**選択済み**] ウィンドウで [**Disk 2**] を選択し、[**追加**] をクリックして、[**次へ**] をクリックします。 
6. **[ドライブ文字またはパスの割り当て]** ページで既定値をそのまま使用し、**[次へ]** をクリックします。 
7. [**ボリュームのフォーマット**] ページの [**アロケーション ユニット サイズ**] ボックスで、[**64K**] を選択します。 [**クイック フォーマットする**] チェック ボックスをオンにして、[**次へ**] をクリックします。 
8. [**新しいミラー ボリューム ウィザードの完了**] ページで設定を確認し、[**完了**] をクリックします。 
9. ベーシック ディスクがダイナミック ディスクに変換されることを示すメッセージが表示されます。 **[はい]** をクリックします。
   
    ![ダイナミック ディスク変換メッセージ](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. ディスクの管理で、Disk 1 と Disk 2 がダイナミック ミラー ボリュームとして表示されていることを確認します  ([状態] 列に **[動的]** と表示され、容量バーの色がミラー化されたボリュームを示す赤に変わっている必要があります)。 
    
    ![ディスクの管理でミラー化されたダイナミック ディスク](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>手順 2: StorSimple Snapshot Manager を使用してバックアップを構成する
ダイナミック ミラー ボリュームのバックアップを構成した後、バックアップをすぐに開始するか、スケジュールされたバックアップのポリシーを作成するには、次の手順に従います。

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>ダイナミック ミラー ボリュームのバックアップを構成するには
1. デスクトップの [StorSimple Snapshot Manager] アイコンをクリックします。 [StorSimple Snapshot Manager] ウィンドウが表示されます。 
2. [**スコープ**] ウィンドウで、[**ボリューム**] ノードを右クリックし、[**ボリュームの再スキャン**] を選択します。 スキャンが終了すると、ボリュームの一覧が [**結果**] ウィンドウに表示されます。 ダイナミック ミラー ボリュームは、1 つのボリュームとして表示されます。 
3. [**結果**] ウィンドウでダイナミック ミラー ボリュームを右クリックし、[**ボリューム グループの作成**] をクリックします。 
4. [**ボリューム グループの作成**] ダイアログ ボックスで、ボリューム グループの名前を入力し、ダイナミック ミラー ボリュームをこのグループに割り当てて、[**OK**] をクリックします。 
5. [**スコープ**] ウィンドウで、[**ボリューム グループ**] ノードを展開します。 **[ボリューム グループ]** ノードの下に新しいボリューム グループが表示されます。 
6. ボリューム グループ名を右クリックします。 
   
   * 対話形式の (オンデマンド) バックアップ ジョブを開始するには、[**バックアップの作成**] をクリックします。 
   * 自動バックアップをスケジュールするには、[**バックアップ ポリシーの作成**] をクリックします。 [**全般**] ページで、一覧からボリューム グループを選択します。 [**スケジュール**] ページで、スケジュールの詳細を入力します。 操作が終了したら、 **[OK]** をクリックします。 
7. バックアップ ジョブの実行状態を監視できます。 **[スコープ]** ウィンドウで、**[ジョブ]** ノードを展開し、**[実行中]** をクリックします。ジョブの詳細が **[結果]** ウィンドウに表示されます。 バックアップ ジョブが終了すると、詳細が **[過去 24 時間]** ジョブ リストに転送されます。 

## <a name="next-steps"></a>次のステップ
* [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理](storsimple-snapshot-manager-admin.md)方法を確認する。
* [StorSimple Snapshot Manager を使用してボリューム グループを作成および管理する](storsimple-snapshot-manager-manage-volume-groups.md)方法を確認します。

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)