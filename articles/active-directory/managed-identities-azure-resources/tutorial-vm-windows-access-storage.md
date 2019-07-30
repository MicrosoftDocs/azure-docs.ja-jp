---
title: Windows VM システムに割り当てられたマネージド ID を使用して Azure Storage にアクセスする | Microsoft Docs
description: Windows VM のシステム割り当てマネージド ID を使用して Azure Storage にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d7200dd89d51817a5d146ff4d33e2501ed2826
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278008"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure Storage にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Azure Storage にアクセスする方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントに BLOB コンテナーを作成する
> * Windows VM のシステム割り当てマネージド ID にストレージ アカウントへのアクセスを許可する
> * アクセス権を取得し、それを使用して Azure Storage を呼び出す

> [!NOTE]
> Azure Storage の Azure Active Directory 認証は、パブリック プレビューの段階です。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

このセクションでは、ストレージ アカウントを作成します。

1. Azure Portal の左上隅にある **[+ リソースの作成]** ボタンをクリックします。
2. **[ストレージ]** 、 **[ストレージ アカウント - Blob、File、Table、Queue]** の順にクリックします。
3. **[名前]** で、ストレージ アカウントの名前を入力します。
4. **[デプロイ モデル]** と **[アカウントの種類]** がそれぞれ **[Resource manager]** と **[ストレージ (汎用 v1)]** に設定されている必要があります。
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>BLOB コンテナーを作成し、ファイルをストレージ アカウントにアップロードする

ファイルには Blob Storage が必要であるため、ファイルを格納する BLOB コンテナーを作成する必要があります。 次に、新しいストレージ アカウントで、BLOB コンテナーにファイルをアップロードします。

1. 新たに作成したストレージ アカウントに戻ります。
2. **[Blob service]** の **[コンテナー]** をクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックします。
4. **[新しいコンテナー]** で、コンテナーの名前を入力し、 **[パブリック アクセス レベル]** で既定値を保持します。

    ![ストレージ コンテナーの作成](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 任意のエディターを使用して、ローカル コンピューターに *hello world.txt* という名前のファイルを作成します。 ファイルを開き、"Hello world! :)" というテキストを (引用符なしで) 追加し、保存します。
6. 新しく作成したコンテナーにファイルをアップロードします。コンテナー名をクリックしてから **[アップロード]** をクリックします。
7. **[BLOB のアップロード]** ウィンドウの **[ファイル]** で、フォルダー アイコンをクリックし、ローカル コンピューターの **hello_world.txt** を参照してファイルを選択して、 **[アップロード]** をクリックします。
    ![テキスト ファイルをアップロードする](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>VM に Azure Storage コンテナーへのアクセスを許可する

VM のシステム割り当てマネージド ID を使用して、Azure Storage Blob のデータを取得できます。

1. 新たに作成したストレージ アカウントに戻ります。
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。
3. ページの上部にある **[+ ロール割り当ての追加]** をクリックして、VM 用に新しいロールの割り当てを追加します。
4. **[ロール]** で、ドロップダウンから **[ストレージ BLOB データ閲覧者]** を選択します。
5. 次のドロップダウンで、 **[アクセスの割り当て先]** として **[仮想マシン]** を選択します。
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、 **[リソース グループ]** を **[すべてのリソース グループ]** に設定します。
7. **[選択]** で、VM を選択し、 **[保存]** をクリックします。

    ![アクセス許可の割り当て](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>アクセス トークン取得し、それを使用して Azure Storage を呼び出す 

Azure Storage は Azure AD 認証をネイティブにサポートするため、マネージド ID を使用して取得したアクセス トークンを直接受け入れることができます。 これは Azure Storage の Azure AD との統合の一部であり、接続文字列に資格情報を提供することとは異なります。

次に示すのは、アクセス トークンを使用して Azure Storage への接続を開き、以前に作成したファイルの内容を読み取る .NET コードの例です。 このコードは、VM のマネージド ID のエンドポイントにアクセスできる VM 上で実行する必要があります。 アクセス トークン メソッドを使用するには、.NET Framework 4.6 以降が必要です。 適宜、`<URI to blob file>` の値を置き換えます。 この値は、以前に作成して Blob Storage にアップロードしたファイルに移動し、 **[概要]** ページの **[プロパティ]** で **URL** をコピーすることで取得できます。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");

            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);

            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }
    }
}
```

応答には、次のようなファイルの内容が含まれています。

`Hello world! :)`

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Windows VM のシステム割り当て ID を使用して Azure Storage にアクセスする方法を説明しました。  Azure Storage の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
