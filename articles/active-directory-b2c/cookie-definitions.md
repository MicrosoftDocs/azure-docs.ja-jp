---
title: Cookie の定義
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で使われる Cookie の定義を提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e44e2e1eb37e808e60134a6fba5051552e84029
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389345"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C での Cookie の定義

以下のセクションでは、Azure Active Directory B2C (Azure AD B2C) で使用される Cookie に関する情報を提供します。

## <a name="samesite"></a>SameSite

Microsoft Azure AD B2C サービスは、SameSite ブラウザー構成と互換性があります。これには、`SameSite=None` と、`Secure` 属性のサポートが含まれます。

サイトへのアクセスを保護するために、Web ブラウザーでは、新しい "既定でセキュリティ保護" モデルが導入されます。このモデルでは、特に指定のない場合、すべての Cookie を外部アクセスから保護していることが前提になります。 この変更が最初に実装されるのは Chrome ブラウザーであり、[2020 年 2 月の Chrome 80](https://www.chromium.org/updates/same-site) から実装されます。 Chrome での変更に向けた準備の詳細については、「[Developers: Get Ready for New SameSite=None; Secure Cookie Settings](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)」(開発者へ: 新しい SameSite=None; セキュア Cookie 設定に備えよ) という Chromium ブログの記事を参照してください。

開発者は、新しい Cookie 設定 `SameSite=None` を使用して、クロスサイト アクセス用の Cookie を指定する必要があります。 `SameSite=None` 属性が存在する場合は、追加の `Secure` 属性を使用して、クロスサイト Cookie には HTTPS 接続経由でしかアクセスできないようにする必要があります。 Azure AD B2C を使用するアプリケーションをはじめ、すべてのアプリケーションを検証およびテストします。

詳細については、次を参照してください。

* [Chrome ブラウザーにおける SameSite Cookie の変更の処理](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Chrome バージョン 80 以降のお客様の Web サイトと Microsoft のサービスおよび製品への影響](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookie

次の表では、Azure AD B2C で使われる Cookie の一覧を示します。

| 名前 | Domain | 有効期限 | 目的 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | [ブラウザー セッション](session-behavior.md)の終了 | テナント間でユーザーのメンバーシップ データを保持します。 ユーザーがメンバーになっているテナントと、メンバーシップのレベル (管理者またはユーザー) です。 |
| `x-ms-cpim-slice` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | 適切な運用インスタンスに要求をルーティングするために使われます。 |
| `x-ms-cpim-trans` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | トランザクション (Azure AD B2C に対する認証要求の数) と現在のトランザクションを追跡するために使われます。 |
| `x-ms-cpim-sso:{Id}` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | SSO セッションを維持するために使われます。 |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了、認証の成功 | 要求の状態を維持するために使われます。 |
| `x-ms-cpim-csrf` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | クロスサイト リクエスト フォージェリの保護に使われる CRSF トークン。 |
| `x-ms-cpim-dc` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | Azure AD B2C のネットワーク ルーティングに使われます。 |
| `x-ms-cpim-ctx` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | Context |
| `x-ms-cpim-rp` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | リソース プロバイダー テナントのメンバーシップ データの格納に使われます。 |
| `x-ms-cpim-rc` | b2clogin.com、login.microsoftonline.com、ブランド化されたドメイン | [ブラウザー セッション](session-behavior.md)の終了 | リレー Cookie を格納するために使われます。 |
