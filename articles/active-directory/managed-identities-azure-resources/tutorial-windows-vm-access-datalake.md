---
title: チュートリアル`:` マネージド ID を使用して Azure Data Lake Store にアクセスする - Windows - Azure AD
description: Windows VM のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスする方法を説明するチュートリアルです。
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd28ee509dc76ea0b2aca9264c591a7176ae2661
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093816"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>チュートリアル: Windows VM のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスする方法

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Azure Data Lake Store にアクセスする方法について説明します。 管理対象サービス ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VM に Azure Data Lake Store へのアクセスを許可する
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure Data Lake Store にアクセスする

## <a name="prerequisites"></a>前提条件

- マネージド ID の知識。 Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウント。[無料アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
- 必要なリソース作成とロール管理の手順を実行するための、適切なスコープ (サブスクリプションまたはリソース グループ) の "所有者" アクセス許可。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- システム割り当てマネージド ID が有効になっている Windows 仮想マシンも必要です。
  - このチュートリアル用に仮想マシンを作成する必要がある場合は、[システム割り当てマネージド ID を有効にした仮想マシンの作成](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)に関する記事に従ってください。




## <a name="enable"></a>有効化

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>アクセス権の付与

この時点で、VM に Azure Data Lake Store のファイルとフォルダーへのアクセスを付与できます。  この手順では、既存の Data Lake Store を使用することも、新しいものを作成することもできます。  Azure Portal を使用して新しい Data Lake Store を作成するには、この [Azure Data Lake Store のクイック スタート](../../data-lake-store/data-lake-store-get-started-portal.md)の手順を実行します。 [Azure Data Lake Store のドキュメント](../../data-lake-store/data-lake-store-overview.md)に、Azure CLI と Azure PowerShell を使用するクイック スタートも用意されています。

Data Lake Store で新しいフォルダーを作成し、VM のシステム割り当て ID にそのフォルダー内のファイルに対する読み取り、書き込み、および実行を行うためのアクセス許可を付与します。

1. Azure Portal で、左側のナビゲーションの **[Data Lake Store]** をクリックします。
2. このチュートリアルで使用する Data Lake Store をクリックします。
3. コマンド バーの **[データ エクスプローラー]** をクリックします。
4. Data Lake Store のルート フォルダーが選択されます。  コマンド バーの **[アクセス]** をクリックします。
5. **[追加]** をクリックします。  **[選択]** フィールドにお使いの VM の名前 (例: **DevTestVM**) を入力します。  検索結果からお使いの VM をクリックして選択し、**[選択]** をクリックします。
6. **[アクセス許可の選択]** をクリックします。  **[読み取り]** と **[実行]** を選択して **[このフォルダー]** に追加し、 **[An access permission only]\(アクセス許可のみ\)** として追加します。  **[OK]** をクリックします。  アクセス許可が正常に追加されます。
7. **[アクセス]** ブレードを閉じます。
8. このチュートリアル用に新しいフォルダーを作成します。  コマンド バーの **[新しいフォルダー]** をクリックし、この新しいフォルダーに名前 (例: **TestFolder**) を付けます。  **[OK]** をクリックします。
9. 作成したフォルダーをクリックし、コマンド バーの **[アクセス]** をクリックします。
10. 手順 5 と同様に **[追加]** をクリックし、**[選択]** フィールドに VM の名前を入力したあと、この VM を選択して **[選択]** をクリックします。
11. 手順 6 と同様に **[アクセス許可の選択]** を選択し、**[読み取り]**、**[書き込み]** および **[実行]** を選択して **[このフォルダー]** に追加したあと、**[アクセス許可エントリと既定のアクセス許可エントリ]** として追加します。  **[OK]** をクリックします。  アクセス許可が正常に追加されます。

この時点で VM のシステム割り当てマネージド ID は、作成したフォルダーのファイルに対してすべての操作を実行できます。  Data Lake Store のアクセス管理の詳細については、[Data Lake Store のアクセスの制御](../../data-lake-store/data-lake-store-access-control.md)に関するページの記事をご覧ください。

## <a name="access-data"></a>データにアクセスする

Azure Data Lake Store は Azure AD 認証をネイティブにサポートするため、Azure リソースのマネージド ID を使用して取得されたアクセス トークンを直接受け入れることができます。  Data Lake Store のファイルシステムと認証を行うには、Azure AD によって発行されたアクセス トークンの Authorization ヘッダーを、"Bearer < ACCESS_TOKEN_VALUE >" の形式で Data Lake Store ファイルシステムのエンドポイントに送信します。  Azure AD 認証の Data Lake Store のサポートに関する詳細については、「[Data Lake Store での Azure Active Directory を使用した認証](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)」をご覧ください。

> [!NOTE]
> Data Lake Store ファイルシステムのクライアント SDK では、Azure リソースのマネージド ID はまだサポートされていません。  このチュートリアルは、SDK のサポートが追加されたときに更新されます。

このチュートリアルでは、PowerShell を使用して Data Lake Store ファイルシステムの REST API と認証し、REST 要求を行います。 VM のシステム割り当てマネージド ID を認証で使用するには、VM から要求を行う必要があります。

1. ポータルで **[仮想マシン]** を開いてお客様の Windows VM に移動し、**[概要]** の **[接続]** をクリックします。
2. Windows VM を作成したときに追加した **ユーザー名** と **パスワード** を入力します。 
3. これで、仮想マシンを使用する **リモート デスクトップ接続** が作成されました。リモート セッションで **PowerShell** を開きます。 
4. PowerShell の `Invoke-WebRequest` を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure Data Lake Store のアクセス トークンを取得するよう要求します。  Data Lake Store のリソース識別子は `https://datalake.azure.net/` です。  Data Lake はリソース識別子と正確に一致します。末尾のスラッシュが重要です。

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   応答を JSON オブジェクトから PowerShell オブジェクトに変換します。 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   応答からアクセス トークンを抽出します。
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. PowerShell の 'Invoke-WebRequest' を使用して、ルート フォルダー内のフォルダーを一覧表示するよう Data Lake Store の REST エンドポイントに要求します。  これは、すべてが正しく構成されていることを簡単に確認する方法です。  Authorization ヘッダーの文字列 "Bearer" に、大文字の "B" があることが重要です。  Azure Portal の [Data Lake Store] ブレードの **[概要]** セクションで、ご利用の Data Lake Store の名前を確認できます。

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   正常な応答は次のようになります。

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. これで、Data Lake Store へのファイルのアップロードを試すことができます。  まず、アップロードするファイルを作成します。

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. PowerShell の `Invoke-WebRequest` を使用して、先ほど作成したフォルダーにファイルをアップロードするよう Data Lake Store の REST エンドポイントに要求します。  この要求では 2 つの手順が実行されます。  最初の手順では、要求を行うとファイルをアップロードする場所にリダイレクトされます。  2 番目の手順では、実際にファイルをアップロードします。  このチュートリアルとは異なる値を使用する場合は、フォルダーとファイルの名前を正しく設定するのを忘れないでください。 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   `$HdfsRedirectResponse` の値を確認すると、次の応答のような値になります。

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   リダイレクト エンドポイントに要求を送信してアップロードを完了します。

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   正常な応答は次のようになります。

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

その他の Data Lake Store ファイルシステムの API を使用すると、ファイルへの追加、ファイルのダウンロードなどができます。


## <a name="disable"></a>Disable

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows 仮想マシンのシステム割り当てマネージド ID を使用して、Azure Data Lake Store にアクセスする方法について学びました。 Azure Data Lake Store の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)