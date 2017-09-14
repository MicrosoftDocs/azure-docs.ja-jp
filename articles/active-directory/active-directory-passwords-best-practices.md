---
title: "展開: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワードのリセットを正常に展開するためのヒント"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 4cfc1652377f0cfd059e336aec6994b40d32c559
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="roll-out-password-reset-for-users"></a>ユーザーのパスワード リセットの展開

ほとんどのお客様は、次の手順に従って、SSPR 機能をスムーズに展開しています。

1. [ディレクトリでパスワードのリセットを有効にします](active-directory-passwords-getting-started.md)。
2. [パスワード ライトバック用にオンプレミスの AD のアクセス許可を構成します](active-directory-passwords-how-it-works.md#active-directory-permissions)。
3. [パスワード ライトバックを構成](active-directory-passwords-writeback.md#configuring-password-writeback)し、パスワードを Azure AD からオンプレミス ディレクトリに書き戻します。
4. [必要なライセンスを割り当て、確認します](active-directory-passwords-licensing.md)。
5. 段階的に展開する場合は、パスワードのリセットをユーザー グループに制限して、この機能を時間をかけて展開できます。 そのためには、**[セルフ サービスによるパスワードのリセットが有効]** を **[全員]** から **[グループ]** に切り替え、パスワードのリセットを有効にするセキュリティ グループを選択します。 そのグループのメンバー全員にライセンスが割り当てられている必要があります。これが[グループ ベースのライセンス](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing)を有効にする優れた方法です。
6. ポリシーに基づいて、[認証データ](active-directory-passwords-data.md)の最小セットを設定します。
7. 登録方法とリセット方法が記載されている手順書をユーザーに送信して、SSPR の使用方法を伝えます。
    > [!NOTE]
    > Microsoft では Azure 管理者タイプのアカウントに対して強力な認証要件を適用しているため、管理者ではなく、ユーザーで SSPR をテストします。 管理者のパスワード ポリシーの詳細については、[詳細な記事](active-directory-passwords-how-it-works.md)を参照してください。

8. 任意の時点で登録を強制し、一定期間が経ったら認証情報の再確認をユーザーに求めることができます。 ユーザーに登録を強制しない場合は、[エンド ユーザーの登録を必須にしないでパスワードのリセットをデプロイ](active-directory-passwords-data.md)できます。
9. 一定の期間、[Azure AD によって提供されるレポート](active-directory-passwords-reporting.md)を確認して、ユーザーの登録と使用状況をチェックします。

## <a name="email-based-rollout"></a>電子メール ベースの展開

多くのお客様は、電子メール キャンペーンで簡単な使用手順を配布することがユーザーの SSPR の使用を促す最も簡単な方法だと考えています。 [展開に役立つテンプレートとして使用できる 3 つの簡単な電子メールを作成しました。](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **準備中**電子メール テンプレート。ユーザーに必要な作業を通知するために展開の数週間前または数日前に使用します。
* **利用開始**電子メール テンプレート。ユーザーが必要なときに SSPR を使用できるように、登録し、認証データを確認してもらうために展開当日に使用します。
* **サインアップ リマインダー**電子メール テンプレート。ユーザーに登録と認証データの確認を行うよう念を押すためにデプロイから数週間に数日後に使用します。

## <a name="creating-your-own-password-portal"></a>独自のパスワードのポータルを作成する

大手企業のお客様の多くは、Web ページをホストし、ルート DNS エントリ (https://passwords.contoso.com など) を作成することを選択しています。そのページに Azure AD のパスワード リセット、パスワード リセットの登録、パスワード変更ポータル、およびその他の組織に固有の情報へのリンクを設けています。 ユーザーがサービスを使用する必要がある場合にアクセス可能な覚えやすいブランド化された URL を、送信する任意の電子メールまたは広告に含めることができます。

* パスワード リセット ポータル - https://passwordreset.microsoftonline.com/
* パスワード リセット登録ポータル - http://aka.ms/ssprsetup
* パスワード変更ポータル - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>強制登録の使用

ユーザーにパスワード リセットの登録を行ってもらう場合は、ユーザーが Azure AD を使用してサインインしたときに、登録を強制できます。 このオプションは、ディレクトリの **[パスワード リセット]** ブレードから **[登録]** タブの **[サインイン時にユーザーに登録を求めますか]** オプションを有効にすることで、有効にできます。

管理者は、**[ユーザーが認証情報を再確認するように求められるまでの日数]** を 0 ～ 730 日の範囲で設定することで、一定期間が経過したらユーザーに再登録を求めることができます。

このオプションを有効にすると、ユーザーがサインインしたときに、管理者がユーザーに認証情報を確認するよう求めていることを通知するメッセージが表示されます。

## <a name="populate-authentication-data"></a>認証データの設定

[ユーザーの代わりに認証データを設定する](active-directory-passwords-data.md)と、SSPR を利用する前にユーザーがパスワード リセットの登録を行う必要がありません。 ユーザーは、定義されたパスワード リセット ポリシーに従って認証データが定義されている限り、自分のパスワードをリセットできます。

## <a name="disabling-self-service-password-reset"></a>セルフ サービスによるパスワードのリセットの無効化

セルフ サービスによるパスワードのリセットを無効にするのは簡単です。Azure AD テナントを開き、**[パスワード リセット]**、**[プロパティ]** の順に移動し、**[セルフ サービスによるパスワードのリセットが有効]** で **[なし]** を選択します。

## <a name="next-steps"></a>次のステップ

次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [**クイック スタート**](active-directory-passwords-getting-started.md) - Azure AD のセルフサービスによるパスワードのリセットの管理を始めることができます。 
* [**ライセンス**](active-directory-passwords-licensing.md) - Azure AD のライセンスを構成します。
* [**データ**](active-directory-passwords-data.md) - パスワード管理に必要なデータとその使用方法がわかります
* [**カスタマイズ**](active-directory-passwords-customize.md) - 会社の SSPR エクスペリエンスの外観をカスタマイズします
* [**ポリシー**](active-directory-passwords-policy.md) - Azure AD のパスワード ポリシーを把握し、設定します。
* [**パスワード ライトバック**](active-directory-passwords-writeback.md) - オンプレミスのディレクトリでのパスワード ライトバックのしくみ
* [**レポート**](active-directory-passwords-reporting.md) - ユーザーが SSPR 機能にアクセスしたかどうかや、アクセスしたタイミングと場所を検出します。
* [**技術的詳細**](active-directory-passwords-how-it-works.md) - しくみを詳しく説明しています
* [**よく寄せられる質問**](active-directory-passwords-faq.md) - どのようにですか? なぜですか? 何ですか? どこですか? 誰がですか? いつですか? - ずっと確認したかった質問に対する回答
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - SSPR の一般的な問題を解決する方法について説明しています
