---
title: アプリ パスワードを管理する方法 - Azure Active Directory | Microsoft Docs
description: アプリ パスワードの詳細と、2 段階認証に関連した用途について学習します。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 07303a0b0b3007ade9adb90af7397855a5014cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179424"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>2 段階認証のアプリ パスワードを管理する

> [!Important]
>アプリ パスワードの使用が組織の管理者によって許可されていない場合があります。 オプションとして **[アプリ パスワード]** が表示されない場合、アプリ パスワードを組織で利用することはできません。

アプリ パスワードを使用する場合は、次の点に注意してください。

- アプリ パスワードは自動生成されるので、アプリごとに 1 回作成し、入力しなければならない。

- ユーザー 1 人あたりのパスワード数の上限は 40 個である。 この上限を超えて作成を試行すると、新しいパスワードの作成が許可されるように、事前に既存のパスワードを削除するよう求められます。

    >[!Note]
    >Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートしており、2 段階認証を使用できます。 このサポートは、2 段階認証が有効になれば、以降は Office 2013 クライアントに対するアプリ パスワードは必要なくなることを意味します。 詳細については、「[Office 2013 クライアント アプリと Office 2016 クライアント アプリでの先進認証のしくみ](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)」記事を参照してください。

## <a name="create-new-app-passwords"></a>新しいアプリ パスワードを作成する

初回の 2 要素認証登録プロセス中、アプリ パスワードが 1 つ与えられます。 複数のパスワードが必要な場合、自分で作成する必要があります。 組織における 2 要素認証の設定方法に基づき、アプリ パスワードは複数の領域から作成できます。 職場または学校のアカウントで 2 要素認証を使用する目的で登録する方法については、「[2 要素認証と職場または学校アカウントの概要](multi-factor-authentication-end-user-first-time.md)」とその関連記事を参照してください。

### <a name="where-to-create-and-delete-your-app-passwords"></a>アプリ パスワードを作成および削除する場所

2 要素認証の使用方法に基づいて、アプリ パスワードを作成し、削除できます。

