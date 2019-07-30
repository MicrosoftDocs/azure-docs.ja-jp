---
title: Azure Active Directory アプリケーション プロキシのワイルドカード アプリケーション | Microsoft Docs
description: Azure Active Directory アプリケーション プロキシでワイルドカード アプリケーションを使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd29fc00a1c25a7c092393591060ca7e2938155
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481265"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシのワイルドカード アプリケーション

Azure Active Directory (Azure AD) では、大量のオンプレミス アプリケーションを構成すると、すぐに管理できなくなる可能性があります。また、それらのアプリケーションの多数で同じ設定が必要な場合に構成エラーが発生する不要なリスクが生じます。 [Azure AD アプリケーション プロキシ](application-proxy.md)では、ワイルドカード アプリケーション発行を使用して多数のアプリケーションを同時に発行および管理し、この問題を解決できます。 このソリューションでは、以下が可能になります。

- 管理オーバーヘッドを軽減する
- 潜在的な構成エラーの数を減少させる
- ユーザーがより多くのリソースに安全にアクセスできるようにする

この記事では、ご利用の環境でワイルドカード アプリケーション発行を構成するために必要な情報を提供します。

## <a name="create-a-wildcard-application"></a>ワイルドカード アプリケーションの作成

構成が同じアプリケーションのグループがある場合、ワイルドカード (*) アプリケーションを作成できます。 ワイルドカード アプリケーションの候補として考えられるのは、次の設定を共有しているアプリケーションです。

- それらへのアクセス権を持つユーザーのグループ
- SSO 方法
- アクセス プロトコル (http、https)

内部 URL と外部 URL の両方が次の形式の場合、ワイルドカードでアプリケーションを発行できます。

> http(s)://*.\<ドメイン\>

(例: `http(s)://*.adventure-works.com`)。 内部 URL と外部 URL には異なるドメインを使用できますが、同じドメインにすることがベスト プラクティスとして推奨されます。 アプリケーションの発行時、いずれかの URL にワイルドカードがないとエラーが表示されます。

構成設定が異なるその他のアプリケーションがある場合、これらの例外を別個のアプリケーションとして発行し、ワイルドカードに設定された既定値を上書きする必要があります。 ワイルドカードのないアプリケーションは、常にワイルドカード アプリケーションよりも優先されます。 構成上、これらは "ただの" 通常のアプリケーションです。

ワイルドカード アプリケーションの作成は、他のすべてのアプリケーションで使用できるのと同じ[アプリケーション発行フロー](application-proxy-add-on-premises-application.md)に基づきます。 唯一の違いは、URL にワイルドカードを含めることだけです。場合によっては SSO 構成にもワイルドカードを含めます。

## <a name="prerequisites"></a>前提条件

開始するには、これらの要件を満たしていることを確認してください。

### <a name="custom-domains"></a>カスタム ドメイン

[カスタム ドメイン](application-proxy-configure-custom-domain.md)は他のすべてのアプリケーションではオプションですが、ワイルドカード アプリケーションでは前提条件です。 カスタム ドメインの作成には以下が必要です。

1. Azure 内で確認済みドメインを作成する。
2. PFX 形式の SSL 証明書をアプリケーション プロキシにアップロードする。

作成する予定のアプリケーションに一致するワイルドカード証明書を使用することを検討する必要があります。 または、特定のアプリケーションのみが記載された証明書を使用することもできます。 この場合、証明書に記載されているアプリケーションのみが、ワイルドカード アプリケーションを通じてアクセス可能になります。

セキュリティ上の理由から、これは厳格な要件になっています。外部 URL にカスタム ドメインを使用できないアプリケーションのワイルドカードはサポートされません。

### <a name="dns-updates"></a>DNS の更新

カスタム ドメインを使用する場合、アプリケーション プロキシ エンドポイントの外部 URL を指す外部 URL 用の CNAME レコードで DNS エントリ (例: `*.adventure-works.com`) を作成します。ワイルドカード アプリケーションでは、CNAME レコードが関連する外部 URL を指す必要があります。

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

