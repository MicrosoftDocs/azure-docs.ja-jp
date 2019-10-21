---
title: B2B コラボレーションのトラブルシューティング - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの一般的な問題の対処方法
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: v-miegge
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f91ddee8668316df69c98ed14fbcabcb06b6da82
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983390"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B コラボレーションのトラブルシューティング

以下に、Azure Active Directory (Azure AD) B2B コラボレーションの一般的な問題のいくつかの対処方法を示します。

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>外部ユーザーを追加しましたが、グローバル アドレス帳またはユーザー選択ウィンドウに表示されません

外部ユーザーが一覧に表示されない場合は、オブジェクトのレプリケートに数分かかっていることがあります。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B のゲスト ユーザーが SharePoint Online/OneDrive ユーザー選択ウィンドウに表示されません

SharePoint Online (SPO) のユーザー選択ウィンドウで既存のゲスト ユーザーを検索する機能は、従来の動作と一致させるために、既定ではオフになっています。

この機能は、テナントとサイト コレクション レベルで 'ShowPeoplePickerSuggestionsForGuestUsers' 設定を使用することで有効にできます。 この機能は、Set-SPOTenant コマンドレットと Set-SPOSite コマンドレットを使用して設定できます。これにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索することができます。 テナントのスコープの変更は、既にプロビジョニングされている SPO サイトには影響しません。

## <a name="invitations-have-been-disabled-for-directory"></a>ディレクトリに対して招待が無効になっています

ユーザーを招待するアクセス許可がないことを通知された場合は、[Azure Active Directory] > [ユーザー設定] > [外部ユーザー] > [Manage external collaboration settings]\(外部コラボレーションの設定の管理\) の順に選択して、自分のユーザー アカウントが外部ユーザーの招待を承認されていることを確認します。

![[外部ユーザー] 設定を示すスクリーンショット](media/troubleshoot/external-user-settings.png)

これらの設定を変更したり、ユーザーにゲストの招待元ロールを割り当てたりしたばかりの場合は、変更が有効になるまでに 15 ～ 60 分かかることがあります。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>招待したユーザーが招待に応じようとしたときにエラー メッセージが表示されます

一般的なエラーの理由は、次のとおりです。

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>招待されたユーザーの管理者が、メールで確認済みのユーザーをテナント内に作成することを許可していません

Azure Active Directory を使用している組織のユーザーを招待していて、その特定のユーザーのアカウントが存在しない場合 (たとえばユーザーが AAD contoso.com に存在しない場合)、 contoso.com の管理者が、ユーザーが作成されないようにするポリシーを設定している可能性があります。 ユーザーは、外部ユーザーが許可されるかどうかを管理者に確認する必要があります。 外部ユーザーの管理者は、メールで確認済みのユーザーをドメインで許可しなければならない場合があります (メールで確認済みのユーザーの許可については、[こちらの記事](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)を参照してください)。

![メールで確認済みのユーザーがテナントで許可されないことを示すエラー](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部ユーザーがフェデレーション ドメインに既に存在しません

フェデレーション認証を使用しているときに、ユーザーが Azure Active Directory に既に存在しない場合は、そのユーザーを招待することはできません。

この問題を解決するには、外部ユーザーの管理者がユーザーのアカウントを Azure Active Directory に同期する必要があります。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>通常は無効な文字である "\#" は、どのように Azure AD と同期しますか。

"\#" は、Azure AD B2B コラボレーションまたは外部ユーザー向けの UPN 内の予約文字です。理由は、招待されたアカウント user@contoso.com が user_contoso.com#EXT#@fabrikam.onmicrosoft.com になるためです。 したがって、オンプレミスから送信されるUPN 内の \# は、Azure ポータルにサインインするために使用することはできません。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>同期済みグループに外部ユーザーを追加すると、エラー メッセージが表示されます

外部ユーザーは、"割り当て済み" または "セキュリティ" グループのみに追加できます。オンプレミスで管理されているグループには追加できません。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>外部ユーザーが招待の電子メールを受信しませんでした

招待されるユーザーは、Invites@microsoft.com というアドレスが許可されていることを ISP またはスパム フィルターで確認する必要があります。

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>カスタム メッセージが招待メッセージに含まれないことがあります

プライバシーに関する法律を順守するため、以下に該当する場合、API は、電子メール招待状にカスタム メッセージを含めません。

- 招待元が招待元のテナントで電子メール アドレスを持っていない場合
- appservice プリンシパルが招待を送信する場合

これがお客様にとって重要なシナリオである場合は、API による招待メール送信を抑制し、選択した電子メール メカニズムを使用して送信できます。 お客様の組織の弁護士に相談して、この方法による電子メールの送信がプライバシーに関する法律を順守していることを確認してください。

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Azure リソースにログインしようとすると “AADSTS65005” エラーを受け取ります

ゲスト アカウントを持つユーザーがログオンできず、次のエラー メッセージを受け取ります。

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

このユーザーは Azure ユーザー アカウントを持っており、破棄されているか非管理対象のバイラル テナントです。 さらに、このテナントにはグローバル管理者も会社の管理者もいません。

この問題を解決するには、破棄されたテナントを引き継ぐ必要があります。 「[Azure Active Directory の非管理対象ディレクトリを管理者として引き継ぐ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)」を参照してください。 また、自分が名前空間を管理しているという直接の証拠を提供するために、問題のドメイン サフィックスに対するインターネット接続の DNS にアクセスする必要があります。 テナントが管理対象状態に戻ったら、ユーザーおよび検証済みドメイン名を残すことが組織にとって最良の選択肢かどうかをお客様と話し合ってください。

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Just-In-Time または "バイラル” テナントを持つゲスト ユーザーは、自分のパスワードをリセットすることはできません

ID テナントが Just-In-Time (JIT) テナントまたはバイラル テナント (つまり、独立したアンマネージド Azure テナント) である場合は、ゲスト ユーザーだけが自分のパスワードをリセットできます。 場合によっては、組織が、従業員が仕事用メール アドレスを使用してサービスにサインアップするときに作成される[バイラル テナントの管理を引き継ぎます](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)。 組織がバイラル テナントを引き継いだ後は、その組織の管理者しか、ユーザーのパスワードをリセットしたり SSPR を有効にしたりできなくなります。 必要に応じて、招待側の組織としては、ディレクトリからゲスト ユーザー アカウントを削除し、招待を再送信することができます。

## <a name="next-steps"></a>次の手順

[B2B コラボレーションのサポートの利用](get-support.md)
