---
title: クイック スタート - Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して多要素認証 (MFA) を必要にする | Microsoft Docs
description: このクイック スタートでは、Azure Active Directory (Azure AD) の条件付きアクセスを使用して、アクセスされるクラウド アプリケーションの種類と認証要件を結び付ける方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: microsoftguyjflo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36cb3b1555a339249528e290e376454dd78f1e53
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509065"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>クイック スタート:Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする

ユーザーのサインイン エクスペリエンスを簡易化するために、ユーザー名とパスワードを使用してクラウド アプリにサインインできるようにすることができます。 ただし、多くの環境には、多要素認証 (MFA) など、より強力な形式のアカウント確認を必要にすることが勧められるアプリが少なくともいくつかあります。 これには、組織のメール システムや HR アプリにアクセスする場合などがあります。 Azure Active Directory (Azure AD) では、条件付きアクセス ポリシーを使用してこの目標を達成できます。

このクイック スタートでは、環境内で選択したクラウド アプリに対して多要素認証を必要とする [Azure AD の条件付きアクセス ポリシー](../active-directory-conditional-access-azure-portal.md)を構成する方法について説明します。

![Azure portal での条件付きアクセス ポリシーの例](./media/app-based-mfa/32.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートのシナリオを完了するための要件を次に示します。

- **Azure AD Premium エディションへのアクセス** - Azure AD の条件付きアクセスは Azure AD Premium の機能です。
- **Isabella Simonsen というテスト アカウント** - テスト アカウントの作成方法がわからない場合は、「[クラウド ベースのユーザーを追加する](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)」を参照してください。

このクイック スタートのシナリオでは、テスト アカウントに対して、ユーザーごとの MFA が有効にされていない必要があります。 詳細については、「[ユーザーに 2 段階認証を要求する方法](../authentication/howto-mfa-userstates.md)」を参照してください。

## <a name="test-your-experience"></a>エクスペリエンスをテストする

この手順の目的は、条件付きアクセス ポリシーがないエクスペリエンスの印象を確認することです。

**環境を初期化するには:**

1. Isabella Simonsen として Azure portal にサインインします。
1. サインアウトします。

## <a name="create-your-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

このセクションでは、必要な条件付きアクセス ポリシーを作成する方法について説明します。 このクイック スタートのシナリオでは、以下を使用します。

- MFA を必要とするクラウド アプリのプレースホルダーとしての Azure Portal 
- 条件付きアクセス ポリシーをテストするサンプル ユーザー  

ポリシーに以下の内容を設定します。

| Setting | 値 |
| --- | --- |
| ユーザーとグループ | Isabella Simonsen |
| クラウド アプリ | Microsoft Azure の管理 |
| アクセス権の付与 | 多要素認証が必要です |

![展開された条件付きアクセス ポリシー](./media/app-based-mfa/31.png)

**条件付きアクセス ポリシーを構成するには:**

1. [Azure portal](https://portal.azure.com) に全体管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. Azure Portal の左側のナビゲーション バーで、 **[Azure Active Directory]** をクリックします。

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. **[Azure Active Directory]** ページの **[セキュリティ]** セクションで、 **[条件付きアクセス]** をクリックします。

   ![条件付きアクセス](./media/app-based-mfa/03.png)

1. **[条件付きアクセス]** ページで、上部のツール バーの **[新しいポリシー]** をクリックします。

   ![Add](./media/app-based-mfa/04.png)

1. **[新規]** ページの **[名前]** テキストボックスに、「**Azure Portal にアクセスするには MFA が必要です**」と入力します。

   ![Name](./media/app-based-mfa/05.png)

1. **[割り当て]** セクションで **[ユーザーとグループ]** をクリックします。

   ![ユーザーとグループ](./media/app-based-mfa/06.png)

1. **[ユーザーとグループ]** ページで、次の手順を実行します。

   ![ユーザーとグループ](./media/app-based-mfa/24.png)

   1. **[ユーザーとグループの選択]** をクリックし、 **[ユーザーとグループ]** を選択します。
   1. **[選択]** をクリックします。
   1. **[選択]** ページで **[Isabella Simonsen]** を選択し、 **[選択]** をクリックします。
   1. **[ユーザーとグループ]** ページで、 **[完了]** をクリックします。

1. **[クラウド アプリ]** をクリックします。

   ![クラウド アプリ](./media/app-based-mfa/08.png)

1. **[クラウド アプリ]** ページで、次の手順を実行します。

   ![クラウド アプリを選択](./media/app-based-mfa/26.png)

   1. **[アプリを選択]** をクリックします。
   1. **[選択]** をクリックします。
   1. **[選択]** ページで **[Microsoft Azure の管理]** を選択し、 **[選択]** をクリックします。
   1. **[クラウド アプリ]** ページで、 **[完了]** をクリックします。

1. **[アクセス制御]** セクションで、 **[許可]** をクリックします。

   ![アクセス制御](./media/app-based-mfa/10.png)

1. **[許可]** ページで、次の手順を実行します。

   ![許可](./media/app-based-mfa/11.png)

   1. **[アクセス権の付与]** を選択します。
   1. **[多要素認証が必要です]** を選択します。
   1. **[選択]** をクリックします。

1. **[ポリシーを有効にする]** セクションで **[オン]** をクリックします。

   ![ポリシーを有効にする](./media/app-based-mfa/18.png)

1. **Create** をクリックしてください。

## <a name="evaluate-a-simulated-sign-in"></a>シミュレートされたサインインを評価する

条件付きアクセス ポリシーを構成したら、期待どおりに動作しているかどうかを確認してみましょう。 最初の手順として、条件付きアクセスの What-If ポリシー ツールを使用して、テスト ユーザーのサインインをシミュレートします。 シミュレーションでは、このサインインがポリシーに与える影響を推定し、シミュレーション レポートが生成されます。  

**What If** ポリシー評価ツールを初期化するには、次のように設定します。

- ユーザーに「**Isabella Simonsen**」
- クラウド アプリに「**Microsoft Azure の管理**」

**[What If]** をクリックすると、シミュレート レポートが作成され、次のように表示されます。

- **[適用するポリシー]** の下に "**Azure Portal にアクセスするには MFA が必要です**"
- **[制御の許可]** に "**多要素認証が必要です**"

![What If ポリシー ツール](./media/app-based-mfa/23.png)

**条件付きアクセス ポリシーを評価するには:**

1. [[条件付きアクセス - ポリシー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) ページで、上部のメニューの **[What If]** をクリックします。  

   ![What If](./media/app-based-mfa/14.png)

1. **[ユーザー]** をクリックし、 **[Isabella Simonsen]** を選択し、 **[選択]** をクリックします。

   ![User](./media/app-based-mfa/15.png)

1. クラウド アプリを選択するには、次の手順を実行します。

   ![クラウド アプリ](./media/app-based-mfa/16.png)

   1. **[クラウド アプリ]** をクリックします。
   1. **[クラウド アプリ]** ページで、 **[アプリの選択]** をクリックします。
   1. **[選択]** をクリックします。
   1. **[選択]** ページで **[Microsoft Azure の管理]** を選択し、 **[選択]** をクリックします。
   1. [クラウド アプリ] ページで、 **[完了]** をクリックします。

1. **[What If]** をクリックします。

## <a name="test-your-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする

前のセクションでは、シミュレートされたサインインを評価する方法を学びました。 期待どおりに動作することを確認するには、シミュレーションに加え、条件付きアクセス ポリシーもテストすることをお勧めします。

ポリシーをテストするには、**Isabella Simonsen** テスト アカウントを使用して [Azure portal](https://portal.azure.com) にサインインしてみます。 追加のセキュリティ確認のために、アカウントを設定する必要があるというダイアログが表示されます。

![多要素認証](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、テスト ユーザーと条件付きアクセス ポリシーを削除します。

- Azure AD ユーザーの削除方法がわからない場合は、「[Azure AD からユーザーを削除する](../fundamentals/add-users-azure-active-directory.md#delete-a-user)」を参照してください。
- ポリシーを削除するには、ポリシーを選択し、クイック アクセス ツール バーの **[削除]** をクリックします。

    ![多要素認証](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [使用条件への同意を要求する](require-tou.md)
> [セッションのリスクが検出されたときにアクセスをブロックする](app-sign-in-risk.md)
