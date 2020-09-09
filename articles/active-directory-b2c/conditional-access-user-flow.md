---
title: Azure AD B2C のユーザー フローに条件付きアクセスを追加する
description: Azure AD B2C のユーザー フローに条件付きアクセスを追加する方法について説明します。 ユーザー フローで多要素認証 (MFA) 設定と条件付きアクセス ポリシーを構成して、ポリシーを適用し、危険なサインインを修復します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfac3b80e772e7b359b1e926d5fb84e447a8fb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270796"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フローに条件付きアクセスを追加する

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

対象の Azure Active Directory B2C ユーザー フローに条件付きアクセスを追加して、アプリケーションへの危険なサインインを管理できます。 Azure AD B2C での Identity Protection と条件付きアクセスの統合により、危険なサインイン動作を識別するポリシーを設定し、ユーザーまたは管理者によるさらなるアクションを必要とするポリシーを適用できます。 次に示すのは、Azure AD B2C のユーザー フローで条件付きアクセスを有効にするコンポーネントです。

- **ユーザー フロー**。 サインインおよびサインアップ プロセスでユーザーを案内するユーザー フローを作成します。 ユーザー フローの設定で、ユーザーがユーザー フローに従うときに条件付きアクセス ポリシーをアクティブにするかどうかを指定します。
- **ユーザーをユーザー フローに誘導するアプリケーション**。 アプリ内でユーザー フロー エンドポイントを指定して、ユーザーを適切なサインアップおよびサインイン ユーザー フローに誘導するようにアプリを構成します。
- **条件付きアクセス ポリシー**。 [条件付きアクセス ポリシーを作成](conditional-access-identity-protection-setup.md)し、ポリシーを適用する対象のアプリを指定します。 ユーザーがアプリのサインインまたはサインアップ ユーザー フローに従う際に、条件付きアクセス ポリシーで Identity Protection 信号を使用して危険なサインインが識別され、必要に応じて適切な修復アクションが提示されます。

条件付きアクセスは、ユーザー フローの最新バージョンでサポートされています。 条件付きアクセス ポリシーは、作成時に新しいユーザー フローに追加できます。また、条件付きアクセスがサポートされているバージョンであれば、既存のユーザー フローに追加することもできます。 既存のユーザー フローに条件付きアクセスを追加する場合は、次の 2 つの設定を確認する必要があります。

- **[多要素認証 (MFA)]** : ユーザーは、現在、SMS または音声によるワンタイム コードか、メールによるワンタイム パスワードを多要素認証に使用できます。 MFA の設定は、条件付きアクセスの設定とは別になっています。 MFA を **[常にオン]** に設定すると、条件付きアクセスの設定に関係なく MFA が常に必須になります。 または、MFA を **[条件付き]** に設定すると、アクティブな条件付きアクセス ポリシーで MFA が求められる場合にのみ MFA が必須になります。

- **条件付きアクセス**:この設定は常に **[オン]** にする必要があります。 通常、この設定は、トラブルシューティングや移行中、またはレガシー実装に対してのみ **[オフ]** にします。

Azure AD B2C における [Identity Protection と条件付きアクセス](conditional-access-identity-protection-overview.md)の詳細を確認するか、その[設定方法](conditional-access-identity-protection-setup.md)を確認してください。

## <a name="add-conditional-access-to-a-new-user-flow"></a>新しいユーザー フローに条件付きアクセスを追加する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローを作成する]** ページで、ユーザー フローの種類を選択します。
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。 (ユーザー フローのバージョンに関する[詳細情報](user-flow-versions.md))。

    ![プロパティが強調表示されている、Azure portal 内の [ユーザー フローの作成] ページ](./media/tutorial-create-user-flows/select-version.png)

1. ユーザー フローの **[名前]** を入力します。 たとえば、「*signupsignin1*」と入力します。
1. **[ID プロバイダー]** セクションで、このユーザー フローに対して許可する ID プロバイダーを選択します。
2. **[多要素認証]** セクションで、目的の **MFA メソッド**を選択し、 **[MFA enforcement]\(MFA の適用\)** で **[Conditional (Recommended)]\(条件付き (推奨)\)** を選択します。
 
   ![多要素認証を構成する](media/conditional-access-user-flow/configure-mfa.png)

