---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736140"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>クラウド アプライアンスのリモート管理を構成するには

1. StorSimple デバイス マネージャー サービスで **[デバイス]** をクリックします。 サービスに接続されているデバイスの一覧から、クラウド アプライアンスを選択してクリックします。
    ![StorSimple でのクラウド アプライアンスの選択](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. **[設定]、[セキュリティ]** の順に移動し、**[セキュリティの設定]** ブレードを開きます。

     ![StorSimple のセキュリティの設定](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. **[リモート管理]** セクションに移動します。 **[リモート管理]** ボックスをクリックします。
     ![StorSimple のリモート管理](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. **[リモート管理]** ブレードで、次の操作を行います。

    1. **[リモート管理の有効化]** が有効になっていることを確認します。
    2. 既定では HTTPS 経由で接続します。 HTTP を使用した接続を選択できるようになります。 HTTP 経由での接続は、信頼のおけるネットワークでのみ許容されます。 HTTP が有効になっていることを確認します。
    3. ブレードの上部にあるコマンド バーで **[...More]\(...詳細\)**、**[証明書のダウンロード]** の順にクリックし、リモート管理証明書をダウンロードします。 このファイルを保存する場所を指定できます。 この証明書は、さらに、クラウド アプライアンスへの接続に使用するクライアントまたはホスト コンピューターにインストールする必要があります。

        ![[リモート管理] ブレード](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. **[保存]** をクリックし、メッセージが表示されたら変更を確認します。