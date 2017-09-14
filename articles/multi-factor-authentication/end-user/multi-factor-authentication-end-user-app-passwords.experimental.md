---
title: "Azure MFA でアプリ パスワードを使用する方法 | Microsoft Docs"
description: "このページは、アプリ パスワードの詳細と、Azure MFA に関連した用途を理解するのに役立ちます。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: kgremban
ms.custom: end-user
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 1ecc2bdef5ff7ef8ed8dded7dc12428ce9657821
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のアプリ パスワードとは
現在、特定の非ブラウザー アプリ (Exchange Active Sync を使用する Apple ネイティブ電子メールクライアントなど) は、多要素認証をサポートしていません。 多要素認証はユーザーごとに有効にします。  これは、次の場合にユーザーが多要素認証を使用できないことを意味します。

- ユーザーに対して多要素認証が有効になっている
- ユーザーが非ブラウザー アプリを使用しようとしている。

アプリ パスワードによって、ユーザーはアプリを使用できるようになります。

アプリ パスワードが取得できたら、これらの非ブラウザー アプリに対して、元のパスワードの代わりに使用します。 2 段階認証の登録をすると、2 番目の認証を実行できない限りは元のパスワードでサインインできなくなります。 たとえば、Apple のネイティブ電子メール クライアントは 2 段階認証を要求できないので、そのままではサインインできません。 この問題の解決策は、日常的に使用しない、より安全なアプリ パスワードを作成することです。 アプリ パスワードは 2 段階認証をサポートできないアプリ向けです。 このアプリ パスワードを使用することで、アプリが多要素認証をバイパスして動作を続行できるようになります。


> [!NOTE]
> Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートしており、2 段階認証を使用できます。 Office 2013 クライアントでは、アプリ パスワードは不要です。  詳しくは、「[Office 2013 modern authentication public preview announcement (Office 2013 の最新の認証のパブリック プレビューに関する発表)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」を参照してください。


## <a name="how-to-use-app-passwords"></a>アプリ パスワードを使用する方法
アプリ パスワードについて理解しておくべきこと

* アプリ パスワードはユーザーが作成するわけではなく、 自動的に生成されます。
* 現在は、1 ユーザーあたり 40 個というパスワード数の制限があります。 
* 制限に達した後にアプリ パスワードを作成しようとした場合、新しいものを作成できるよう既存のアプリ パスワードのいずれかを削除する必要があります。
* アプリ パスワードは、アプリケーションごとではなく、デバイスごとに使用します。 たとえば、ノート PC 用に 1 つのアプリ パスワードを作成し、そのアプリ パスワードをノート PC 上のすべてのアプリケーションで使用します。 次に、デスクトップ PC 上のすべてのアプリに使用する 2 番目のアプリ パスワードを作成します。 
* 最初に 2 段階認証の登録をしたときに、アプリ パスワードが 1 つ提供されます。  追加のアプリ パスワードが必要な場合は、作成できます。



## <a name="creating-and-deleting-app-passwords"></a>アプリ パスワードの作成と削除
最初のサインイン時に、使用可能なアプリ パスワードが提供されます。  後でアプリ パスワードを作成したり、削除したりすることもできます。 アプリ パスワードを削除する方法は、多要素認証を使用する方法によって異なります。 次の質問に答えていくと、アプリ パスワードを管理できる場所がわかります。 

1. 個人の Microsoft アカウントで 2 段階認証を使用していますか。 使用している場合は、「[アプリ パスワードと 2 段階認証](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification)」を参照してください。 使用していない場合は、次に進んでください。

2. あなたは職場または学校のアカウントで 2 段階認証を使用しているようです。 Office 365 アプリへのサインインに 2 段階認証を使用していますか。 使用している場合は、「[Office 365 のアプリ パスワードを作成する](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183)」を参照してください。 使用していない場合は、次に進んでください。 

3. Microsoft Azure で 2 段階認証を使用していますか。 使用している場合は、この記事の「[Azure Portal でアプリ パスワードを管理する](#manage-app-passwords-in-the-Azure-portal)」セクションに進んでください。 使用していない場合は、次に進んでください。

4. 2 段階認証をどこで使用しているかわからない場合は、 この記事の「[MyApps ポータルでアプリ パスワードを管理する](#manage-app-passwords-with-the-myapps-portal)」セクションに進んでください。 


## <a name="manage-app-passwords-in-the-azure-portal"></a>Azure Portal でアプリ パスワードを管理する
Azure で 2 段階認証を使用している場合は、Azure Portal でアプリ パスワードを作成できます。

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Azure Portal でアプリ パスワードを作成するには
1. Azure クラシック ポータルにサインインします。
2. 上部のユーザー名を右クリックし、[追加のセキュリティ確認] を選択します。
3. 確認ページの上部の [アプリ パスワード] を選択します。
4. **[作成]**をクリックします。
5. アプリ パスワードの名前を入力し、 **[次へ]**
6. アプリ パスワードをクリップボードにコピーし、アプリに貼り付けます。
   
   ![クラウド](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Azure Portal でアプリ パスワードを削除するには
1. Azure クラシック ポータルにサインインします。
2. 上部のユーザー名を右クリックし、[追加のセキュリティ確認] を選択します。
3. 上部で、追加のセキュリティ確認の隣にある **[アプリ パスワード]** を選択します。
4. 削除するアプリ パスワードの横にある **[削除]** をクリックします。
5. **[はい]**をクリックし、削除を確定します。
6. アプリ パスワードを削除したら、**[閉じる]** をクリックできます。


## <a name="manage-app-passwords-with-the-myapps-portal"></a>MyApps ポータルでアプリ パスワードを管理する
多要素認証をどこで使用しているかわからない場合は、いつでも Myapps ポータルでアプリ パスワードの作成や削除ができます。

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Myapps ポータルでアプリ パスワードを作成するには
1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。
2. 右上にある名前をクリックし、**[プロファイル]** を選択します。
3. **[追加のセキュリティ確認]** を選択します。
   ![[追加のセキュリティ確認] を選択する - スクリーンショット](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. **[アプリ パスワード]** を選択します。
   ![[アプリ パスワード] を選択する - スクリーンショット](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. ページの下部にある **[Create]**」を参照してください。
6. アプリ パスワードの名前を入力し、 **[次へ]**をクリックします。
7. アプリ パスワードをクリップボードにコピーし、アプリに貼り付けます。
   ![アプリケーション パスワードの作成](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Myapps ポータルでアプリ パスワードを削除するには
1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。
2. 上部にある [プロファイル] を選択します。
3. **[追加のセキュリティ確認]** を選択します。

   ![[追加のセキュリティ確認] を選択する - スクリーンショット](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. **[アプリ パスワード]** を選択します。

   ![[アプリ パスワード] を選択する - スクリーンショット](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. 削除するアプリ パスワードの横にある **[削除]**をクリックします。

   ![アプリケーション パスワードの削除](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. **[はい]** をクリックして、そのパスワードを削除することを確定します。
7. アプリ パスワードを削除したら、**[閉じる]** をクリックできます。

## <a name="next-steps"></a>次のステップ

- [2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)

- SMS や電話を受ける代わりに、[Microsoft Authenticator アプリ](microsoft-authenticator-app-how-to.md)の通知を使用してサインインを確認する 

