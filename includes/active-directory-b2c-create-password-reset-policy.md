---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742230"
---
アプリケーションできめ細かなパスワード リセットを有効にするには、**パスワード リセット** ユーザー フローを使用します。 [ここ](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md)に示されているテナント全体のパスワード リセット オプションに注意してください。 このユーザー フローは、パスワード リセット中の顧客のエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

**[管理]** にある **[ユーザー フロー]** を選択し、**[+ 新しいユーザー フロー]** をクリックします。

![新しいユーザー フローを選択する](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

**[推奨]** タブの **[パスワードのリセット]** を選択します。

参照するアプリケーションのユーザー フローの**名前**を入力します。 たとえば、「 `SSPR`」のように入力します。

**[ID プロバイダー]** で、**[Reset password using email address]\(メール アドレスを使用してパスワードをリセットする\)** チェック ボックスをオンにします。

![名前を入力し、ID プロバイダーとして [Reset password using email address]\(メール アドレスを使用してパスワードをリセットする\) を選択](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

**[アプリケーション要求]** で **[さらに表示する]** をクリックし、パスワード リセット エクスペリエンスの成功後にアプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、**[User's Object ID] (ユーザーのオブジェクト ID)** を選択します。

Click **OK**.

![アプリケーション要求を選択して [OK] をクリック](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

**[作成]** をクリックして、ユーザー フローを追加します。 ユーザー フローが **B2C_1_SSPR** として表示されます。 名前には **B2C_1_** というプレフィックスが追加されます。

**[ユーザー フローを実行します]** をクリックします。 テーブルに指定されている設定を確認し、**[ユーザー フローを実行します]** をクリックします。

![ユーザー フローを選択して実行](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Setting      | 値  |
| ------------ | ------ |
| **アプリケーション** | Contoso B2C アプリ |
| **応答 URL の選択** | `https://localhost:44316/` |

新しいブラウザー タブが開き、アプリケーションでパスワード リセットのコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
>
