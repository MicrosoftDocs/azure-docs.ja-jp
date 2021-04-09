---
title: チュートリアル:Azure PowerShell を使用して Azure カスタム ロールを作成する - Azure RBAC
description: このチュートリアルでは、Azure PowerShell と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure カスタム ロールを作成します。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 8b1815e7598410cd709572d93082d5dee5e0b0fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369244"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Azure カスタム ロールを作成する

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 このチュートリアルでは、Azure PowerShell を使用して、Reader Support Tickets というカスタム ロールを作成します。 このカスタム ロールが割り当てられたユーザーは、サブスクリプションの管理プレーンにすべてを表示することができ、サポート チケットを開くこともできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * カスタム ロールの作成
> * カスタム ロールの一覧表示
> * カスタム ロールの更新
> * カスタム ロールの削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の要件があります。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可
- [Azure Cloud Shell](../cloud-shell/overview.md) または [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Azure PowerShell へのサインイン

[Azure PowerShell](/powershell/azure/authenticate-azureps) にサインインします。

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、組み込みのロールから始めて、そのロールを編集して新しいロールを作成するのが最も簡単です。

1. PowerShell で、[Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) コマンドを使用して、Microsoft.Support リソース プロバイダーの操作の一覧を取得します。 アクセス許可の作成に使用できる操作を知るための参考にしてください。 「[Azure リソース プロバイダー操作](resource-provider-operations.md#microsoftsupport)」でも、すべての操作の一覧をご覧いただけます。

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを使用して、[閲覧者](built-in-roles.md#reader)ロールを JSON 形式で出力します。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. エディターで **ReaderSupportRole.json** ファイルを開きます。

    この JSON 出力を次に示します。 各種のプロパティについては、[Azure カスタム ロール](custom-roles.md)に関するページを参照してください。

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. JSON ファイルを編集して、`Actions` プロパティに `"Microsoft.Support/*"` 操作を追加します。 read 操作の後に必ずコンマを追加してください。 このアクションによって、ユーザーがサポート チケットを作成できるようになります。

1. [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) コマンドを使用して、サブスクリプションの ID を取得します。

    ```azurepowershell
    Get-AzSubscription
    ```

1. `AssignableScopes` に、`"/subscriptions/00000000-0000-0000-0000-000000000000"` 形式でサブスクリプション ID を追加します。

    明示的にサブスクリプション ID を追加してください。それ以外の場合、サブスクリプションにロールをインポートできなくなります。

1. `Id` プロパティ行を削除し、`IsCustom` プロパティを `true` に変更します。

1. `Name` プロパティと `Description` プロパティを、それぞれ "Reader Support Tickets" と "View everything in the subscription and also open support tickets." に変更します。

    編集後の JSON ファイルは次のようになります。

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. 新しいカスタム ロールを作成するには、[New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) コマンドを使用して、JSON ロール定義ファイルを指定します。

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Azure portal で新しいカスタム ロールが利用できる状態となり、組み込みロールと同じように、ユーザー、グループ、またはサービス プリンシパルに割り当てることができます。

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

- すべてのカスタム ロールを一覧表示するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを使用します。

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    カスタム ロールは、Azure portal で確認することもできます。

    ![Azure Portal にインポートされたカスタム ロールのスクリーンショット](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールを更新するには、JSON ファイルを更新するか、または `PSRoleDefinition` オブジェクトを使用します。

1. JSON ファイルを更新するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを使用して、カスタム ロールを JSON 形式で出力します。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. このファイルをエディターで開きます。

1. `Actions` に、リソース グループのデプロイを作成および管理するための操作を追加します (`"Microsoft.Resources/deployments/*"`)。

    更新後の JSON ファイルは次のようになります。

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. カスタム ロールを更新するには、[Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) コマンドを使用して、更新済みの JSON ファイルを指定します。

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. `PSRoleDefintion` オブジェクトを使用してカスタム ロールを更新するには、まず [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを使用してそのロールを取得します。

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. `Add` メソッドを呼び出して、診断設定を読み取るための操作を追加します。

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) を使用してロールを更新します。

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>カスタム ロールの削除

1. [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを使用して、カスタム ロールの ID を取得します。

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) コマンドを使用し、ロール ID を指定して、カスタム ロールを削除します。

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. 確認を求められたら、「**Y**」と入力します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure PowerShell を使用して Azure カスタム ロールを作成または更新する](custom-roles-powershell.md)
