---
title: StorSimple Snapshot Manager のデプロイ | Microsoft Docs
description: StorSimple Snapshot Manager、MMC スナップインをダウンロードしてインストールし、StorSimple データ保護およびバックアップ機能を管理する方法を説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 5f94564c5e769d3cf0e0abbe92a309a1ee2117a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003873"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>StorSimple Snapshot Manager MMC スナップインのデプロイ

## <a name="overview"></a>概要
StorSimple Snapshot Manager は、Microsoft Azure StorSimple 環境でのデータ保護とバックアップ管理を簡略化する Microsoft 管理コンソール (MMC) スナップインです。 StorSimple Snapshot Manager を使用すれば、Microsoft Azure StorSimple をオンプレミスで管理し、完全に統合されたストレージ システムであるかのようにクラウド ストレージを管理できるため、バックアップと復元処理が簡略化され、コストを削減できます。 

このチュートリアルでは構成要件に加え、StorSimple Snapshot Manager のインストール、削除、およびアップグレード手順についても説明します。

> [!NOTE]
> * StorSimple Snapshot Manager を使用して、Microsoft Azure StorSimple Virtual Array (別名 StorSimple オンプレミス仮想デバイス) を管理することはできません。
> * StorSimple デバイスに StorSimple Update 2 をインストールする場合は、 **StorSimple Update 2 をインストールする前に**、最新バージョンの StorSimple Snapshot Manager をダウンロードしてインストールする必要があります。 最新バージョンの StorSimple Snapshot Manager には下位互換性があり、製品版のすべての Microsoft Azure StorSimple で動作します。 以前のバージョンの StorSimple Snapshot Manager を使用している場合は更新する必要があります (新しいバージョンをインストールする前に、以前のバージョンをアンインストールする必要はありません)。


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager のインストール
StorSimple Snapshot Manager は、Windows Server 2008 R2 SP1、Windows Server 2012、または Windows Server 2012 R2 オペレーティング システムを実行しているコンピューターにインストールできます。 Windows 2008 R2 を実行しているサーバーでは、Windows Server 2008 SP1 と Windows Management Framework 3.0 をインストールする必要もあります。

Microsoft 管理コンソール (MMC) 用に StorSimple Snapshot Manager スナップインをインストールまたはアップグレードする前に、Microsoft Azure StorSimple デバイスとホスト サーバーが正しく構成されていることを確認してください。

