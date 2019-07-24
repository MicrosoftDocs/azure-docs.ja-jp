---
title: サインイン後、アプリケーションのページでエラーが発生 | Microsoft Docs
description: アプリケーション自体がエラーを出力するときに Azure AD サインインで問題を解決する方法
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 438f4f60c80cb798c3c40f57e22c9194a9fd5f86
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110853"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>サインイン後、アプリケーションのページでエラーが発生する

このシナリオでは、ユーザーが Azure AD でサインインしましたが、ユーザーがサインイン フローを正常に完了できないというエラーをアプリケーションが表示します。 このシナリオでは、アプリケーションが Azure AD による応答の問題を受け入れません。

アプリケーションが Azure AD からの応答を受け入れなかったいくつかの理由が考えられます。 アプリケーションのエラーで、応答に不足している情報を明確に把握できない場合は、次の手順を実行します。

-   アプリケーションが Azure AD ギャラリーの場合は、[Azure Active Directory のアプリケーションに対する SAML ベースに基づいたシングル サインオンをデバッグする方法](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)という記事に記載されたすべての手順を実行していることを確認します。

-   [Fiddler](https://www.telerik.com/fiddler) などのツールを使用して、SAML 要求、SAML 応答、および SAML トークンをキャプチャします。

-   SAML 応答をアプリケーション ベンダーと共有して、不足している情報を把握します。

## <a name="missing-attributes-in-the-saml-response"></a>SAML 応答に欠落している属性

Azure AD 応答で送信される属性を Azure AD の構成に追加するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6. シングル サインオンを構成するアプリケーションを選択します。

7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

8. **[ユーザー属性]** セクションの下にある **[すべてのユーザー属性を表示および編集する]** をクリックして、ユーザーがサインインするときに、SAML トークンでアプリケーションに送信される属性を編集します。

   属性を追加するには、次の手順に従います。

   * **[属性の追加]** をクリックします。 **[名前]** を入力し、ドロップダウンから **[値]** を選択します。

   * **[保存]** をクリックします。 テーブルに新しい属性が表示されます。

9. 構成を保存します。

次回ユーザーがアプリケーションにサインインするときに、Azure AD は SAML 応答で新しい属性を送信します。

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>アプリケーションが別のユーザー識別子の値または形式を想定する

SAML 応答にロールなどの属性が欠落しているため、またはアプリケーションが EntityID 属性に別の形式を想定しているため、アプリケーションへのサインインが失敗します。

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Azure AD アプリケーションの構成に属性を追加する:

ユーザー識別子の値を変更するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6. シングル サインオンを構成するアプリケーションを選択します。

7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

8. **[ユーザー属性]** で、**[ユーザー識別子]** ドロップダウンからユーザーの一意の識別子を選択します。

## <a name="change-entityid-user-identifier-format"></a>EntityID (ユーザー識別子) の形式を変更する

アプリケーションが EntityID 属性に別の形式を想定している場合、 Azure AD は、ユーザー認証後の応答でアプリケーションに送信する EntityID (ユーザー ID) の形式を選択することはできません。

Azure AD は、選択した値に基づく NameID 属性 (ユーザーID) の形式、または SAML AuthRequest でアプリケーションによって要求された形式を選択します。 詳細については、NameIDPolicy セクションにある[シングル サインオンの SAML プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)の記事を参照してください。

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>アプリケーションが SAML 応答に別の署名方法を想定する

Azure Active Directory によってデジタル署名される SAML トークンの部分を変更するには、 次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6. シングル サインオンを構成するアプリケーションを選択します。

7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

8. **[SAML 署名証明書]** セクションの下にある **[詳細な証明書署名設定の表示]** をクリックします。

9. アプリケーションで想定されている適切な**署名オプション**を選択します。

   * SAML 応答の署名

   * SAML 応答とアサーションへの署名

   * SAML アサーションへの署名

次回ユーザーがアプリケーションにサインインするときに、Azure AD は選択した SAML 応答の部分に署名します。

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>アプリケーションは署名アルゴリズムが SHA-1 であると想定する

既定では、Azure AD は、ほとんどのセキュリティ アルゴリズムを使用して SAML トークンに署名します。 アプリケーションによって要求されない限り、署名アルゴリズムを SHA-1 に変更しないでください。

署名アルゴリズムを変更するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6. シングル サインオンを構成するアプリケーションを選択します。

7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

8. **[SAML 署名証明書]** セクションの下にある **[詳細な証明書署名設定の表示]** をクリックします。

9. **[署名アルゴリズム]** で SHA-1 を選択します。

次回ユーザーがアプリケーションにサインインするときに、Azure AD は SHA-1 アルゴリズムを使用して SAML トークンに署名します。

## <a name="next-steps"></a>次の手順
[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
