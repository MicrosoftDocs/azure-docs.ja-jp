---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 74f15452c7def068403b97b11e784170835edc18
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804487"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Azure Resource Manager の要求を認証するための準備
[Azure Resource Manager][lnk-authenticate-arm] と Azure Active Directory (AD) を使用して、リソース上で実行するすべての操作を認証する必要があります。 これを構成するうえで、PowerShell または Azure CLI を使用するのが最も簡単な方法です。

先に進む前に [Azure PowerShell コマンドレット][lnk-powershell-install]をインストールしてください。

次のステップでは、PowerShell を使用して AD アプリケーション用のパスワード認証を設定する方法を説明します。 これらのコマンドは標準的な PowerShell セッションで実行できます。

1. 次のコマンドを使用して Azure サブスクリプションにサインインします。

    ```powershell
    Connect-AzAccount
    ```

1. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべての Azure サブスクリプションへのアクセスが許可されます。 次のコマンドで、使用できる Azure サブスクリプションの一覧を表示します。

    ```powershell
    Get-AzSubscription
    ```

    次のコマンドで、使用するサブスクリプションを選択して、IoT Hub を管理するためのコマンドを実行します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. **TenantId** と **SubscriptionId** を書き留めておきます。 この情報は後で必要になります。
3. 次のコマンドを使用して新しい Azure Active Directory アプリケーションを作成します。プレース ホルダーを以下のとおりに置き換えます。
   
   * **{Display name}:** **MySampleApp** など、アプリケーションの表示名。
   * **{Home page URL}:** **http:\//mysampleapp/home** など、お使いのアプリのホーム ページの URL。 この URL は実際のアプリケーションを示している必要はありません。
   * **{Application identifier}:**  **http:\//mysampleapp** などの一意の識別子。 この URL は実際のアプリケーションを示している必要はありません。
   * **{Password}:** 自分のアプリで認証に使用するパスワード。
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. 作成したアプリケーションの **ApplicationId** を書き留めておきます。 この情報は後で必要になります。
5. 次のコマンドを使用して新しいサービス プリンシパルを作成します。**{MyApplicationId}** を前の手順で書き留めた **ApplicationId** と置き換えます。
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. 次のコマンドを使用してロール割り当てを設定します。**{MyApplicationId}** を自分の **ApplicationId** と置き換えます。
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

これで、カスタム C# アプリケーションから認証できるようにする Azure AD アプリケーションの作成が完了しました。 このチュートリアルでは後で次の値が必要になります。

* TenantId
* SubscriptionId
* ApplicationId
* パスワード

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-Az-ps
