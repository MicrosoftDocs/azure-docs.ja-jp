---
title: 構成可能なトークンの有効期間
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームによって発行されるアクセス、SAML、ID トークンの有効期間を設定する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 374e8bb61886a78289fcf44ce9582df325a4e64c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549041"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Microsoft ID プラットフォームでの構成可能なトークンの有効期間 (プレビュー)

Microsoft ID プラットフォームによって発行されるアクセス、ID、または SAML トークンの有効期間を指定できます。 組織のすべてのアプリ、マルチテナント (複数の組織) アプリケーション、または組織の特定のサービス プリンシパルに対して、トークンの有効期間を設定できます。 ただし、現時点では、[マネージド ID サービス プリンシパル](../managed-identities-azure-resources/overview.md)のトークン有効期間の構成はサポートされていません。

Azure AD では、ポリシー オブジェクトは、組織の個々のアプリケーションまたはすべてのアプリケーションに適用される規則のセットを表します。 それぞれのポリシーの種類は、割り当てられているオブジェクトに適用されるプロパティのセットを含む一意の構造体を持ちます。

組織の既定のポリシーとして、ポリシーを指定できます。 ポリシーは、優先度が高いポリシーによってオーバーライドされない限り、組織内のすべてのアプリケーションに適用されます。 ポリシーを特定のアプリケーションに割り当てることもできます。 優先順位の順序は、ポリシーの種類によって異なります。

例については、[トークンの有効期間の構成方法の例](configure-token-lifetimes.md)をご覧ください。

> [!NOTE]
> 構成可能なトークン ライフタイム ポリシーは、SharePoint Online リソースと OneDrive for Business リソースにアクセスするモバイル クライアントとデスクトップ クライアントにのみ適用され、Web ブラウザー セッションには適用されません。
> SharePoint Online と OneDrive for Business の Web ブラウザー セッションのライフタイムを管理するには、[条件付きアクセス セッション ライフタイム](../conditional-access/howto-conditional-access-session-lifetime.md)機能を使用します。 アイドル状態のセッションのタイムアウトの構成に関する詳細については、[SharePoint Online のブログ](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)を参照してください。

## <a name="license-requirements"></a>ライセンスの要件

