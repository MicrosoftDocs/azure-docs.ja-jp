---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742256"
---
アプリケーションでプロファイル編集を有効にする場合は、**プロファイル編集**ユーザー フローを使用します。 このユーザー フローは、プロファイル編集中の顧客のエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

**[管理]** にある **[ユーザー フロー]** を選択し、**[+ 新しいユーザー フロー]** をクリックします。

![新しいユーザー フローを選択する](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

**[推奨]** タブの **[プロファイル編集]** を選択します。

参照するアプリケーションのユーザー フローの**名前**を入力します。 たとえば、「 `SiPe`」のように入力します。

**[ID プロバイダー]** の **[ローカル アカウント サインイン]** チェック ボックスをオンにします。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。

![ID プロバイダーとして [ローカル アカウント サインイン] を選択して [OK] をクリック](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

**[ユーザー属性]** の **[さらに表示する]** をクリックします。 **[属性を収集する]** 列で、コンシューマーがそのプロファイル内で表示したり編集したりすることのできる属性を選択します。 たとえば、**[国/リージョン]**、**[表示名]**、**[郵便番号]** の各チェック ボックスをオンにします。

**[要求を返す]** 列で、プロファイル編集エクスペリエンスの成功後にアプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、**[表示名]** および **[郵便番号]** を選択します。

Click **OK**.

![アプリケーション要求を選択して [OK] をクリック](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

**[作成]** をクリックして、ユーザー フローを追加します。 ユーザー フローが **B2C_1_SiPe** として表示されます。 名前には **B2C_1_** というプレフィックスが追加されます。

**[ユーザー フローを実行します]** を選択します。 テーブルに指定されている設定を確認し、**[ユーザー フローを実行します]** をクリックします。

![ユーザー フローを選択して実行](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Setting      | 値  |
| ------------ | ------ |
| **アプリケーション** | Contoso B2C アプリ |
| **応答 URL** | `https://localhost:44316/` |

新しいブラウザー タブが開き、構成したプロファイル編集のコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ユーザー フロー の作成と更新が有効になるまで、最大で 1 分間かかります。
>