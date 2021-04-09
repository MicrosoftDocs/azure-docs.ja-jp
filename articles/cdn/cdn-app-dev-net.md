---
title: .NET 向け Azure CDN ライブラリの概要 | Microsoft Docs
description: Visual Studio を使用して Azure CDN を管理するための .NET アプリケーションを記述する方法について説明します。
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: a812704c42a4da5ddf89fe6c5ba0c9a684047f75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88919490"
---
# <a name="get-started-with-the-azure-cdn-library-for-net"></a>.NET 向け Azure CDN ライブラリの概要
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

[.NET 向け Azure CDN ライブラリ](/dotnet/api/overview/azure/cdn) を使用すると、CDN プロファイルとエンドポイントの作成と管理を自動化できます。  このチュートリアルでは、単純な .NET コンソール アプリケーションを作成しながら、使用可能な操作のいくつかを紹介します。  このチュートリアルは、.NET 向け Azure CDN ライブラリのすべての側面を詳細に説明することを目的としていません。

このチュートリアルを完了するには、Visual Studio 2015 が必要です。  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) は、自由にダウンロードできます。

> [!TIP]
> [このチュートリアルに沿って作成されたプロジェクト](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) は MSDN からダウンロードできます。
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>プロジェクトを作成し、NuGet パッケージを追加する
これまでの手順で、CDN プロファイルのリソース グループを作成し、そのグループ内の CDN プロファイルとエンドポイントを管理するための Azure AD アプリケーション アクセス許可を割り当てました。この段階で、アプリケーションの作成を開始できます。

Visual Studio 2015 で、 **[ファイル]** 、 **[新規]** 、 **[プロジェクト]** の順にクリックして、新しいプロジェクト ダイアログを開きます。  左側のウィンドウで **[Visual C#]** を展開し、 **[Windows]** を選択します。  中央のウィンドウで **[コンソール アプリケーション]** をクリックします。  プロジェクトに名前を付け、 **[OK]** をクリックします。

![[新しいプロジェクト]](./media/cdn-app-dev-net/cdn-new-project.png)

このプロジェクトでは、NuGet パッケージに含まれているいくつかの Azure ライブラリを使用します。  それでは、ライブラリをプロジェクトに追加していきましょう。

1. **[ツール]** メニュー、 **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** を順にクリックします。

    ![Manage Nuget Packages](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. パッケージ マネージャー コンソールで、次のコマンドを実行して、 **Active Directory Authentication Library (ADAL)** をインストールします。

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. 次のコマンドを実行して、 **Azure CDN Management Library** をインストールします。

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>ディレクティブ、定数、main メソッド、およびヘルパー メソッド
ここでは、プログラムの基本的な構造を記述します。

1. [Program.cs] タブに戻り、最上部の `using` ディレクティブを次の内容で置き換えます。

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. 次に、メソッドで使用するいくつかの定数を定義します。  `Program` クラスの `Main` メソッドの前に次のコードを追加します。  **&lt;山かっこ&gt;** などのプレースホルダーは、必要に応じて自分の環境に合わせて置き換えます。

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. さらに、クラス レベルで次の 2 つの変数を定義します。  これらの変数は、後でプロファイルとエンドポイントが既に存在しているかどうかを判定するために使用します。

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. 次のように、 `Main` メソッドを置き換えます。

   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();

       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };

       ListProfilesAndEndpoints(cdn);

       // Create CDN Profile
       CreateCdnProfile(cdn);

       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);

       Console.WriteLine();

       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);

       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);

       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);

       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. 他のいくつかのメソッドでは、ユーザーが "はい/いいえ" で回答する質問を行います。  この処理を少し簡単にするために、次のメソッドを追加します。

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

これで、プログラムの基本的な構造が作成されました。次に、`Main` メソッドによって呼び出されるメソッドを作成します。

## <a name="authentication"></a>認証
Azure CDN Management Library を使用するには、サービス プリンシパルを認証し、認証トークンを取得する必要があります。  このメソッドは、ADAL を使用してトークンを取得します。

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult =
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

個別ユーザー認証を使用する場合の `GetAccessToken` メソッドは多少異なります。

> [!IMPORTANT]
> このコード サンプルは、サービス プリンシパルの代わりに個別ユーザー認証を行う場合にのみ使用してください。
>
>

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

`<redirect URI>` は、アプリケーションを Azure AD に登録するときに入力したリダイレクト URI に置き換えてください。

## <a name="list-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントのリストの取得
これで、CDN の操作を実行する準備ができました。  このメソッドでは、まずリソース グループ内のすべてのプロファイルとエンドポイントのリストを取得し、定数に指定されているプロファイルとエンドポイントの名前に一致するものが見つかった場合は重複を作成しないようにその名前を記録します。

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの作成
次に、プロファイルを作成します。

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

プロファイルを作成した後は、エンドポイントを作成します。

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> 上の例では、ホスト名 `www.contoso.com` を持つ *Contoso* という名前のオリジンをエンドポイントに割り当てています。  オリジンのホスト名を指すようにこれを変更する必要があります。
>
>

## <a name="purge-an-endpoint"></a>エンドポイントの消去
エンドポイントが作成されたと仮定して、プログラムで実行する一般的なタスクの 1 つに、エンドポイントの内容を消去することがあります。

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> 上記の例の文字列 `/*` は、エンドポイント パスのルートにあるものをすべて消去することを示します。  これは、Azure Portal の "消去" ダイアログで **[すべて消去]** を選択するのと同じです。 `CreateCdnProfile` メソッドでは、`Sku = new Sku(SkuName.StandardVerizon)` というコードを使用して **Azure CDN from Verizon** としてプロファイルを作成しているため、このコードは動作します。  ただし、**Azure CDN from Akamai** プロファイルでは **[すべて消去]** がサポートされません。このチュートリアルで使用するプロファイルが Akamai プロファイルであった場合は、消去する特定のパスを含める必要があります。
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの削除
最後のメソッドによって、このエンドポイントとプロファイルが削除されます。

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>プログラムの実行
Visual Studio の **[開始]** をクリックして、プログラムをコンパイルおよび実行します。

![Program running](./media/cdn-app-dev-net/cdn-program-running-1.png)

プログラムによって上記のプロンプトが表示された時点で、Azure ポータルでリソース グループに戻り、このプロファイルが作成されたことを確認できます。

![Success!](./media/cdn-app-dev-net/cdn-success.png)

次に、プログラムの残りの部分を実行するかどうかを確認できます。

![Program completing](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>次の手順
このチュートリアルの完成したプロジェクトを確認するには、 [サンプルをダウンロード](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)してください。

Azure CDN Management Library for .NET に関連するドキュメントについては、 [MSDN のリファレンス](/dotnet/api/overview/azure/cdn)を参照してください。

[PowerShell](cdn-manage-powershell.md) で CDN リソースを管理します。
