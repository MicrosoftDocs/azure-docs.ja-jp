---
title: 条件付きアクセスで利用規約を必須にする - Azure Active Directory
description: このクイック スタートでは、選択したクラウド アプリへのアクセスが Azure Active Directory の条件付きアクセスによって許可される前に、使用条件への同意が要求されるようにする方法を学習します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab484e8caaffaf57f19f1fcd1e65f4b8e723f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077899"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>クイック スタート:クラウド アプリにアクセスする前に利用規約への同意を要求する

環境内の特定のクラウド アプリにアクセスする前に、使用条件 (ToU) を受け入れる形式でユーザーからの同意を得る場合があります。 Azure Active Directory (Azure AD) の条件付きアクセスでは、次のものが提供されます。

- ToU を構成する簡単な方法
- 条件付きアクセス ポリシーを介して使用条件に同意を要求するオプション  

このクイック スタートでは、環境内で選択したクラウド アプリに対して ToU への同意を必要とする [Azure AD の条件付きアクセス ポリシー](./overview.md)を構成する方法について説明します。

:::image type="content" source="./media/require-tou/5555.png" alt-text="Azure portal のスクリーンショット。[Require T O U for Isabella]\(Isabella に対して T O U を必須にする\) という名前のポリシーを定義するペインが表示されています。" border="false":::

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートのシナリオを完了するための要件を次に示します。

- **Azure AD Premium エディションへのアクセス** - Azure AD の条件付きアクセスは Azure AD Premium の機能です。
- **Isabella Simonsen というテスト アカウント** - テスト アカウントの作成方法がわからない場合は、「[クラウド ベースのユーザーを追加する](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)」を参照してください。

## <a name="test-your-sign-in"></a>サインインをテストする

この手順の目的は、条件付きアクセス ポリシーがないサインイン エクスペリエンスの印象を確認することです。

**サインインをテストするには:**