1. **[条件付きアクセス]** セクションで、 **[Enforce conditional access policies]\(条件付きアクセス ポリシーを適用する\)** チェック ボックスをオンにします。

   ![条件付きアクセスの設定を構成する](media/conditional-access-user-flow/configure-conditional-access.png)

1. **[ユーザー属性と要求]** セクションで、サインアップ中にユーザーから収集して送信する要求と属性を選択します。 たとえば、 **[Show more]\(さらに表示\)** を選択し、 **[国または地域]** と **[表示名]** の属性と要求を選択します。 **[OK]** を選択します。

    ![3 つの要求が選択されている [属性と要求の選択] ページ](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. **[作成]** をクリックして、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

## <a name="add-conditional-access-to-an-existing-user-flow"></a>既存のユーザー フローに条件付きアクセスを追加する

> [!NOTE]
> 既存のユーザー フローは、条件付きアクセスがサポートされているバージョンである必要があります。 これらのユーザー フローのバージョンには、"**推奨**" というラベルが付けられています。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。

1. **[ポリシー]** で **[ユーザー フロー]** を選択します。 次に、ユーザー フローを選択します。

1. **[プロパティ]** を選択し、このユーザー フローで条件付きアクセスがサポートされていることを確認します。そのためには、 **[プロパティ]** を選択し、 **[条件付きアクセス]** というラベルの設定を探します。
 
   ![[プロパティ] で MFA と条件付きアクセスを構成する](media/conditional-access-user-flow/add-conditional-access.png)

1. **[多要素認証]** セクションで、目的の **MFA メソッド**を選択し、 **[MFA enforcement]\(MFA の適用\)** で **[Conditional (Recommended)]\(条件付き (推奨)\)** を選択します。
 
1. **[条件付きアクセス]** セクションで、 **[Enforce conditional access policies]\(条件付きアクセス ポリシーを適用する\)** チェック ボックスをオンにします。

1. **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

対象のユーザー フローでの条件付きアクセスをテストするには、[条件付きアクセス ポリシーを作成](conditional-access-identity-protection-setup.md)し、前述のようにユーザー フローで条件付きアクセスを有効にします。 

### <a name="prerequisites"></a>前提条件

- 危険なサインインのポリシーを作成するには、Azure AD B2C Premium 2 が必要です。 Premium P1 テナントでは、場所、アプリ、またはグループベースのポリシーを作成できます。
- テスト目的では、`https://jwt.ms` [テスト用 Web アプリケーションを登録](tutorial-register-applications.md)できます。これは、トークンのデコードされたコンテンツを表示する、Microsoft が所有する Web アプリケーションです (トークンのコンテンツがお使いのブラウザー外に出ることはありません)。 
- 危険なサインインをシミュレートするには、Tor Browser をダウンロードし、ユーザー フロー エンドポイントへのサインインを試みます。
- 次の設定を使用して、[条件付きアクセス ポリシーを作成](conditional-access-identity-protection-setup.md)します。
   
   - **[ユーザーとグループ]** で、テスト ユーザーを選択します ( **[すべてのユーザー]** を選択しないでください。選択すると、自分がサインインできなくなる可能性があります)。
   - **[クラウド アプリまたは操作]** で、 **[アプリの選択]** を選択し、次に証明書利用者アプリケーションを選択します。
   - [条件] で、 **[サインイン リスク]** を選択し、 **[高]** 、 **[中]** 、 **[低]** のリスク レベルを選択します。
   - **[付与]** で、 **[アクセスをブロックする]** を選択します。

      ![リスク検出](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

### <a name="run-the-user-flow"></a>ユーザー フローを実行する

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。 **[アプリケーション]** で、 *[webapp1]* を選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。

   ![[ユーザー フローを実行します] ボタンが強調表示されているポータル内の [ユーザー フローを実行します] ページ](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. **[ユーザー フロー エンドポイントを実行]** の URL をコピーします。

1. 危険なサインインをシミュレートするには、[Tor Browser](https://www.torproject.org/download/) を開き、プレビュー手順でコピーした URL を使用して登録済みのアプリにサインインします。

1. サインイン ページに要求された情報を入力し、サインインを試みます。 トークンが `https://jwt.ms` に返され、表示されます。 jwt.ms のデコードされたトークンで、サインインがブロックされたことがわかります。

   ![ブロックされたサインインをテストする](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="next-steps"></a>次のステップ

[Azure AD B2C ユーザー フローのユーザー インターフェイスをカスタマイズする](customize-ui-overview.md)