この機能を使用するには、Azure AD Premium P1 ライセンスが必要です。 ご自分の要件に対して適切なライセンスを探すには、[一般公開されている Free および Premium エディションの機能比較](https://azure.microsoft.com/pricing/details/active-directory/)に関するページをご覧ください。

[Microsoft 365 Business ライセンス](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)をお持ちのお客様も、条件付きアクセス機能にアクセスできます。

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>アクセス トークン、SAML トークン、および ID トークンのトークン有効期間ポリシー

アクセス トークン、SAML トークン、および ID トークンにトークン有効期間ポリシーを設定できます。 

### <a name="access-tokens"></a>アクセス トークン

クライアントは保護されたリソースにアクセスするためにアクセス トークンを使用します。 アクセス トークンは、ユーザー、クライアント、およびリソースの特定の組み合わせに対してのみ使用できます。 アクセス トークンは取り消すことはできず、有効期限まで有効です。 悪意のあるアクターがアクセス トークンを取得した場合は、その有効期間にわたって使用される可能性があります。 アクセス トークンの有効期間の調整は、システム パフォーマンスの向上と、ユーザーのアカウントが無効になった後にクライアントがアクセスを保持する時間の増加との間で、トレードオフとなります。 システム パフォーマンスの向上は、クライアントが新しいアクセス トークンを取得しなければならない回数を減らすことで実現されます。  既定値は 1 時間です。この場合、クライアントは 1 時間後に更新トークンを使用して、新しい更新トークンとアクセス トークンを (通常は自動で) 取得する必要があります。

### <a name="saml-tokens"></a>SAML トークン

SAML トークンは、Web ベースの SaaS アプリケーションの多くで使用され、Azure Active Directory の SAML2 プロトコル エンドポイントを使用して取得されます。 また、WS-Federation を使用するアプリケーションでも使用されます。 トークンの既定の有効期間は 1 時間です。 アプリケーションの観点からは、トークンの有効期間は、そのトークン内の `<conditions …>` 要素の NotOnOrAfter 値によって指定されます。 トークンの有効期間が終了したら、クライアントは新しい認証要求を開始する必要があります。これは多くの場合、シングル サインオン (SSO) セッション トークンの結果として、対話型サインインなしで満たされます。

NotOnOrAfter の値は、`TokenLifetimePolicy` 内の `AccessTokenLifetime` パラメーターを使用して変更できます。 この値は、ポリシーで構成されている有効期間に設定され (構成されている場合)、クロック スキュー係数が 5 分になります。

`<SubjectConfirmationData>` 要素で指定されているサブジェクト確認 NotOnOrAfter は、トークンの有効期間の構成には影響されません。 

### <a name="id-tokens"></a>ID トークン

ID トークンは、Web サイトとネイティブ クライアントに渡されます。 ID トークンは、ユーザーに関するプロファイル情報を格納します。 ID トークンは、ユーザーとクライアントの特定の組み合わせにバインドされます。 ID トークンは、それらの有効期限まで有効とみなされます。 通常、Web アプリケーションは、アプリケーションにおけるユーザーのセッションの有効期間と、ユーザーに対して発行された ID トークンの有効期間を照合します。 ID トークンの有効期間を調整して、Web アプリケーションでアプリケーション セッションが期限切れになる頻度、および Microsoft ID プラットフォームでユーザーの再認証 (自動または対話形式) が要求される頻度を制御できます。

### <a name="token-lifetime-policy-properties"></a>トークンの有効期間ポリシーのプロパティ

トークンの有効期間ポリシーとは、トークンの有効期間の規則が含まれる一種のポリシー オブジェクトです。 このポリシーは、このリソースのアクセス トークン、SAML トークン、および ID トークンが有効とみなされる期間を制御します。 ポリシーが設定されていない場合は、既定の有効期間の値が適用されます。 

Access Token Lifetime プロパティを減らすと、悪意のあるアクターによって、長時間にわたってアクセス トークンや ID トークンが使用されるリスクが軽減します (これらのトークンは取り消しできません)。このトレードオフは、トークンを頻繁に交換する必要があるため、パフォーマンスが影響を受けることです。

例については、「[Web サインインのポリシーを作成する](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)」を参照してください。

| プロパティ | ポリシーのプロパティ文字列 | 影響 | Default | 最小値 | 最大値 |
| --- | --- | --- | --- | --- | --- |
| アクセス トークンの有効期間 |AccessTokenLifetime |アクセス トークン、ID トークン、SAML2 トークン |1 時間 |10 分 |1 日 |

> [!NOTE]
> Microsoft Teams Web クライアントを確実に機能させるには、Microsoft Teams に対して AccessTokenLifetime が常に 15 分より大きい値に維持されるようにしておくことをお勧めします。

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>更新トークンとセッション トークンのトークン有効期間ポリシー

更新トークンとセッション トークンに対してトークン有効期間ポリシーを設定することはできません。

> [!IMPORTANT]
> 2021 年 1 月 30 日の時点で、更新およびセッション トークンの有効期間は構成できません。 Azure Active Directory では、既存のポリシーの更新およびセッション トークンの構成が考慮されなくなくなりました。  既存のトークンの有効期限が切れた後に発行される新しいトークンは、[既定の構成](#configurable-token-lifetime-properties-after-the-retirement)に設定されるようになりました。 更新およびセッション トークンの構成の廃止後も、アクセス、SAML、ID の各トークンの有効期間を構成することはできます。
>
> 既存のトークンの有効期間は変更されません。 有効期限が切れると、既定値に基づいて新しいトークンが発行されます。
>
> ユーザーが再度サインインするように求められるまでの時間を定義し続ける必要がある場合は、条件付きアクセスでサインインの頻度を構成します。 条件付きアクセスの詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)」をお読みください。

:::image type="content" source="./media/active-directory-configurable-token-lifetimes/roadmap.svg" alt-text="廃止に関する情報":::

### <a name="refresh-tokens"></a>更新トークン

クライアントは保護されたリソースにアクセスするためのアクセス トークンを取得するときに、更新トークンも受け取ります。 更新トークンを使用して、現在のアクセス トークンの有効期限が切れたときに、新しいアクセス トークンと更新トークンのペアを取得します。 更新トークンは、ユーザーとクライアントの組み合わせにバインドされます。 更新トークンは[いつでも取り消す](access-tokens.md#token-revocation)ことができ、トークンが使用されるたびに、トークンの有効性がチェックされます。  新しいアクセス トークンをフェッチするために使用した更新トークンは取り消されませんが、新しいトークンを取得する際に古いトークンを安全に削除することをお勧めします。

Confidential クライアントとパブリック クライアントを区別することは重要です。更新トークンを使用できる時間に影響するためです。 さまざまな種類のクライアントの詳細については、[RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1) を参照してください。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Confidential クライアントの更新トークンの有効期間
Confidential クライアントは、クライアント パスワード (シークレット) を安全に格納できるアプリケーションです。 それらは、要求が悪意のあるアクターからではなく、セキュリティで保護されたクライアント アプリケーションから送信されていることを証明できます。 たとえば、Web アプリは、Web サーバーにクライアント シークレットを格納できるため、Confidential クライアントです。 これは公開されません。 これらのフローは安全性をより高めているため、フローに対して発行される更新トークンの既定の有効期間は `until-revoked` で、ポリシーを使用して変更することはできず、任意にパスワードをリセットしても取り消されません。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>パブリック クライアントの更新トークンの有効期間

パブリック クライアントは、クライアントのパスワード (シークレット) を安全に格納できません。 たとえば、iOS や Android アプリは、リソース所有者のシークレットを難読化できないため、パブリック クライアントとみなされます。 リソースにポリシーを設定して、指定した期間よりも古いパブリック クライアントの更新トークンが、新しいアクセス トークンと更新トークン ペアを取得しないようにできます これを行うには、[Refresh Token Max Inactive Time プロパティ](#refresh-token-max-inactive-time) (`MaxInactiveTime`) を使用します。 それを超えると更新トークンを受け付けなくなる期間を設定するポリシーを使用することもできます これを行うには、[Single-Factor Refresh Token Max Age](#single-factor-session-token-max-age) または [Multi-Factor Session Token Max Age](#multi-factor-refresh-token-max-age) のいずれかのプロパティを使用します。 更新トークンの有効期間を調整して、パブリック クライアント アプリケーションの使用時に、ユーザーが自動的に再認証されるのではなく、資格情報を再入力する必要があるタイミングと頻度を制御できます。

Max Age プロパティは、1 つのトークンを使用できる期間です。 

### <a name="single-sign-on-session-tokens"></a>シングル サインオン セッション トークン
ユーザーが Microsoft ID プラットフォームで認証を行うと、ユーザーのブラウザーと Microsoft ID プラットフォームでシングル サインオン (SSO) セッションが確立されます。 SSO トークンは、Cookie の形式でこのセッションを表します。 SSO セッション トークンは特定のリソース/クライアント アプリケーションにバインドされていません。 SSO セッション トークンは失効させることができ、使用時に必ず有効性がチェックされます。

Microsoft ID プラットフォームでは、永続的と非永続的の 2 種類の SSO セッション トークンが使用されます。 永続的セッション トークンは、ブラウザーで永続的な Cookie として保存されます。 非永続的セッション トークンは、セッション Cookie として保存されます (セッション Cookie は、ブラウザーを閉じると破棄されます)。通常は、非永続的セッション トークンが保存されます。 ただし、ユーザーが認証時に **[サインインしたままにする]** チェック ボックスをオンにした場合は、永続的セッション トークンが保存されます。

非永続的セッション トークンには、24 時間の有効期間があります。 永続的トークンには、90 日間の有効期間があります。 有効期間内に SSO セッション トークンを使用した時点で、有効期間はトークンの種類に応じて、さらに 24 時間または 90 日間延長されます。 SSO セッション トークンが有効期間内に使用されない場合は、期限切れとみなされ、受け付けられなくなります。

ポリシーを使用して最初のセッション トークンが発行された後の時間を設定し、それを超えるとセッションのトークンを受け付けないようにすることができます (これを行うには、Session Token Max Age プロパティを使用します)。セッション トークンの有効期間を調整して、Web アプリケーションの使用時に、ユーザーが自動的に再認証されるのではなく、資格情報を再入力する必要があるタイミングと頻度を制御できます。

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>更新トークンとセッション トークンの有効期間ポリシーのプロパティ
トークンの有効期間ポリシーとは、トークンの有効期間の規則が含まれる一種のポリシー オブジェクトです。 ポリシーのプロパティは、指定したトークンの有効期間の制御に使用します。 ポリシーが設定されていない場合は、既定の有効期間の値が適用されます。

#### <a name="configurable-token-lifetime-properties"></a>構成可能なトークンの有効期間のプロパティ
| プロパティ | ポリシーのプロパティ文字列 | 影響 | Default | 最小値 | 最大値 |
| --- | --- | --- | --- | --- | --- |
| 更新トークンの最大非アクティブ時間 |MaxInactiveTime |更新トークン |90 日間 |10 分 |90 日間 |
| 単一要素更新トークンの最長有効期間 |MaxAgeSingleFactor |更新トークン (すべてのユーザー向け) |Until-revoked |10 分 |Until-revoked<sup>1</sup> |
| 多要素更新トークンの最長有効期間 |MaxAgeMultiFactor |更新トークン (すべてのユーザー向け) | Until-revoked |10 分 |180 日<sup>1</sup> |
| 単一要素セッション トークンの最長有効期間 |MaxAgeSessionSingleFactor |セッション トークン (永続的および非永続的) |Until-revoked |10 分 |Until-revoked<sup>1</sup> |
| 多要素セッション トークンの最長有効期間 |MaxAgeSessionMultiFactor |セッション トークン (永続的および非永続的) | Until-revoked |10 分 | 180 日<sup>1</sup> |

* <sup>1</sup>これらの属性に対して明示的に設定できる最大期間は 365 日です。

#### <a name="exceptions"></a>例外
| プロパティ | 影響 | Default |
| --- | --- | --- |
| Refresh Token Max Age (失効情報が不十分なフェデレーション ユーザーに発行<sup>1</sup>) |Refresh Token (失効情報が不十分なフェデレーション ユーザーに発行<sup>1</sup>) |12 時間 |
| Refresh Token Max Inactive Time (Confidential クライアントに発行) |更新トークン (Confidential クライアントに発行) |90 日間 |
| Refresh token Max Age (Confidential クライアントに発行) |更新トークン (Confidential クライアントに発行) |Until-revoked |

* <sup>1</sup> 失効情報が不十分なフェデレーション ユーザーには、"LastPasswordChangeTimestamp" 属性が同期されないすべてのユーザーが含まれます。 これらのユーザーにはこの短い Max Age が与えられます。その理由は、Azure Active Directory は、古い資格情報 (変更済みのパスワードなど) に関連付けられたトークンを取り消すタイミングを確認できず、ユーザーおよび関連付けられているトークンがまだ良好であることをより頻繁に確認する必要があるためです。 このエクスペリエンスを向上させるには、テナント管理者は、"LastPasswordChangeTimestamp" 属性 (PowerShell または AADSync を使用してユーザー オブジェクトに設定できる) が同期されていることを確認する必要があります。

### <a name="configurable-policy-property-details"></a>構成可能なポリシーのプロパティの詳細

#### <a name="refresh-token-max-inactive-time"></a>更新トークンの最大非アクティブ時間
**文字列:** MaxInactiveTime

**影響:** 更新トークン

**概要:** このポリシーは、更新トークンがどの程度古くなると、クライアントがこのリソースにアクセスするときに、新しいアクセス トークンと更新トークンのペアの取得に更新トークンを使用できなくするのかを制御します。 更新トークンを使用すると、通常は新しい更新トークンが返されるため、クライアントが指定した期間に、現在の更新トークンを使用して、リソースにアクセスを試みた場合に、このポリシーによって、アクセスが妨げられます。

このポリシーは、クライアントでアクティブでなかったユーザーを強制的に再認証し、新しい更新トークンを取得させることができます。

Refresh Token Max Inactive Time プロパティは Single-Factor Token Max Age および Multi-Factor Refresh Token Max Age プロパティよりも小さな値に設定する必要があります。

例については、「[Web API を呼び出すネイティブ アプリケーションのポリシーを作成する](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)」を参照してください。

#### <a name="single-factor-refresh-token-max-age"></a>単一要素更新トークンの最長有効期間
**文字列:** MaxAgeSingleFactor

**影響:** 更新トークン

**概要:** このポリシーは、ユーザーが前回単一要素のみで認証に成功した後に、新しいアクセス トークンと更新トークンのペアを取得するために更新トークンを使用できる期間を制御します。 ユーザーが認証し、新しい更新トークンを受け取ると、ユーザーは、指定した期間のあいだ更新トークン フローを使用できます (これは現在の更新トークンが失効しておらず、非アクティブ時間より長く未使用のままにされていない場合にあてはまります)。その時点で、ユーザーは新しい更新トークンを受け取るために再認証するよう強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Multi-Factor Refresh Token Max Age プロパティ以下の値に設定することをお勧めします。

例については、「[Web API を呼び出すネイティブ アプリケーションのポリシーを作成する](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)」を参照してください。

#### <a name="multi-factor-refresh-token-max-age"></a>多要素更新トークンの最長有効期間
**文字列:** MaxAgeMultiFactor

**影響:** 更新トークン

**概要:** このポリシーは、ユーザーが前回多要素で認証に成功した後に、新しいアクセス トークンと更新トークンのペアを取得するために更新トークンを使うことができる期間を制御します。 ユーザーが認証し、新しい更新トークンを受け取ると、ユーザーは、指定した期間のあいだ更新トークン フローを使用できます (これは現在の更新トークンが失効しておらず、非アクティブ時間より長く未使用のままにされていない場合にあてはまります)。その時点で、ユーザーは新しい更新トークンを受け取るために再認証を強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Single-Factor Refresh Token Max Age プロパティ以上の値に設定することをお勧めします。

例については、「[Web API を呼び出すネイティブ アプリケーションのポリシーを作成する](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)」を参照してください。

#### <a name="single-factor-session-token-max-age"></a>単一要素セッション トークンの最長有効期間
**文字列:** MaxAgeSessionSingleFactor

**影響:** セッション トークン (永続的および非永続的)

**概要:** このポリシーは、ユーザーが前回単一要素のみで認証に成功した後に、新しい ID とセッション トークンを取得するためにセッショントークンを使用できる期間を制御します。 ユーザーが認証し、新しいセッション トークンを受け取ると、ユーザーは、指定した期間のあいだセッション トークン フローを使用できます (これは、現在のセッション トークンが失効しておらず、有効期限が切れていない場合にあてはまります)。指定した期間の後、ユーザーは新しいセッション トークンを受け取るために再認証が強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Multi-Factor Session Token Max Age プロパティ以下の値に設定することをお勧めします。

例については、「[Web サインインのポリシーを作成する](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)」を参照してください。

#### <a name="multi-factor-session-token-max-age"></a>多要素セッション トークンの最長有効期間
**文字列:** MaxAgeSessionMultiFactor

**影響:** セッション トークン (永続的および非永続的)

**概要:** このポリシーは、ユーザーが前回多要素で認証に成功した後に、新しい ID とセッション トークンを取得するためにセッショントークンを使用できる期間を制御します。 ユーザーが認証し、新しいセッション トークンを受け取ると、ユーザーは、指定した期間のあいだセッション トークン フローを使用できます (これは、現在のセッション トークンが失効しておらず、有効期限が切れていない場合にあてはまります)。指定した期間の後、ユーザーは新しいセッション トークンを受け取るために再認証が強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Single-Factor Session Token Max Age プロパティ以上の値に設定することをお勧めします。

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>廃止後に構成可能なトークンの有効期間プロパティ
更新とセッション トークンの構成は、次のプロパティとそれぞれの設定値の影響を受けます。 2021 年 1 月 30 日に更新トークンとセッション トークンの構成が廃止された後、Azure AD では、以下に説明する既定値のみを受け入れます。 廃止後に、サインイン頻度を管理するための条件付きアクセスを使用しない場合、更新トークンとセッション トークンは廃止日の既定の構成に設定され、有効期間を変更することはできなくなります。  

|プロパティ   |ポリシーのプロパティ文字列    |影響 |Default |
|----------|-----------|------------|------------|
|アクセス トークンの有効期間 |AccessTokenLifetime |アクセス トークン、ID トークン、SAML2 トークン |1 時間 |
|更新トークンの最大非アクティブ時間 |MaxInactiveTime  |更新トークン |90 日間  |
|単一要素更新トークンの最長有効期間  |MaxAgeSingleFactor  |更新トークン (すべてのユーザー向け)  |Until-revoked  |
|多要素更新トークンの最長有効期間  |MaxAgeMultiFactor  |更新トークン (すべてのユーザー向け) |Until-revoked  |
|単一要素セッション トークンの最長有効期間  |MaxAgeSessionSingleFactor |セッション トークン (永続的および非永続的)  |Until-revoked |
|多要素セッション トークンの最長有効期間  |MaxAgeSessionMultiFactor  |セッション トークン (永続的および非永続的)  |Until-revoked |

PowerShell を使用すると、廃止の影響を受けるポリシーを特定できます。  [PowerShell コマンドレット](configure-token-lifetimes.md#get-started)を使用すると、組織で作成されたすべてのポリシーを確認したり、特定のポリシーにリンクされているアプリとサービス プリンシパルを検索したりできます。

## <a name="policy-evaluation-and-prioritization"></a>ポリシーの評価と優先順位付け
トークン有効期間ポリシーを作成して、特定のアプリケーション、組織、およびサービス プリンシパルに割り当てることができます。 複数のポリシーを、特定のアプリケーションに適用できます。 有効なトークン有効期間ポリシーは、次の規則に従います。

* ポリシーが明示的にサービス プリンシパルに割り当てられている場合は、そのポリシーが適用されます。
* 明示的にサービス プリンシパルに割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。
* サービス プリンシパルまたは組織に明示的に割り当てられているポリシーがない場合、アプリケーションに割り当てられているポリシーが適用されます。
* サービス プリンシパル、組織、またはアプリケーション オブジェクトに割り当てられているポリシーがない場合は、既定値が適用されます。 ([構成可能なトークンの有効期間のプロパティ](#configurable-token-lifetime-properties-after-the-retirement)の表を参照してください)。

アプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。

トークンの有効性は、トークンの使用時に評価されます。 アクセスされているアプリケーションに対して、最も優先度が高いポリシーが有効になります。

ここで使用されるすべての時間帯は、C# [TimeSpan](/dotnet/api/system.timespan) オブジェクト (D.HH:MM:SS) 形式に従って書式設定されます。  つまり、80 日と 30 分は `80.00:30:00` になります。  0 の場合、先頭の D を削除することができますので 90 分は `00:90:00` になります。  

### <a name="example-scenario"></a>シナリオ例

ユーザーは次の 2 つの Web アプリケーションに接続したいものとします:Web アプリケーション A と Web アプリケーション B。

考慮すべき要素:
* 両方の Web アプリケーションとも、同じ親組織にあります。
* Session Token Max Age を 8 時間に設定したトークンの有効期間ポリシー 1 が、親組織の既定値として設定されています。
* Web アプリケーション A は、日常的に使用する Web アプリケーションで、どのポリシーにもリンクされていません。
* Web アプリケーション B は、機密性の高いプロセスに使用されます。 そのサービス プリンシパルは、30 分の　Session Token Max Age が設定されているトークンの有効期間ポリシー 2 にリンクされています。

正午に、ユーザーは新しいブラウザー セッションを開始し、Web アプリケーション A へのアクセスを試みます。ユーザーは Microsoft ID プラットフォームにリダイレクトされ、サインインするよう指示されます。 これによって、ブラウザーにセッション トークンを含む Cookie が作成されます。 ユーザーは、Web アプリケーション A にアクセスするための ID トークンによって、Web アプリケーション A にリダイレクトされます。

午後 0 時 15 分に、ユーザーは Web アプリケーション B へのアクセスを試みます。ブラウザーは Microsoft ID プラットフォームにリダイレクトされ、そこでセッション Cookie が検出されます。 Web アプリケーション B のサービス プリンシパルは、トークンの有効期間ポリシー 2 にリンクされているだけでなく、既定のトークンの有効期間ポリシー 1 が設定されている親組織の一部にもなっています。 サービス プリンシパルにリンクされているポリシーは、組織の既定のポリシーより優先順位が高いため、トークンの有効期間ポリシー 2 が有効になります。 セッション トークンが最初に発行されたのは、過去 30 分以内であるため、このトークンは有効とみなされます。 ユーザーは、アクセス権限を与える ID トークンによって Web アプリケーション B にリダイレクトされます。

午後 1 時に、ユーザーは Web アプリケーション A へのアクセスを試みます。ユーザーは、Microsoft ID プラットフォームにリダイレクトされます。 Web アプリケーション A は、どのポリシーにもリンクされていませんが、既定のトークンの有効期間ポリシー 1 が設定された組織内にあるため、そのポリシーが有効になります。 過去 8 時間以内で最初に発行されたセッション Cookie が検出されます。 ユーザーは自動的に新しい ID トークンで Web アプリケーション A にリダイレクトされます。 ユーザーは認証する必要がありません。

その後直ちにユーザーは Web アプリケーション B へのアクセスを試みます。ユーザーは、Microsoft ID プラットフォームにリダイレクトされます。 以前と同様、トークンの有効期間ポリシー 2 が有効になります。 トークンは 30 分以上前に発行されたため、ユーザーはサインイン資格情報を再入力するように求められます。 新しいセッション トークンと ID トークンが発行されます。 これで、ユーザーは Web アプリケーション B にアクセスできます。

## <a name="cmdlet-reference"></a>コマンドレット リファレンス

これらは、[Azure Active Directory PowerShell for Graph Preview モジュール](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)のコマンドレットです。

### <a name="manage-policies"></a>ポリシーの管理

次のコマンドレットを使用して、ポリシーを管理することができます。

| コマンドレット | 説明 | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 新しいポリシーを作成します。 |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | すべての AzureAD ポリシーまたは指定されたポリシーを取得します。 |
| [Get AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | ポリシーにリンクされたすべてのアプリとサービス プリンシパルを取得します。 |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 既存のポリシーを更新します。 |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したポリシーを削除します。 |

### <a name="application-policies"></a>アプリケーション ポリシー
アプリケーション ポリシーには次のコマンドレットを使用できます。</br></br>

| コマンドレット | 説明 | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したポリシーをアプリケーションにリンクします。 |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | アプリケーションに割り当てられているポリシーを取得します。 |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | アプリケーションからポリシーを削除します。 |

### <a name="service-principal-policies"></a>サービス プリンシパル ポリシー
サービス プリンシパル ポリシーには次のコマンドレットを使用できます。

| コマンドレット | 説明 | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したポリシーをサービス プリンシパルにリンクします。 |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したサービス プリンシパルにリンクされている任意のポリシーを取得します。|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したサービス プリンシパルからポリシーを削除します。|

## <a name="next-steps"></a>次のステップ

詳細については、[トークンの有効期間の構成方法の例](configure-token-lifetimes.md)をご覧ください。
