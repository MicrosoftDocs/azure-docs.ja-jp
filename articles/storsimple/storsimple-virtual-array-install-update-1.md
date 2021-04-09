---
title: StorSimple Virtual Array への Update 1.0 のインストール | Microsoft Docs
description: Azure portal と修正プログラムによる方法を使用して Update 1.0 を適用するために、StorSimple Virtual Array の Web UI を使用する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: d05395f97aded0b57780f898e9cba6ddca491f95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005821"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>StorSimple Virtual Array に Update 1.0 をインストールする

## <a name="overview"></a>概要

この記事では、ローカル Web UI および Azure Portal を使用して、StorSimple Virtual Array に Update 1.0 をインストールする手順について説明します。

StorSimple Virtual Array を最新の状態に保つために、ソフトウェアの更新プログラムまたは修正プログラムを適用します。 更新プログラムを適用する前に、最初にホストで、次にデバイスで、ボリュームまたは共有をオフラインにすることをお勧めします。 これにより、データ破損の可能性を最小限に抑えられます。 ボリュームまたは共有をオフラインにしたら、デバイスの手動バックアップも作成する必要があります。

> [!IMPORTANT]
>
> - Update 1.0 は、デバイス上ではソフトウェア バージョン **10.0.10296.0** になります。 この更新プログラムの新機能については、[Update 1.0 のリリース ノート](storsimple-virtual-array-update-1-release-notes.md)に関するページをご覧ください。
>
> - 更新プログラムまたは修正プログラムをインストールすると、デバイスが再起動されることに注意してください。 StorSimple Virtual Array は単一ノード デバイスであることから、実行中のすべての IO が中断され、デバイスでダウンタイムが発生します。
>
> - 仮想アレイで Update 0.6 を実行している場合にのみ、Azure Portal で Update 1 を入手できます。 仮想アレイで Update 0.6 よりも前のバージョンを実行している場合は、最初に Update 0.6 をインストールしてから、Update 1 を適用してください。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Update 0.2 以降を実行している場合は、Azure Portal から更新プログラムをインストールすることをお勧めします。 ポータルの手順では、ユーザーは更新プログラムをスキャン、ダウンロード、およびインストールする必要があります。 仮想アレイで実行されるソフトウェアのバージョンによって、Azure Portal から適用するアップデートが異なります。

 - 仮想アレイで Update 0.6 を実行している場合は、Azure Portal からデバイスに Update 1 (10.0.10296.0) が直接インストールされます。 この手順の所要時間は約 7 分です。
 - 仮想アレイで Update 0.6 より前のバージョンを実行している場合は、2 段階で更新が行われます。 最初に、Azure Portal からデバイスに Update 0.6 (10.0.10293.0) がインストールされます。 仮想アレイが再起動されて、ポータルからデバイスに Update 1 (10.0.10296.0) がインストールされます。 この手順の所要時間は約 15 分です。


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

インストールが完了したら、StorSimple デバイス マネージャー サービスに移動します。 **[デバイス]** を選択してから、先ほど更新したデバイスをクリックします。 **[設定] > [管理] > [デバイスの更新プログラム]** の順に移動します。 表示されるソフトウェアのバージョンは **10.0.10296.0** です。