- **組織で 2 要素認証と [追加のセキュリティ確認] ページを使用する。** 組織で 2 要素認証と共に職場または学校のアカウント (alain@contoso.com など) を使用している場合、[[追加のセキュリティ確認] ページ](https://account.activedirectory.windowsazure.com/Proofup.aspx)からアプリ パスワードを管理できます。 詳細な手順については、この記事の「[[追加のセキュリティ確認] ページを使用してアプリ パスワードを作成および削除する](#create-and-delete-app-passwords-from-the-additional-security-verification-page)」を参照してください。

- **組織で 2 要素認証と Office 365 ポータルを使用する。** 組織で職場または学校のアカウント (alain@contoso.com など)、2 要素認証、Microsoft 365 アプリを使用している場合、[Office 365 ポータル ページ](https://www.office.com)からアプリ パスワードを管理できます。 詳細な手順については、この記事の「[Office 365 ポータル ページを使用してアプリ パスワードを作成および削除する](#create-and-delete-app-passwords-using-the-office-365-portal)」を参照してください。

- **個人の Microsoft アカウントで 2 要素認証を使用している。** 2 要素認証と共に個人の Microsoft アカウント (alain@outlook.com など) を使用している場合、[[セキュリティの基本] ページ](https://account.microsoft.com/security/)からアプリ パスワードを管理できます。 詳細な手順については、[2 段階認証をサポートしていないアプリでのアプリ パスワードの使用](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)に関するページをご覧ください。

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>[追加のセキュリティ確認] ページを使用してアプリ パスワードを作成および削除する

アプリ パスワードは、職場または学校アカウントの **[追加のセキュリティ確認]** ページから作成し、削除できます。

1. [[追加のセキュリティ確認]](https://account.activedirectory.windowsazure.com/Proofup.aspx) ページにサインインし、 **[アプリ パスワード]** を選択します。

    ![[アプリ パスワード] タブが強調表示されている [アプリ パスワード] ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. **[作成]** を選択し、アプリ パスワードを必要とするアプリの名前を入力し、 **[次へ]** を選択します。

    ![パスワードを必要とするアプリの名前を入力する [アプリケーション パスワードの作成] ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. **[アプリケーション パスワード]** ページからパスワードをコピーし、 **[閉じる]** を選択します。

    ![指定したアプリのパスワードが表示されている [アプリ パスワード] ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. **[アプリ パスワード]** ページで、ご利用のアプリが一覧に表示されていることを確認します。

    ![新しいアプリが一覧に表示されている [アプリ パスワード] ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. アプリ パスワードを作成したアプリ (例: Outlook 2010) を開き、入力を求められたら、アプリ パスワードを貼り付けます。 これはアプリごとに 1 回だけ行う必要があります。

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>[アプリ パスワード] ページを使用してアプリ パスワードを削除するには

1. **[アプリ パスワード]** ページで、削除するアプリ パスワードの横にある **[削除]** を選択します。

   ![[アプリ パスワード] ページでアプリ パスワードの削除を示すスクリーンショット](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. **[はい]** を選択してパスワードの削除を確定し、 **[閉じる]** を選択します。

    アプリ パスワードが正常に削除されます。

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Office 365 ポータルを使用してアプリ パスワードを作成および削除する

職場または学校のアカウントと Microsoft 365 アプリで 2 段階認証を使用する場合は、Office 365 ポータルを使用してアプリ パスワードを作成および削除できます。

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Office 365 ポータルを使用してアプリ パスワードを作成するには

1. 職場または学校アカウントにサインインして、[[マイ アカウント]](https://myaccount.microsoft.com) ページに移動し、 **[セキュリティ情報]** を選択します。

    ![[セキュリティ情報] タブが表示されている Office ポータル](media/multi-factor-authentication-end-user-app-passwords/mfa-security-info.png)

2. **[方法の追加]** を選択し、ドロップダウン リストから **[アプリ パスワード]** を選択して、 **[追加]** をクリックします。

    ![[方法を追加します] のドロップダウン リストを含む、[セキュリティ情報] ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-add-method.png)

3. アプリ パスワードの名前を入力してから、 **[次へ]** を選択します。

    ![アプリ パスワードの名前を含む、アプリ パスワードの作成ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-enter-app-password-name.png)

4. **[アプリ パスワード]** ページからパスワードをコピーして、 **[完了]** を選択します。

    ![作成した新しいアプリ パスワードが表示された [アプリ パスワード] ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-copy-app-password.png)

5. **[セキュリティ情報]** ページで、アプリ パスワードの一覧が表示されていることを確認します。

    ![新しいアプリ パスワードが一覧に表示されている [セキュリティ情報] ページ](media/multi-factor-authentication-end-user-app-passwords/mfa-verify-app-password.png)  

6. アプリ パスワードを作成したアプリ (例: Outlook 2016) を開き、入力を求められたら、アプリ パスワードを貼り付けます。 これはアプリごとに 1 回だけ行う必要があります。

### <a name="to-delete-app-passwords-using-the-security-info-page"></a>[セキュリティ情報] ページを使用してアプリ パスワードを削除するには

1. **[セキュリティ情報]** ページで、削除するアプリ パスワードの横にある **[削除]** を選択します。

   ![[セキュリティ情報] ページのアプリ パスワードの削除を示すスクリーンショット](media/multi-factor-authentication-end-user-app-passwords/mfa-delete-app-password.png)

2. 確認ボックスで **[OK]** を選択します。

    アプリ パスワードが正常に削除されます。

## <a name="if-your-app-passwords-arent-working-properly"></a>アプリ パスワードが適切に機能しない場合

パスワードを正しく入力したことを確認します。 パスワードを正しく入力したことをが確認できた場合、サインインを再試行して新しいアプリ パスワードを作成できます。 どちらの方法でも問題を解決できない場合は、既存のアプリ パスワードを削除して新しいパスワードを作成できるように、組織のヘルプ デスクに連絡してください。

## <a name="next-steps"></a>次のステップ

- [2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)

- SMS や電話を受ける代わりに、[Microsoft Authenticator アプリ](user-help-auth-app-download-install.md)の通知を使用してサインインを確認する
