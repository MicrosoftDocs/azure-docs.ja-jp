---
title: Azure AD エンタイトルメント管理でアクセス パッケージ管理者にアクセス ガバナンスを委任する - Azure Active Directory
description: アクセス パッケージ管理者とプロジェクト マネージャーが自分でアクセスを管理できるよう、IT 管理者からアクセス ガバナンスを委任する方法について説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e81389ec953829115062a31a019caf040850cbea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798641"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージ管理者にアクセス ガバナンスを委任する

カタログ内のアクセス パッケージの作成と管理を委任するには、アクセス パッケージ管理者ロールにユーザーを追加します。 アクセス パッケージ管理者は、カタログ内のリソースに対するアクセスをユーザーが要求するニーズを理解しておく必要があります。 たとえば、あるプロジェクトにあるカタログが使用される場合、プロジェクト リーダーはそのカタログのアクセス パッケージ管理者になることがあります。  アクセス パッケージ管理者はカタログにリソースを追加できませんが、カタログ内のアクセス パッケージやポリシーを管理できます。  アクセス パッケージ管理者に委任すると、その管理者は次のことを担当します。

- カタログ内のリソースに対してユーザーに与えられるロール
- アクセスを必要とするユーザー
- アクセス要求の承認が必要なユーザー
- プロジェクトの継続期間

この動画では、カタログの所有者からアクセス パッケージ管理者にアクセス ガバナンスを委任する方法を概説します。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>カタログ所有者としてアクセス パッケージ管理者に委任する

アクセス パッケージ管理者のロールにユーザーを割り当てるには、以下の手順のようにします。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、管理者を追加するカタログを開きます。

1. 左側のメニューで **[ロールと管理者]** をクリックします。

    ![カタログのロールと管理者](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **[アクセス パッケージ管理者の追加]** をクリックし、それらのロールのメンバーを選択します。

1. **[選択]** をクリックすると、これらのメンバーが追加されます。

## <a name="remove-an-access-package-manager"></a>アクセス パッケージ管理者を削除する

アクセス パッケージ管理者のロールからユーザーを削除するには、以下の手順のようにします。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、管理者を追加するカタログを開きます。

1. 左側のメニューで **[ロールと管理者]** をクリックします。

1. 削除するアクセス パッケージ管理者の横にチェックマークを追加します。

1. **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [新しいアクセス パッケージを作成する](entitlement-management-access-package-create.md)
