---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c3cfab78ab4bcc92b3e65b5e4fa04a29bf688de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376071"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Azure ポータルから 更新プログラムをインストールするには

1. StorSimple サービス ページでデバイスを選択します。

    ![デバイスの削除](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. **[デバイスの設定]**  >  **[デバイスの更新プログラム]** の順に移動します。

    ![[デバイスの更新プログラム] をクリック](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. 新しい更新プログラムが利用できる場合は、通知が表示されます。 または、 **[デバイスの更新プログラム]** ブレードで **[更新プログラムのスキャン]** をクリックします。 利用可能な更新プログラムをスキャンするジョブが作成されます。 ジョブが正常に完了すると、その旨が通知されます。

    ![[デバイスの更新プログラム] をクリック 2](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. 更新プログラムをデバイスに適用する前に、リリース ノートを確認することをお勧めします。 **[更新プログラムのインストール]** をクリックすると、更新プログラムが適用されます。 **[定期更新プログラムの確認]** ブレードで、更新プログラムを適用する前に完了する必要のある前提条件を確認します。 デバイスを更新する準備ができたことを示すチェック ボックスをオンにし、 **[インストール]** をクリックします。

    ![[デバイスの更新プログラム] をクリック 3](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. 一連の前提条件のチェックが開始されます。 これらのチェックは次のとおりです。
   
   * **コントローラーの正常性チェック** では、両方のデバイス コントローラーが正常であり、オンラインであることを確認します。
   * **ハードウェア コンポーネントの正常性チェック** では、StorSimple デバイスのすべてのハードウェア コンポーネントが正常であることを確認します。
   * **DATA 0 チェック** では、デバイスで DATA 0 が有効であることを確認します。 このインターフェイスが有効でない場合は、有効にしてから再試行する必要があります。

     すべてのチェックが正常に完了した場合にのみ、更新プログラムがダウンロードされてインストールされます。 チェックが実行中のときは通知されます。 事前チェックに失敗した場合、失敗の理由が表示されます。 それらの問題を解決してから操作をやり直してください。 これらの問題に自分で対処できない場合、Microsoft サポートに連絡することが必要になる場合があります。

7. 事前チェックが正常に完了したら、更新ジョブが作成されます。 更新ジョブが正常に作成されると、通知されます。
   
    ![Update job creation](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    その後、更新プログラムがデバイスに適用されます。

9. 更新の完了には数時間かかります。 更新ジョブを選択し、 **[詳細]** をクリックすると、ジョブの詳細をいつでも表示できます。

    ![更新ジョブの作成 2](./media/storsimple-8000-install-update4-via-portal/update8.png)

     **[デバイスの設定]、[ジョブ]** の順に移動して、更新ジョブの進行状況を監視することもできます。 **[ジョブ]** ブレードで、更新の進行状況を確認できます。

     ![更新ジョブの作成 3](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. ジョブが完了したら、 **[デバイスの設定]、[デバイスの更新プログラム]** の順に移動します。 ソフトウェアのバージョンが更新されています。

   ![更新ジョブの作成 4](./media/storsimple-8000-install-update4-via-portal/update9.png)