CNAME を正しく構成したことを確認するには、いずれかのターゲット エンドポイントで [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) を使用できます (例: `expenses.adventure-works.com`)。  応答には、既に述べたエイリアス (`<yourAADTenantId>.tenant.runtime.msappproxy.net`) が含まれます。

## <a name="considerations"></a>考慮事項

ワイルドカード アプリケーションに対して考慮する必要があるいくつかの考慮事項を次に示します。

### <a name="accepted-formats"></a>許容される形式

ワイルドカード アプリケーションでは、**内部 URL** は `http(s)://*.<domain>` の形式である必要があります。

![内部 URL には http(s)://*.<domain> の形式を使用する](./media/application-proxy-wildcard/22.png)

**外部 URL** を構成する際は、`https://*.<custom domain>` の形式を使用する必要があります。

![外部 URL には https://*.<custom domain> の形式を使用する](./media/application-proxy-wildcard/21.png)

他の位置のワイルドカード、複数のワイルドカード、または他の正規表現文字列はサポートされておらず、エラーの原因になります。

### <a name="excluding-applications-from-the-wildcard"></a>ワイルドカードからのアプリケーションの除外

次の方法で、ワイルドカード アプリケーションからアプリケーションを除外できます

- 例外アプリケーションを通常のアプリケーションとして発行する
- DNS 設定を通じて、特定のアプリケーションに関してのみワイルドカードを有効にする  

アプリケーションを通常のアプリケーションとして発行することは、ワイルドカードからアプリケーションを除外する際に推奨される方法です。 除外されるアプリケーションをワイルドカード アプリケーションの前に発行する必要があります。これにより、最初から例外が適用されます。 最も固有なアプリケーションは常に優先されます。`budgets.finance.adventure-works.com` として発行されたアプリケーションは、アプリケーション `*.finance.adventure-works.com` より優先されます。そして、このアプリケーションはアプリケーション `*.adventure-works.com` より優先されます。

DNS 管理を通じて、特定のアプリケーションに対してのみ機能するようワイルドカードを制限することもできます。 ワイルドカードが含まれており、構成した外部 URL の形式に一致する CNAME エントリを作成することがベスト プラクティスとして推奨されます。 ただし、代わりに特定のアプリケーション URL でワイルドカードを指すこともできます。 たとえば、`*.adventure-works.com` の代わりに `hr.adventure-works.com`、`expenses.adventure-works.com`、`travel.adventure-works.com individually` で `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` を指します。 

このオプションを使用する場合、同じ場所を指している、値 `AppId.domain` の別の CNAME エントリ (たとえば `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`) も必要です。 ワイルドカード アプリケーションのアプリケーション プロパティ ページで **AppId** を見つけることができます。

![アプリのプロパティ ページでアプリケーション ID を見つける](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>MyApps パネルのホームページ URL の設定

[MyApps パネル](https://myapps.microsoft.com)では、ワイルドカード アプリケーションが単一のタイルで表されます。 既定では、このタイルは非表示です。 タイルを表示してユーザーを特定のページに到達させるには:

1. [ホームページ URL の設定](application-proxy-configure-custom-home-page.md)に関するガイドラインに従います。
2. アプリケーション プロパティ ページで **[Show Application]\(アプリケーションの表示\)** を **true** に設定します。

### <a name="kerberos-constrained-delegation"></a>Kerberos の制約付き委任

[Kerberos の制約付き委任 (KCD) を SSO 方法として](application-proxy-configure-single-sign-on-with-kcd.md)使用しているアプリケーションでは、SSO 方法に関してリストされる SPN にもワイルドカードが必要な場合があります。 たとえば、SPN は `HTTP/*.adventure-works.com` などになります。 さらに、バックエンド サーバーで個別の SPN を構成する必要があります (例: `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`)。

## <a name="scenario-1-general-wildcard-application"></a>シナリオ 1:一般的なワイルドカード アプリケーション

このシナリオでは、発行したい異なる 3 つのアプリケーションがあります。

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

3 つのアプリケーションはすべて、

- 全ユーザーによって使用されます
- "*統合 Windows 認証*" を使用します
- プロパティが同じです

「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md)」で説明されている手順を使用して、ワイルドカード アプリケーションを発行できます。 このシナリオは以下を前提とします。

- ID が `000aa000-11b1-2ccc-d333-4444eee4444e` のテナント
- `adventure-works.com` という名前の確認済みドメインが構成されている。
- `*.adventure-works.com` が `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` を指す **CNAME** エントリが作成されている。

[文書化されている手順](application-proxy-add-on-premises-application.md)に従って、新しいアプリケーション プロキシ アプリケーションをテナントに作成します。 この例では、ワイルドカードが次のフィールド内にあります。

- 内部 URL:

    ![例:内部 URL のワイルドカード](./media/application-proxy-wildcard/42.png)

- 外部 URL:

    ![例:外部 URL のワイルドカード](./media/application-proxy-wildcard/43.png)

- 内部アプリケーション SPN:

    ![例:SPN 構成のワイルドカード](./media/application-proxy-wildcard/44.png)

ワイルドカード アプリケーションを発行することで、良く知っている URL に移動して 3 つのアプリケーションにアクセスできるようになりました (`travel.adventure-works.com` など)。

この構成では次の構造が実装されます。

![例の構成によって実装された構造を示しています](./media/application-proxy-wildcard/05.png)

| 色 | 説明 |
| ---   | ---         |
| 青  | 明示的に発行されており、Azure portal に表示されるアプリケーション。 |
| グレー  | 親アプリケーション経由でアクセスできるアプリケーション。 |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>シナリオ 2:一般的なワイルドカード アプリケーション (例外あり)

このシナリオでは、3 つの一般的なアプリケーションに加えてもう 1 つアプリケーション (`finance.adventure-works.com`) を用意します。これは、財務部門のみがアクセスできるようにする必要があります。 現在のアプリケーション構造では、ワイルドカード アプリケーションを通じてすべての従業員が財務アプリケーションにアクセスできます。 これを変更するため、財務をアクセス許可の制限が強い別個のアプリケーションとして構成して、ワイルドカードからアプリケーションを除外します。

`finance.adventure-works.com` がアプリケーション用のアプリケーション プロキシ ページで指定された特定のアプリケーション エンドポイントを指す CNAME レコードを用意する必要があります。 このシナリオでは、`finance.adventure-works.com` が `https://finance-awcycles.msappproxy.net/` を指します。

[文書化された手順](application-proxy-add-on-premises-application.md)に従うと、このシナリオには以下の設定が必要です。

- **内部 URL** で、ワイルドカードの代わりに **finance** を設定します。

    ![例:内部 URL でワイルドカードの代わりに finance を設定する](./media/application-proxy-wildcard/52.png)

- **外部 URL** で、ワイルドカードの代わりに **finance** を設定します。

    ![例:外部 URL でワイルドカードの代わりに finance を設定する](./media/application-proxy-wildcard/53.png)

- 内部アプリケーション SPN で、ワイルドカードの代わりに **finance** を設定します。

    ![例:SPN 構成でワイルドカードの代わりに finance を設定する](./media/application-proxy-wildcard/54.png)

この構成では次のシナリオが実装されます。

![サンプル シナリオで実装されている構成を示しています](./media/application-proxy-wildcard/09.png)

`finance.adventure-works.com` は `*.adventure-works.com` よりも具体的な URL なので優先されます。 `finance.adventure-works.com` に移動するユーザーには、財務リソースのアプリケーションで指定されたエクスペリエンスが表示されます。 この場合、`finance.adventure-works.com` にアクセスできるのは財務関連の従業員のみです。

財務用に発行された複数のアプリケーションがあり、`finance.adventure-works.com` が確認済みドメインとしてある場合、別のワイルドカード アプリケーション `*.finance.adventure-works.com` を発行できます。 これは汎用の `*.adventure-works.com` よりも具体的であるため、ユーザーが finance ドメインのアプリケーションにアクセスする際に優先されます。

## <a name="next-steps"></a>次の手順

- **カスタム ドメイン**の詳細については、「[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)」を参照してください。
- **アプリケーションの発行**の詳細については、[Azure AD アプリケーション プロキシを使用したアプリケーションの発行](application-proxy-add-on-premises-application.md)に関するページを参照してください。
