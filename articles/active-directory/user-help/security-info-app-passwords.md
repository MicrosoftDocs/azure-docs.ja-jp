---
title: '[Security info (preview)] (セキュリティ情報 (プレビュー)) ページからアプリ パスワードを作成する - Azure AD'
description: 組織内の非ブラウザー アプリや 2 要素検証をサポートしていないアプリで使用する自動生成されたパスワード (アプリ パスワード) を作成します。 このアプリ パスワードは、通常のパスワードとは別のものであり、[セキュリティ情報] ページから設定できます。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 84588130788e9be8d3be52a8ea0f3988dce7b952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537006"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>[Security info (preview)] (セキュリティ情報 (プレビュー)) ページからアプリ パスワードを作成する

Outlook 2010 などの特定のアプリでは、2 段階認証はサポートされていません。 このサポートの欠如は、組織で 2 段階認証を使用している場合、アプリが動作しないことを意味します。 この問題を回避するために、通常のパスワードとは別に、各非ブラウザー アプリで使用する自動生成パスワードを作成できます。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>アプリ パスワードの使用が組織の管理者によって許可されていない場合があります。 オプションとして **[アプリ パスワード]** が表示されない場合、アプリ パスワードを組織で利用することはできません。

アプリ パスワードを使用する場合は、次の点に注意してください。

- アプリ パスワードは自動生成され、アプリごとに 1 回作成し、入力しなければならない。

- ユーザー 1 人あたりのパスワード数の上限は 40 個である。 この上限を超えて作成を試行すると、新しいパスワードの作成が許可されるように、事前に既存のパスワードを削除するよう求められます。

    >[!Note]
    >Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートしており、2 段階認証を使用できます。 このサポートは、2 段階認証が有効になれば、以降は Office 2013 クライアントに対するアプリ パスワードは必要なくなることを意味します。 詳細については、「[Office 2013 クライアント アプリと Office 2016 クライアント アプリでの先進認証のしくみ](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)」記事を参照してください。

## <a name="create-new-app-passwords"></a>新しいアプリ パスワードを作成する

職場または学校のアカウントで 2 段階認証を使用し、管理者によってセキュリティ情報エクスペリエンスが有効化されている場合は、 **[セキュリティ情報]** ページを使用してアプリ パスワードの作成と削除を実行できます。

>[!Note]
>管理者がセキュリティ情報エクスペリエンスを有効にしていない場合は、[2 段階認証用のアプリ パスワードの管理](multi-factor-authentication-end-user-app-passwords.md)に関するセクションの手順と情報に従う必要があります。

### <a name="to-create-a-new-app-password"></a>新しいアプリ パスワードを作成するには

1. 職場または学校アカウントにサインインした後、、 https://myaccount.microsoft.com/ ページに移動します。

    ![強調表示されているセキュリティ情報リンクを示す [マイ プロファイル] ページ](media/security-info/securityinfo-myprofile-page.png)

2. 左側のナビゲーション ウィンドウから、または **[セキュリティ情報]** ブロックのリンクから **[セキュリティ情報]** を選択した後、 **[セキュリティ情報]** ページで **[メソッドの追加]** を選択します。

    ![[メソッドの追加] オプションが強調表示されている [セキュリティ情報] ページ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **[メソッドの追加]** ページで、ドロップダウン リストから **[アプリ パスワード]** を選択し、 **[追加]** を選択します。

    ![[アプリ パスワード] が選択されている [メソッドの追加] ボックス](media/security-info/securityinfo-myprofile-addpassword.png)

4. アプリ パスワードを必要とするアプリの名前を入力し、 **[次へ]** を選択します。

    ![アプリの名前が入力された、[アプリ パスワード] ページを表示するスクリーンショット。](media/security-info/securityinfo-myprofile-password-appname.png)

5. **[パスワード]** ボックスのテキストをコピーし、そのパスワードを、アプリ (この例では Outlook 2010) のパスワード エリアに貼り付けた後、 **[完了]** を選択します。

    ![アプリの名前が表示されている [アプリ パスワード] ページ](media/security-info/securityinfo-myprofile-password-copytext.png)

    パスワードが追加され、今後はアプリに正常にログインできます。

## <a name="delete-your-app-passwords"></a>アプリ パスワードを削除する

アプリ パスワードを必要とするアプリの使用が不要になった場合は、関連付けられているアプリ パスワードを削除できます。 アプリ パスワードを削除すると、今後利用できるアプリ パスワードのいずれかのスポットが解放されまます。

>[!Important]
>アプリ パスワードを誤って削除した場合、元に戻す方法はありません。 この記事の「[新しいアプリ パスワードを作成する](#create-new-app-passwords)」の手順に従って新しいアプリ パスワードを作成し、アプリに再入力する必要あります。

### <a name="to-delete-an-app-password"></a>アプリ パスワードを削除するには

1. **[セキュリティ情報]** ページで、特定のアプリ用の **[アプリ パスワード]** オプションの横にある **[削除]** リンクを選択します。

    ![セキュリティ情報からアプリ パスワード メソッドを削除するためのリンク](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. 確認ボックスで **[はい]** を選択して、**アプリ パスワード** を削除します。 アプリ パスワードを削除すると、セキュリティ情報から削除され、 **[セキュリティ情報]** ページから消去されます。

## <a name="for-more-information"></a>詳細情報

- **[セキュリティ情報]** ページとその設定方法の詳細については、「[セキュリティ情報 (プレビュー) の概要](./security-info-setup-signin.md)」を参照してください。