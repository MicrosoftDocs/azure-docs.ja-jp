---
title: アプリ パスワードを管理する方法 - Azure Active Directory | Microsoft Docs
description: アプリ パスワードの詳細と、2 段階認証に関連した用途について学習します。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6790fa1cbb10999a751b31bcb27db2edcb67b4a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517761"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>2 段階認証のアプリ パスワードを管理する

Outlook 2010 などの特定の非ブラウザー アプリは、2 段階認証をサポートしていません。 このサポートの欠如は、2 段階認証を使用している場合、アプリが動作しないことを意味します。 この問題を回避するために、通常のパスワードとは別に、各非ブラウザー アプリで使用する自動生成パスワードを作成できます。

アプリ パスワードを使用する場合は、次の点に注意してください。

- アプリ パスワードは自動生成され、アプリごとに 1 回しか入力されない。

- ユーザー 1 人あたりのパスワード数の上限は 40 個である。 この上限を超えて作成を試行すると、新しいパスワードの作成が許可されるように、事前に既存のパスワードを削除するよう求められます。

- アプリ パスワードは、アプリごとではなく、デバイスごとに 1 つ使用します。 たとえば、ラップトップ上のすべてのアプリに対して単一のパスワードを作成してから、デスクトップ上のすべてのアプリに対してもう 1 つの別のパスワードを作成します。

    >[!Note]
    >Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートしており、2 段階認証を使用できます。 このサポートは、2 段階認証が有効になれば、以降は Office 2013 クライアントに対するアプリ パスワードは必要なくなることを意味します。 詳細については、「[Office 2013 クライアント アプリと Office 2016 クライアント アプリでの先進認証のしくみ](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)」記事を参照してください。

## <a name="where-to-create-and-delete-your-app-passwords"></a>アプリ パスワードを作成および削除する場所

アプリ パスワードは、最初の 2 段階認証の登録時に付与されます。 複数のパスワードが必要な場合、次のような 2 段階認証の使用方法に基づいて、追加のパスワードを作成できます。

- **職場または学校のアカウントと MyApps ポータルで 2 段階認証を使用する。** この記事の「MyApps ポータルを使用してアプリ パスワードを作成および削除する」セクションの手順を使用して、アプリ パスワードを作成および削除します。 MyApps ポータルとその使用方法の詳細については、「[Azure Active Directory の MyApps ポータルとは](active-directory-saas-access-panel-introduction.md)」を参照してください。

- **職場または学校のアカウントと Office 365 ポータルで 2 段階認証を使用する。** この記事の「[Office 365 ポータルを使用してアプリ パスワードを作成および削除する](#create-and-delete-app-passwords-using-the-office-365-portal)」セクションの手順を使用して、アプリ パスワードを作成および削除します。

- **個人の Microsoft アカウントで 2 段階認証を使用する。** 個人の Microsoft アカウントで [[Security basics]\(セキュリティの基礎\)](https://account.microsoft.com/account/) ページを使用して、アプリ パスワードを作成および削除します。 詳細については、「[アプリ パスワードと 2 段階認証 ](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification)」の記事を参照してください。

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>MyApps ポータルを使用してアプリ パスワードを作成および削除する
My Apps ポータル経由でアプリ パスワードを作成および削除できます。

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>MyApps ポータルでアプリ パスワードを作成するには

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。

2. 右上にある自分の名前を選択して、**[プロファイル]** を選択します。

3. **[追加のセキュリティ確認]** を選択します。

   ![[追加のセキュリティ確認] を選択する - スクリーンショット](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. **[アプリ パスワード]** を選択します。

   ![[アプリ パスワード] を選択する - スクリーンショット](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. **Create** をクリックしてください。

6. アプリ パスワードの名前を入力してから、**[次へ]** を選択します。

7. アプリ パスワードをクリップボードにコピーし、アプリに貼り付けます。
   
    ![アプリケーション パスワードの作成](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>MyApps ポータルでアプリ パスワードを削除するには

1. プロファイルに移動して、**[追加のセキュリティ確認]** を選択します。

2. **[アプリ パスワード]** を選択し、削除したいアプリ パスワードの横にある **[削除]** を選択します。

   ![アプリケーション パスワードの削除](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. **[はい]** を選択してパスワードの削除を確定し、**[閉じる]** を選択します。

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Office 365 ポータルを使用してアプリ パスワードを作成および削除する

職場または学校のアカウントと Office 365 アプリで 2 段階認証を使用する場合は、Office 365 ポータルを使用してアプリ パスワードを作成および削除できます。 一度に最大 40 個のアプリ パスワードを保持できます。 上限を超えてさらにアプリ パスワードが必要な場合は、既存のアプリ パスワードのいずれかを削除する必要があります。

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Office 365 ポータルを使用してアプリ パスワードを作成するには

1. 職場または学校のアカウントにサインインします。

2. [https://portal.office.com](https://portal.office.com) に移動して、**Office 365 ポータル** ページの右上の **[設定]** アイコンを選択し、**[追加のセキュリティ確認]** を展開します。

    ![展開した [追加のセキュリティ確認] 領域を示す Office ポータル](media/security-info/security-info-o365password.png)

3. **アプリ パスワードを作成して管理する**ことを示す該当のテキストを選択して、**アプリ パスワード** ページを開きます。

4. **[作成]** を選択し、アプリ パスワードが必要なアプリのフレンドリ名を入力し、**[次へ]** を選択します。

5. **[Copy password to clipboard]** \(パスワードをクリップボードにコピーする\) を選択して、**[閉じる]** を選択します。

6. コピーされたアプリ パスワードを使用して、非ブラウザー アプリにサインインします。 このパスワードは 1 回入力するだけでよく、以降は記憶されます。

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Office 365 ポータルでアプリ パスワードを削除するには

1. 職場または学校のアカウントにサインインします。

2. [https://portal.office.com](https://portal.office.com) に移動して、**Office 365 ポータル** ページの右上の **[設定]** アイコンを選択し、**[追加のセキュリティ確認]** を選択します。

3. **アプリ パスワードを作成して管理する**ことを示す該当のテキストを選択して、**アプリ パスワード** ページを開きます。

4. 削除するアプリ パスワードの **[削除]** を選択し、確認ボックスで **[はい]** を選択してから、**[閉じる]** を選択します。

    アプリ パスワードが正常に削除されます。

5. アプリ パスワードの作成手順に従って、新しいアプリ パスワードを作成します。

## <a name="if-your-app-passwords-arent-working-properly"></a>アプリ パスワードが適切に機能しない場合

パスワードを正しく入力したことを確認します。 パスワードを正しく入力したことをが確認できた場合、サインインを再試行して新しいアプリ パスワードを作成できます。 どちらの方法でも問題を解決できない場合は、既存のアプリ パスワードを削除して新しいパスワードを作成できるように、会社のサポート担当者に連絡してください。 

## <a name="next-steps"></a>次の手順

- [2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)

- SMS や電話を受ける代わりに、[Microsoft Authenticator アプリ](user-help-auth-app-download-install.md)の通知を使用してサインインを確認する
