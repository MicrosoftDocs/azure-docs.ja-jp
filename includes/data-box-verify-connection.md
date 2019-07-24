---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550420"
---
次の手順に従ってストレージ アカウントに接続し、その接続を確認します。

1. Storage Explorer で、**[Azure Storage へ接続]** ダイアログを開きます。 **[Azure Storage へ接続]** ダイアログで、**[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択します。

    ![Data Box ダッシュボード](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. 自分の**アカウント名**と**アカウント キー** (ローカル Web UI の **[接続とコピー]** ページのキー 1 の値) を貼り付けます。 ストレージ エンドポイント ドメインに **[Other (enter below)]\(その他 (以下に入力)\)** を選択し、Blob service エンドポイントを指定します (下図)。 **[HTTP を使用する]** オプションは、*http* で転送する場合にのみオンにします。 *https* を使用する場合は、このオプションをオフのままにしてください。 **[次へ]** を選択します。

    ![Data Box ダッシュボード](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. **[接続の概要]** ダイアログで、指定した情報を確認します。 **[接続]** を選択します。

    ![Data Box ダッシュボード](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. 正常に追加されたアカウントが、名前の後に "External, Other (外部、その他)" を伴って Storage Explorer に表示されます。 **[BLOB コンテナー]** をクリックすると、コンテナーが表示されます。

    ![Data Box ダッシュボード](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
