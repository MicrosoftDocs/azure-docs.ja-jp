---
title: エンドユーザー認証 - .NET と Data Lake Storage Gen1 - Azure
description: Azure Active Directory を .NET SDK と共に使用した Data Lake Storage Gen1 でエンドユーザー認証を行う方法について説明します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89004784"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Azure Data Lake Storage Gen1 での .NET SDK を使用したエンドユーザー認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK の使用](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-end-user-authenticate-python.md)
> * [REST API の使用](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

この記事では、.NET SDK を使用して、Azure Data Lake Storage Gen1 に対するエンドユーザー認証を行う方法について説明します。 .NET SDK を使用した Data Lake Storage Gen1 に対するサービス間認証については、[.NET SDK を使用した Data Lake Storage Gen1 に対するサービス間認証](data-lake-store-service-to-service-authenticate-net-sdk.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
* **Visual Studio 2013 以降**。 以下の手順では、Visual Studio 2019 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "ネイティブ" アプリケーションを作成します**。 「[End-user authentication with Data Lake Storage Gen1 using Azure Active Directory (Azure Active Directory を使用した Data Lake Storage Gen1 でのエンドユーザー認証)](data-lake-store-end-user-authenticate-using-active-directory.md)」のステップを完了している必要があります。

## <a name="create-a-net-application"></a>.NET アプリケーションの作成
1. Visual Studio で、 **[ファイル]** メニュー、 **[新規作成]** 、 **[プロジェクト]** の順に選択します。
2. **[コンソール アプリ (.NET Framework)]** を選択し、 **[次へ]** を選択します。
3. **[プロジェクト名]** に `CreateADLApplication` と入力して、 **[作成]** を選択します。

4. NuGet パッケージをプロジェクトに追加します。

   1. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[NuGet パッケージの管理]** をクリックします。
   2. **[NuGet パッケージ マネージャー]** タブで、 **[パッケージ ソース]** が **nuget.org** に設定されており、 **[プレリリースを含める]** チェック ボックスがオンになっていることを確認します。
   3. 以下の NuGet パッケージを検索してインストールします。

      * `Microsoft.Azure.Management.DataLake.Store` - このチュートリアルでは、v2.1.3-preview を使用します。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - このチュートリアルでは、v2.2.12 を使用します。

        ![NuGet ソースを追加する](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "新しい Azure Data Lake アカウントの作成")
   4. **NuGet パッケージ マネージャー** を閉じます。

5. **Program.cs** を開きます。
6. using ステートメントを次の行に置き換えます。

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>エンドユーザー認証
このスニペットを .NET クライアント アプリケーションに追加します。 プレースホルダーの値を、Azure AD のネイティブ アプリケーション (前提条件として一覧表示) から取得した値で置き換えます。 このスニペットを使用すると、Data Lake Storage Gen1 でアプリケーションを **対話的** に認証できます。つまり、Azure 資格情報を入力するように求められます。

使いやすくするために、次のスニペットでは、クライアント ID とリダイレクト URI について、すべての Azure サブスクリプションで有効な既定値を使用しています。 次のスニペットでは、入力する必要があるのはテナント ID の値のみです。 テナント ID は、「[テナント ID を取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」で説明されている指示に従って取得できます。
    
- Main() 関数を次のコードに置き換えます。

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

上記のスニペットに関して、以下のいくつかの点に留意してください。

* 上記のスニペットでは、ヘルパー関数 `GetTokenCache` と `GetCreds_User_Popup` を使用しています。 これらのヘルパー関数のコードは[こちらの GitHub で](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache)入手できます。
* できるだけ短時間でチュートリアルを終了できるよう、このスニペットでは、すべての Azure サブスクリプションで既定で使用できるネイティブ アプリケーション クライアント ID を使用しています。 そのため、**このスニペットを実際のアプリケーションで使用するときは、現状のままで使用** してください。
* ただし、独自の Azure AD ドメインとアプリケーション クライアント ID を使う必要がある場合は、Azure AD ネイティブ アプリケーションを作成したうえで、作成したアプリケーションの Azure AD テナント ID、クライアント ID、およびリダイレクト URI を使用する必要があります。 手順については、[Data Lake Storage Gen1 でのエンド ユーザー認証のための Active Directory アプリケーションの作成](data-lake-store-end-user-authenticate-using-active-directory.md)に関するページを参照してください。

  
## <a name="next-steps"></a>次のステップ
この記事では、Azure Data Lake Storage Gen1 に対し、.NET SDK からエンドユーザー認証を使って認証を行う方法について説明しました。 これで、.NET SDK を使用して Azure Data Lake Storage Gen1 を使用する方法について説明した次の記事に進めるようになりました。

* [.NET SDK を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-net-sdk.md)
* [.NET SDK を使用した Data Lake Storage Gen1 に対するデータ操作](data-lake-store-data-operations-net-sdk.md)