## <a name="configure-prerequisites"></a>構成の前提条件
次の手順では、StorSimple Snapshot Manager をインストールする前に完了する必要がある構成タスクの概要を説明します。 システム要件や詳細な手順の説明など、Microsoft Azure StorSimple の構成とセットアップに関する完全な情報については、「 [オンプレミスの StorSimple デバイスのデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」をご覧ください。

> [!IMPORTANT]
> 開始する前に、[「オンプレミスの StorSimple デバイスのデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」にある「[デプロイメント チェックリスト](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)」と「[デプロイメントの前提条件](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)」をご確認ください。
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager をインストールする前に
1. 「[StorSimple 8100 デバイスの取り付け](storsimple-8100-hardware-installation.md)」または「[StorSimple 8600 デバイスの取り付け](storsimple-8600-hardware-installation.md)」で説明されているように、Microsoft Azure StorSimple デバイスを開梱、マウント、および接続します。
2. ホスト コンピューターで、次のいずれかのオペレーティング システムが実行されていることを確認します。
   
   * Windows Server 2008 R2 (Windows 2008 R2 を実行しているサーバーでは、Windows Server 2008 SP1 および Windows Management Framework 3.0 もインストールする必要があります)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     StorSimple 仮想デバイスの場合、ホストは Microsoft Azure Virtual Machine である必要があります。
3. すべての Microsoft Azure StorSimple 構成要件を満たしていることを確認します。 詳細については、「 [デプロイメントの前提条件](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)」を確認してください。
4. ホストにデバイスを接続し、初期構成を実行します。 詳細については、「 [デプロイメントの手順](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps)」を確認してください。
5. デバイスにボリュームを作成し、それらをホストに割り当て、ホストがボリュームをマウントし、使用できることを確認します。 StorSimple Snapshot Manager では、次のタイプのボリュームがサポートされています。
   
   * 基本ボリューム
   * シンプル ボリューム
   * 動的ボリューム
   * ミラー化された動的ボリューム (RAID 1)
   * クラスターの共有ボリューム
     
     StorSimple デバイスまたは StorSimple 仮想デバイスにボリュームを作成する方法については、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」の「[手順 6: ボリュームを作成する](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)」をご確認ください。

## <a name="install-a-new-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager の新規インストール
StorSimple Snapshot Manager をインストールする前に、StorSimple デバイスまたは StorSimple 仮想デバイスに作成したボリュームが、「 [構成の前提条件](#configure-prerequisites)」に説明されているようにマウント、初期化、およびフォーマットされていることを確認します。

> [!IMPORTANT]
> * StorSimple 仮想デバイスの場合、ホストは Microsoft Azure Virtual Machine である必要があります。 
> * ホストは、Windows 2008 R2、Windows Server 2012、または Windows Server 2012 R2 を実行している必要があります。 サーバーが Windows Server 2008 R2 を実行している場合、Windows Server 2008 SP1 と Windows Management Framework 3.0 もインストールする必要があります。
> * デバイスを StorSimple Snapshot Manager に接続するには、ホストから StorSimple デバイスに iSCSI 接続を構成する必要があります。

次の手順に従って、StorSimple Snapshot Manager のフレッシュ インストールを実行します。 アップグレードをインストールする場合は、「 [StorSimple Snapshot Manager のアップグレードまたは再インストール](#upgrade-or-reinstall-storsimple-snapshot-manager)」をご覧ください。

* 手順 1: StorSimple Snapshot Manager をインストールします 
* 手順 2: StorSimple Snapshot Manager をデバイスに接続します 
* 手順 3: デバイスへの接続を確認します 

### <a name="step-1-install-storsimple-snapshot-manager"></a>手順 1: StorSimple Snapshot Manager をインストールします
StorSimple Snapshot Manager をインストールするには、以下の手順を実行します。

#### <a name="to-install-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager をインストールするには
1. StorSimple Snapshot Manager ソフトウェアをダウンロードして (Microsoft ダウンロード センターの [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) にアクセス)、それをホストのローカルに保存します。
2. エクスプローラーで圧縮フォルダーを右クリックし、**[すべて展開]** をクリックします。
3. **[圧縮 (ZIP 形式) フォルダーの展開]** ウィンドウの **[展開先の選択とファイルの展開]** ボックスで、ファイルの展開先となるパスを入力するか、参照画面で選択します。
   
    > [!IMPORTANT]
    > StorSimple Snapshot Manager は C: ドライブにインストールする必要があります。
    
4. **[完了時に展開されたファイルを表示する]** チェック ボックスをオンにし、**[展開]** をクリックします。
   
    ![ファイル展開ダイアログ ボックス](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. 展開が完了したら、展開先フォルダーが開きます。 展開先フォルダーに表示されるアプリケーションのセットアップ アイコンをダブルクリックします。
6. **"セットアップ完了"** のメッセージが表示されたら、**[閉じる]** をクリックします。 デスクトップに [StorSimple Snapshot Manager] アイコンが表示されます。
   
    ![デスクトップ アイコン](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>手順 2: StorSimple Snapshot Manager をデバイスに接続します
次の手順に従って、StorSimple Snapshot Manager を StorSimple デバイスに接続します。

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>StorSimple Snapshot Manager をデバイスに接続するには
1. デスクトップの [StorSimple Snapshot Manager] アイコンをクリックします。 [StorSimple Snapshot Manager] ウィンドウが表示されます。 このウィンドウには、**[スコープ]**、**[結果]**、**[操作]** の各ウィンドウが含まれます。 
   
    ![StorSimple Snapshot Manager のユーザー インターフェイス](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **[スコープ]** ウィンドウ (左側のウィンドウ) には、一連のノードがツリー構造で表示されます。 一部のノードは展開して、そのノードに関連するビューまたは特定のデータを選択できます。 ノードを展開または折りたたむには、矢印アイコンをクリックします。 **[スコープ]** ウィンドウで項目を右クリックすると、その項目に対して使用できる操作が一覧表示されます。
   * **[結果]** ウィンドウ (中央のウィンドウ) には、**[スコープ]** ウィンドウで選択したノード、ビュー、データの詳細なステータス情報が表示されます。
   * **[操作]** ウィンドウには、**[スコープ]** ウィンドウで選択したノード、ビュー、データに対して実行できる操作が一覧表示されます。
     
     StorSimple Snapshot Manager ユーザー インターフェイスの完全な説明については、「 [StorSimple Snapshot Manager のユーザー インターフェイス](storsimple-use-snapshot-manager.md)」をご覧ください。
2. [**スコープ**] ウィンドウで [**デバイス**] ノードを右クリックし、[**デバイスの構成**] をクリックします。 [**デバイスの構成**] ダイアログ ボックスが表示されます。
   
    ![[デバイスの構成]](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. **[デバイス]** ボックスの一覧で、Microsoft Azure StorSimple デバイスまたは仮想デバイスの IP アドレスを選択します。 **[パスワード]** テキスト ボックスに、Azure ポータルでデバイス用に作成した StorSimple Snapshot Manager パスワードを入力します。 **[OK]** をクリックします。
4. StorSimple Snapshot Manager により、指定したデバイスが検索されます。 デバイスを使用できる場合は、StorSimple Snapshot Manager によって接続が追加されます。 [デバイスへの接続を検証](#to-verify-the-connection) して、接続が正常に追加されたことを確認できます。
   
    何かの理由でデバイスを使用できない場合は、StorSimple Snapshot Manager によってエラー メッセージが返されます。 [**OK**] をクリックしてエラー メッセージを閉じ、[**キャンセル**] をクリックして [**デバイスの構成**] ダイアログ ボックスを閉じます。
5. ボリューム グループに関連するバックアップがある場合、デバイスに接続されると、StorSimple Snapshot Manager は、そのデバイス用に構成された各ボリューム グループをインポートします。 関連付けられているバックアップがないボリューム グループはインポートされません。 また、ボリューム グループに対して作成されたバックアップ ポリシーはインポートされません。 インポートされたグループを確認するには、**[スコープ]** ウィンドウで一番上の **[ボリューム グループ]** ノードを右クリックし、**[インポートされたグループの切り替え]** をクリックします。

### <a name="step-3-verify-the-connection-to-the-device"></a>手順 3: デバイスへの接続を確認します
次の手順に従って、StorSimple Snapshot Manager が StorSimple デバイスに接続されていることを確認します。

#### <a name="to-verify-the-connection"></a>接続を確認するには
1. **[スコープ]** ウィンドウで、**[デバイス]** ノードをクリックします。
   
    ![StorSimple Snapshot Manager デバイスの状態](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. **[結果]** ウィンドウを確認します。 
   
   * デバイス アイコンに緑色のインジケーターが表示され、**[状態]** 列に **[使用可能]** が表示されている場合、デバイスは接続されています。 
   * デバイス アイコンに赤色のインジケーターが表示され、 **[状態]** 列に [使用不可] が表示されている場合、デバイスは接続されていません。 
   * **[状態]** 列に **[更新しています]** が表示されている場合、StorSimple Snapshot Manager は、接続されたデバイスのボリューム グループと関連するバックアップを取得中です。

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager のアップグレードまたは再インストール
StorSimple Snapshot Manager をアップグレードまたは再インストールするには、このソフトウェアを完全にアンインストールする必要があります。 

StorSimple Snapshot Manager を再インストールする前に、ホスト コンピューターで既存の StorSimple Snapshot Manager データベースをバックアップします。 これにより、バックアップ ポリシーと構成情報が保存されるため、バックアップからこのデータを簡単に復元できます。

StorSimple Snapshot Manager をアップグレードまたは再インストールするには、以下の手順を実行します。

* 手順 1: StorSimple Snapshot Manager をアンインストールします 
* 手順 2: StorSimple Snapshot Manager データベースをバックアップします 
* 手順 3: StorSimple Snapshot Manager を再インストールし、データベースを復元します 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>手順 1: StorSimple Snapshot Manager をアンインストールします
StorSimple Snapshot Manager をアンインストールするには、以下の手順を実行します。

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager をアンインストールするには
1. ホスト コンピューターで [**コントロール パネル**] を開き、[**プログラム**] をクリックし、[**プログラムと機能**] をクリックします。
2. 左ウィンドウで、[**プログラムのアンインストールと変更**] をクリックします。
3. [**StorSimple Snapshot Manager**] を右クリックし、[**アンインストール**] をクリックします。
4. これにより、StorSimple Snapshot Manager のセットアップ プログラムが開始します。 [**セットアップの変更**] をクリックし、[**アンインストール**] をクリックします。
   
   > [!NOTE]
   > StorSimple Snapshot Manager や ディスクの管理など、バック グラウンドで実行されている MMC プロセスがある場合、アンインストールは失敗し、プログラムのアンインストールを試行する前に、MMC のすべてのインスタンスを閉じるように求めるメッセージが表示されます。 [**セットアップの完了後、アプリケーションを自動的に終了して、再起動する**] を選択し、[**OK**] をクリックします。
   > 
   > 
5. アンインストール プロセスが完了すると、[**セットアップ完了**] メッセージが表示されます。 **[閉じる]** をクリックします。

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>手順 2: StorSimple Snapshot Manager データベースをバックアップします
StorSimple Snapshot Manager データベースのコピーを作成して保存するには、以下の手順を実行します。

#### <a name="to-back-up-the-database"></a>データベースをバックアップするには
1. Microsoft StorSimple Management Service を停止します。
   
   1. Server Manager を起動します。
   2. サーバー マネージャーのダッシュボードで、**[ツール]** メニューの **[サービス]** を選択します。
   3. **[サービス]** ページで、**[Microsoft StorSimple Management Service]** を選択します。
   4. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスの停止]** をクリックします。
      
        ![StorSimple Device Manager サービスを停止する](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. C:\ProgramData\Microsoft\StorSimple\BACatalog を参照します。 
   
   > [!NOTE]
   > ProgramData は隠しフォルダーです。
  
3. カタログ XML ファイルを検索し、ファイルをコピーして、安全な場所、またはクラウドにコピーを保存します。
   
    ![StorSimple バックアップ カタログ ファイル](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Microsoft StorSimple Management Service を再起動します。 
   
   1. サーバー マネージャーのダッシュボードで、**[ツール]** メニューの **[サービス]** を選択します。
   2. [**サービス**] ページで、[**Microsoft StorSimple Management Service**] を選択します。
   3. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスの再起動]** をクリックします。 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>手順 3: StorSimple Snapshot Manager を再インストールし、データベースを復元します
StorSimple Snapshot Manager を再インストールするには、「 [StorSimple Snapshot Manager の新規インストール](#install-a-new-storsimple-snapshot-manager)」の手順に従います。 次に、以下の手順に従って、StorSimple Snapshot Manager データベースを復元します。

#### <a name="to-restore-the-database"></a>データベースを復元するには
1. Microsoft StorSimple Management Service を停止します。
   
   1. Server Manager を起動します。
   2. サーバー マネージャーのダッシュボードで、**[ツール]** メニューの **[サービス]** を選択します。
   3. **[サービス]** ページで、**[Microsoft StorSimple Management Service]** を選択します。
   4. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスの停止]** をクリックします。
2. C:\ProgramData\Microsoft\StorSimple\BACatalog を参照します。
   
   > [!NOTE]
   > ProgramData は隠しフォルダーです。
   > 
   > 
3. カタログ XML ファイルを削除し、以前に保存したバージョンで置き換えます。
4. Microsoft StorSimple Management Service を再起動します。 
   
   1. サーバー マネージャーのダッシュボードで、**[ツール]** メニューの **[サービス]** を選択します。
   2. **[サービス]** ページで、**[Microsoft StorSimple Management Service]** を選択します。
   3. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスの再起動]** をクリックします。

## <a name="next-steps"></a>次のステップ
* StorSimple Snapshot Manager の詳細については、「 [StorSimple Snapshot Manager について](storsimple-what-is-snapshot-manager.md)」を参照してください。
* StorSimple Snapshot Manager ユーザー インターフェイスの詳細については、「 [StorSimple Snapshot Manager のユーザー インターフェイス](storsimple-use-snapshot-manager.md)」を参照してください。
* StorSimple Snapshot Manager の使用方法については、「 [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理](storsimple-snapshot-manager-admin.md)」を参照してください。

