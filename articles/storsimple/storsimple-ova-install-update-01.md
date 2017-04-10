---
title: "StorSimple Virtual Array への更新プログラムのインストール | Microsoft Docs"
description: "ポータルと修正プログラムを使用して更新プログラムを適用するために、StorSimple Virtual Array の Web UI をどのように使用するかについて説明します"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 7f1b1e7d-04d0-4ca2-9dbb-77077ff19bb9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bccb0d49c1959a690d513961c32d946763385a87


---
# <a name="install-updates-on-your-storsimple-virtual-array"></a>StorSimple Virtual Array に更新プログラムをインストールします
## <a name="overview"></a>Overview
この記事では、ローカル Web UI および Azure クラシック ポータルを使用して、StorSimple Virtual Array に更新プログラムをインストールする手順について説明します。 StorSimple Virtual Array を最新の状態に保つために、ソフトウェアの更新プログラムまたは修正プログラムを適用することが必要な場合があります。 

更新プログラムまたは修正プログラムをインストールすると、デバイスが再起動されることに注意してください。 StorSimple Virtual Array は単一ノード デバイスであることから、実行中のすべての IO が中断され、デバイスでダウンタイムが発生します。 

更新プログラムを適用する前に、最初にホストで、次にデバイスで、ボリュームまたは共有をオフラインにすることをお勧めします。 これにより、データ破損の可能性を最小限に抑えられます。

> [!IMPORTANT]
> Update 0.1 または GA ソフトウェア バージョンを実行している場合は、ローカル Web UI で修正プログラムを使用する方法で、Update 0.3 をインストールする必要があります。 Update 0.2 を実行している場合、更新プログラムのインストールには Azure クラシック ポータルを使用することをお勧めします。
> 
> 

## <a name="use-the-local-web-ui"></a>ローカル Web UI を使用する
ローカル Web UI を使用するときは、2 つの手順があります。

* 更新プログラムまたは修正プログラムをダウンロードする
* 更新プログラムまたは修正プログラムをインストールする

### <a name="download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードする
次の手順を実行して、Microsoft Update カタログからソフトウェア更新プログラムをダウンロードします。

#### <a name="to-download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードするには
1. Internet Explorer を起動し、 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)に移動します。
2. このコンピューターで Microsoft Update カタログを初めて使用する場合は、Microsoft Update カタログ アドオンのインストールを求められたら、 **[インストール]** をクリックします。
3. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号を入力します。 Update 0.3 については、「**3182061**」を入力して、**[検索]** をクリックします。
   
    **StorSimple Virtual Array Update 0.3**など、修正プログラムの一覧が表示されます。
   
    ![カタログの検索](./media/storsimple-ova-install-update-01/download1.png)
4. **[追加]**をクリックします。 更新プログラムがバスケットに追加されます。
5. **[バスケットの表示]**をクリックします。
6. **[Download]**をクリックします。 ダウンロード先となるローカルの場所を指定または **参照** します。 更新プログラムが指定した場所にダウンロードされて、更新プログラムと同じ名前のサブフォルダーに配置されます。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。
7. コピーしたフォルダーを開くと、Microsoft 更新プログラムのスタンドアロン パッケージ ファイル `WindowsTH-KB3011067-x64`があります。 このファイルを使用して、更新プログラムまたは修正プログラムをインストールします。

### <a name="install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールする
更新プログラムまたは修正プログラムをインストールする前に、更新プログラムまたは修正プログラムがローカルでホストにダウンロードされているか、ネットワーク共有を介してアクセス可能であることを確認してください。 

次の手順を実行して、更新プログラムを、GA または Update 0.1 ソフトウェア バージョンが実行されているデバイスにインストールします。 各更新手順の所要時間は 2 分未満です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

#### <a name="to-install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールするには
1. ローカル Web UI で、**[メンテナンス]** > **[ソフトウェア更新プログラム]** に移動します。
   
    ![デバイスの更新](./media/storsimple-ova-install-update-01/update1m.png)
2. **[Update file path (更新プログラムのファイル パス)]**に、更新プログラムまたは修正プログラムのファイル名を入力します。 更新プログラムまたは修正プログラムのインストール ファイルがネットワーク共有にある場合は、ファイルを参照することもできます。 **[適用]**をクリックします。
   
    ![デバイスの更新](./media/storsimple-ova-install-update-01/update2m.png)
3. 警告が表示されます。 これは単一ノード デバイスであることから、更新プログラムが適用された後にデバイスが再起動され、ダウンタイムが発生します。 チェック マーク アイコンをクリックします。
   
   ![デバイスの更新](./media/storsimple-ova-install-update-01/update3m.png)
4. 更新プログラムが開始します。 デバイスが正常に更新されると、再起動されます。 この期間は、ローカル UI にはアクセスできません。
   
    ![デバイスの更新](./media/storsimple-ova-install-update-01/update5m.png)
5. 再起動が完了したら、 **サインイン** ページが表示されます。 デバイス ソフトウェアが更新されたことを確認するには、ローカル Web UI で、**[メンテナンス]** > **[ソフトウェア更新プログラム]**に移動します。 表示されるソフトウェアのバージョンは、Update 0.3 では **10.0.0.0.0.10288.0** です。
   
   > [!NOTE]
   > ローカル Web UI と Azure クラシック ポータルでは、ソフトウェアのバージョンの表示方法が少し異なります。 たとえば、同じバージョンの場合、ローカル Web UI では **10.0.0.0.0.10288** と表示され、Azure クラシック ポータルでは **10.0.10288.0** と表示されます。 
   > 
   > 
   
    ![デバイスの更新](./media/storsimple-ova-install-update-01/update6m.png)

## <a name="use-the-azure-classic-portal"></a>Azure クラシック ポータルを使用する
Update 0.2 を実行している場合は、Azure クラシック ポータルから更新プログラムをインストールすることをお勧めします。 ポータルの手順では、ユーザーは更新プログラムをスキャン、ダウンロード、およびインストールする必要があります。 この手順の所要時間は約 7 分です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

[!INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

インストールが完了したら (ジョブの状態が 100% と示されます)、**[デバイス]、[メンテナンス]、[ソフトウェア更新プログラム]** の順に移動します。 表示されるソフトウェアのバージョンは 10.0.10288.0 です。

![デバイスの更新](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>次のステップ
[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。




<!--HONumber=Nov16_HO3-->


