---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803008"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Azure Resource Manager トークンの取得
Azure Active Directory では、Azure リソース マネージャーを使用してリソース上で実行するすべてのタスクを認証する必要があります。 ここに示す例では、パスワード認証が使用されます。その他の手法については、[Azure Resource Manager の要求の認証][lnk-authenticate-arm]に関する記事を参照してください。

1. Program.cs の **Main** メソッドに次のコードを追加し、アプリケーション ID とパスワードを利用して Azure AD からトークンを取得します。
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. **Main** メソッドの終わりに次のコードを追加し、トークンを使用する **ResourceManagementClient** オブジェクトを作成します。
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. 使用するリソース グループを作成するか、その参照を取得します。
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx