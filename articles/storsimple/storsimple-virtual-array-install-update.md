---
title: Microsoft Azure StorSimple Virtual Array に更新プログラムをインストールする | Microsoft Docs
description: ポータルと修正プログラムを使用して更新プログラムを適用するために、StorSimple Virtual Array の Web UI をどのように使用するかについて説明します
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3bd6f298ad2bb01503492b52c2d50dec82ec0ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534049"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>StorSimple Virtual Array に更新プログラムをインストールする - Azure Portal

## <a name="overview"></a>概要

この記事では、ローカル Web UI および Azure Portal を使用して、StorSimple Virtual Array に更新プログラムをインストールする手順について説明します。 StorSimple Virtual Array を最新の状態に保つために、ソフトウェアの更新プログラムまたは修正プログラムを適用することが必要な場合があります。 

更新プログラムまたは修正プログラムをインストールすると、デバイスが再起動されることに注意してください。 StorSimple Virtual Array は単一ノード デバイスであることから、実行中のすべての IO が中断され、デバイスでダウンタイムが発生します。 

更新プログラムを適用する前に、最初にホストで、次にデバイスで、ボリュームまたは共有をオフラインにすることをお勧めします。 これにより、データ破損の可能性を最小限に抑えられます。

> [!IMPORTANT]
> Update 0.1 または GA ソフトウェア バージョンを実行している場合は、ローカル Web UI で修正プログラムを使用する方法で、Update 0.3 をインストールする必要があります。 Update 0.2 を実行している場合、更新プログラムのインストールには Azure クラシック ポータルを使用することをお勧めします。
 

## <a name="use-the-local-web-ui"></a>ローカル Web UI を使用する

ローカル Web UI を使用するときは、2 つの手順があります。

* 更新プログラムまたは修正プログラムをダウンロードする
* 更新プログラムまたは修正プログラムをインストールする

### <a name="download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードする

次の手順を実行して、Microsoft Update カタログからソフトウェア更新プログラムをダウンロードします。

#### <a name="to-download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードするには

1. Internet Explorer を起動し、[https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) に移動します。

2. このコンピューターで Microsoft Update カタログを初めて使用する場合は、Microsoft Update カタログ アドオンのインストールを求められたら、 **[インストール]** をクリックします。

3. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号を入力します。 Update 0.3 については、「**3182061**」を入力して、 **[検索]** をクリックします。
   
    **StorSimple Virtual Array Update 0.3** など、修正プログラムの一覧が表示されます。
   
    ![カタログの検索](./media/storsimple-virtual-array-install-update/download1.png)

4. **[追加]** をクリックします。 更新プログラムがバスケットに追加されます。

5. **[バスケットの表示]** をクリックします。

6. **[Download]** をクリックします。 ダウンロード先となるローカルの場所を指定または **参照** します。 更新プログラムが指定した場所にダウンロードされて、更新プログラムと同じ名前のサブフォルダーに配置されます。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。

7. コピーしたフォルダーを開くと、Microsoft 更新プログラムのスタンドアロン パッケージ ファイル `WindowsTH-KB3011067-x64`があります。 このファイルを使用して、更新プログラムまたは修正プログラムをインストールします。

### <a name="install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールする

更新プログラムまたは修正プログラムをインストールする前に、更新プログラムまたは修正プログラムがローカルでホストにダウンロードされているか、ネットワーク共有を介してアクセス可能であることを確認してください。 

次の手順を実行して、更新プログラムを、GA または Update 0.1 ソフトウェア バージョンが実行されているデバイスにインストールします。 各更新手順の所要時間は 2 分未満です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

#### <a name="to-install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールするには

1. ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。
   
    ![[メンテナンス] メニューから [ソフトウェア更新プログラム] が選択されていることを示すスクリーンショット。](./media/storsimple-virtual-array-install-update/update1m.png)

2. **[Update file path (更新プログラムのファイル パス)]** に、更新プログラムまたは修正プログラムのファイル名を入力します。 更新プログラムまたは修正プログラムのインストール ファイルがネットワーク共有にある場合は、ファイルを参照することもできます。 **[Apply]** をクリックします。
   
    ![[ソフトウェア更新プログラム] ページの [ファイル パスの更新] テキスト ボックスを示すスクリーンショット。](./media/storsimple-virtual-array-install-update/update2m.png)

3. 警告が表示されます。 これは単一ノード デバイスであることから、更新プログラムが適用された後にデバイスが再起動され、ダウンタイムが発生します。 チェック マーク アイコンをクリックします。
   
   ![ダウンタイムを警告するダイアログ ボックスを示すスクリーンショット。](./media/storsimple-virtual-array-install-update/update3m.png)

4. 更新プログラムが開始します。 デバイスが正常に更新されると、再起動されます。 この期間は、ローカル UI にはアクセスできません。
   
    ![更新の成功メッセージを示すスクリーンショット。](./media/storsimple-virtual-array-install-update/update5m.png)

5. 再起動が完了したら、 **サインイン** ページが表示されます。 デバイス ソフトウェアが更新されたことを確認するには、ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 表示されるソフトウェアのバージョンは、Update 0.3 では **10.0.0.0.0.10288.0** です。
   
   > [!NOTE]
   > ローカル Web UI と Azure Portal では、ソフトウェアのバージョンの表示方法が少し異なります。 たとえば、同じバージョンの場合、ローカル Web UI では **10.0.0.0.0.10288** と表示され、Azure Portal では **10.0.10288.0** と表示されます。
   
    ![現在のソフトウェアのバージョンが表示された [ソフトウェア更新プログラム] ページを示すスクリーンショット。](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Update 0.2 を実行している場合は、Azure Portal から更新プログラムをインストールすることをお勧めします。 ポータルの手順では、ユーザーは更新プログラムをスキャン、ダウンロード、およびインストールする必要があります。 この手順の所要時間は約 7 分です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

インストールが完了したら (ジョブの状態が 100% と示されます)、StorSimple デバイス マネージャー サービスに移動します。 **[デバイス]** を選択し、このサービスに接続されているデバイスの一覧から、更新するデバイスを選択してクリックします。 **[設定]** ブレードで、 **[管理]** セクションに移動し、 **[デバイスの更新プログラム]** を選択します。 表示されるソフトウェアのバージョンは **10.0.10288.0** です。


## <a name="next-steps"></a>次のステップ

[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。

