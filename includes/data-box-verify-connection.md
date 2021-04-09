---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553206"
---
次の手順に従ってストレージ アカウントに接続し、その接続を確認します。

1. Storage Explorer で、 **[Azure Storage へ接続]** ダイアログを開きます。 **[Azure Storage へ接続]** ダイアログで、 **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択します。

    ![[ストレージ アカウント名とキーを使用する] が選択された [Azure Storage へ接続] ダイアログ ボックスを示すスクリーンショット。](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. 自分の **アカウント名** と **アカウント キー** (ローカル Web UI の **[接続とコピー]** ページのキー 1 の値) を貼り付けます。 ストレージ エンドポイント ドメインに **[Other (enter below)]\(その他 (以下に入力)\)** を選択し、Blob service エンドポイントを指定します (下図)。 **[HTTP を使用する]** オプションは、*http* で転送する場合にのみオンにします。 *https* を使用する場合は、このオプションをオフのままにしてください。 **[次へ]** を選択します。

    ![値が入力された [名前とキーを使用して接続する] ダイアログ ボックスを示すスクリーンショット。](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. **[接続の概要]** ダイアログで、指定した情報を確認します。 **[接続]** を選択します。

    ![[接続] が選択された [接続の概要] ダイアログ ボックスを示すスクリーンショット。](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. 正常に追加されたアカウントが、名前の後に "External, Other (外部、その他)" を伴って Storage Explorer に表示されます。 **[BLOB コンテナー]** をクリックすると、コンテナーが表示されます。

    ![[BLOB コンテナー] が選択された Explorer メニューを示すスクリーンショット。](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
