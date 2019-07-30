---
title: サインイン後、アプリのページでエラーが発生する | Microsoft Docs
description: アプリがエラーを返すときに Azure AD サインインで問題を解決する方法。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e6a3d0b533dccc3c3111382b014907d5c026ab
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612660"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>ユーザーがサインインした後、アプリのページにエラー メッセージが表示される

このシナリオでは、Azure Active Directory (Azure AD) でユーザーをサインインします。 ただし、アプリケーションにはエラー メッセージが表示され、ユーザーはサインイン フローを完了できません。 問題は、Azure AD が発行した応答をアプリが受け取らなかったことです。

アプリが Azure AD からの応答を受け入れなかったいくつかの理由が考えられます。 エラー メッセージで、応答に不足しているものを明確に識別できない場合、次の手順を試してください。

-   アプリが Azure AD ギャラリーの場合は、[Azure AD のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)に関するページの手順に従ったことを確認します。

-   [Fiddler](https://www.telerik.com/fiddler) などのツールを使用して、SAML の要求、応答、およびトークンをキャプチャします。

-   SAML 応答をアプリのベンダーに送信し、不足しているものを確認します。

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 応答に属性が不足している

Azure AD 応答で送信される属性を Azure AD の構成に追加するには、次の手順に従います。

1. [**Azure portal**](https://portal.azure.com/) を開き、グローバル管理者または共同管理者としてサインインします。

2. 左側のナビゲーション ウィンドウの上部にある **[すべてのサービス]** を選択して、Azure AD 拡張機能を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** を選択します。

4. Azure AD ナビゲーション ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

5. **[すべてのアプリケーション]** を選択して、ご自分のアプリの一覧を表示します。

   > [!NOTE]
   > 目的のアプリが表示されない場合、 **[すべてのアプリケーションの一覧]** の上部にある **[フィルター]** コントロールを使用します。 **[表示]** オプションを [すべてのアプリケーション] に設定します。

6. シングル サインオンを構成するアプリケーションを選択します。

7. アプリが読み込まれたら、ナビゲーション ウィンドウで、 **[シングル サインオン]** を選択します。

8. **[ユーザー属性]** セクションで、 **[その他のすべてのユーザー属性を表示および編集する]** を選択します。 ここで、ユーザーがサインインするときに SAML トークンでアプリに送信する属性を変更できます。

   属性を追加するには、次の手順に従います。

   1. **[属性の追加]** を選択します。 **[名前]** を入力し、ドロップダウン リストから **[値]** を選択します。

   1.  **[保存]** を選択します。 テーブルに新しい属性が表示されます。

9. 構成を保存します。

   次回ユーザーがアプリにサインインするときに、Azure AD は SAML 応答で新しい属性を送信します。

## <a name="the-app-doesnt-identify-the-user"></a>アプリでユーザーが識別されない

SAML 応答にロールなどの属性が不足しているために、アプリへのサインインが失敗します。 または、アプリが **NameID** (ユーザー識別子) 属性に別の形式または値を期待しているため、失敗します。

アプリケーション内のユーザーを作成、管理、削除するために [Azure AD 自動ユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を使用している場合は、ユーザーが SaaS アプリにプロビジョニングされていることを確認してください。 詳細については、「[Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)」を参照してください。

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Azure AD アプリの構成に属性を追加する

ユーザー識別子の値を変更するには、次の手順に従います。

1. [**Azure portal**](https://portal.azure.com/) を開き、グローバル管理者または共同管理者としてサインインします。

2. 左側のナビゲーション ウィンドウの上部にある **[すべてのサービス]** を選択して、Azure AD 拡張機能を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** を選択します。

4. Azure AD ナビゲーション ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

5. **[すべてのアプリケーション]** を選択して、ご自分のアプリの一覧を表示します。

   > [!NOTE]
   > 目的のアプリが表示されない場合、 **[すべてのアプリケーションの一覧]** の上部にある **[フィルター]** コントロールを使用します。 **[表示]** オプションを [すべてのアプリケーション] に設定します。

6. SSO を構成するアプリを選択します。

7. アプリが読み込まれたら、ナビゲーション ウィンドウで、 **[シングル サインオン]** を選択します。

8. **[ユーザー属性]** で、 **[ユーザー識別子]** ドロップダウン リストからユーザーの一意の識別子を選択します。

## <a name="change-the-nameid-format"></a>NameID の形式を変更する

アプリケーションが **NameID** (ユーザー識別子) 属性に別の形式を期待している場合は、「[NameID の編集](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid)」を参照して NameID の形式を変更します。

Azure AD は、選択した値に基づく **NameID** 属性 (ユーザー識別子) の形式、または SAML AuthRequest でアプリによって要求された形式を選択します。 詳細については、「[シングル サインオンの SAML プロトコル](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)」の「NameIDPolicy」を参照してください。

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>アプリが SAML 応答に別の署名方法を想定する

Azure AD によってデジタル署名される SAML トークンの部分を変更するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) を開き、グローバル管理者または共同管理者としてサインインします。

2. 左側のナビゲーション ウィンドウの上部にある **[すべてのサービス]** を選択して、Azure AD 拡張機能を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** を選択します。

4. Azure AD ナビゲーション ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

5. **[すべてのアプリケーション]** を選択して、ご自分のアプリの一覧を表示します。

   > [!NOTE]
   > 目的のアプリケーションが表示されない場合、 **[すべてのアプリケーションの一覧]** の上部にある **[フィルター]** コントロールを使用します。 **[表示]** オプションを [すべてのアプリケーション] に設定します。

6. シングル サインオンを構成するアプリケーションを選択します。

7. アプリケーションが読み込まれたら、ナビゲーション ウィンドウで、 **[シングル サインオン]** を選択します。

8. **[SAML 署名証明書]** の下にある **[詳細な証明書署名設定の表示]** を選択します。

9. 次のオプションの中から、アプリが期待する **[署名オプション]** を選択します。

   * **SAML 応答の署名**
   * **SAML 応答とアサーションへの署名**
   * **SAML アサーションへの署名**

   次回ユーザーがアプリにサインインするときに、Azure AD は選択した SAML 応答の部分に署名します。

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>アプリが SHA-1 署名アルゴリズムを期待している

既定では、Azure AD は、最も安全なアルゴリズムを使用して SAML トークンに署名します。 アプリに SHA-1 が必要な場合を除き、署名アルゴリズムを *SHA-1* に変更しないことをお勧めします。

署名アルゴリズムを変更するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) を開き、グローバル管理者または共同管理者としてサインインします。

2. 左側のナビゲーション ウィンドウの上部にある **[すべてのサービス]** を選択して、Azure AD 拡張機能を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** を選択します。

4. Azure AD ナビゲーション ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

5. **[すべてのアプリケーション]** を選択して、アプリケーションの一覧を表示します。

   > [!NOTE]
   > 目的のアプリケーションが表示されない場合、 **[すべてのアプリケーションの一覧]** の上部にある **[フィルター]** コントロールを使用します。 **[表示]** オプションを [すべてのアプリケーション] に設定します。

6. シングル サインオンを構成するアプリを選択します。

7. アプリが読み込まれたら、アプリの左側にあるナビゲーション ウィンドウから **[シングル サインオン]** を選択します。

8. **[SAML 署名証明書]** の下にある **[詳細な証明書署名設定の表示]** を選択します。

9. **[署名アルゴリズム]** として **[SHA-1]** を選択します。

   次回ユーザーがアプリにサインインするときに、Azure AD は SHA-1 アルゴリズムを使用して SAML トークンに署名します。

## <a name="next-steps"></a>次の手順
[Azure AD のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。
