---
title: クイック スタート Azure AD のセルフサービスによるパスワードのリセット
description: このクイック スタートでは、ユーザーが自分のパスワードをリセットできるように、Azure AD のセルフサービスによるパスワードのリセットをすばやく構成します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: microsoftguyjflo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58e3254d499e013dc686bf6b7d53f919a457c901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371285"
---
# <a name="quickstart-self-service-password-reset"></a>クイック スタート: セルフサービスのパスワード リセット

このクイック スタートでは、IT 管理者がユーザーにパスワードのリセットやアカウントのロック解除を許可するための単純な方法として、セルフサービスによるパスワードのリセット (SSPR) を構成する方法を説明します。

## <a name="prerequisites"></a>前提条件

* 少なくとも試用版ライセンスが有効になっている、動作している Azure AD テナント。
* グローバル管理者特権を持つアカウント。
* 管理者が知っているパスワードを持つ、管理者以外のテスト ユーザー。ユーザーを作成する必要がある場合は、「[クイック スタート:Azure Active Directory に新しいユーザーを追加する](../add-users-azure-active-directory.md)」を参照してください。
* 管理者以外のテスト ユーザーが所属している、テストするパイロット グループ。グループを作成する必要がある場合は、「[Azure Active Directory でグループを作成し、メンバーを追加する](../active-directory-groups-create-azure-portal.md)」を参照してください。

## <a name="enable-self-service-password-reset"></a>セルフ サービスのパスワード リセットを有効にする

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. 既存の Azure AD テナントから、**Azure portal** の **[Azure Active Directory]** で **[パスワードのリセット]** を選択します。

2. **[プロパティ]** ページの **[セルフ サービスによるパスワードのリセットが有効]** オプションの下で、 **[選択済み]** を選択します。
    * **[グループの選択]** で、この記事の前提条件セクションで作成したパイロット グループを選択します。
    * **[保存]** をクリックします。

3. **[認証方法]** ページで、以下を選択します。
   * リセットのために必要な方法の数: **1**
   * ユーザーが使用できる方法:
      * **電子メール**
      * **モバイル アプリ コード (プレビュー)**
   * **[保存]** をクリックします。

     ![SSPR 用の認証方法を選択する][Authentication]

4. **[登録]** ページで、以下を選択します。
   * サインイン時にユーザーに登録を求める:**はい**
   * ユーザーが認証情報を再確認するように求められるまでの日数を設定する:**365**

## <a name="test-self-service-password-reset"></a>セルフサービスによるパスワードのリセットをテストする

テスト ユーザーで SSPR の構成をテストしてみましょう。 Microsoft は Azure 管理者アカウントに対して強力な認証要件を強制しているため、管理者アカウントを使用してテストすると結果が変わる可能性があります。 管理者のパスワード ポリシーの詳細については、[パスワード ポリシー](concept-sspr-policy.md)に関する記事を参照してください。

1. InPrivate または シークレット モードで新しいブラウザー ウィンドウを開き、[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) に移動します。
2. 管理者以外のテスト ユーザーとしてサインインし、認証用電話を登録します。
3. 完了したら、 **[問題ありません]** ボタンをクリックし、ブラウザー ウィンドウを閉じます。
4. InPrivate またはシークレット モードで新しいブラウザー ウィンドウを開き、[https://aka.ms/sspr](https://aka.ms/sspr) に移動します。
5. 管理者以外のテスト ユーザーのユーザー ID、CAPTCHA の文字を入力し、 **[次へ]** をクリックします。
6. 検証の手順に従って、パスワードをリセットします

## <a name="clean-up-resources"></a>リソースのクリーンアップ

セルフサービスのパスワード リセットは簡単に無効にできます。 Azure AD テナントを開いて **[プロパティ]**  >  **[パスワードのリセット]** の順に移動し、 **[セルフ サービスによるパスワードのリセットが有効]** を **[なし]** に選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、クラウド限定ユーザーのためにセルフサービスによるパスワードのリセットをすばやく構成する方法について説明しました。 より詳細なロール アウトを完了する方法については、ロール アウト ガイドに進んでください。

> [!div class="nextstepaction"]
> [セルフサービスによるパスワードのリセットをロール アウトする](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Azure AD の使用できる認証方法と必要な数量"
