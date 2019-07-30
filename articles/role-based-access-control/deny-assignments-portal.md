---
title: Azure portal を使用して Azure リソースの拒否割り当てを一覧表示する | Microsoft Docs
description: Azure portal を使用して、特定のスコープの特定の Azure リソース アクションへのアクセスが拒否されているユーザー、グループ、サービス プリンシパル、およびマネージド ID を一覧表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 21ffb1a2539a2e724a91dd3b2818270a5e573ef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127483"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Azure portal を使用して Azure リソースの拒否割り当てを一覧表示する

[拒否割り当て](deny-assignments.md)を使用すると、ロールの割り当てでアクセスを許可されている場合であっても、指定した Azure リソース アクションをユーザーが実行できなくなります。 この記事では、Azure portal を使用して拒否割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 独自の拒否割り当てを直接作成することはできません。 割り当て拒否が作成されるしくみの詳細については、[拒否割り当て](deny-assignments.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

拒否割り当てに関する情報を取得するのに必要なものは次のとおりです:

- ほとんどの [Azure リソースの組み込みロール](built-in-roles.md)に含まれている `Microsoft.Authorization/denyAssignments/read` アクセス許可。

## <a name="list-deny-assignments"></a>拒否割り当てを一覧表示する

サブスクリプションまたは管理グループのスコープで拒否割り当てを一覧表示するには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** 、 **[管理グループ]** または **[サブスクリプション]** の順に選択します。

1. 一覧表示する管理グループまたはサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[拒否割り当て]** タブをクリックします (または、拒否割り当てを表示しますタイルで **[表示]** ボタンをクリックします)。

    このスコープに拒否割り当てがある場合、またはこのスコープに継承されている場合、表示されます。

    ![アクセス制御 - [拒否割り当て] タブ](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. 追加の列を表示するために **[列の編集]** をクリックします。

    ![拒否割り当て - 列](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Name** | 拒否割り当ての名前です。 |
    | **プリンシパルの種類** | ユーザー、グループ、システム定義のグループ、またはサービス プリンシパルです。 |
    | **拒否**  | 拒否割り当てに含まれているセキュリティ プリンシパルの名前です。 |
    | **Id** | 拒否割り当ての一意の識別子です。 |
    | **除外されたプリンシパル** | 拒否割り当てから除外されているセキュリティ プリンシパルがあるかどうかです。 |
    | **子に適用しません (子には適用しない)** | 拒否割り当てがサブスコープに継承されているかどうかです。 |
    | **保護されているシステム** | Azure が拒否割り当てを管理しているかどうかです。 現時点では、常に [はい] です。 |
    | **スコープ** | 管理グループ、サブスクリプション、リソース グループ、またはリソースです。 |

1. 有効になっている任意の項目のチェックマークをオンにし、 **[OK]** をクリックして、選択した列を表示します。

## <a name="list-details-about-a-deny-assignment"></a>拒否割り当ての詳細を一覧表示する

拒否割り当ての詳細をさらに一覧表示するには、次の手順を実行します。

1. 前のセクションの説明に従って、 **[拒否割り当て]** ウィンドウを開きます。

1. 拒否割り当て名をクリックし、 **[ユーザー]** ブレードを開きます。

    ![拒否割り当て - ユーザー](./media/deny-assignments-portal/deny-assignment-users.png)

    **[ユーザー]** ブレードには、次の 2 つのセクションがあります。

    |  |  |
    | --- | --- |
    | **以下に拒否割り当てを適用します**  | 拒否割り当ての適用対象のセキュリティ プリンシパルです。 |
    | **拒否割り当てによって以下が除外されます** | 拒否割り当てから除外対象のセキュリティ プリンシパルです。 |

    **システム定義のプリンシパル**は、Azure AD ディレクトリのすべてのユーザー、グループ、サービス プリンシパルおよびマネージド ID を表します。

1. 拒否されたアクセス許可の一覧を表示するには、 **[拒否されたアクセス許可]** をクリックします。

    ![拒否割り当て - 拒否されたアクセス許可](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | アクションの種類 | 説明 |
    | --- | --- |
    | **アクション**  | 拒否された管理操作です。 |
    | **NotActions** | 拒否された管理操作から除外された管理操作です。 |
    | **DataActions**  | 拒否されたデータ操作です。 |
    | **NotDataActions** | 拒否されたデータ操作から除外されたデータ操作です。 |

    前のスクリーンショットの例で有効なアクセス許可は、次のとおりです。

    - データ プレーンのすべての記憶域の操作は、計算の操作以外すべて拒否されます。

1. 拒否割り当てのプロパティを表示するには、 **[プロパティ]** をクリックします。

    ![拒否割り当て - プロパティ](./media/deny-assignments-portal/deny-assignment-properties.png)

    **[プロパティ]** ブレードには、拒否割り当ての名前、ID、説明およびスコープが表示されます。 **[Does not apply to children**]\(子には適用しない\) スイッチは、拒否割り当てがサブスコープに継承されるかどうかを示します。 **[保護されているシステム]** スイッチは、Azure によって拒否割り当てが管理されるかどうかを示します。 現在、これはすべてのケースにおいて **[はい]** です。

## <a name="next-steps"></a>次の手順

* [Azure リソースの拒否割り当ての概要](deny-assignments.md)
* [Azure PowerShell を使用して Azure リソースの拒否割り当てを一覧表示する](deny-assignments-powershell.md)
