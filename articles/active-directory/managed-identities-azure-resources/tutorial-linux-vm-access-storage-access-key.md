---
title: チュートリアル`:` マネージド ID を使用してアクセス キーで Azure Storage にアクセスする - Linux - Azure AD
description: Linux VM のシステム割り当てマネージド ID を使用し、アクセス キー経由で Azure Storage にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/04/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99a199fcc5f43f3710fe2e2fcfe55b7e624987b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317465"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>チュートリアル: Linux VM のシステム割り当てマネージド ID を使用してアクセス キーで Azure Storage にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) のシステム割り当てマネージド ID を使用してストレージ アカウント アクセス キーを取得する方法について説明します。 ストレージ SDK の使用時など、ストレージ操作を実行するときに、ストレージ アクセス キーを通常どおりに使用できます。 このチュートリアルでは、Azure CLI を使用して BLOB をアップロードおよびダウンロードします。 学習内容:

> [!div class="checklist"]
> * Resource Manager で VM にストレージ アカウント アクセス キーへのアクセス権を付与する 
> * VM の ID を使用してアクセス トークンを取得し、それを使用して Resource Manager からストレージ アクセス キーを取得する  

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

まだお持ちでない場合は、この時点でストレージ アカウントを作成します。  この手順をスキップし、既存のストレージ アカウントのキーへのアクセス権を、VM のシステム割り当てマネージド ID に付与することもできます。 

1. Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2. **[ストレージ]** 、次に **[ストレージ アカウント]** をクリックすると、新しい [ストレージ アカウントの作成] パネルが表示されます。
3. 後で使用する、ストレージ アカウントの **名前** を入力します。  
4. **[デプロイ モデル]** と **[アカウントの種類]** が "Resource manager" と "General purpose" にそれぞれ設定されている必要があります。 
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>ストレージ アカウントに BLOB コンテナーを作成する

後で、新しいストレージ アカウントにファイルをアップロードおよびダウンロードします。 ファイルには Blob Storage が必要であるため、ファイルを格納する BLOB コンテナーを作成する必要があります。

1. 新たに作成したストレージ アカウントに戻ります。
2. 左側の **[コンテナー]** リンク ([Blob service] の下にある) をクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックすると、[新しいコンテナー] パネルがスライドして現れます。
4. コンテナーに名前を付け、アクセス レベルを選択して、 **[OK]** をクリックします。 指定した名前は、後ほどチュートリアルで使用されます。 

    ![ストレージ コンテナーの作成](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>VM のシステム割り当てマネージド ID にストレージ アカウント アクセス キーを使用するためのアクセス権を付与する

この手順では、ストレージ アカウントのキーへのアクセス権を VM のシステム割り当てマネージド ID に付与します。   

1. 新たに作成したストレージ アカウントに戻ります。
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。  
3. ページの上部にある **[+ ロール割り当ての追加]** をクリックして、VM 用に新しいロールの割り当てを追加します
4. ページの右側で、 **[ロール]** を "ストレージ アカウント キー オペレーターのサービス ロール" に設定します。 
5. 次のドロップダウンで、 **[アクセスの割り当て先]** を ［仮想マシン］ リソースに設定します。  
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、 **[リソース グループ]** を [すべてのリソース グループ] に設定します。  
7. 最後に、 **[選択]** のドロップダウンで Linux 仮想マシンを選択し、 **[保存]** をクリックします。 

    ![イメージ テキスト](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/install-win10) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、 **[概要]** ページの上部にある **[接続]** をクリックします。 VM に接続する文字列をコピーします。 
2. SSH クライアントを使用して VM に接続します。  
3. 次に、**Linux VM** の作成時に追加した **パスワード** の入力を求められます。 パスワードを入力すると、正常にサインインできます。  
4. CURL を使用して Azure Resource Manager のアクセス トークンを取得します。  

    アクセス トークンの CURL 要求と応答を次に示します。
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
    ```
    
    > [!NOTE]
    > 前述の要求では、"resource" パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    > 次の応答では、簡潔にするため access_token 要素が短縮されています。
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>ストレージ呼び出しを行うために Azure Resource Manager からストレージ アカウント アクセス キーを取得する  

ここで、CURL を使用して、前のセクションで取得したアクセス トークンで Resource Manager を呼び出し、ストレージ アクセス キーを取得します。 ストレージ アクセス キーを取得したら、ストレージのアップロード/ダウンロード操作を呼び出すことができます。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、および `<STORAGE ACCOUNT NAME>` の各パラメーターの値は、必ず実際の値に置き換えてください。 `<ACCESS TOKEN>` の値は、以前に取得したアクセス トークンに置き換えます。

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> 前述の URL のテキストでは大文字小文字が区別されるので、リソース グループに使用されている大文字小文字が正しく反映されていることを確認してください。 また、これは GET 要求ではなく POST 要求であることを認識し、-d (NULL を指定可能) を指定して長さの制限を取得するための値を渡すことが重要です。  

CURL 応答では、キーのリストが返されます。  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
BLOB ストレージ コンテナーにアップロードするサンプル BLOB ファイルを作成します。 Linux VM でこれを行うには、次のコマンドを使用します。 

```bash
echo "This is a test file." > test.txt
```

次に、ストレージ アクセス キーを使用して CLI `az storage` コマンドで認証を行い、ファイルを BLOB コンテナーにアップロードします。 この手順では、VM に[最新の Azure CLI をインストールする](/cli/azure/install-azure-cli)必要があります (まだインストールされていない場合)。
 

```azurecli
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

応答: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

さらに、Azure CLI を使用してファイルをダウンロードし、ストレージ アクセス キーを使用して認証することもできます。 

要求: 

```azurecli
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

応答: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Linux VM のシステム割り当てマネージド ID を使用してアクセス キーで Azure Storage にアクセスする方法について説明しました。  Azure Storage アクセス キーの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[ストレージ アクセス キーの管理](../../storage/common/storage-account-create.md)
