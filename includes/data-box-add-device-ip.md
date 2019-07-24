---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550356"
---
1. Data Box デバイスにサインインします。 ロックが解除されていることを確認してください。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. **[ネットワーク インターフェイスの設定]** に移動します。 クライアントへの接続に使用するネットワーク インターフェイスのデバイスの IP アドレスをメモします。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. **[接続とコピー]** に移動し、**[REST (プレビュー)]** をクリックします。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. **[ストレージ アカウントへのアクセスとデータのアップロード]** ダイアログから **[Blob service エンドポイント]** をコピーします。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. **メモ帳**を管理者として起動し、`C:\Windows\System32\Drivers\etc` にある **hosts** ファイルを開きます。
6. **hosts** ファイルに `<device IP address> <Blob service endpoint>` というエントリを追加します。
7. 次の図を参考にしてください。 **hosts** ファイルを保存します。

    ![Data Box ダッシュボード](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
