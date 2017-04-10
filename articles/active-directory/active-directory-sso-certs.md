---
title: "Azure AD でフェデレーション証明書を管理する方法 |Microsoft Docs"
description: "フェデレーション証明書の有効期限をカスタマイズする方法と、有効期限が近づいている証明書を更新する方法について説明します。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: fb33a0b01b7d9806c92dfc66303b0dd2ca1044d9
ms.openlocfilehash: 04276fc2da32b27dc9e0a4601ab45b9f1e95959a
ms.lasthandoff: 01/18/2017


---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理
この記事では、SaaS アプリケーションにフェデレーション シングル サインオン (SSO) を確立するために Azure Active Directory で作成される証明書に関連する一般的な質問について説明します。

次の例に示すように、この記事では、 **Azure AD のシングル サインオン**を使用するように構成されたアプリのみに関連する内容を取り上げます。

![Azure AD のシングル サインオン](./media/active-directory-sso-certs/fed-sso.PNG)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate"></a>フェデレーション証明書の有効期限をカスタマイズする方法
既定では、証明書は&2; 年後に期限切れになるように設定されています。 次の手順に従って、証明書の別の有効期限を選択できます。 ここに用意したスクリーンショットでは、例として Salesforce を使用していますが、これらの手順は、他のフェデレーション SaaS アプリにも適用できます。

1. Azure Active Directory にあるアプリケーションの [クイック スタート] ページで、 **[シングル サインオンの構成]**をクリックします。
   
    ![SSO 構成ウィザードを開く](./media/active-directory-sso-certs/config-sso.png)
2. **[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
3. アプリケーションの **[サインオン URL]** を入力し、**[フェデレーション シングル サインオンに使用する証明書を構成します]** チェック ボックスをオンにします。 その後、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン](./media/active-directory-sso-certs/new-app-config-sso.PNG)
4. 次のページで **[新しい証明書の生成]**を選択し、証明書の有効期限を選択します。 その後、 **[次へ]**をクリックします。
   
    ![[新しい証明書の生成]](./media/active-directory-sso-certs/new-app-config-cert.PNG)
5. 次に、 **[証明書のダウンロード]**をクリックします。 特定の SaaS アプリに証明書をアップロードする方法については、 **[構成手順の表示]**をクリックします。
   
    ![証明書をダウンロードした後アップロードする](./media/active-directory-sso-certs/new-app-config-app.PNG)
6. ダイアログの下部にある確認のチェック ボックスをオンにして [送信] をクリックするまで、証明書は有効になりません。

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>有効期限が近づいている証明書を更新する方法
次に示す更新手順を実行すると、ユーザーに対する大幅なダウンタイムがなくなり理想的です。 このセクションで使用するスクリーンショットでは、例として Salesforce を取り上げていますが、これらの手順は他のフェデレーション SaaS アプリにも適用できます。

1. Azure Active Directory にあるアプリケーションの [クイック スタート] ページで、 **[シングル サインオンの構成]**をクリックします。
   
    ![SSO 構成ウィザードを開く](./media/active-directory-sso-certs/renew-sso-button.PNG)
2. ダイアログの最初のページで **[Azure AD のシングル サインオン]** を既に選択しているので、**[次へ]** をクリックします。
3. 2 番目のページで、 **[フェデレーション シングル サインオンに使用する証明書を構成します]**チェック ボックスをオンにします。 その後、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン](./media/active-directory-sso-certs/renew-config-sso.PNG)
4. 次のページで **[新しい証明書の生成]**を選択し、新しい証明書の有効期限を選択します。 その後、 **[次へ]**をクリックします。
   
    ![[新しい証明書の生成]](./media/active-directory-sso-certs/new-app-config-cert.PNG)
5. **[証明書のダウンロード]**をクリックします。 正常に証明書を更新するには、次の&2; つの手順を実行する必要があります。
   
   * SaaS アプリのシングル サインオンの構成画面に新しい証明書をアップロードします。 特定の SaaS アプリでこの手順を実行する方法については、 **[構成手順の表示]**をクリックします。
   * Azure AD で、ダイアログの下部にある確認のチェック ボックスをオンにして新しい証明書を有効にし、 **[次へ]** をクリックして送信します。
     
     > [!IMPORTANT]
     > これら&2; つの手順のいずれかを完了すると、一時的にアプリへのシングル サインオンが無効になりますが、2 番目の手順を完了すると再度有効になります。 そのため、ダウンタイムを最小限に抑えるために、2 つの手順を間をおかずに短時間で完了してください。
     > 
     > 
     
     ![証明書をダウンロードした後アップロードする](./media/active-directory-sso-certs/renew-config-app.PNG)

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](active-directory-saml-debugging.md)


