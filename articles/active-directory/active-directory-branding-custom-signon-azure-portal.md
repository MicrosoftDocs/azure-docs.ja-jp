---
title: "Azure Active Directory プレビューでサインイン ページをカスタマイズする | Microsoft Docs"
description: "Azure サインイン ページに会社のブランドを追加する方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 48f85fd7e50c1cb3ece7f8b75779fe7cde92c7da


---
# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Azure Active Directory プレビューでサインイン ページに会社のブランドを追加する
多くの企業は、管理下にある Web サイトとサービスに関して利用者が戸惑わないよう、そのすべての外観に統一感を持たせたいと考えています。 Azure Active Directory プレビューでは、会社のロゴや独自の配色でサインイン ページの外観をカスタマイズできるようにすることでこれを実現します。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md) サインイン ページは、Office 365 など、Azure AD を ID プロバイダーとして使用する Web ベースのアプリケーションにサインインするときに表示されるページです。 このページを使用して資格情報を入力します。

このページに会社のブランド、色、およびその他のカスタマイズ可能な要素を表示する場合は、次の画像を参照して、2 つのエクスペリエンスの違いを理解してください。

次のスクリーンショットは、デスクトップ コンピューターに表示された、カスタマイズ **前** の Office 365 サインイン ページの例を示しています。

![Office 365 sign-in page before customization](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

次のスクリーンショットは、デスクトップ コンピューターに表示された、カスタマイズ **後** の Office 365 サインイン ページの例を示しています。

![Office 365 sign-in page after customization](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)

## <a name="customizing-the-sign-in-page"></a>サインイン ページのカスタマイズ
クラウド アプリやクラウド サービスを勤務先の会社がサブスクライブしているとき、それらがブラウザーでアクセスする仕様になっている場合、通常、サインイン ページが使用されます。

サインイン ページに変更を適用した場合、変更が反映されるまでに最大 1 時間かかる場合があります。

ブランド化されたサインイン ページが表示されるのは、テナント固有の URL (https://outlook.com/**contoso**.com や https://mail.**contoso**.com など) でサービスにアクセスした場合だけです。

テナント固有ではない URL (https://mail.office365.com など) を使用してサービスにアクセスした場合は、ブランド化されていないサインイン ページが表示されます。 この場合、ユーザー ID を入力するか、ユーザー タイルを選択すると、ブランドが表示されます。

> [!NOTE]
> * ドメイン名は、ブランドを構成した Azure Portal の **[ドメイン]** 部分に "アクティブ" として表示される必要があります。 詳細については、 [カスタム ドメイン名の追加](active-directory-domains-add-azure-portal.md)に関する記事をご覧ください。
> * サインイン ページのブランド化は、Microsoft のコンシューマー向けサインイン ページには継承されません。 Microsoft アカウントでサインインすると、Azure AD によってレンダリングされるユーザー タイルのブランド化された一覧が表示されることがありますが、組織のブランドは Microsoft アカウントのサインイン ページには適用されません。
>
>

サインイン ページで **[サインインしたままにする]** チェック ボックスをオンにすると、ユーザーはブラウザーを閉じて開き直したときにもサインインしたままにしておくことができます。

   ![サインインしたままにする](./media/active-directory-branding-custom-signon-azure-portal/01.png)

セッションの有効期間には影響しません。 Azure Active Directory サインイン ページのチェック ボックスを非表示にすることができます。
チェック ボックスが表示されるかどうかは、**[[サインインしたままにする] は無効]** の設定によって異なります。

   ![サインインしたままにする](./media/active-directory-branding-custom-signon-azure-portal/02.png)

チェック ボックスを非表示にするには、この設定を **[はい]** に構成します。

> [!NOTE]
> SharePoint Online と Office 2010 の一部の機能は、ユーザーがこのチェック ボックスをオンにできるかどうかに依存します。 この設定を非表示に構成する場合、ユーザーにサインインを求める追加のメッセージが予期せず表示されることがあります。
>
>

**ディレクトリに会社のブランドを追加するには**

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![ユーザー管理を開く](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[会社のブランド]** を選択します。
4. **[Users and groups - Company branding (ユーザーとグループ - 会社のブランド)]** ブレードで、**[編集]** をクリックします。

    ![カスタム ブランドを編集する](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. カスタマイズする要素に変更を加えます。 すべての要素は省略できます。
6. [ **Save**] をクリックします。

サインイン ページのブランドに加えた変更が表示されるまでに、最大 1 時間かかる場合があります。

## <a name="next-steps"></a>次のステップ
[言語固有の会社のブランドを追加する](active-directory-branding-localize-azure-portal.md)



<!--HONumber=Dec16_HO5-->


