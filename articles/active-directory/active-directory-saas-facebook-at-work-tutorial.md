---
title: "チュートリアル: Azure Active Directory と Workplace by Facebook の統合 | Microsoft Docs"
description: "Azure Active Directory と Workplace by Facebook の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>チュートリアル: Azure Active Directory と Workplace by Facebook の統合
このチュートリアルの目的は、Workplace by Facebook と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Workplace by Facebook と Azure AD の統合には、次の利点があります。 

* Workplace by Facebook にアクセスする Azure AD ユーザーを制御できます 
* Workplace by Facebook へのアクセス権が付与されているユーザーには、アカウントを自動的にプロビジョニングできます
* ユーザーが自分の Azure AD アカウントで自動的に Workplace by Facebook にサインオン (シングル サインオン) できるようにします
* 1 つの場所でアカウントを管理できます 

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
CS Stars と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* シングル サインオンを有効にした Workplace by Facebook

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>ギャラリーからの Workplace by Facebook の追加
Azure AD への Workplace by Facebook の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workplace by Facebook を追加する必要があります。

**ギャラリーから Workplace by Facebook を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**Workplace by Facebook**」と入力します。
7. 結果ウィンドウで **[Workplace by Facebook]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure Active Directory のアカウントで Workplace by Facebook に対する認証を行えるようにする方法を説明します。

**Workplace by Facebook で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルで Workplace by Facebook を追加したら、**[シングル サインオンの構成]** をクリックします。
2. **[アプリケーション URL の構成]** 画面に、ユーザーが Workplace by Facebook アプリケーションにサインインする URL を入力します。 これが Workplace by Facebook テナント URL です (例: https://example.facebook.com/)。 完了したら、 **[次へ]**をクリックします。
3. 別の Web ブラウザーのウィンドウで、Workplace by Facebook 企業サイトに管理者としてログインします。
4. ID プロバイダーとして Azure AD を使用するように Workplace by Facebook を構成するには、次の URL の指示に従います。[https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. 完了したら、Azure クラシック ポータルが表示されているブラウザー ウィンドウに戻り、手順を完了したことを確認するチェック ボックスをオンにして、**[次へ]** をクリックし、**[完了]** をクリックします。


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>ユーザーを Workplace by Facebook に自動的にプロビジョニングする
Azure AD は、割り当てられたユーザーのアカウントの詳細を Workplace by Facebook と自動的に同期する機能をサポートしています。 この自動同期機能によって、ユーザーが初めてサインインする前に、Workplace by Facebook がユーザーのアクセスを承認するために必要なデータを取得できます。 また、Azure AD のアクセス権が取り消された場合、Workplace by Facebook からユーザーのプロビジョニングを解除できます。

自動プロビジョニングを設定するには、Azure クラシック ポータル ウィンドウで **[アカウント プロビジョニングの構成]** をクリックします。

自動プロビジョニングの詳細な構成方法については、 [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-workplace-by-facebook"></a>Workplace by Facebook へのユーザーの割り当て
プロビジョニングされた AAD ユーザーがアクセス パネルに Workplace by Facebook を表示できるようにするには、Azure クラシック ポータル内でアクセス権を割り当てる必要があります。

**Workplace by Facebook へのユーザーの割り当て:**

1. Azure クラシック ポータルの Workplace by Facebook のスタート ページで、**[アカウントの割り当て]** をクリックします。
2. **[表示]** メニューで、Workplace by Facebook にユーザーとグループのどちらを割り当てるかを選択し、チェックマーク ボタンをクリックします。
3. 結果の一覧で、Workplace by Facebook を割り当てるユーザーまたはグループを選択します。
4. ページ フッターにある **[割り当て]** をクリックします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->


