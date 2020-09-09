---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 3474a97f4b05fd1f944e18abebcef927232453e4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89080572"
---
Storage Explorer の初回使用時は、次の手順を実行する必要があります。

1. 一番上のコマンド バーから **[編集] > [Azure Stack API を対象にする]** に移動します。

    ![Storage Explorer を構成する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Storage Explorer を再起動して、変更を反映させます。


次の手順に従ってストレージ アカウントに接続し、その接続を確認します。

1. Storage Explorer でストレージ アカウントを選択します。 右クリックして **[Azure Storage へ接続する]** オプションを選択します。 

    ![Storage Explorer を構成する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. **[Azure Storage へ接続]** ダイアログで、**[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択します。

    ![Storage Explorer を構成する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. **[名前とキーを使用して接続する]** ダイアログで、次の手順のようにします。

    1. Edge ストレージ アカウントの表示名を入力します。 
    2. Edge ストレージ アカウントの名前を指定します。
    3. Azure Resource Manager を使用して、デバイスのローカル API から取得したアクセス キーを貼り付けます。
    4. ストレージ ドメインとして **[その他 (下に入力してください)]** を選択し、`<appliance name>.<DNSdomain>` の形式で Blob service エンドポイントのサフィックスを指定します。 
    5. 転送は *http* 経由なので、 **[HTTP を使用する]** オプションをオンにします。 
    6. **[次へ]** を選択します。

    ![Storage Explorer を構成する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. **[接続の概要]** ダイアログで、指定した情報を確認します。 **[接続]** を選択します。

    ![Storage Explorer を構成する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. 正常に追加されたアカウントが、名前の後に "External, Other (外部、その他)" を伴って Storage Explorer に表示されます。 **[BLOB コンテナー]** を選択すると、コンテナーが表示されます。

    ![BLOB コンテナーを表示する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

次の検証ステップでは、データ転送がこの接続を介して実際に正常に機能していることを確認します。

次の手順のようにして、デバイス上の Edge ストレージ アカウントにデータを読み込むと、マップされた Azure ストレージ アカウントに自動的に階層化されます。

1. Edge ストレージ アカウントにデータを読み込むコンテナーを選択します。 **[アップロード]** を選択し、 **[ファイルのアップロード]** を選択します。

    ![データ転送を検証する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. **[ファイルのアップロード]** ダイアログで、アップロードするファイルに移動して選択します。 **[次へ]** を選択します。

    ![データ転送を検証する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. ファイルがアップロードされたことを確認します。 アップロードされたファイルがコンテナーに表示されます。

    ![データ転送を検証する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. 次に、この Edge ストレージ アカウントにマップされた Azure ストレージ アカウントに接続します。 Edge ストレージ アカウントにアップロードされたデータは、Azure ストレージ アカウントに自動的に階層化されます。 
    
    Azure ストレージ アカウントの接続文字列を取得するには、 **[Azure ストレージ アカウント] > [アクセス キー]** に移動し、接続文字列をコピーします。

    ![データ転送を検証する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    接続文字列を使用して、Azure ストレージ アカウントに接続します。  

    ![データ転送を検証する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. **[接続の概要]** ダイアログで、指定した情報を確認します。 **[接続]** を選択します。

    ![データ転送を検証する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Edge ストレージ アカウントにアップロードしたファイルが Azure ストレージ アカウントに転送されたことがわかります。

    ![データ転送を検証する](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)