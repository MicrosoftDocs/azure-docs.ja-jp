---
title: ポータルを使用して管理グループを作成する
description: このクイックスタートでは、Azure portal を使用して、リソースを階層で整理する管理グループを作成します。
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 5a336fe4caf5d2f825a93b5c7b8f8d05f6255c39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592434"
---
# <a name="quickstart-create-a-management-group"></a>クイック スタート:管理グループの作成

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- [階層の保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)が有効になっていない場合、テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループ ([既定の管理グループ](./how-to/protect-resource-hierarchy.md#setting---default-management-group)) の子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート レベルで最初の管理グループを作成できるようになっています。

### <a name="create-in-portal"></a>ポータルで作成する

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]** > **[管理 + ガバナンス]** を選択します。

1. **[管理グループ]** を選択します。

1. **[+ 管理グループの追加]** を選択します。

   :::image type="content" source="./media/main.png" alt-text="子管理グループとサブスクリプションが表示されている [管理グループ] ページのスクリーンショット。" border="false":::

1. **[新規作成]** をオンのままにして、管理グループ ID をフィールドに入力します。

   - **[管理グループ ID]** は、この管理グループでコマンドを送信するために使用するディレクトリの一意識別子です。 この識別子は、このグループを識別するために Azure システム全体で使用されるため、作成後は編集できません。 [ルート管理グループ](./overview.md#root-management-group-for-each-directory)は、Azure Active Directory ID である ID を使用して自動的に作成されます。 他のすべての管理グループには、一意の ID を割り当てます。
   - 表示名フィールドは、Azure Portal 内で表示される名前です。 管理グループの作成時には別の表示名は省略可能なフィールドで、いつでも変更できます。

   :::image type="content" source="./media/create_context_menu.png" alt-text="新しい管理グループを作成するための [管理グループの追加] オプションのスクリーンショット。":::

1. **[保存]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した管理グループを削除するには、次の手順に従います。

1. **[すべてのサービス]** > **[管理 + ガバナンス]** を選択します。

1. **[管理グループ]** を選択します。

1. 先ほど作成した管理グループを見つけて選択し、名前の横にある **[詳細]** を選択します。
   次に、**[削除]** を選択して操作を確定します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リソース階層を整理するための管理グループを作成しました。 管理グループには、サブスクリプションや他の管理グループを含めることができます。

管理グループについて、またリソース階層の管理方法について詳しくは、次の記事に進んでください。

> [!div class="nextstepaction"]
> [管理グループを使用してリソースを管理する](./manage.md)