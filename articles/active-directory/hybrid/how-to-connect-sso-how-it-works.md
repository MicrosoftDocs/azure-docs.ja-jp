---
title: Azure AD Connect:シームレス シングル サインオン - しくみ | Microsoft Docs
description: この記事では、Azure Active Directory シームレス シングル サインオン機能のしくみについて説明します。
services: active-directory
keywords: Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde937adba8d2469390a6cf404f6cce8c5008e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369647"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory シームレス シングル サインオン:技術的な詳細情報

この記事では、Azure Active Directory シームレス シングル サインオン (シームレス SSO) 機能の技術的なしくみについて説明します。

## <a name="how-does-seamless-sso-work"></a>シームレス SSO のしくみ

このセクションは、3 つの部分に分かれています。

1. シームレス SSO 機能の設定。
2. Web ブラウザーでの 1 人のユーザーのシングル サインイン トランザクションのシームレス SSO での動作。
3. ネイティブ クライアントでの 1 人のユーザーのシングル サインイン トランザクションのシームレス SSO での動作。

### <a name="how-does-set-up-work"></a>設定のしくみ

シームレス SSO は、[こちら](how-to-connect-sso-quick-start.md)で示す通り、Azure AD Connect を使用して有効にできます。 この機能を有効にすると、次の手順が発生します。

- (Azure AD Connect を使用して) Azure AD と同期する各 AD フォレストのオンプレミス Active Directory (AD) にコンピューター アカウント (`AZUREADSSOACC`) が作成されます。
- また、Azure AD のサインイン プロセス中に使用される多数の Kerberos サービス プリンシパル名 (SPN) が作成されます。
- コンピューター アカウントの Kerberos の復号化キーは、Azure AD と安全に共有されます。 複数の AD フォレストがある場合は、各コンピューター アカウントに、固有の Kerberos 復号化キーが割り当てられます。

>[!IMPORTANT]
> `AZUREADSSOACC` コンピューター アカウントは、セキュリティ上の理由から強固に保護する必要があります。 ドメイン管理者だけがこのコンピューター アカウントを管理できるようにしてください。 コンピューター アカウント上で Kerberos 委任が無効になっていること、および Active Directory 内の他のどのアカウントにも、`AZUREADSSOACC` コンピューター アカウント上の委任のアクセス許可がないことを確認してください。 このコンピューター アカウントは、不注意で削除されるおそれがなく、ドメイン管理者のみがアクセスできる組織単位 (OU) に格納してください。 このコンピューター アカウントの Kerberos の復号化キーも機密として扱う必要があります。 少なくとも 30 日ごとに、`AZUREADSSOACC` コンピューター アカウントの [Kerberos の復号化キーをロールオーバーする](how-to-connect-sso-faq.md)ことを強くお勧めします。

>[!IMPORTANT]
> シームレス SSO では、Kerberos の AES256_HMAC_SHA1、AES128_HMAC_SHA1、および RC4_HMAC_MD5 暗号化の種類がサポートされます。 AzureADSSOAcc$ アカウントの暗号化の種類を AES256_HMAC_SHA1 に設定するか、AES タイプまたはRC4 のいずれかに設定してセキュリティを強化することをお勧めします。 暗号化の種類は、Active Directory 内のアカウントの属性の msDS-SupportedEncryptionTypes 属性に格納されます。  AzureADSSOAcc$ アカウントの暗号化の種類が RC4_HMAC_MD5 に設定されていて、それを AES 暗号化の種類のいずれかに変更する場合は、[FAQ ドキュメント](how-to-connect-sso-faq.md)の関連する質問に説明されているように、まず AzureADSSOAcc$ アカウントの Kerberos 復号化キーをロールオーバーするようにしてください。そうしないと、シームレス SSO は行われません。

このセットアップが完了すると、シームレス SSO は、統合 Windows 認証 (IWA) を使用するその他のサインインと同様に機能します。

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Web ブラウザーでのシームレス SSO によるサインインのしくみ

Web ブラウザーでのサインインのフローは次のとおりです。

