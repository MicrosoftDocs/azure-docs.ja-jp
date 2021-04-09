---
title: StorSimple Virtual Array への Update 0.5 のインストール | Microsoft Docs
description: Azure portal と修正プログラムによる方法を使用して Update 0.5 を適用するために、StorSimple Virtual Array の Web UI を使用する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: 5723e8d9fc7b0a72393dda1b225ca073a6474a0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534315"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>StorSimple Virtual Array に Update 0.5 をインストールする

## <a name="overview"></a>概要

この記事では、ローカル Web UI および Azure Portal を使用して、StorSimple Virtual Array に Update 0.5 をインストールする手順について説明します。 StorSimple Virtual Array を最新の状態に保つために、ソフトウェアの更新プログラムまたは修正プログラムを適用することが必要な場合があります。

更新プログラムを適用する前に、最初にホストで、次にデバイスで、ボリュームまたは共有をオフラインにすることをお勧めします。 これにより、データ破損の可能性を最小限に抑えられます。 ボリュームまたは共有をオフラインにしたら、デバイスの手動バックアップも作成する必要があります。

> [!IMPORTANT]
> - Update 0.5 は、デバイス上ではソフトウェア バージョン **10.0.10290.0** になります。 この更新プログラムの新機能については、[Update 0.5 のリリース ノート](storsimple-virtual-array-update-05-release-notes.md)を参照してください。
>
> - Update 0.2 以降を実行している場合は、Azure Portal から更新プログラムをインストールすることをお勧めします。 Update 0.1 または GA ソフトウェア バージョンを実行している場合は、ローカル Web UI で修正プログラムを使用する方法で、Update 0.5 をインストールする必要があります。
>
> - 更新プログラムまたは修正プログラムをインストールすると、デバイスが再起動されることに注意してください。 StorSimple Virtual Array は単一ノード デバイスであることから、実行中のすべての IO が中断され、デバイスでダウンタイムが発生します。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Update 0.2 以降を実行している場合は、Azure Portal から更新プログラムをインストールすることをお勧めします。 ポータルの手順では、ユーザーは更新プログラムをスキャン、ダウンロード、およびインストールする必要があります。 この手順の所要時間は約 7 分です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

インストールが完了したら、StorSimple デバイス マネージャー サービスに移動します。 **[デバイス]** を選択してから、先ほど更新したデバイスをクリックします。 **[設定] > [管理] > [デバイスの更新プログラム]** の順に移動します。 表示されるソフトウェアのバージョンは **10.0.10290.0** です。

## <a name="use-the-local-web-ui"></a>ローカル Web UI を使用する

ローカル Web UI を使用するときは、2 つの手順があります。

* 更新プログラムまたは修正プログラムをダウンロードする
* 更新プログラムまたは修正プログラムをインストールする

### <a name="download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードする

次の手順を実行して、Microsoft Update カタログからソフトウェア更新プログラムをダウンロードします。

#### <a name="to-download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードするには

1. Internet Explorer を起動し、[https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) に移動します。

2. このコンピューターで Microsoft Update カタログを初めて使用する場合は、Microsoft Update カタログ アドオンのインストールを求められたら、 **[インストール]** をクリックします。

3. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号を入力します。 Update 0.5 については、「**4021576**」を入力して、 **[検索]** をクリックします。
   
    **StorSimple Virtual Array Update 0.5** など、修正プログラムの一覧が表示されます。
   
    ![カタログの検索](./media/storsimple-virtual-array-install-update-05/download1.png)

4. **[Download]** をクリックします。 

5. ダウンロードする 2 つのファイル、 *.msu* と *.cab* ファイルが表示されます。 各ファイルをフォルダーにダウンロードします。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。

6. ファイルが置かれているフォルダーを開きます。
    ![パッケージ内のファイル](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    次のものがあります。
    -  Microsoft 更新プログラムのスタンドアロン パッケージ ファイル `WindowsTH-KB3011067-x64`。 このファイルは、デバイス ソフトウェアを更新するために使用されます。
    - Geneva Monitoring Agent パッケージ ファイル `GenevaMonitoringAgentPackageInstaller`。 このファイルはを監視および診断サービス (MDS) エージェントを更新するために使用されます。 cab ファイルをダブルクリックします。 .msi が表示されます。 ファイルを選択し、右クリックしてファイルの **[抽出]** を選択します。 エージェントを更新するには、 _.msi_ ファイルを使用します。

        ![MDS のエージェントの更新プログラム ファイルを抽出する](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールする

更新プログラムまたは修正プログラムをインストールする前に、更新プログラムまたは修正プログラムがローカルでホストにダウンロードされているか、ネットワーク共有を介してアクセス可能であることを確認してください。

次の手順を実行して、更新プログラムを、GA または Update 0.1 ソフトウェア バージョンが実行されているデバイスにインストールします。 各更新手順の所要時間は 2 分未満です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

#### <a name="to-install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールするには

1. ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。
   
    ![[メンテナンス] メニューから [ソフトウェア更新プログラム] が選択されていることを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. **[Update file path (更新プログラムのファイル パス)]** に、更新プログラムまたは修正プログラムのファイル名を入力します。 更新プログラムまたは修正プログラムのインストール ファイルがネットワーク共有にある場合は、ファイルを参照することもできます。 **[Apply]** をクリックします。
   
    ![[ソフトウェア更新プログラム] ページの [ファイル パスの更新] テキスト ボックスを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. 警告が表示されます。 これは単一ノード デバイスであることから、更新プログラムが適用された後にデバイスが再起動され、ダウンタイムが発生します。 チェック マーク アイコンをクリックします。
   
   ![ダウンタイムを警告するダイアログ ボックスを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. 更新プログラムが開始します。 デバイスが正常に更新されると、再起動されます。 この期間は、ローカル UI にはアクセスできません。
   
    ![更新の成功メッセージを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. 再起動が完了したら、 **サインイン** ページが表示されます。 デバイス ソフトウェアが更新されたことを確認するには、ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 表示されるソフトウェアのバージョンは、Update 0.5 では **10.0.0.0.0.10290.0** です。
   
   > [!NOTE]
   > ローカル Web UI と Azure Portal では、ソフトウェアのバージョンの表示方法が少し異なります。 たとえば、同じバージョンの場合、ローカル Web UI では **10.0.0.0.0.10290** と表示され、Azure Portal では **10.0.10290.0** と表示されます。
   
    ![現在のソフトウェアのバージョンが表示された [ソフトウェア更新プログラム] ページを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. 次の手順では、MDS エージェントを更新します。 **[ソフトウェア更新]** ページで、 **[ファイル パスの更新]** に移動して、`GenevaMonitoringAgentPackageInstaller.msi` ファイルを参照します。 手順 2 - 4 を繰り返します。 仮想アレイを再起動したら、ローカル Web UI にサインインします。

これで更新が完成しました。

## <a name="next-steps"></a>次のステップ

[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。

