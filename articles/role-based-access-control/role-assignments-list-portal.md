---
title: Azure portal を使用して Azure でのロールの割り当てを一覧表示する - Azure RBAC
description: Azure portal と Azure ロールベースのアクセス制御 (RBAC) を使用して、リソース ユーザー、グループ、サービス プリンシパル、およびマネージド ID のアクセスを決定する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f7d56ecc7fc6bd850fced33c2c1cf20902bb2df4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361852"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Azure portal を使用して Azure でのロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] この記事では、Azure portal を使用してロールの割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 組織が、[Azure の委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を使用するサービス プロバイダーに管理機能を外部委託している場合、そのサービス プロバイダーによって承認されているロールの割り当てはここに表示されません。

## <a name="list-role-assignments-for-a-user-or-group"></a>ユーザーまたはグループのロールの割り当てを一覧表示する

サブスクリプションのユーザーまたはグループに割り当てられているロールを最も簡単に確認する方法は、 **[Azure でのロールの割り当て]** ペインを使用することです。

1. Azure portal で、Azure portal メニューから **[すべてのサービス]** を選択します。

1. **[Azure Active Directory]** を選択し、 **[ユーザー]** または **[グループ]** を選択します。

1. ロールの割り当てを一覧表示するユーザーまたはグループをクリックします。

