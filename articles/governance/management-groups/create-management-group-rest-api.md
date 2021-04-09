---
title: クイック スタート:REST API を使用して管理グループを作成する
description: このクイックスタートでは、REST API を使用して、リソースを階層で整理する管理グループを作成します。
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ff1487bf25945c733402ddb74d1e102bea80b4b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592502"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>クイック スタート:REST API を使用して管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- [ARMClient](https://github.com/projectkudu/ARMClient) をまだインストールしていない場合はインストールします。 これは、Azure Resource Manager ベースの REST API に HTTP 要求を送信するツールです。 代わりに、REST ドキュメントの "試してみる" 機能、または PowerShell の [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) や [Postman](https://www.postman.com) などのツールを使用することもできます。

- [階層の保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)が有効になっていない場合、テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループ ([既定の管理グループ](./how-to/protect-resource-hierarchy.md#setting---default-management-group)) の子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート レベルで最初の管理グループを作成できるようになっています。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>REST API で作成する

REST API では、[管理グループの作成と更新](/rest/api/resources/managementgroups/createorupdate)エンドポイントを使用して新しい管理グループを作成します。 この例では、管理グループの **groupId** は _Contoso_ です。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 要求本文はありません

**groupId** は、作成される一意識別子です。 この ID は、このグループを参照するために他のコマンドで使用され、後で変更することはできません。

Azure portal 内で管理グループを別の名前で表示する場合は、要求本文に **properties.displayName** プロパティを追加します。 たとえば、_Contoso_ という **groupId** と _Contoso Group_ という表示名の管理グループを作成する場合は、次のエンドポイントと要求本文を使用します。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

前述の例では、新しい管理グループはルート管理グループ以下に作成されます。 別の管理グループを親として指定するには、**properties.parent.id** プロパティを使用します。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

先ほど作成した管理グループを削除するには、[管理グループの削除](/rest/api/resources/managementgroups/delete)エンドポイントを使用します。

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 要求本文はありません

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リソース階層を整理するための管理グループを作成しました。 管理グループには、サブスクリプションや他の管理グループを含めることができます。

管理グループについて、またリソース階層の管理方法について詳しくは、次の記事に進んでください。

> [!div class="nextstepaction"]
> [管理グループを使用してリソースを管理する](./manage.md)