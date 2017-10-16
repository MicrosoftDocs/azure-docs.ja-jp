---
title: "Azure AD Connect: シームレス シングル サインオン - しくみ | Microsoft Docs"
description: "この記事では、Azure Active Directory シームレス シングル サインオン機能のしくみについて説明します。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 38b107513e72635fd034bb86d0d866bcb0fcb8e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory シームレス シングル サインオン: 技術的な詳細

この記事では、Azure Active Directory シームレス シングル サインオン (シームレス SSO) 機能の技術的なしくみについて説明します。

## <a name="how-does-seamless-sso-work"></a>シームレス SSO のしくみ

このセクションは、2 つの部分に分かれています。
1. シームレス SSO 機能の設定。
2. 1 人のユーザーのシングル サインイン トランザクションのシームレス SSO での動作。

### <a name="how-does-set-up-work"></a>設定のしくみ

シームレス SSO は、[こちら](active-directory-aadconnect-sso-quick-start.md)で示す通り、Azure AD Connect を使用して有効にできます。 この機能を有効にすると、次の手順が発生します。
- オンプレミスの Active Directory (AD) で (Azure AD を表す) `AZUREADSSOACC` という名前のコンピューター アカウントが作成されます。
- コンピューター アカウントの Kerberos の復号化キーは、Azure AD と安全に共有されます。
- また、Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) も作成されます。

>[!NOTE]
> (Azure AD Connect を使用して) Azure AD と同期する各 AD フォレストとシームレス SSO を有効にするユーザー用に、コンピューター アカウントと Kerberos SPN が作成されます。 同じ方法で管理され削除されないことを保証するために、その他のコンピューター アカウントが格納されている `AZUREADSSOACC` コンピューター アカウントに、組織単位 (OU) が移動されます。

>[!IMPORTANT]
>少なくとも 30 日ごとに、`AZUREADSSOACC` コンピューター アカウントの [Kerberos の復号化キーをロールオーバーする](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)ことを強くお勧めします。

### <a name="how-does-sign-in-with-seamless-sso-work"></a>シームレス SSO でのサインインのしくみ

このセットアップが完了すると、シームレス SSO は、統合 Windows 認証 (IWA) を使用するその他のサインインと同様に機能します。 フローは次のとおりです。

1. ユーザーは、(https://outlook.office365.com/owa/ の Outlook Web App などの) アプリケーションに企業ネットワーク内のドメインに参加している会社のデバイスからアクセスします。
2. まだサインインしていない場合、ユーザーは、Azure AD のサインイン ページにリダイレクトされます。

  >[!NOTE]
  >Azure AD に対するサインイン要求に、`domain_hint` (例: テナントを識別する contoso.onmicrosoft.com) または `login_hint` (例: ユーザーを識別する user@contoso.onmicrosoft.com または user@contoso.com) パラメーターが含まれている場合は、手順 2 は省略されます。

3. ユーザーが、Azure AD サインイン ページにユーザー名を入力します。
4. Azure AD が JavaScript をバックグラウンドで使用して、Kerberos チケットを提供するよう、401 認証エラーを通じてクライアントに要求します。
5. ブラウザーは、代わりに Active Directory から (Azure AD を表す) `AZUREADSSOACC` コンピューター アカウント用にチケットを要求します。
6. Active Directory がコンピューター アカウントを検索し、コンピューター アカウントのシークレットで暗号化された Kerberos チケットをブラウザーに返します。
7. ブラウザーが Active Directory から取得した Kerberos チケットが Azure AD ([ブラウザーのイントラネット ゾーンの設定に追加した Azure AD の URL の 1 つ](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature)) に転送されます。
8. Azure AD が、会社のデバイスにサインインしているユーザーの ID を含む Kerberos チケットを以前に共有していたキーを使用して解読します。
9. 評価後、Azure AD はアプリケーションにトークンを返すか、多要素認証などの他の検査を実行するようユーザーに要求します。
10. ユーザーのサインインが成功すると、アプリケーションにアクセスできるようになります。

次の図に、すべてのコンポーネントと必要な手順を示します。

![シームレス シングル サインオン](./media/active-directory-aadconnect-sso/sso2.png)

シームレス SSO は便宜的であり、何らかの理由で失敗した場合、サインイン エクスペリエンスはその通常の動作に戻ります。つまり、ユーザーはサインインするためにパスワードを入力する必要があります。

## <a name="next-steps"></a>次のステップ

- [**クイック スタート**](active-directory-aadconnect-sso-quick-start.md) - Azure AD シームレス SSO を動作させます。
- [**よく寄せられる質問**](active-directory-aadconnect-sso-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-sso.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。
