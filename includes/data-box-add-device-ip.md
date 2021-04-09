---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553227"
---
1. Data Box デバイスにサインインします。 ロックが解除されていることを確認してください。

    ![デバイスが [ロック解除] として表示されたダッシュボード示すスクリーンショット。](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. **[ネットワーク インターフェイスの設定]** に移動します。 クライアントへの接続に使用するネットワーク インターフェイスのデバイスの IP アドレスをメモします。

    ![I P アドレスを確認できる [ネットワーク設定] を示すスクリーンショット。](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. **[接続とコピー]** に移動し、 **[REST]** をクリックします。

    ![アクセス設定として [REST] を選択できる [接続とコピー] ペインを示すスクリーンショット。](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. **[ストレージ アカウントへのアクセスとデータのアップロード]** ダイアログから **[Blob service エンドポイント]** をコピーします。

    ![[Blob service エンドポイント] をコピーできる [ストレージ アカウントへのアクセスとデータのアップロード] ダイアログ ボックスを示すスクリーンショット。](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. **メモ帳** を管理者として起動し、`C:\Windows\System32\Drivers\etc` にある **hosts** ファイルを開きます。
6. **hosts** ファイルに `<device IP address> <Blob service endpoint>` というエントリを追加します。
7. 次の図を参考にしてください。 **hosts** ファイルを保存します。

    ![I P アドレスと Blob service エンドポイントが追加されたメモ帳ドキュメントを示すスクリーンショット。](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