1. ユーザーは、web のアプリケーション（たとえば、outlook　Web アプリケーションに）、 https://outlook.office365.com/owa/) 企業ネットワーク内のドメインに参加している、会社のデバイスからアクセスします。
2. まだサインインしていない場合、ユーザーは、Azure AD のサインイン ページにリダイレクトされます。
3. ユーザーが、Azure AD サインイン ページにユーザー名を入力します。

   >[!NOTE]
   >[特定のアプリケーション](./how-to-connect-sso-faq.md)では、手順 2. と 3. をスキップします。

4. Azure AD が JavaScript をバックグラウンドで使用して、Kerberos チケットを提供するよう、401 認証エラーを通じてクライアントに要求します。
5. ブラウザーは、代わりに Active Directory から (Azure AD を表す) `AZUREADSSOACC` コンピューター アカウント用にチケットを要求します。
6. Active Directory がコンピューター アカウントを検索し、コンピューター アカウントのシークレットで暗号化された Kerberos チケットをブラウザーに返します。
7. ブラウザーは、Active Directory から取得した Kerberos チケットを Azure AD に転送します。
8. Azure AD が、会社のデバイスにサインインしているユーザーの ID を含む Kerberos チケットを以前に共有していたキーを使用して解読します。
9. 評価後、Azure AD はアプリケーションにトークンを返すか、多要素認証などの他の検査を実行するようユーザーに要求します。
10. ユーザーのサインインが成功すると、アプリケーションにアクセスできるようになります。

次の図に、すべてのコンポーネントと必要な手順を示します。

![シームレス シングル サインオン - Web アプリのフロー](./media/how-to-connect-sso-how-it-works/sso2.png)

シームレス SSO は便宜的であり、何らかの理由で失敗した場合、サインイン エクスペリエンスはその通常の動作に戻ります。つまり、ユーザーはサインインするためにパスワードを入力する必要があります。

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>ネイティブ クライアントでのシームレス SSO によるサインインのしくみ

ネイティブ クライアントでのサインインのフローは次のとおりです。

1. ユーザーは、(Outlook クライアントなどの) ネイティブ アプリケーションに企業ネットワーク内のドメインに参加している会社のデバイスからアクセスします。
2. ユーザーがまだサインインしていない場合は、ネイティブ アプリケーションが、デバイスの Windows セッションからそのユーザーのユーザー名を取得します。
3. アプリが、Azure AD にユーザー名を送信し、テナントの WS-Trust MEX エンドポイントを取得します。 この WS-Trust エンドポイントは、シームレス SSO 機能によって排他的に使用されるもので、Azure AD 上の WS-Trust プロトコルの一般的な実装ではありません。
4. 次にアプリは、統合認証エンドポイントが使用可能かどうかを確認するために、WS-Trust MEX エンドポイントにクエリを実行します。 統合認証エンドポイントは、シームレス SSO 機能によって排他的に使用されます。
5. 手順 4. が成功した場合は、Kerberos チャレンジが発行されます。
6. アプリが Kerberos チケットを取得できる場合は、Azure AD の統合認証エンドポイントにそのチケットを転送します。
7. Azure AD が Kerberos チケットを復号化して検証します。
8. Azure AD は、ユーザーをサインインさせ、アプリに SAML トークンを発行します。
9. アプリは、Azure AD の OAuth2 トークン エンドポイントに SAML トークンを送信します。
10. Azure AD は SAML トークンを検証し、指定されたリソースのアクセス トークンおよび更新トークンと、ID トークンをアプリに発行します。
11. ユーザーは、アプリのリソースにアクセスできます。

次の図に、すべてのコンポーネントと必要な手順を示します。

![シームレス シングル サインオン - ネイティブ アプリ フロー](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>次のステップ

- [**クイック スタート**](how-to-connect-sso-quick-start.md) - Azure AD シームレス SSO を動作させます。
- [**よく寄せられる質問**](how-to-connect-sso-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](tshoot-connect-sso.md) - この機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。
