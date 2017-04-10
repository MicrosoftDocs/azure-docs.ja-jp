---
title: "チュートリアル: Azure Active Directory と Evidence.com の統合 | Microsoft Docs"
description: "Azure Active Directory と Evidence.com の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d5183b136a0ceca939f754f67130d1179f6f4f7


---
# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>チュートリアル: Azure Active Directory と Evidence.com の統合
このチュートリアルでは、Azure Active Directory (AAD) と Syncplicity の間でのシングル サインオンを設定する方法を説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Microsoft Azure サブスクリプション
* シングル サインオンが有効になっている Evidence.com サブスクリプション (SAML ベースのシングル サインオンが有効になっていない場合は、 earlyaccess@evidence.com 宛てに電子メールをお送りください)

このチュートリアルを完了すると、Evidence.com に割り当てられている AAD ユーザーは、AAD アクセス パネルを使用してアプリケーションへのシングル サインオンが可能になります。

## <a name="add-evidencecom-to-your-directory"></a>目的のディレクトリへの Evidence.com の追加
このセクションでは、Evidence.com を統合アプリケーションとして Azure Active Directory に追加する方法を説明します。

**Evidence のアプリケーション統合を有効にするには**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
4. アプリケーション ギャラリーを開くには、**[追加]** をクリックし、**[ギャラリーからアプリケーションを追加します]** をクリックします。
5. 検索ボックスに、「 **Evidence.com**」と入力します。
6. 結果ウィンドウで **[Evidence.com]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Evidence.com で Azure Active Directory のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルで Evidence.com を追加したら、 **[シングル サインオンの構成]**をクリックします。 
2. 次の画面で、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
3. [アプリケーション URL の構成] 画面で、ユーザーが Evidence.com テナントへのサインインに使用する URL (例: https://yourtenant.evidence.com) を入力し、**[次へ]** をクリックします。 
4. **[証明書のダウンロード]** リンクをクリックし、証明書をローカル ドライブに保存します。 この証明書とメタデータ URL (エンティティ ID、SSO サインイン URL、サインアウト URL) を使用して、Evidence.com サイトで SSO をセットアップします。 
5. 別の Web ブラウザー ウィンドウで、Evidence.com テナントに管理者としてログインし、 **[Admin] \(管理)** タブに移動します。
6.  **[Agency Single Sign On] \(代理店のシングル サインオン)**
7.  **[SAML Based Single Sign On] \(SAML ベースのシングル サインオン)**
8. Azure クラシック ポータルに表示されている **[発行者の URL]**、**[シングル サインオン]**、**[シングル サインアウト]** の値をコピーし、Evidence.com の対応するフィールドに貼り付けます。
9. 手順 4 でダウンロードした証明書を Notepad.exe などのテキスト エディターで開き、その内容をコピーして **[Security Certificate] \(セキュリティ証明書)** ボックスに貼り付けます。 
10. Evidence.com の構成を保存します。
11. Azure クラシック ポータルで、 **[説明どおりにシングル サインオンを構成したことを確認してください]**チェックボックスをオンにします。 これをオンにすると、このアプリケーションのチェック ボックスの機能を、現在の証明書で開始できるようになります。
12. [シングル サインオンの確認] ページで **[完了]**をクリックします。  

## <a name="creating-an-evidencecom-test-user"></a>Evidence.com テスト ユーザーの作成
Azure AD ユーザーがサインインできるようにするには、ユーザーを Evidence.com アプリケーションにプロビジョニングする必要があります。 このセクションでは、Evidence.com で Azure AD ユーザー アカウントを作成する方法について説明します。

**Evidence.com でユーザー アカウントをプロビジョニングするには**

1. Web ブラウザー ウィンドウで、Evidence.com 企業サイトに管理者としてログインします。
2. **[Admin] \(管理)** タブをクリックします。
3. **[Add User] \(ユーザーの追加)**をクリックします。
4. **[Add] \(追加)** ボタンをクリックします。
5. 追加したユーザーの **[Email Address] \(電子メール アドレス)** が、アクセス権を付与する Azure AD 内のユーザーのユーザー名と一致する必要があります。 組織内でユーザー名と電子メール アドレスが同じ値でない場合は、Azure クラシック ポータルの **[Evidence.com] > [属性] > [シングル サインオン]** セクションを使用して、Evidence.com に送信される nameidenitifer を電子メール アドレスに変更できます。

## <a name="assigning-users-to-evidencecom"></a>Evidence.com へのユーザーの割り当て
プロビジョニングされた AAD ユーザーがアクセス パネルに Evidence.com を表示できるようにするには、Azure クラシック ポータル内でアクセス権を割り当てる必要があります。

**Evidence.com にユーザーを割り当てるには**

1. Azure クラシック ポータルの Evidence.com 用のクイック スタート ページで、 **[ユーザーを Evidence.com に割り当てる]**をクリックします。
2. **[表示]** メニューで、Evidence.com にユーザーとグループのどちらを割り当てるかを選択し、チェックマーク ボタンをクリックします。
3. **[ユーザー]** 一覧で、Evidence.com を割り当てるユーザーまたはグループを選択します。
4. ページ フッターにある **[割り当て]** をクリックします。




<!--HONumber=Nov16_HO3-->


