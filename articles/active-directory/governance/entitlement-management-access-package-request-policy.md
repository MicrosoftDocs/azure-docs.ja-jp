---
title: Azure AD エンタイトルメント管理でアクセス パッケージの要求と承認の設定を変更する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理でアクセス パッケージの要求と承認の設定を変更する方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: edf38013efb14e412fbcd43e06dcf17e61c3bc4a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798768"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージの要求と承認の設定を変更する

アクセス パッケージの管理者は、ポリシーを編集するか新しいポリシーを追加することで、アクセス パッケージを要求できるユーザーをいつでも変更できます。 承認設定を変更することもできます。

この記事では、既存のアクセス パッケージの要求と承認の設定を変更する方法について説明します。

## <a name="choose-between-one-or-multiple-polices"></a>1 つまたは複数のポリシーを選択する

アクセス パッケージを要求できるユーザーを指定するには、ポリシーを使用します。 アクセス パッケージを作成するときに、ポリシーを作成する要求と承認の設定を指定します。 ほとんどのアクセス パッケージのポリシーは 1 つですが、1 つのアクセス パッケージに複数のポリシーを設定できます。 異なるユーザーのセットに異なる要求と承認が設定された割り当てを付与できるようにしたい場合は、アクセス パッケージ用の複数のポリシーを作成します。 たとえば、1 つのポリシーを使用して、内部ユーザーと外部ユーザーを同じアクセス パッケージに割り当てることはできません。 ただし、同じアクセス パッケージ内に 2 つのポリシー (内部ユーザー用のポリシーと外部ユーザー用のポリシー) を作成することができます。 ユーザーに適用されるポリシーが複数ある場合は、要求時に、割り当て先にするポリシーを選択するよう求められます。 次の図は、2 つのポリシーがあるアクセス パッケージを示しています。

![アクセス パッケージ内の複数のポリシー](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| シナリオ | ポリシーの数 |
| --- | --- |
| ディレクトリのユーザー全員に対して、アクセス パッケージの要求と承認の設定を同じにしたい | 1 つ |
| 特定の接続された組織のユーザー全員がアクセス パッケージを要求できるようにしたい | 1 つ |
| ディレクトリのユーザーだけでなくディレクトリ外のユーザーもアクセス パッケージを要求できるようにしたい | 複数 |
| 一部のユーザーに対して異なる承認設定を指定したい | 複数 |
| アクセス パッケージの割り当てに有効期限があるユーザーと、アクセス権を自分で延長できるユーザーを分けたい | 複数 |

複数のポリシーが適用されるときに使用される優先度ロジックについては、[複数のポリシー](entitlement-management-troubleshoot.md#multiple-policies
)を参照してください。

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>要求と承認の設定の既存ポリシーを開く

アクセス パッケージの要求と承認の設定を変更するには、対応するポリシーを開く必要があります。 次の手順を実行して、アクセス パッケージの要求と承認の設定を開きます。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[ポリシー]** をクリックして、編集するポリシーをクリックします。

    ページの下部で [ポリシーの詳細] ウィンドウが開きます。

    ![アクセス パッケージ - [ポリシーの詳細] ウィンドウ](./media/entitlement-management-shared/policy-details.png)

1. **[編集]** をクリックしてポリシーを編集します。

    ![アクセス パッケージ - ポリシーの編集](./media/entitlement-management-shared/policy-edit.png)

1. **[要求]** タブをクリックして、要求と承認の設定を開きます。

1. 次のいずれかの要求セクションにある手順を実行します。

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>要求と承認の設定の新しいポリシーを追加する

異なる要求と承認の設定を持つことが望ましいユーザーのセットがある場合、おそらく新しいポリシーを作成する必要があります。 既存のアクセス パッケージに新しいポリシーを追加するには、次の手順に従います。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[ポリシー]** をクリックし、 **[ポリシーの追加]** をクリックします。

1. ポリシーの名前と説明を入力します。

    ![名前と説明を含むポリシーを作成する](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **[次へ]** をクリックして、 **[要求]** タブを開きます。

1. 次のいずれかの要求セクションにある手順を実行します。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

ポリシーを編集する場合は、 **[更新]** をクリックします。 新しいポリシーを追加する場合は、 **[作成]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [Change lifecycle settings for an access package](entitlement-management-access-package-lifecycle-policy.md) (アクセス パッケージのライフサイクル設定を変更する)
- [View requests for an access package](entitlement-management-access-package-requests.md) (アクセス パッケージの要求を表示する)