---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742346"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

**[管理]** にある **[ユーザー フロー]** を選択し、**[+ 新しいユーザー フロー]** をクリックします。

![新しいユーザー フローを選択する](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

**[推奨]** タブで **[Sign up and sign in]\(サインアップとサインイン\)** を選択します。

![[Sign up and sign in]\(サインアップとサインイン\) ユーザー フローを選択する](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

参照するアプリケーションのユーザー フローの**名前**を入力します。 たとえば、「 `SiUpIn`」のように入力します。

**[ID プロバイダー]** の **[電子メールのサインアップ]** チェック ボックスをオンにします。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。

**[多要素認証]** で、**[有効]** または **[無効]** を選択します。

![名前を入力し、[電子メールのサインアップ] を ID プロバイダーとして選択する](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

**[ユーザー属性と要求]** の **[さらに表示する]** を選択すると、選択できるすべての属性と要求が一覧表示されます。

サインアップ時にコンシューマーから収集する属性を **[属性を収集する]** 列で選択します。 たとえば、**[国/リージョン]**、**[表示名]**、**[郵便番号]** の各チェック ボックスをオンにします。

**[要求を返す]** 列で、サインアップまたはサインイン エクスペリエンスの成功後にアプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、**[表示名]**、**[ID プロバイダー]**、**[郵便番号]**、**[User is new]\(ユーザーは新規\)**、および **[User's Object ID]\(ユーザーのオブジェクト ID\)** を選択します。

Click **OK**.

![いくつかのユーザー属性と要求を選択して [OK] をクリックする](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

**[作成]** をクリックして、ユーザー フローを追加します。 ユーザー フローが **B2C_1_SiUpIn** として表示されます。 名前には **B2C_1_** というプレフィックスが追加されます。

**[ユーザー フローを実行します]** を選択します。 テーブルに指定されている設定を確認し、**[ユーザー フローを実行します]** をクリックします。

![[ユーザー フローを実行します] を選択する](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Setting      | 値  |
| ------------ | ------ |
| **アプリケーション** | Contoso B2C アプリ |
| **応答 URL** | `https://localhost:44316/` |

新しいブラウザー タブが開き、構成したサインアップまたはサインインのコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ユーザー フロー の作成と更新が有効になるまで、最大で 1 分間かかります。
>