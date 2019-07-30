---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181840"
---
1. Data Box デバイスにサインインします。 ロックが解除されていることを確認してください。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. **[ネットワーク インターフェイスの設定]** に移動します。 クライアントへの接続に使用するネットワーク インターフェイスのデバイスの IP アドレスをメモします。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. **[接続とコピー]** に移動し、 **[REST]** をクリックします。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. **[ストレージ アカウントへのアクセスとデータのアップロード]** ダイアログから **[Blob service エンドポイント]** をコピーします。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. **メモ帳**を管理者として起動し、`C:\Windows\System32\Drivers\etc` にある **hosts** ファイルを開きます。
6. **hosts** ファイルに `<device IP address> <Blob service endpoint>` というエントリを追加します。
7. 次の図を参考にしてください。 **hosts** ファイルを保存します。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
