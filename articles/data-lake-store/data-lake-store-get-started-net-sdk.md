---
title: .NET を使用した Azure Data Lake Storage Gen1 アカウントの管理
description: .NET SDK を使用して Azure Data Lake Storage Gen1 に対するアカウント管理操作を行う方法について説明します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c4ed092604e9511023a7025a69dc7a78652ea56
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430481"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>.NET SDK を使用した Azure Data Lake Storage Gen1 に対するアカウント管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

この記事では、.NET SDK を使用して Azure Data Lake Storage Gen1 に対するアカウント管理操作を実行する方法について説明します。 アカウント管理操作には、Data Lake Storage Gen1 アカウントの作成、Azure サブスクリプション内のアカウントの一覧表示、アカウントの削除などがあります。

.NET SDK を使用して Data Lake Storage Gen1 に対するデータ管理操作を実行する方法については、[.NET SDK を使用した Data Lake Storage Gen1 に対するファイルシステム操作](data-lake-store-data-operations-net-sdk.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
* **Visual Studio 2013 以降**。 以下の手順では、Visual Studio 2019 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

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

        ![NuGet ソースを追加する](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "新しい Azure Data Lake Store アカウントを作成する")
   4. **NuGet パッケージ マネージャー** を閉じます。
5. **Program.cs** を開き、既存のコードを削除し、次のステートメントに置き換えて、名前空間の参照を追加します。

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

6. 変数を宣言し、プレースホルダーの値を指定します。 さらに、指定したローカル パスとファイル名がコンピューターに存在していることを確認します。

    ```csharp
    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
                
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _subId;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                _location = "East US 2";
                _subId = "<SUBSCRIPTION-ID>";                    
            }
        }
    }
    ```

記事の以降のセクションでは、認証、ファイルのアップロードなどの操作を実行する .NET メソッドの使用方法について説明します。

## <a name="authentication"></a>認証

* アプリケーションのエンドユーザー認証については、[Data Lake Storage Gen1 での .NET SDK を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-net-sdk.md)に関するページを参照してください。
* アプリケーションのサービス間認証については、[.NET SDK を使用した Data Lake Storage Gen1 に対するサービス間認証](data-lake-store-service-to-service-authenticate-net-sdk.md)に関するページを参照してください。

## <a name="create-client-object"></a>クライアント オブジェクトを作成する
次のスニペットを使用して、アカウントの作成やアカウントの削除など、アカウント管理要求をサービスに発行するために使用される Data Lake Storage Gen1 アカウント クライアント オブジェクトを作成します。

```csharp
// Create client objects and set the subscription ID
_adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
```
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントを作成する
次のスニペットを使用して、Data Lake Storage Gen1 アカウント クライアント オブジェクトの作成中に指定した Azure サブスクリプションに Data Lake Storage Gen1 アカウントを作成します。

```csharp
// Create Data Lake Storage Gen1 account
var adlsParameters = new DataLakeStoreAccount(location: _location);
_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
```

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>サブスクリプション内のすべての Data Lake Storage Gen1 アカウントを一覧表示する
次のメソッドをクラス定義に追加します。 次のスニペットは、特定の Azure サブスクリプション内のすべての Data Lake Storage Gen1 アカウントを一覧表示します。

```csharp
// List all Data Lake Storage Gen1 accounts within the subscription
public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
{
    var response = _adlsClient.Account.List(_adlsAccountName);
    var accounts = new List<DataLakeStoreAccountBasic>(response);

    while (response.NextPageLink != null)
    {
        response = _adlsClient.Account.ListNext(response.NextPageLink);
        accounts.AddRange(response);
    }

    return accounts;
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントの削除
次のスニペットを使用して、以前に作成した Data Lake Storage Gen1 アカウントを削除します。

```csharp
// Delete Data Lake Storage Gen1 account
_adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
```

## <a name="see-also"></a>関連項目
* [.NET SDK を使用した Azure Data Lake Storage Gen1 に対するファイルシステム操作](data-lake-store-data-operations-net-sdk.md)。
* [Data Lake Storage Gen1 .NET SDK リファレンス](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>次のステップ
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)