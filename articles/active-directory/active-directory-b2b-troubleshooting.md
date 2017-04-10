---
title: "Azure Active Directory B2B コラボレーションのトラブルシューティング | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションの一般的な問題の対処方法"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/14/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 66c0084c89b5c7510196142afd27b58953d0dc86
ms.lasthandoff: 03/15/2017


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B コラボレーションのトラブルシューティング

以下に、Azure Active Directory (Azure AD) B2B コラボレーションの一般的な問題のいくつかの対処方法を示します。

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>既存の連絡先が理由で、外部ユーザーを作成できません

招待する外部ユーザーに既存の連絡先オブジェクトがある場合は、競合を解決する (通常は連絡先オブジェクトを削除する) まで、そのユーザーを招待することはできません。 B2B コラボレーションの一般公開までは、競合を手動で解決する必要があります。

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>外部ユーザーを追加しましたが、グローバル アドレス帳またはユーザー選択ウィンドウに表示されません

外部ユーザーが一覧に表示されない場合は、オブジェクトのレプリケートに数分かかっていることがあります。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B のゲスト ユーザーが SharePoint Online/OneDrive ユーザー選択ウィンドウに表示されません 
 
SharePoint Online のユーザー選択ウィンドウで既存のゲスト ユーザーを検索する機能は、従来の動作と一致させるために、既定ではオフになっています。
これは、テナントとサイト コレクション レベルで 'ShowPeoplePickerSuggestionsForGuestUsers' 設定を使用することで有効にできます。 この設定は、Set-SPOTenant コマンドレットと Set-SPOSite コマンドレットを使用して設定できます。これにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索することができます。 テナントのスコープの変更は、既にプロビジョニングされている SPO サイトには影響しません。

## <a name="invitations-have-been-disabled-for-directory"></a>ディレクトリに対して招待が無効になっています

ユーザーを招待するアクセス許可がないことを示すエラー メッセージが表示される場合は、自分のユーザー アカウントが外部ユーザーの招待を承認されていることを確認します。 この操作は、次のように、[ユーザー設定] で行うことができます。

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

これらの設定を変更したり、ユーザーにゲストの招待元ロールを割り当てたりしたばかりの場合は、変更が有効になるまでに 15 ～ 60 分かかることがあります。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>招待したユーザーが招待に応じようとしたときにエラー メッセージが表示されます

一般的なエラーの理由は、次のとおりです。

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>招待されたユーザーの管理者が、メールで確認済みのユーザーをテナント内に作成することを許可していません

Azure Active Directory を使用している組織のユーザーを招待していて、その特定のユーザーのアカウントが存在しない場合 (たとえばユーザーが AAD contoso.com に存在しない場合)、 contoso.com の管理者が、ユーザーが作成されないようにするポリシーを設定している可能性があります。 ユーザーは、外部ユーザーが許可されるかどうかを管理者に確認する必要があります。 外部ユーザーの管理者は、メールで確認済みのユーザーをドメインで許可しなければならない場合があります (メールで確認済みのユーザーの許可については、[こちらの記事](https://docs.microsoft.com/powershell/msonline/v1/set-msolcompanysettings#parameters)を参照してください)。

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部ユーザーがフェデレーション ドメインに既に存在しません

オンプレミスで認証が行われているフェデレーション ソリューションを外部ユーザーが使用していて、そのユーザーが既に Azure Active Directory に存在しない場合、そのユーザーを招待することはできません。

この問題を解決するには、外部ユーザーの管理者がユーザーのアカウントを Azure Active Directory に同期する必要があります。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>通常は無効な文字である "\#" は、どのように Azure AD と同期しますか。

"\#" は Azure AD B2B コラボレーションまたは外部ユーザーの UPN の予約文字です (つまり、招待された &lt;user@contoso.com&gt; は &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt; になります)。そのため、オンプレミスから来る UPN の \# では、Azure Portal へのサインインは許可されません。

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>同期済みグループに外部ユーザーを追加すると、エラー メッセージが表示されます

外部ユーザーは、"割り当て済み" または "セキュリティ" グループのみに追加できます。オンプレミスで管理されているグループには追加できません。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>外部ユーザーが招待の電子メールを受信しませんでした

招待されるユーザーは、&lt;Invites@microsoft.com&gt; というアドレスが許可されていることを、ISP に確認するか、スパム フィルターで確認する必要があります。

## <a name="my-recipient-received-multiple-emails-from-me"></a>受信者が複数の電子メールを受け取りました

招待の受信者がアカウントに複数のエイリアスを持っている場合は、2 通の招待を受信することがあります。 このような場合は、利用される最初のリンクでアカウントが作成され、2 番目に利用されるリンクは無効になります。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

