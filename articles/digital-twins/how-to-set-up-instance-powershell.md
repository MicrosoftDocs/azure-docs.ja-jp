---
title: インスタンスと認証を設定する (PowerShell)
titleSuffix: Azure Digital Twins
description: Azure PowerShell を使用して、Azure Digital Twins サービスのインスタンスを設定する方法について説明します
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98044273"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Azure Digital Twins インスタンスと認証を設定する (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する** 手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が整います。

この記事のこのバージョンでは、[Azure PowerShell](/powershell/azure/new-azureps-module-az) を使用して、これらの手順を 1 つずつ手動で実行します。

* Azure portal を使用してこれらの手順を手動で実行するには、この記事のポータル バージョンである [*方法: インスタンスと認証の設定 (ポータル)*](how-to-set-up-instance-portal.md) に関するページを参照してください。
* デプロイ スクリプトのサンプルを使用して自動化された設定を実行するには、この記事のスクリプト化バージョンである [*方法: インスタンスと認証の設定 (スクリプト化)*](how-to-set-up-instance-scripted.md) に関するページを参照してください。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>環境を準備する

1. まず、この記事のコマンドを実行する場所を選択します。 Azure PowerShell のローカル インストールを使用するか、[Azure Cloud Shell](https://shell.azure.com) を使用してブラウザー ウィンドウで Azure PowerShell コマンドを実行できます。
    * Azure PowerShell をローカルで使用する場合は、次のようにします。
       1. [Az PowerShell モジュールをインストールします](/powershell/azure/install-az-ps)。
       1. コンピューターで PowerShell ウィンドウを開きます。
       1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して、Azure アカウントに接続します。
    * Azure Cloud Shell を使用する場合は、次のようにします。
        1. Cloud Shell の詳細については、[Azure Cloud Shell の概要](../cloud-shell/overview.md)に関するページを参照してください。
        1. ブラウザーで[このリンク](https://shell.azure.com)を開き、Cloud Shell ウィンドウを開きます。
        1. Cloud Shell アイコン バーで、PowerShell バージョンを実行するように設定されていることを確認します。
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell バージョンが選択されていることを示す Cloud Shell ウィンドウ":::
    
1. 複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. このサブスクリプションで Azure Digital Twins を使用するのが初めての場合は、**Microsoft.DigitalTwins** リソース プロバイダーを登録する必要があります。 (それが不明な場合は、以前に実行していたとしても、もう一度実行して問題ありません)。

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. 次のコマンドを使用して、**Az.DigitalTwins** PowerShell モジュールをインストールします。
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> **Az.DigitalTwins** PowerShell モジュールがプレビュー段階にある間は、前述のとおり、`Install-Module` コマンドレットを使用して、これを個別にインストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを作成する

このセクションでは、Azure PowerShell を使用して、**Azure Digital Twins の新しいインスタンスを作成** します。
次のものを指定する必要があります。

* インスタンスがデプロイされる [Azure リソース グループ](../azure-resource-manager/management/overview.md)。 まだ既存のリソース グループがない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して作成できます。

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* デプロイのためのリージョン。 Azure Digital Twins がどのリージョンでサポートされているかを確認するには、[*リージョン別の利用可能な Azure 製品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)に関するページを参照してください。
* インスタンスの名前。 新しいインスタンスの名前は、サブスクリプションのリージョン内で一意である必要があります。 サブスクリプションのリージョンに、指定した名前がすでに使用されている別の Azure Digital Twins インスタンスがある場合は、別の名前を選択するように求められます。

次のコマンドで実際の値を使用してインスタンスを作成します。

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>成功を確認して重要な値を収集する

インスタンスが正常に作成された場合、結果は、作成したリソースに関する情報を含む次の出力のようになります。

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

次に、`Get-AzDigitalTwinsInstance` を実行し、`Select-Object -Property *` にパイプ処理して新しいインスタンスのプロパティを表示します。

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> このコマンドを使用すると、インスタンスのすべてのプロパティをいつでも確認できます。

Azure Digital Twins インスタンスの **HostName**、**Name**、**ResourceGroup** を確認します。 これらは、認証および関連する Azure リソースを設定するために、Azure Digital Twins インスタンスを引き続き操作する場合に必要になる可能性がある重要な値です。 他のユーザーがそのインスタンスに対してプログラミングする場合は、これらの値を彼らと共有する必要があります。

これで、Azure Digital Twins インスタンスの準備が完了しました。 次に、適切な Azure ユーザーにそれを管理するためのアクセス許可を付与します。

## <a name="set-up-user-access-permissions"></a>ユーザーのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

まず、ロールを割り当てる必要があるユーザーの Azure AD アカウントの **ObjectId** を特定します。 この値は、[Get-AzAdUser](/powershell/module/az.resources/get-azaduser) コマンドレットを使用して見つけることができます。そのためには、Azure AD アカウントのユーザー プリンシパル名を渡して、その ObjectId (およびその他のユーザー情報) を取得します。 ほとんどの場合、ユーザー プリンシパル名は、Azure AD アカウントのユーザーの電子メールと一致します。

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

次に、次のコマンドで **ObjectId** を使用して、ロールを割り当てます。 このコマンドを使用するには、インスタンスの作成時に選択したものと同じサブスクリプション ID、リソース グループ名、および Azure Digital Twins のインスタンス名も入力する必要があります。 このコマンドは、Azure サブスクリプションで[十分なアクセス許可](#prerequisites-permission-requirements)を持つユーザーによって実行される必要があります。

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

このコマンドの結果として、作成されたロールの割り当てに関する情報が出力されます。

### <a name="verify-success"></a>成功を確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

これで Azure Digital Twins インスタンスの準備が完了し、それを管理するためのアクセス許可が割り当てられました。

## <a name="next-steps"></a>次のステップ

認証コードを使ってクライアント アプリケーションをインスタンスに接続する方法を確認します。
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)
