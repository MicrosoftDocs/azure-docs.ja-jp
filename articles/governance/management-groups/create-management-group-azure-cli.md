---
title: クイック スタート:Azure CLI を使用して管理グループを作成する
description: このクイックスタートでは、Azure CLI を使用して、リソースを階層で整理する管理グループを作成します。
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe38882bd3b025635662e228ae919a24b03dee78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592451"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>クイック スタート:Azure CLI を使用して管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- このクイックスタートでは、CLI をローカルにインストールして使用するために、Azure CLI バージョン 2.0.76 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

- [階層の保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)が有効になっていない場合、テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループ ([既定の管理グループ](./how-to/protect-resource-hierarchy.md#setting---default-management-group)) の子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート レベルで最初の管理グループを作成できるようになっています。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Azure CLI で作成する

Azure CLI では、[az account management-group create](/cli/azure/account/management-group#az_account_management_group_create) コマンドを使用して新しい管理グループを作成します。 この例では、管理グループの **name** は _Contoso_ です。

```azurecli-interactive
az account management-group create --name 'Contoso'
```

**name** は、作成される一意識別子です。 この ID は、このグループを参照するために他のコマンドで使用され、後で変更することはできません。

Azure portal 内で管理グループを別の名前で表示する場合は、**display-name** パラメーターを追加します。 たとえば、Contoso という GroupName と "Contoso Group" という表示名を持つ管理グループを作成する場合は、次のコマンドを使用します。

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

前述の例では、新しい管理グループはルート管理グループ以下に作成されます。 別の管理グループを親として指定するには、**parent** パラメーターを使用し、親グループの名前を指定します。

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

先ほど作成した管理グループを削除するには、[az account management-group delete](/cli/azure/account/management-group#az_account_management_group_delete) コマンドを使用します。

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リソース階層を整理するための管理グループを作成しました。 管理グループには、サブスクリプションや他の管理グループを含めることができます。

管理グループについて、またリソース階層の管理方法について詳しくは、次の記事に進んでください。

> [!div class="nextstepaction"]
> [管理グループを使用してリソースを管理する](./manage.md)