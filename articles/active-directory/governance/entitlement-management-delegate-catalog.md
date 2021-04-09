---
title: Azure AD エンタイトルメント管理でカタログ作成者にアクセス ガバナンスを委任する - Azure Active Directory
description: カタログ作成者とプロジェクト マネージャーが自分でアクセスを管理できるよう、IT 管理者からアクセス ガバナンスを委任する方法について説明します。
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
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798428"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でカタログ作成者にアクセス ガバナンスを委任する

カタログは、リソースとアクセス パッケージのコンテナーです。 関連するリソースとアクセス パッケージをグループ化するときは、カタログを作成します。 既定では、グローバル管理者またはユーザー管理者は[カタログを作成する](entitlement-management-catalog-create.md)ことができ、カタログ所有者としてさらにユーザーを追加できます。

自分のカタログを作成できるよう、管理者ではないユーザーに委任する目的で、Azure AD エンタイトルメント管理で定義されたカタログ作成者ロールにそのユーザーを追加できます。 個々のユーザーを追加することも、グループを追加することもできます。グループのメンバーはカタログを作成できるようになります。  カタログを作成した後、所有しているリソースをそのカタログに追加できます。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>IT 管理者としてカタログ作成者に委任する

カタログ作成者ロールにユーザーを割り当てるには、これらの手順に従います。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューの **[エンタイトルメント管理]** セクションで、 **[設定]** をクリックします。

1. **[編集]** をクリックします。

    ![カタログ作成者を追加するための設定](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. **[Delegate entitlement management]\(エンタイトルメント管理の委任\)** セクションで、**[Add catalog creators]\(カタログ作成者の追加\)** をクリックし、このエンタイトルメント管理ロールを委任するユーザーまたはグループを選択します。

1. **[選択]** をクリックします。

1. **[保存]** をクリックします。

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>委任されたロールに Azure portal へのアクセスを許可する

委任されたロール (カタログ作成者、アクセス パッケージ マネージャーなど) がアクセス パッケージを管理するために Azure portal にアクセスできるようにするには、管理ポータルの設定を確認する必要があります。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、**[ユーザー]** をクリックします。

1. 左側のメニューで **[ユーザー設定]** をクリックします。

1. **[Azure AD 管理ポータルへのアクセスを制限する]** が **[いいえ]** に設定されていることを確認します。

    ![Azure AD ユーザー設定 - 管理ポータル](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>次のステップ

- [リソースのカタログを作成および管理する](entitlement-management-catalog-create.md)
- [アクセス パッケージ管理者にアクセス ガバナンスを委任する](entitlement-management-delegate-managers.md)

