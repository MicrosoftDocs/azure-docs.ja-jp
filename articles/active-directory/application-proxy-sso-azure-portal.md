---
title: "Azure AD アプリケーション プロキシを使用したアプリへのシングル サインオン | Microsoft Docs"
description: "Azure Portal で Azure AD アプリケーション プロキシを使用してオンプレミス アプリケーションへのシングルサインオンを有効にします。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fcec43ad50b9c78443ada116b9ca444605c4730
ms.openlocfilehash: fa2a2f8ee61b6edf92db8d1f92ab9aca8bc673c5

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>Azure AD アプリケーション プロキシを使用したシングル サインオンの提供 - パブリック プレビュー

Azure Active Directory アプリケーション プロキシを使用してオンプレミス アプリケーションを発行して、遠隔地の従業員がオンプレミス アプリケーションに安全にアクセスできるようにすることで生産性を上げることができます。 Azure Portal で、これらのアプリに対するシングル サインオン (SSO) を設定することもできます。 これで、ユーザーは、Azure AD で認証されれば、もう一度サインインしなくても、エンタープライズ アプリケーションにアクセスるようになります。

この記事では、パスワード ベースのアプリを例として使用して、パスワード保管による単純な SSO エクスペリエンスを示します。 

アプリケーション プロキシを使用したアプリの発行とテストを既に行っている必要があります。 そうでない場合は、「[Azure AD アプリケーション プロキシを使用したアプリの発行](application-proxy-publish-azure-portal.md)」の手順に従った後、この記事に戻ってください。 

アプリケーション プロキシを初めて使用する場合は、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)」で、この機能の詳細を確認してください。

## <a name="set-up-password-vaulting-for-your-application"></a>アプリケーションのパスワード保管の設定

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択します。
3. 一覧から、SSO を設定するアプリを選択します。 多数のアプリがある場合は、検索ボックスでフィルター処理を行って、目的のアプリを選択できます。  
4. [管理] セクションで、**[シングル サインオン]** をクリックします。

   ![[シングル サインオン] の選択](./media/application-proxy-sso-azure-portal/select-sso.png)

5. SSO モードとして、**[パスワード ベースのサインオン]** を選択します。
6. [サインオン URL] に、ユーザーがアプリにサインインするためのユーザー名とパスワードを入力するページの URL を入力します。 これは、アプリケーション プロキシを使用してアプリを発行したときに作成した外部 URL にする必要があります。 

   ![パスワード ベースのサインオンの選択と URL の入力](./media/application-proxy-sso-azure-portal/password-sso.png)

7. [**保存**] を選択します。

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>アプリをテストする

[MyApps サイト](https://myapps.microsoft.com)に移動し、先ほど構成したアプリを選択します。 そのアプリの資格情報 (または設定したアクセス権を持つテスト アカウントの資格情報) でサインインします。 正常にサインインした場合は、アプリを終了すると、資格情報をもう一度入力しなくても前の場所に戻ることができます。 

## <a name="next-steps"></a>次のステップ

[アプリケーション プロキシを使用したシングル サインオン](active-directory-application-proxy-sso-using-kcd.md)を実装するための他の方法を参照します。



<!--HONumber=Jan17_HO2-->


