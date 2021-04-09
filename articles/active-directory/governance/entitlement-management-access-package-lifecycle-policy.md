---
title: Azure AD エンタイトルメント管理でアクセス パッケージのライフサイクル設定を変更する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理でアクセス パッケージの要求元情報とライフサイクル設定を変更する方法を説明します。
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
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980139"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージのライフサイクル設定を変更する

アクセス パッケージ管理者は、既存のポリシーを編集することで、アクセス パッケージのライフサイクル設定をいつでも変更できます。 ポリシーの有効期限を変更する場合、既に承認待ちまたは承認済みの状態になっている要求の有効期限は変更されません。

この記事では、既存のアクセス パッケージのライフサイクル設定を変更する方法について説明します。

## <a name="open-requestor-information"></a>要求元情報を開く
アクセス パッケージにアクセスするための正しい権限をユーザーに確実に与えるために、カスタムの質問を構成し、特定のアクセス パッケージへのアクセスを要求しているユーザーに質問できます。 構成オプションには、ローカライズ、必須または任意、テキストまたは複数選択式の回答形式などがあります。 要求元には、パッケージを要求したときにこの質問が表示されます。承認者には質問に対する回答が表示されるため、それを意志決定に役立てます。 アクセス パッケージの質問は次の手順で構成します。

## <a name="open-lifecycle-settings"></a>ライフサイクル設定を開く

アクセス パッケージのライフサイクル設定を変更するには、対応するポリシーを開く必要があります。 次の手順を実行して、アクセス パッケージのライフサイクル設定を開きます。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[ポリシー]** をクリックして、編集するライフサイクル設定があるポリシーをクリックします。

    ページの下部で [ポリシーの詳細] ウィンドウが開きます。

    ![アクセス パッケージ - [ポリシーの詳細] ウィンドウ](./media/entitlement-management-shared/policy-details.png)

1. **[編集]** をクリックしてポリシーを編集します。

    ![アクセス パッケージ - ポリシーの編集](./media/entitlement-management-shared/policy-edit.png)

1. **[ライフサイクル]** タブをクリックして、ライフサイクル設定を開きます。

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>次の手順

- [Change request and approval settings for an access package](entitlement-management-access-package-request-policy.md) (アクセス パッケージの要求と承認の設定を変更する)