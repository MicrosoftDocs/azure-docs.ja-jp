---
title: Azure Active Directory B2C の Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されているコンシューマー向けアプリケーションで Multi-factor Authentication を有効にする方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033522"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C の多要素認証 | Microsoft Docs

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) は [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) と直接統合されているため、ご自分のアプリケーションのサインアップおよびサインイン エクスペリエンスに第 2 のセキュリティ層を簡単に追加できます。 コードを 1 行も記述する必要なく多要素認証を有効にできます。 サインアップおよびサインイン ユーザー フローを既に作成していても、多要素認証を有効にできます。

この機能を利用すると、次のようなシナリオをアプリケーションで処理するのに役立ちます。

- 多要素認証が、あるアプリケーションのアクセスには必要ないが、別のアプリケーションのアクセスには必要な場合。 たとえば、顧客が自動車保険アプリケーションにサインインするにはソーシャルまたはローカル アカウントを使用できますが、同じディレクトリに登録されている住宅保険アプリケーションにアクセスするには事前に電話番号を確認する必要があるような場合です。
- 通常のアプリケーションへのアクセスには多要素認証は必要ないが、アプリケーション内の機密性が高い部分へのアクセスにはそれが必要である場合。 たとえば、顧客が銀行取引アプリケーションにサインインし、口座の残高を確認するにはソーシャルまたはローカル アカウントを使用できますが、ネット送金を行うには事前に電話番号の確認が必要になるような場合です。

## <a name="set-multi-factor-authentication"></a>多要素認証の設定

::: zone pivot="b2c-user-flow"

1. [Azure ポータル](https://portal.azure.com)
1. 上部のメニューにある **[ディレクトリとサブスクリプション]** フィルターを使用して、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. MFA を有効にするユーザー フローを選択します。 たとえば、*B2C_1_signinsignup* です。
1. **[プロパティ]** を選択します。
1. **[多要素認証]** セクションで、目的の **[MFA メソッド]** を選択し、 **[MFA enforcement]\(MFA の適用\)** で **[常にオン]** または [**Conditional (Recommended)]\(条件付き (推奨)\)** を選択します。
   > [!NOTE]
   >
   > - **[Conditional (Recommended)]\(条件付き (推奨)\)** を選択した場合、[条件付きアクセスをユーザー フローに追加](conditional-access-user-flow.md)し、ポリシーを適用するアプリを指定する必要もあります。
   > - サインアップのユーザー フローでは、多要素認証 (MFA) は既定で無効になっています。 電話でのサインアップを使用したユーザー フローで MFA を有効にすることはできますが、電話番号がプライマリ ID として使用されるため、2 番目の認証要素に使用できる唯一のオプションは電子メールによるワンタイム パスコードです。

1. **[保存]** を選択します。 このユーザー フローに対して MFA が有効になります。

エクスペリエンスを検証するには、**[ユーザー フローを実行します]** を使用します。 次のシナリオで確認してみましょう。

多要素認証の手順が実行される前に、顧客のアカウントがテナントに作成されます。 この手順の過程で、顧客は自分の電話番号を入力し、確認することを求められます。 検証が成功した場合、後で使用できるように電話番号がアカウントに関連付けられます。 顧客がキャンセルまたは中止した場合であっても、多要素認証が有効になっている場合、次にサインインするときに顧客は電話番号の確認を再度求められる場合があります。

::: zone-end

::: zone pivot="b2c-custom-policy"

多要素認証を有効にするには、GitHub からカスタム ポリシー スターター パックを取得し、**SocialAndLocalAccountsWithMFA** スターター パック内の XML ファイルを Azure AD B2C テナント名で更新します。 **SocialAndLocalAccountsWithMFA** を使用すると、ソーシャル、ローカル、および多要素認証の各オプションを有効にできます。 詳細については、「[Active Directory B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」を参照してください。 

::: zone-end