1. **[Azure でのロールの割り当て]** をクリックします。

    管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなスコープで、選択したユーザーまたはグループに割り当てられているロールの一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![ユーザーのロールの割り当て](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

## <a name="list-owners-of-a-subscription"></a>サブスクリプションの所有者を一覧表示する

サブスクリプションの[所有者](built-in-roles.md#owner)ロールが割り当てられているユーザーは、サブスクリプション内のすべてを管理できます。 サブスクリプションの所有者を一覧表示するには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** 、 **[サブスクリプション]**  の順にクリックします。

1. 所有者を一覧表示するサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このサブスクリプションのすべてのロールの割り当てを表示します。

1. **[所有者]** セクションまでスクロールして、このサブスクリプションの所有者ロールが割り当てられているすべてのユーザーを表示します。

   ![サブスクリプションへのアクセスの制御 - [ロールの割り当て] タブ](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>あるスコープのロールの割り当てを一覧表示する

1. Azure portal で、 **[すべてのサービス]**  をクリックしてからスコープを選びます。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

   ![アクセス制御 - [ロールの割り当て] タブ](./media/role-assignments-list-portal/access-control-role-assignments.png)

   [ロールの割り当て] タブでは、このスコープにアクセス権があるユーザーを確認できます。 スコープが **[このリソース]** のロールもあれば、別のスコープからスコープを **[(継承)]** しているロールもあることに注目してください。 アクセス権は、このリソースに明確に割り当てられるか、または親スコープへの割り当てから継承されます。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>あるスコープのユーザーのロールの割り当てを一覧表示する

ユーザー、グループ、サービス プリンシパル、またはマネージド ID のアクセス権を一覧表示するには、ロールの割り当てを一覧表示します。 特定のスコープで単独のユーザー、グループ、サービス プリンシパル、またはマネージド ID のロールの割り当てを一覧表示するには、次の手順のようにします。

1. Azure portal で、 **[すべてのサービス]**  をクリックしてからスコープを選びます。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[アクセスの確認]** タブをクリックします。

    ![アクセス制御 - [アクセスの確認] タブ](./media/role-assignments-list-portal/access-control-check-access.png)

1. **[検索]** 一覧で、アクセス権を確認するセキュリティ プリンシパルの種類を選択します。

1. 検索ボックスに、表示名、メール アドレス、またはオブジェクト識別子のディレクトリを検索するための文字列を入力します。

    ![[アクセスの確認] の選択リスト](./media/role-assignments-list-portal/check-access-select.png)

1. セキュリティ プリンシパルをクリックして **[割り当て]** ウィンドウを開きます。

    ![[割り当て] ウィンドウ](./media/role-assignments-list-portal/check-access-assignments.png)

    このウィンドウでは、選択したセキュリティ プリンシパルに割り当てられているロールとスコープを確認できます。 このスコープに拒否割り当てがある場合、またはこのスコープに継承されている場合は、それが表示されます。

## <a name="list-role-assignments-for-a-managed-identity"></a>マネージド ID のロールの割り当ての一覧表示

前に説明したように、 **[アクセス制御 (IAM)]** ブレードを使用すると、特定のスコープでシステムによって割り当てられたマネージド ID とユーザーが割り当てたマネージド ID に対するロールの割り当てを一覧表示できます。 ここでは、マネージド ID に対するロールの割り当てのみを一覧表示する方法について説明します。

### <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

1. Azure portal で、システム割り当てマネージド ID を開きます。

1. 左側のメニューで、 **[ID]** をクリックします。

    ![システム割り当てマネージド ID](./media/shared/identity-system-assigned.png)

1. **[アクセス許可]** で、 **[Azure でのロールの割り当て]** をクリックします。

    管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなスコープで、選択したシステム割り当てマネージド ID に割り当てられているロールの一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![システム割り当てマネージド ID のロールの割り当て](./media/shared/role-assignments-system-assigned.png)

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

### <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

1. Azure portal で、ユーザー割り当てマネージド ID を開きます。

1. **[Azure でのロールの割り当て]** をクリックします。

    管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなスコープで、選択したユーザー割り当てマネージド ID に割り当てられているロールの一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![システム割り当てマネージド ID のロールの割り当て](./media/shared/role-assignments-user-assigned.png)

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

## <a name="list-number-of-role-assignments"></a>ロールの割り当ての数の一覧

各サブスクリプションには、最大 **2,000** 個のロールの割り当てを保持できます。 この制限には、サブスクリプション、リソース グループ、およびリソースのスコープでのロールの割り当てが含まれます。 **[ロールの割り当て]** タブには、この制限の追跡に役立つ、現在のサブスクリプションに対するロールの割り当て数を示すグラフが表示されます。

![アクセス制御 - ロールの割り当ての数のグラフ](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

最大数に近づいているときにロールの割り当てを追加しようとすると、 **[ロールの割り当ての追加]** ウィンドウに警告が表示されます。 ロールの割り当て数を減らす方法については、「[Azure リソースの RBAC のトラブルシューティング](troubleshooting.md#azure-role-assignments-limit)」を参照してください。

![アクセス制御 - ロールの割り当ての追加の警告](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments-preview"></a>ロールの割り当てのダウンロード (プレビュー)

スコープ内のロールの割り当ては、CSV 形式または JSON 形式でダウンロードできます。 これは、スプレッドシート内のリストを検査したり、サブスクリプションの移行時にインベントリを取得したりする必要がある場合に役立ちます。

> [!IMPORTANT]
> ロールの割り当てのダウンロードは、現在パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ロールの割り当てをダウンロードするときは、次の条件に注意する必要があります。

- ディレクトリ リーダー ロールなどのディレクトリを読み取るためのアクセス許可がない場合、DisplayName 列、SignInName 列、ObjectType 列は空白になります。
- セキュリティ プリンシパルが削除されているロールの割り当てが含まれていないこと。
- 従来の管理者に付与されたアクセス権が含まれていないこと。

スコープ内のロールの割り当てをダウンロードするには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** をクリックし、ロールの割り当てをダウンロードするスコープを選択します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当てのダウンロード] (プレビュー)** をクリックして、[ロールの割り当てのダウンロード] ペインを開きます。

    ![アクセス制御 - ロールの割り当てのダウンロード](./media/role-assignments-list-portal/download-role-assignments.png)

1. チェック ボックスを使用して、ダウンロードされるファイルに含めるロールの割り当てを選択します。

    - **継承済み** - 現在のスコープの継承されたロールの割り当てを含めます。
    - **現在のスコープ** - 現在のスコープのロールの割り当てを含めます。
    - **子** - 現在のスコープの下位レベルのロールの割り当てを含めます。 管理グループのスコープでは、このチェック ボックスはオフになっています。

1. ファイル形式を選択します。これには、コンマ区切り値 (CSV) または JavaScript Object Notation (JSON) を指定できます。

1. ファイル名を指定します。

1. **[開始]** をクリックしてダウンロードを開始します。

    各ファイル形式の出力の例を次に示します。

    ![ロールの割り当てを CSV としてダウンロード](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![ロールの割り当てを CSV としてダウンロード](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure でのロールの割り当てを追加または削除する](role-assignments-portal.md)
- [Azure RBAC のトラブルシューティング](troubleshooting.md)