![更新後のソフトウェア バージョン](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>ローカル Web UI を使用する

ローカル Web UI を使用するときは、2 つの手順があります。

* 更新プログラムまたは修正プログラムをダウンロードする
* 更新プログラムまたは修正プログラムをインストールする

> [!IMPORTANT] 
> **Update 0.6 (10.0.10293.0) を実行している場合にのみ、この更新プログラムを続行します。前のバージョンを実行している場合は、最初に [Update 0.6 をインストール](storsimple-virtual-array-install-update-06.md)してから、Update 1 を適用してください**。

### <a name="download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードする

仮想アレイで Update 0.6 を実行している場合は、次の手順を実行して、Microsoft Update カタログから Update 1 をダウンロードします。

#### <a name="to-download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードするには

1. Internet Explorer を起動し、[https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) に移動します。

2. このコンピューターで Microsoft Update カタログを初めて使用する場合は、Microsoft Update カタログ アドオンのインストールを求められたら、 **[インストール]** をクリックします。

3. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号を入力します。 Update 1.0 については、「**4047203**」を入力して、 **[検索]** をクリックします。
   
    **StorSimple Virtual Array Update 1.0** など、修正プログラムの一覧が表示されます。
   
    ![カタログの検索](./media/storsimple-virtual-array-install-update-1/download1.png)

4. **[Download]** をクリックします。

5. 2 つのファイルをフォルダーにダウンロードします。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。

6. ファイルが置かれているフォルダーを開きます。

    ![パッケージ内のファイル](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    2 つのファイルが表示されます。
    -  Microsoft 更新プログラムのスタンドアロン パッケージ ファイル `WindowsTH-KB3011067-x64`。 このファイルは、デバイス ソフトウェアを更新するために使用されます。
    - 8 月の累積的な更新プログラムを含むファイル`windows8.1-kb4034681-x64`。 このプログラムのロールアップに含まれる更新プログラムの詳細については、[8 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810)に関するページをご覧ください。

### <a name="install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールする

更新プログラムまたは修正プログラムをインストールする前に、次のことを確認してください。

 - 更新プログラムまたは修正プログラムがローカルでホストにダウンロードされている、またはネットワーク共有を介してアクセスできる。
 - 仮想アレイが Update 0.6 (10.0.10293.0) を実行している。 Update 0.6 よりも前のバージョンを実行している場合は、最初に [Update 0.6 をインストール](storsimple-virtual-array-install-update-06.md)してから、Update 1 を適用してください。

この手順の所要時間は約 4 分です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

#### <a name="to-install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールするには

1. ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 実行しているソフトウェアのバージョンをメモしておきます。 **Update 0.6 (10.0.10293.0) を実行している場合にのみ、この更新プログラムを続行します。前のバージョンを実行している場合は、最初に [Update 0.6 をインストール](storsimple-virtual-array-install-update-06.md)してから、Update 1 を適用してください**。
   
    ![[メンテナンス] メニューから [ソフトウェア更新プログラム] が選択されていることを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. **[Update file path (更新プログラムのファイル パス)]** に、更新プログラムまたは修正プログラムのファイル名を入力します。 更新プログラムまたは修正プログラムのインストール ファイルがネットワーク共有にある場合は、ファイルを参照することもできます。 **[Apply]** をクリックします。
   
    ![[ソフトウェア更新プログラム] ページの現在のソフトウェア バージョンと [ファイル パスの更新] テキスト ボックスを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. 警告が表示されます。 仮想アレイは単一ノード デバイスであることから、更新プログラムが適用された後にデバイスが再起動され、ダウンタイムが発生します。 チェック マーク アイコンをクリックします。
   
   ![ダウンタイムを警告するダイアログ ボックスを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. 更新プログラムが開始します。 デバイスが正常に更新されると、再起動されます。 この期間は、ローカル UI にはアクセスできません。
   
    ![更新の成功メッセージを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. 再起動が完了したら、 **サインイン** ページが表示されます。 デバイス ソフトウェアが更新されたことを確認するには、ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 表示されるソフトウェアのバージョンは、Update 1.0 では **10.0.0.0.0.10296** です。
   
   > [!NOTE]
   > ローカル Web UI と Azure Portal では、ソフトウェアのバージョンの表示方法が少し異なります。 たとえば、同じバージョンの場合、ローカル Web UI では **10.0.0.0.0.10296** と表示され、Azure Portal では **10.0.10296.0** と表示されます。
   
    ![現在のソフトウェアのバージョンが表示された [ソフトウェア更新プログラム] ページを示すスクリーンショット。](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. ファイル `windows8.1-kb4012213-x64` を使用して、Windows セキュリティ修正プログラムをインストールする手順 2 から 4 を繰り返します。 インストール後に仮想アレイが再起動するため、ユーザーはローカル Web UI にサインインする必要があります。

> [!NOTE]
> Update 0.6 より前のバージョンを実行しているデバイスに直接 Update 1 を適用すると、一部の更新プログラムはインストールされません。 Microsoft サポートに対処法をお問い合わせください。

## <a name="next-steps"></a>次のステップ

[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。
