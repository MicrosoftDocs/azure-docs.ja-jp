---
title: "クイック スタート: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワードのリセットを迅速にデプロイする"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 07c7f3ad066c735054cb339f6e09aa4d7d23f23a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/11/2017

---
# <a name="quickstart-azure-ad-self-service-password-reset"></a>クイック スタート: Azure AD のセルフ サービスによるパスワードのリセット

> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md)にお進みください。

## <a name="rapidly-deploy-self-service-password-reset"></a>セルフ サービスによるパスワードのリセットを迅速にデプロイする

セルフ サービスによるパスワードのリセット (SSPR) は、ユーザーが自分のパスワードやアカウントのリセットまたはロック解除を行うことができるようにする簡単な方法を IT 管理者に提供します。 このシステムには、ユーザーがいつシステムを使用したかを追跡する詳細なレポートと、誤用や悪用について警告する通知が用意されています。

このガイドでは、試用版またはライセンス版の Azure AD テナントが既に稼働していることを前提としています。 Azure AD の設定に関するヘルプが必要な場合は、[Azure AD の概要](https://azure.microsoft.com/trial/get-started-active-directory/)に関する記事を参照してください。

1. 既存の Azure AD テナントから **[パスワードのリセット]** を選択します。

2. **[プロパティ]** 画面の [セルフ サービスによるパスワードのリセットが有効] オプションで、次のいずれかを選択します。
    * [なし] - 誰も SSPR 機能を使用できません
    * [グループ] - 選択した特定の Azure AD グループのメンバーだけが SSPR 機能を使用できます
    * [全員] - Azure AD テナントにアカウントを持つすべてのユーザーが SSPR 機能を使用できます

3. **[認証方法]** 画面で、次を選択します。
    * [リセットのために必要な方法の数] - 最小で 1 つ、最大で 2 つサポートしています
    * [ユーザーが使用できる方法] - 少なくとも 1 つ必要ですが、追加の方法があってもかまいません
        * **[電子メール]** - ユーザーが構成した認証用電子メール アドレスにコードが送信されます
        * **[携帯電話]** - ユーザーは、構成した携帯電話番号への通話またはテキスト メッセージのどちらでコードを受け取るかを選択できます
        * **[会社電話]** - ユーザーは、構成した会社の電話番号への通話でコードを受け取ります
        * **[セキュリティの質問]** - 管理者が選択する必要があります
            * [登録する必要がある質問の数] は、登録を完了するための最小数です。そのため、ユーザーは、選択可能な質問をたくさん作成しておいて、それ以上の数の質問に回答することができます。 このオプションには 3 ～ 5 を設定できます。[リセットに必要な質問の数] 以上にする必要があります。
                * セキュリティの質問を選択する際に [カスタム] をクリックすると、カスタムの質問を追加することができます。
            * [リセットに必要な質問の数] には、ユーザーがパスワードをリセットまたはロック解除するために正解する必要がある質問の数を 3 ～ 5 の範囲で設定できます。

4. 推奨: **[カスタマイズ]** を使用すると、定義したページまたは電子メール アドレスを指すように [管理者に連絡してください] リンクを変更できます

5. 省略可能: **[登録]** 画面には、管理者向けの次のオプションが表示されます。
    * [サインイン時にユーザーに登録を求めますか]
    * [ユーザーが認証情報を再確認するように求められるまでの日数]

6. 省略可能: **[通知]** 画面には、管理者向けの次のオプションが表示されます。
    * [パスワードのリセットについてユーザーに通知しますか]
    * [他の管理者が自分のパスワードをリセットしたときに、すべての管理者に通知しますか]

**この時点で、Azure AD テナントの SSPR が構成できました**。 ここで止めるか、続いて、オンプレミスの AD ドメインへのパスワードの同期を構成することができます。

> [!NOTE]
> Microsoft では Azure 管理者タイプのアカウントに対して強力な認証要件を適用しているため、管理者ではなく、ユーザーで SSPR をテストします。 管理者のパスワード ポリシーの詳細については、[パスワード ポリシーに関する記事](active-directory-passwords-policy.md#administrator-password-policy-differences)を参照してください。

## <a name="configure-synchronization-to-existing-identity-source"></a>既存の ID ソースへの同期の構成

Azure AD へのオンプレミスの ID 同期を有効にするには、組織のサーバーに [Azure AD Connect](./connect/active-directory-aadconnect.md) をインストールして構成する必要があります。 このアプリケーションは、既存の ID ソースから Azure AD テナントへのユーザーとグループの同期を処理します。

* [DirSync または Azure AD Sync から Azure AD Connect へのアップグレード](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [簡単設定を使用した Azure AD Connect の開始](./connect/active-directory-aadconnect-get-started-express.md)
* [パスワード ライトバックを構成](active-directory-passwords-writeback.md#configuring-password-writeback)し、パスワードを Azure AD からオンプレミス ディレクトリに書き戻します。

## <a name="disabling-self-service-password-reset"></a>セルフ サービスによるパスワードのリセットの無効化

セルフ サービスによるパスワードのリセットを無効にするのは簡単です。Azure AD テナントを開き、**[パスワード リセット]、[プロパティ]** の順に移動し、**[セルフ サービスによるパスワードのリセットが有効]** で **[なし]** を選択します。

### <a name="learn-more"></a>詳細情報
次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [**ライセンス**](active-directory-passwords-licensing.md) - Azure AD のライセンスを構成します
* [**データ**](active-directory-passwords-data.md) - パスワード管理に必要なデータとその使用方法がわかります
* [**展開**](active-directory-passwords-best-practices.md) - ここで見つかるガイダンスを使用してユーザーに対する SSPR を計画してデプロイできます
* [**カスタマイズ**](active-directory-passwords-customize.md) - 会社の SSPR エクスペリエンスの外観をカスタマイズします。
* [**ポリシー**](active-directory-passwords-policy.md) - Azure AD のパスワード ポリシーを把握し、設定します
* [**レポート**](active-directory-passwords-reporting.md) - ユーザーが SSPR 機能にアクセスしたかどうかや、アクセスしたタイミングと場所を検出します
* [**技術的詳細**](active-directory-passwords-how-it-works.md) - しくみを詳しく説明しています
* [**よく寄せられる質問**](active-directory-passwords-faq.md) - どのようにですか? なぜですか? 何ですか? どこですか? 誰がですか? いつですか? - ずっと確認したかった質問に対する回答
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - SSPR の一般的な問題を解決する方法について説明しています

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、ユーザーのためにセルフ サービスによるパスワードのリセットを構成する方法について説明しました。 引き続き Azure Portal でこれらの手順を実行するには、以下のリンクからポータルにアクセスしてください。

> [!div class="nextstepaction"]
> [セルフ サービスのパスワード リセットを有効にする](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)


