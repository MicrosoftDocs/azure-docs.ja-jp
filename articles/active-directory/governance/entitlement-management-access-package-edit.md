---
title: エンタイトルメント管理でアクセス パッケージを非表示または削除する - Azure AD
description: Azure Active Directory のエンタイトルメント管理でアクセス パッケージを非表示または削除する方法について説明します。
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
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798802"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理でアクセス パッケージを非表示または削除する

アクセス パッケージは既定では表示されます。 つまり、ポリシーでユーザーにアクセス パッケージの要求が許可されている場合、そのユーザーには自動的に、マイ アクセス ポータルにアクセス パッケージの一覧が表示されます。 ただし、**[非表示]** 設定を変更して、アクセス パッケージがユーザーのマイ アクセス ポータルに表示されないようにすることができます。

この記事では、アクセス パッケージを非表示または削除する方法について説明します。

## <a name="change-the-hidden-setting"></a>非表示の設定を変更する

アクセス パッケージの **[非表示]** 設定を変更するには、次の手順に従います。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. [概要] ページで、**[編集]** をクリックします。

1. **[非表示]** 設定を設定します。

    **[いいえ]** に設定すると、アクセス パッケージはユーザーのマイ アクセス ポータルに表示されます。

    **[はい]** に設定すると、アクセス パッケージはユーザーのマイ アクセス ポータルに表示されません。 ユーザーがアクセス パッケージを表示できるのは、アクセス パッケージへの直接の **マイ アクセス ポータルのリンク** を持っている場合のみです。 詳細については、[アクセス パッケージを要求するためのリンクの共有](entitlement-management-access-package-settings.md)に関する記事を参照してください。

## <a name="delete-an-access-package"></a>アクセス パッケージを削除する

アクセス パッケージは、アクティブなユーザー割り当てがない場合にのみ削除できます。 アクセス パッケージを削除するには、次の手順に従います。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[割り当て]** をクリックし、すべてのユーザーのアクセス権を削除します。

1. 左側のメニューで、**[概要]** をクリックしてから、**[削除]** をクリックします。

1. 表示される削除メッセージで、 **[はい]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージの割り当てを表示、追加、および削除する](entitlement-management-access-package-assignments.md)
- [レポートとログを表示する](entitlement-management-reports.md)
