---
title: "チュートリアル: Azure Active Directory と Concur の統合 | Microsoft Docs"
description: "Azure Active Directory で Concur を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1eee0a5d-24fa-4986-9aef-3c543cfe3296
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 87f8547486d9c77aeaa49e574b3c05e1a1fd877a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-concur"></a>チュートリアル: Azure Active Directory と Concur の統合
このチュートリアルでは、Azure と Concur の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Concur のテナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Concur のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-concur-tutorial/IC769766.png "Scenario")

>[!NOTE]
>SAML を使用してフェデレーション SSO に対して Concur サブスクリプションを構成するのは別個の作業です。したがって、Concur 社に実施を依頼してください。 
> 

## <a name="enable-the-application-integration-for-concur"></a>Concur のアプリケーション統合の有効化
このセクションでは、Concur のアプリケーション統合を有効にする方法について説明します。

**Concur のアプリケーション統合を有効にするには、次の手順を実行します。**
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
  ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
  ![アプリケーション](./media/active-directory-saas-concur-tutorial/IC700994.png "Applications")
4. **アプリケーション ギャラリー**を開くには、**[アプリケーションの追加]**、**[組織で使用するアプリケーションを追加]** の順にクリックします。
   
  ![実行する作業を選択してください。](./media/active-directory-saas-concur-tutorial/IC700995.png "実行する作業を選択してください。")
5. **検索ボックス**に、「**Concur**」と入力します。
   
  ![アプリケーション ギャラリー](./media/active-directory-saas-concur-tutorial/IC721727.png "Application Gallery")
6. 結果ウィンドウで **[Concur]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
  ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Concur に対する認証を行うことができるようにする方法を説明します。

>[!NOTE]
>SAML を使用してフェデレーション SSO に対して Concur サブスクリプションを構成するのは別個の作業です。したがって、Concur 社に実施を依頼してください。
> 

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Concur** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-concur-tutorial/IC769767.png "Configure single sign-on")
2. **[ユーザーの Concur へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-concur-tutorial/IC769768.png "Configure single sign-on")
3. **[アプリの URL の構成]** ページの **[Concur サインイン URL]** ボックスに、Concur のテナント サインイン URL を入力し、**[次へ]** をクリックします。 
   
   ![Configure sign in URL](./media/active-directory-saas-concur-tutorial/IC769769.png "Configure sign in URL")
4. **[Concur でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
   ![Configure sign in URL](./media/active-directory-saas-concur-tutorial/IC769770.png "Configure sign in URL")
   1. [メタデータのダウンロード] をクリックし、データ ファイルをコンピューターに保存します。
   2. Concur サポート チームに問い合わせて、テナントの SSO を構成します。
   3. [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。  
   
   >[!NOTE]
   >SAML を使用してフェデレーション SSO に対して Concur サブスクリプションを構成するのは別個の作業です。したがって、Concur 社に実施を依頼してください。 
   > 

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを Concur に対して有効にする方法について説明します。

Expense Service でアプリケーションを有効にするには、正しい設定と共に、Web サービス管理者のプロファイルを使用する必要があります。 T&E 管理機能で使用する既存の管理者プロファイルに WS 管理者のロールを追加するだけでは十分ではありません。

Concur コンサルタントまたはクライアント管理者は、Web サービス管理者プロファイルを別に作成する必要があります。クライアント管理者は、Web サービス管理者の機能 (アプリケーションの有効化など) に対してこのプロファイルを使用します。 このプロファイルは、クライアント管理者が日々使用する T&E 管理者プロファイルとは別にする必要があります (T&E 管理者プロファイルには、WS 管理者のロールを割り当てないでください)。

アプリケーションの有効化に使用するプロファイルを作成するときは、ユーザー プロファイルのフィールドにクライアント管理者の名前を入力します。 これにより、プロファイルに所有者が割り当てられます。プロファイルの作成が完了したら、クライアントはそのプロファイルを使用してログインし、[Web サービス] メニューにあるパートナー アプリケーションの *[有効にする]* ボタンをクリックします。

次の理由から、通常の T&E の管理に使用するプロファイルではこのアクションを実行しないでください。

1. クライアントは、アプリケーションを有効にした後で表示されるダイアログ ウィンドウで *[はい]* をクリックしたクライアントと同一でなければなりません。 このクリックは、クライアントがパートナー アプリケーションに対して自分のデータにアクセスすることを許可する意思を示すものです。したがって、パートナーなど、クライアント以外の人物が [はい] をクリックすることはできません。
2. クライアント管理者が T&E 管理者プロファイルを使用してアプリケーションを有効にした後で、会社を退職した場合 (その結果、プロファイルが非アクティブになった場合)、そのプロファイルを使用して有効にしたアプリケーションは、別のアクティブな WS 管理者プロファイルで有効にするまで機能しなくなります。 このため、WS 管理者プロファイルを別途作成する必要があります。
3. 管理者が会社を退職した場合、その WS 管理者プロファイルに関連付けられている名前を、必要に応じて別の管理者に変更することができます。この方法では、プロファイルを非アクティブ化する必要がないため、有効にしたアプリケーションに影響が及ぶことはありません。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Concur** テナントにログインします。
2. **[管理]** メニューの **[Web サービス]** をクリックします。
   
   ![Concur tenant](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur tenant")
3. 左側の **[Web サービス]** ウィンドウで、**[パートナー アプリケーションの有効化]** を選択します。
   
   ![Enable Partner Application](./media/active-directory-saas-concur-tutorial/IC721730.png "Enable Partner Application")
4. **[アプリケーションを有効にする]** ボックスの一覧で **[Azure Active Directory]** を選択し、**[有効にする]** をクリックします。
   
   ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")
5. **[はい]** をクリックして、**[アクションの確認]** ダイアログを閉じます。
   
   ![Confirm Action](./media/active-directory-saas-concur-tutorial/IC721732.png "Confirm Action")
6. Azure クラシック ポータルで、アプリケーションの一覧から **[Concur]** を選択して **[Concur]** ダイアログ ページを開きます。
7. **[ユーザー プロビジョニングの構成]** ダイアログ ページを開くには、**[ユーザー プロビジョニングの構成]** をクリックします。
8. Concur 管理者のユーザー名とパスワードを入力し、 **[次へ]**をクリックします。
9. 構成を終了するには、**[確認]** ページで **[完了]** をクリックします。

ここで、テスト アカウントを作成して 10 分間待機し、アカウントが Concur に同期されていることを確認します。

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Concur に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Concur** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-concur-tutorial/IC769771.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-concur-tutorial/IC767830.png "Yes")

ここで 10 分間待機し、アカウントが Concur に同期されたことを確認します。

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