1. Isabella Simonsen として [Azure portal](https://portal.azure.com/) にサインインします。
1. サインアウトします。

## <a name="create-your-terms-of-use"></a>使用条件を作成する

このセクションでは、サンプル ToU を作成する手順について説明します。 ToU を作成するときに、 **[条件付きアクセス ポリシー テンプレートを使用して適用します]** の値を選択します。 **[カスタム ポリシー]** を選択すると、ToU が作成されるとすぐに、新しい条件付きアクセス ポリシーを作成するためのダイアログが開かれます。

**使用条件を作成するには:**

1. Microsoft Word で、新しいドキュメントを作成します。
1. 「**My terms of use**」と入力し、ドキュメントをコンピューターに **mytou.pdf** として保存します。
1. [Azure portal](https://portal.azure.com) に全体管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. Azure Portal の左側のナビゲーション バーで、 **[Azure Active Directory]** をクリックします。

   ![Azure Active Directory](./media/require-tou/02.png)

1. **[Azure Active Directory]** ページの **[セキュリティ]** セクションで、 **[条件付きアクセス]** をクリックします。

   ![条件付きアクセス](./media/require-tou/03.png)

1. **[管理]** セクションで、 **[使用条件]** をクリックします。

   :::image type="content" source="./media/require-tou/04.png" alt-text="[Azure Active Directory] ページの [管理] セクションのスクリーンショット。[使用条件] 項目が強調表示されています。" border="false":::

1. 上部のメニューで **[新しい条件]** をクリックします。

   :::image type="content" source="./media/require-tou/05.png" alt-text="[Azure Active Directory] ページのメニューを示すスクリーンショット。[新しい条件] 項目が強調表示されています。" border="false":::

1. **[新しい利用規約]** ページで、次の操作を行います。

   :::image type="content" source="./media/require-tou/112.png" alt-text="[New terms of use]\(新しい使用条件\) ページのスクリーンショット。名前、表示名、ドキュメント、言語、条件付きアクセス、および条件を展開するかどうかの切り替えボタンが強調表示されています。" border="false":::

   1. **[名前]** ボックスに「**My TOU**」と入力します。
   1. **[表示名]** ボックスに、「**My TOU**」と入力します。
   1. 使用条件 PDF ファイルをアップロードします。
   1. **[言語]** で **[英語]** を選択します。
   1. **[ユーザーは使用条件を展開する必要があります]** で、 **[オン]** を選択します。
   1. **[条件付きアクセス ポリシー テンプレートを使用して適用します]** で **[カスタム ポリシー]** を選択します。
   1. **Create** をクリックしてください。

## <a name="create-your-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

このセクションでは、必要な条件付きアクセス ポリシーを作成する方法について説明します。 このクイック スタートのシナリオでは、以下を使用します。

- ToU の同意を必要とするクラウド アプリのプレースホルダーとしての Azure portal。 
- 条件付きアクセス ポリシーをテストするサンプル ユーザー。  

ポリシーに以下の内容を設定します。

| 設定 | 値 |
| --- | --- |
| ユーザーとグループ | Isabella Simonsen |
| クラウド アプリ | Microsoft Azure の管理 |
| アクセス権の付与 | My TOU |

:::image type="content" source="./media/require-tou/1234.png" alt-text="ポリシーを定義する Azure portal ペインのスクリーンショット。矢印は、ポリシーによって My T O U へのアクセスが許可されることと、1 人のユーザーと 1 つのアプリが含まれることを示しています。" border="false":::

**条件付きアクセス ポリシーを構成するには:**

1. **[新規]** ページの **[名前]** ボックスに、「**Require TOU for Isabella**」と入力します。

   ![名前](./media/require-tou/71.png)

1. **[割り当て]** セクションで **[ユーザーとグループ]** をクリックします。

   :::image type="content" source="./media/require-tou/06.png" alt-text="ポリシーを定義する Azure portal ペインの [割り当て] セクションのスクリーンショット。何も選択されていない状態の [ユーザーとグループ] 項目が表示されています。" border="false":::

1. **[ユーザーとグループ]** ページで、次の手順に従います。

   :::image type="content" source="./media/require-tou/24.png" alt-text="[ユーザーとグループ] ページの [Include]\(含める\) タブのスクリーンショット。[ユーザーとグループの選択] および [ユーザーとグループ] が選択されています。[選択] が強調表示されています。" border="false":::

   1. **[ユーザーとグループの選択]** をクリックし、 **[ユーザーとグループ]** を選択します。
   1. **[選択]** をクリックします。
   1. **[選択]** ページで **[Isabella Simonsen]** を選択し、 **[選択]** をクリックします。
   1. **[ユーザーとグループ]** ページで、 **[完了]** をクリックします。
1. **[クラウド アプリ]** をクリックします。

   :::image type="content" source="./media/require-tou/08.png" alt-text="ポリシーを定義する Azure portal ペインの [割り当て] セクションのスクリーンショット。何も選択されていない状態の [クラウド アプリ] 項目が表示されています。" border="false":::

1. **[クラウド アプリ]** ページで、次の手順に従います。

   ![クラウド アプリを選択](./media/require-tou/26.png)

   1. **[アプリを選択]** をクリックします。
   1. **[選択]** をクリックします。
   1. **[選択]** ページで **[Microsoft Azure の管理]** を選択し、 **[選択]** をクリックします。
   1. **[クラウド アプリ]** ページで、 **[完了]** をクリックします。
1. **[アクセス制御]** セクションで、 **[許可]** をクリックします。

   ![アクセス制御](./media/require-tou/10.png)

1. **[許可]** ページで、次の手順に従います。

   ![Grant](./media/require-tou/111.png)

   1. **[アクセス権の付与]** を選択します。
   1. **[My TOU]** を選択します。
   1. **[選択]** をクリックします。
1. **[ポリシーを有効にする]** セクションで **[オン]** をクリックします。

   ![ポリシーを有効にする](./media/require-tou/18.png)

1. **Create** をクリックしてください。

## <a name="evaluate-a-simulated-sign-in"></a>シミュレートされたサインインを評価する

条件付きアクセス ポリシーを構成したら、期待どおりに動作しているかどうかを確認してみましょう。 最初の手順として、条件付きアクセスの What If ポリシー ツールを使用して、テスト ユーザーのサインインをシミュレートします。 シミュレーションでは、サインインがポリシーに与える影響を推定し、シミュレーション レポートが生成されます。  

**What If** ポリシー評価ツールを初期化するには、次のように設定します。

- ユーザーに「**Isabella Simonsen**」
- クラウド アプリに「**Microsoft Azure の管理**」

**[What If]** をクリックすると、シミュレート レポートが作成され、次のように表示されます。

- **[適用するポリシー]** の下に "**Require TOU for Isabella**"
- **[制御の許可]** として "**My TOU**"

![What If ポリシー ツール](./media/require-tou/79.png)

**条件付きアクセス ポリシーを評価するには:**

1. [[条件付きアクセス - ポリシー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) ページで、上部のメニューの **[What If]** をクリックします。  

   ![What If](./media/require-tou/14.png)

1. **[ユーザー]** をクリックし、 **[Isabella Simonsen]** を選択し、 **[選択]** をクリックします。

   ![User](./media/require-tou/15.png)

1. クラウド アプリを選択するには:

   :::image type="content" source="./media/require-tou/16.png" alt-text="[クラウド アプリ] セクションのスクリーンショット。テキストは、1 つのアプリが選択されていることを示しています。" border="false":::

   1. **[クラウド アプリ]** をクリックします。
   1. **[クラウド アプリ]** ページで、 **[アプリの選択]** をクリックします。
   1. **[選択]** をクリックします。
   1. **[選択]** ページで **[Microsoft Azure の管理]** を選択し、 **[選択]** をクリックします。
   1. [クラウド アプリ] ページで、 **[完了]** をクリックします。
1. **[What If]** をクリックします。

## <a name="test-your-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする

前のセクションでは、シミュレートされたサインインを評価する方法を学びました。 期待どおりに動作することを確認するには、シミュレーションに加え、条件付きアクセス ポリシーもテストすることをお勧めします。

ポリシーをテストするには、**Isabella Simonsen** テスト アカウントを使用して [Azure Portal](https://portal.azure.com) にサインインしてみます。 使用条件への同意を求めるダイアログが表示されるはずです。

:::image type="content" source="./media/require-tou/57.png" alt-text="[Identity Security Protection terms of use]\(ID のセキュリティ保護の使用条件\) というタイトルのダイアログ ボックスのスクリーンショット。[拒否] および [Accept]\(承諾\) ボタンと [My T O U] とラベルされたボタンが表示されています。" border="false":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、テスト ユーザーと条件付きアクセス ポリシーを削除します。

- Azure AD ユーザーの削除方法がわからない場合は、「[Azure AD からユーザーを削除する](../fundamentals/add-users-azure-active-directory.md#delete-a-user)」を参照してください。
- ポリシーを削除するには、ポリシーを選択し、クイック アクセス ツール バーの **[削除]** をクリックします。

    :::image type="content" source="./media/require-tou/33.png" alt-text="[Require M F A for Azure portal users]\(Azure portal のユーザーに M F A を要求\) という名前のポリシーを示すスクリーンショット。ショートカット メニューが表示され、[削除] が強調表示されています。" border="false":::

- 使用条件を削除するには、使用条件を選択し、上部のツール バーの **[条件の削除]** をクリックします。

    :::image type="content" source="./media/require-tou/29.png" alt-text="使用条件ドキュメントの一覧表の一部を示すスクリーンショット。[My T O U] ドキュメントが表示されています。メニューでは、[条件の削除] が強調表示されています。" border="false":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [特定のアプリで MFA を必須にする](../authentication/tutorial-enable-azure-mfa.md)