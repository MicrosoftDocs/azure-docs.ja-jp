---
title: チュートリアル`:` マネージド ID を使用して Azure Data Lake Store にアクセスする - Linux - Azure AD
description: Linux VM のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスする方法を説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d465419dfe36fd5dd67abdef22a6f54fba69a98e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89267464"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>チュートリアル: Linux VM のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスする方法について説明します。 学習内容は次のとおりです。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * VM に Azure Data Lake Store へのアクセスを許可する
> * VM のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスするためのアクセス トークンを取得する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>アクセス権の付与

このセクションでは、Azure Data Lake Store 内のファイルとフォルダーへのアクセス権を VM に付与する方法を示します。 この手順では、既存の Data Lake Store インスタンスを使用することも、新しいものを作成することもできます。 Azure Portal を使用して Data Lake Store インスタンスを作成するには、[Azure Data Lake Store のクイック スタート](../../data-lake-store/data-lake-store-get-started-portal.md)の手順を実行します。 [Azure Data Lake Store のドキュメント](../../data-lake-store/data-lake-store-overview.md)に、Azure CLI と Azure PowerShell を使用するクイック スタートも用意されています。

Data Lake Store で新しいフォルダーを作成し、VM のシステム割り当てマネージド ID にそのフォルダー内のファイルに対して読み取り、書き込み、および実行を行うためのアクセス許可を付与します。

1. Azure Portal の左側のウィンドウで **[Data Lake Store]** を選択します。
2. 使用する Data Lake Store インスタンスを選択します。
3. コマンド バーの **[データ エクスプローラー]** を選択します。
4. Data Lake Store インスタンスのルート フォルダーが選択されます。 コマンド バーの **[アクセス]** を選択します。
5. **[追加]** を選択します。  **[選択]** ボックスにお使いの VM の名前 (例: **DevTestVM**) を入力します。 検索結果からお使いの VM を選択し、 **[選択]** をクリックします。
6. **[アクセス許可の選択]** をクリックします。  **[読み取り]** と **[実行]** を選択して **[このフォルダー]** に追加し、 **[An access permission only]\(アクセス許可のみ\)** として追加します。 **[OK]** を選択します。  アクセス許可が正常に追加されます。
7. **[アクセス]** ウィンドウを閉じます。
8. このチュートリアル用に新しいフォルダーを作成します。 コマンド バーの **[新しいフォルダー]** を選択し、この新しいフォルダーに名前 (例: **TestFolder**) を付けます。  **[OK]** を選択します。
9. 作成したフォルダーを選択し、コマンド バーの **[アクセス]** を選択します。
10. 手順 5. と同様、 **[追加]** を選択します。 **[選択]** ボックスにお使いの VM の名前を入力します。 検索結果からお使いの VM を選択し、 **[選択]** をクリックします。
11. 手順 6. と同様に、 **[アクセス許可の選択]** を選択します。 **[読み取り]** 、 **[書き込み]** および **[実行]** を選択して **[このフォルダー]** に追加したあと、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加します。 **[OK]** を選択します。  アクセス許可が正常に追加されます。

この時点で Azure リソースのマネージド ID は、作成したフォルダーのファイルに対してすべての操作を実行できます。 Data Lake Store のアクセス管理の詳細については、[Data Lake Store のアクセスの制御](../../data-lake-store/data-lake-store-access-control.md)に関するページをご覧ください。

## <a name="get-an-access-token"></a>アクセス トークンを取得する 

このセクションでは、アクセス トークンを取得し、Data Lake Store ファイル システムを呼び出す方法を示します。 Azure Data Lake Store は Azure AD 認証をネイティブにサポートするため、Azure リソースのマネージド ID を使用して取得されたアクセス トークンを直接受け入れることができます。 Data Lake Store のファイルシステムに対する認証を行うために、お使いの Data Lake Store ファイルシステムのエンドポイントに Azure AD によって発行されたアクセス トークンを送信します。 アクセス トークンは、Authorization ヘッダーに "Bearer \<ACCESS_TOKEN_VALUE\>" という形式で指定します。  Azure AD 認証の Data Lake Store のサポートに関する詳細については、「[Data Lake Store での Azure Active Directory を使用した認証](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)」をご覧ください。

このチュートリアルでは、cURL から REST 要求を実行することにより、Data Lake Store ファイルシステムの REST API に対する認証を行います。

> [!NOTE]
> Data Lake Store ファイル システムのクライアント SDK では、Azure リソースのマネージド ID はまだサポートされていません。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. ポータルで、お使いの Linux VM に移動します。 **[概要]** の **[接続]** を選択します。  
2. 任意の SSH クライアントを使用して、VM に接続します。 
3. ターミナル ウィンドウで、cURL を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Data Lake Store ファイル システムのアクセス トークンを取得するよう要求します。 Data Lake Store のリソース識別子は `https://datalake.azure.net/` です。  リソース識別子の末尾にスラッシュが含まれることが重要です。
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   成功応答では、次のように Data Lake Store への認証に使用するアクセス トークンが返されます。

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. cURL を使用してルート フォルダー内のフォルダーを一覧表示するには、お使いの Data Lake Store ファイルシステムの REST エンドポイントに要求を行います。 これは、すべてが正しく構成されていることを簡単に確認する方法です。 前の手順で入手したアクセス トークンの値をコピーします。 Authorization ヘッダーの文字列 "Bearer" に、大文字の "B" があることが重要です。 Azure Portal の [Data Lake Store] ウィンドウの **[概要]** セクションで、ご利用の **Data Lake Store** インスタンスの名前を確認できます。

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   成功応答は次のようになります。

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. これで、Data Lake Store インスタンスへのファイルのアップロードを試すことができます。 まず、アップロードするファイルを作成します。

   ```bash
   echo "Test file." > Test1.txt
   ```

6. cURL を使用して先ほど作成したフォルダーにファイルをアップロードするには、Data Lake Store ファイルシステムの REST エンドポイントに要求を行います。 アップロードではリダイレクトが必要ですが、cURL が自動的にリダイレクトします。 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    成功応答は次のようになります。

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Data Lake Store ファイルシステムには、他にもさまざまな API があり、それらを使用して、ファイルへの追加、ファイルのダウンロードなどを実行できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Linux VM のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスする方法について説明しました。 Azure Data Lake Store の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)