---
title: Azure portal を使用して Azure カスタム ロールを作成または更新する - Azure RBAC
description: Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure カスタム ロールを作成する方法について説明します。 これには、カスタム ロールを一覧表示、作成、更新、削除する方法が含まれます。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369228"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Azure portal を使用して Azure カスタム ロールを作成または更新する

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のAzure カスタム ロールを作成することができます。 組み込みロールと同様に、カスタム ロールは、ユーザー、グループ、サービス プリンシパルに対して、サブスクリプションおよびリソース グループのスコープで割り当てることができます。 カスタム ロールは Azure Active Directory (Azure AD) ディレクトリに保存され、サブスクリプション間で共有することができます。 各ディレクトリは、最大 5,000 個のカスタム ロールを持つことができます カスタム ロールは、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用して作成できます。 この記事では、Azure portal を使用してカスタム ロールを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

カスタム ロールを作成するには、次のものが必要です。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可

## <a name="step-1-determine-the-permissions-you-need"></a>手順 1:必要なアクセス許可を決定する

Azure には、カスタム ロールに含めることができる何千ものアクセス許可があります。 カスタム ロールに追加するアクセス許可を決定するのに役立ついくつかの方法を示します。

- 既存の[組み込みロール](built-in-roles.md)を確認します。
- アクセス権を付与する Azure サービスを一覧表示します。
- [Azure サービスにマップされるリソース プロバイダー](../azure-resource-manager/management/azure-services-resource-providers.md)を決定します。 検索方法については、次を参照してください: [ステップ 4: Permissions\(次へ: アクセス許可\)](#step-4-permissions) をクリックします。
- [使用可能なアクセス許可](resource-provider-operations.md)を検索して、含めるアクセス許可を見つけます。 検索方法については、次を参照してください: [ステップ 4: Permissions\(次へ: アクセス許可\)](#step-4-permissions) をクリックします。

## <a name="step-2-choose-how-to-start"></a>手順 2:開始方法を選択する

カスタム ロールの作成を開始するには、次の 3 つの方法があります。 既存のロールを複製するか、最初から始めるか、JSON ファイルから始めることができます。 最も簡単な方法は、必要なアクセス許可のほとんどが備わった既存のロールを検索し、それを複製して、実際のシナリオに合わせて変更することです。 

### <a name="clone-a-role"></a>ロールを複製する

必要なアクセス許可が既存のロールにない場合は、それを複製してから、アクセス許可を変更することができます。 ロールの複製を開始するには、次の手順に従います。

1. Azure portal で、カスタム ロールを割り当て可能にするサブスクリプションまたはリソース グループを開き、 **[アクセス制御 (IAM)]** を開きます。

    次のスクリーンショットは、サブスクリプションに対して開かれている [アクセス制御 (IAM)] ページを示しています。

    ![サブスクリプションの [アクセス制御 (IAM)] ページ](./media/custom-roles-portal/access-control-subscription.png)

1. **[役割]** タブをクリックして、すべての組み込みおよびカスタム ロールの一覧を表示します。

1. 請求閲覧者ロールなど、複製するロールを検索します。

1. 行の末尾にある省略記号 ( **...** ) をクリックし、 **[複製]** をクリックします。

    ![複製のコンテキスト メニュー](./media/custom-roles-portal/clone-menu.png)

    これにより、 **[ロールを複製する]** オプションが選択されたカスタム ロール エディターが開きます。

1. 「[手順 3:基本](#step-3-basics)」に進みます。

### <a name="start-from-scratch"></a>最初から行う

必要に応じて、次の手順に従って、カスタム ロールを最初から始めることができます。

1. Azure portal で、カスタム ロールを割り当て可能にするサブスクリプションまたはリソース グループを開き、 **[アクセス制御 (IAM)]** を開きます。

1. **[追加]** をクリックし、 **[カスタム ロールの追加]** をクリックします。

    ![[カスタム ロールの追加] メニュー](./media/custom-roles-portal/add-custom-role-menu.png)

    これにより、 **[Start from scratch]\(最初から始める\)** オプションが選択されたカスタム ロール エディターが開きます。

1. 「[手順 3:基本](#step-3-basics)」に進みます。

### <a name="start-from-json"></a>JSON から始める

必要に応じて、JSON ファイルでほとんどのカスタム ロール値を指定できます。 カスタム ロール エディターでファイルを開き、追加の変更を行ってから、カスタム ロールを作成できます。 JSON ファイルで始めるには、次の手順に従います。

1. 次の形式の JSON ファイルを作成します。

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. JSON ファイルで、さまざまなプロパティの値を指定します。 いくつかの値が追加された例を次に示します。 各種のプロパティについては、「[Azure ロールの定義について](role-definitions.md)」をご覧ください。

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Azure portal で、 **[アクセス制御 (IAM)]** ページを開きます。

1. **[追加]** をクリックし、 **[カスタム ロールの追加]** をクリックします。

    ![[カスタム ロールの追加] メニュー](./media/custom-roles-portal/add-custom-role-menu.png)

    これにより、カスタム ロール エディターが開きます。

1. [基本] タブの **[ベースラインのアクセス許可]** で、 **[Start from JSON]\(JSON から始める\)** を選択します。

1. [ファイルの選択] ボックスの横にあるフォルダー ボタンをクリックして、[開く] ダイアログ ボックスを開きます。

1. 使用する JSON ファイルを選択し、 **[開く]** をクリックします。

1. 「[手順 3:基本](#step-3-basics)」に進みます。

## <a name="step-3-basics"></a>手順 3:基本

**[基本]** タブで、カスタム ロールの名前、説明、およびベースライン アクセス許可を指定します。

1. **[カスタム ロール名]** ボックスで、カスタム ロールの名前を指定します。 名前は、Azure AD ディレクトリに対して一意である必要があります。 名前には、英字、数字、スペース、特殊文字を含めることができます。

1. **[説明]** ボックスに、カスタム ロールの説明を指定します (省略可能)。 これは、カスタム ロールのヒントになります。

    **[ベースラインのアクセス許可]** オプションは、前の手順に基づいて既に設定されている必要がありますが、変更することができます。

    ![値が指定された [基本] タブ](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>手順 4:アクセス許可

**[アクセス許可]** タブで、カスタム ロールのアクセス許可を指定します。 ロールを複製したのか、JSON を使用して始めたのかによって、[アクセス許可] タブにいくつかのアクセス許可が既に表示されている場合があります。

![[カスタム ロールの作成] の [アクセス許可] タブ](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>アクセス許可を追加または削除する

カスタム ロールのアクセス許可を追加または削除するには、次の手順に従います。

1. アクセス許可を追加するには **[アクセス許可の追加]** をクリックして、[アクセス許可の追加] ペインを開きます。

    このペインには、使用可能なすべてのアクセス許可がカード形式のさまざまなカテゴリにグループ化されて表示されます。 各カテゴリは、Azure リソースを提供するサービスである "*リソース プロバイダー*" を表します。

1. **[Search for a permission]\(アクセス許可の検索\)** ボックスに、アクセス許可を検索する文字列を入力します。 たとえば、請求書に関連するアクセス許可を検索するには、「*請求書*」を検索します。

    検索文字列に基づいて、リソース プロバイダー カードの一覧が表示されます。 リソース プロバイダーが Azure サービスにどのようにマップされるかを示す一覧については、「[Azure サービスのリソース プロバイダー ](../azure-resource-manager/management/azure-services-resource-providers.md)」を参照してください。

    ![リソース プロバイダーが含まれた [アクセス許可の追加] ペイン](./media/custom-roles-portal/add-permissions-provider.png)

1. **[Microsoft Billing]** など、カスタム ロールに追加するアクセス許可を持つ可能性のあるリソース プロバイダー カードをクリックします。

    検索文字列に基づいて、そのリソース プロバイダーの管理アクセス許可の一覧が表示されます。

    ![[アクセス許可の追加] の一覧](./media/custom-roles-portal/add-permissions-list.png)

1. データ プレーンに適用されるアクセス許可を検索する場合は、 **[データ アクション]** をクリックします。 それ以外の場合は、アクションの切り替えを **[アクション]** のままにして、管理プレーンに適用されるアクセス許可を一覧表示します。 管理プレーンとデータ プレーンの違いの詳細については、「[管理操作とデータ操作](role-definitions.md#management-and-data-operations)」を参照してください。

1. 必要に応じて、検索文字列を更新して検索を絞り込みます。

1. カスタム ロールに追加する 1 つ以上のアクセス許可が見つかったら、そのアクセス許可の横にチェック マークを追加します。 たとえば、 **[Other :Download Invoice]\(その他 : 請求書のダウンロード\)** の横にチェック マークを追加して、請求書をダウンロードするアクセス許可を追加します。

1. **[追加]** をクリックして、アクセス許可の一覧にアクセス許可を追加します。

    アクセス許可は、`Actions` または `DataActions` として追加されます。

    ![追加されたアクセス許可](./media/custom-roles-portal/permissions-list-add.png)

1. アクセス許可を削除するには、行の末尾にある削除アイコンをクリックします。 この例では、ユーザーはサポート チケットを作成する権限を必要としないため、`Microsoft.Support/*` のアクセス許可を削除できます。

### <a name="add-wildcard-permissions"></a>ワイルドカード アクセス許可を追加する

選択した開始方法によっては、アクセス許可の一覧にワイルドカード (`*`) を含むアクセス許可が表示される場合があります。 ワイルドカード (`*`) を使用すると、指定されたアクション文字列に一致するものすべてにアクセス許可が拡張されます。 たとえば、次のワイルドカード文字列は、Azure Cost Management およびエクスポートに関連するすべてのアクセス許可を追加します。 これには、今後追加される可能性があるエクスポートのアクセス許可も含まれます。

```
Microsoft.CostManagement/exports/*
```

新しいワイルドカード アクセス許可を追加する場合、 **[アクセス許可の追加]** ペインを使用して追加することはできません。 ワイルドカード アクセス許可を追加するには、 **[JSON]** タブを使用して手動で追加する必要があります。詳細については、「[手順 6:JSON](#step-6-json)」を参照してください。

### <a name="exclude-permissions"></a>アクセス許可を除外する

ロールにワイルドカード (`*`) アクセス許可があり、そのワイルドカード アクセス許可から特定のアクセス許可を除外または削減する場合は、それらを除外できます。 たとえば、次のようなワイルドカード アクセス許可があるとします。

```
Microsoft.CostManagement/exports/*
```

エクスポートの削除を許可しない場合は、次の削除アクセス許可を除外できます。

```
Microsoft.CostManagement/exports/delete
```

アクセス許可を除外すると、それは `NotActions` または `NotDataActions` として追加されます。 有効な管理アクセス許可は、すべての `Actions` を加算してからすべての `NotActions` を減算することによって計算されます。 有効なデータ アクセス許可は、すべての `DataActions` を加算してからすべての `NotDataActions` を減算することによって計算されます。

> [!NOTE]
> アクセス許可の除外は、拒否と同じではありません。 アクセス許可の除外は、ワイルドカード アクセス許可からアクセス許可を削減するための手軽な方法にすぎません。

1. 許可されたワイルドカード アクセス許可からアクセス許可を除外または削減するには、 **[アクセス許可を除外する]** をクリックして、[アクセス許可を除外する] ペインを開きます。

    このペインでは、除外または削減する管理またはデータ アクセス許可を指定します。

1. 除外する 1 つ以上のアクセス許可が見つかったら、そのアクセス許可の横にチェック マークを追加し、 **[追加]** ボタンをクリックします。

    ![[アクセス許可を除外する] ペイン - 選択されたアクセス許可](./media/custom-roles-portal/exclude-permissions-select.png)

    アクセス許可は、`NotActions` または `NotDataActions` として追加されます。

    ![除外されたアクセス許可](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>手順 5:割り当て可能なスコープ

**[Assignable scopes]\(割り当て可能なスコープ\)** タブで、サブスクリプションやリソース グループなど、カスタム ロールを割り当て可能な場所を指定します。 選択した開始方法によっては、[アクセス制御 (IAM)] ページを開いたスコープがこのタブに表示される場合があります。 割り当て可能なスコープをルート スコープ ("/") に設定することはサポートされていません。 現時点では、割り当て可能なスコープとして管理グループを追加することはできません。

1. **[割り当て可能なスコープの追加]** をクリック て、[割り当て可能なスコープの追加] ペインを開きます。

    ![[Assignable scopes]\(割り当て可能なスコープ\) タブ](./media/custom-roles-portal/assignable-scopes.png)

1. 使用する 1 つまたは複数のスコープ (通常はサブスクリプション) をクリックします。

    ![[割り当て可能なスコープの追加]](./media/custom-roles-portal/add-assignable-scopes.png)

1. **[追加]** ボタンをクリックして、割り当て可能なスコープを追加します。

## <a name="step-6-json"></a>手順 6:JSON

**[JSON]** タブには、JSON 形式のカスタム ロールが表示されます。 必要に応じて、JSON を直接編集できます。 ワイルドカード (`*`) アクセス許可を追加する場合は、このタブを使用する必要があります。

1. JSON を編集するには、 **[編集]** をクリックします。

    ![カスタム ロールを示す [JSON] タブ](./media/custom-roles-portal/json.png)

1. JSON に変更を加えます。

    JSON の形式が正しくない場合は、赤色のギザギザ線が表示され、縦の溝にインジケーターが表示されます。

1. 編集が完了したら、 **[保存]** をクリックします。

## <a name="step-7-review--create"></a>手順 7:確認と作成

**[確認と作成]** タブで、カスタム ロールの設定を確認できます。

1. カスタム ロールの設定を確認します。

    ![[確認と作成] タブ](./media/custom-roles-portal/review-create.png)

1. **[作成]** をクリックして、カスタム ロールを作成します。

    しばらくすると、カスタム ロールが正常に作成されたことを示すメッセージ ボックスが表示されます。

    ![カスタム ロールの作成のメッセージ](./media/custom-roles-portal/custom-role-success.png)

    エラーが検出されると、メッセージが表示されます。

    ![[確認と作成] のエラー](./media/custom-roles-portal/review-create-error.png)

1. **[役割]** の一覧で、新しいカスタム ロールを表示します。 カスタム ロールが表示されない場合は、 **[最新の情報に更新]** をクリックします。

     カスタム ロールがすべての場所に表示されるまで、数分かかることがあります。

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

カスタム ロールを表示するには、次の手順に従います。

1. サブスクリプションまたはリソース グループを開き、 **[アクセス制御 (IAM)]** を開きます。

1. **[役割]** タブをクリックして、すべての組み込みおよびカスタム ロールの一覧を表示します。

1. **[種類]** 一覧で **[CustomRole]** を選択し、カスタム ロールのみを表示します。

    カスタム ロールを作成したばかりで、一覧に表示されない場合は、 **[最新の情報に更新]** をクリックします。

    ![カスタム ロールの一覧](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>カスタム ロールの更新

1. この記事で前述したように、カスタム ロールの一覧を開きます。

1. 更新するカスタム ロールの省略記号 ( **...** ) をクリックし、 **[編集]** をクリックします。 組み込みのロールは更新できないことに注意してください。

    カスタム ロールがエディターで開かれます。

    ![カスタム ロール メニュー](./media/custom-roles-portal/edit-menu.png)

1. さまざまなタブを使用して、カスタム ロールを更新します。

1. 変更が完了したら、 **[確認と作成]** タブをクリックして、変更内容を確認します。

1. **[更新]** ボタンをクリックして、カスタム ロールを更新します。

## <a name="delete-a-custom-role"></a>カスタム ロールの削除

1. この記事で前述したように、カスタム ロールの一覧を開きます。

1. カスタム ロールを使用しているロールの割り当てをすべて削除します。

1. 削除するカスタム ロールの省略記号 ( **...** ) をクリックし、 **[削除]** をクリックします。

    ![削除対象として選択できるカスタム ロールの一覧を示しているスクリーンショット。](./media/custom-roles-portal/delete-menu.png)

    カスタム ロールが完全に削除されるまで、数分かかることがあります。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure PowerShell を使用して Azure カスタム ロールを作成する](tutorial-custom-role-powershell.md)
- [Azure カスタム ロール](custom-roles.md)
- [Azure リソース プロバイダーの操作](resource-provider-operations.md)
