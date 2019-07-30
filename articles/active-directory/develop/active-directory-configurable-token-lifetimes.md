---
title: Azure Active Directory における構成可能なトークンの有効期間 | Microsoft Docs
description: Azure AD によって発行されたトークンの有効期間を設定する方法について説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: ryanwi
ms.custom: aaddev, annaba
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1c68d9254b0da2e5296c83d8dd4c95091fde1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111797"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Azure Active Directory における構成可能なトークンの有効期間 (プレビュー)

Azure Active Directory (Azure AD) によって発行されたトークンの有効期間を指定できます。 組織のすべてのアプリ、マルチテナント (複数の組織) アプリケーション、または組織の特定のサービス プリンシパルに対して、トークンの有効期間を設定できます。

> [!IMPORTANT]
> プレビュー中のお客様から聞き取った後、構成可能なトークン有効期間機能を、Azure AD の条件付きアクセスの[認証セッション管理機能](https://go.microsoft.com/fwlink/?linkid=2083106)に置き換えました。 この機能は、2019 年 11 月 1 日に非推奨になります。 構成可能なトークン有効期間ポリシーを使用している場合、新しい条件付きアクセス機能に切り替えます。 

Azure AD では、ポリシー オブジェクトは、組織の個々のアプリケーションまたはすべてのアプリケーションに適用される規則のセットを表します。 それぞれのポリシーの種類は、割り当てられているオブジェクトに適用されるプロパティのセットを含む一意の構造体を持ちます。

組織の既定のポリシーとして、ポリシーを指定できます。 ポリシーは、優先度が高いポリシーによってオーバーライドされない限り、組織内のすべてのアプリケーションに適用されます。 ポリシーを特定のアプリケーションに割り当てることもできます。 優先順位の順序は、ポリシーの種類によって異なります。

> [!NOTE]
> SharePoint Online では、構成可能なトークンの有効期間ポリシーをサポートしていません。  PowerShell を使用してこのポリシーを作成することはできますが、SharePoint Online はこのポリシーを認識しません。 アイドル状態のセッションのタイムアウトの構成に関する詳細については、[SharePoint Online のブログ](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)を参照してください。
>* SharePoint Online のアクセス トークンの既定の有効期間は 1 時間です。 
>* SharePoint Online の更新トークンの既定の最大非アクティブ時間は 90 日間です。

## <a name="token-types"></a>トークンの種類

更新トークン、アクセス トークン、セッション トークン、および ID トークンにトークンの有効期間ポリシーを設定できます。

### <a name="access-tokens"></a>アクセス トークン

クライアントは保護されたリソースにアクセスするためにアクセス トークンを使用します。 アクセス トークンは、ユーザー、クライアント、およびリソースの特定の組み合わせに対してのみ使用できます。 アクセス トークンは取り消すことはできず、有効期限まで有効です。 悪意のあるアクターがアクセス トークンを取得した場合は、その有効期間にわたって使用される可能性があります。 アクセス トークンの有効期間の調整は、システム パフォーマンスの向上と、ユーザーのアカウントが無効になった後にクライアントがアクセスを保持する時間の増加との間で、トレードオフとなります。 システム パフォーマンスの向上は、クライアントが新しいアクセス トークンを取得しなければならない回数を減らすことで実現されます。  既定値は 1 時間です。この場合、クライアントは 1 時間後に更新トークンを使用して、新しい更新トークンとアクセス トークンを (通常は自動で) 取得する必要があります。 

### <a name="refresh-tokens"></a>更新トークン

クライアントは保護されたリソースにアクセスするためのアクセス トークンを取得するときに、更新トークンも受け取ります。 更新トークンを使用して、現在のアクセス トークンの有効期限が切れたときに、新しいアクセス トークンと更新トークンのペアを取得します。 更新トークンは、ユーザーとクライアントの組み合わせにバインドされます。 更新トークンは[いつでも取り消す](access-tokens.md#token-revocation)ことができ、トークンが使用されるたびに、トークンの有効性がチェックされます。  新しいアクセス トークンをフェッチするために使用した更新トークンは取り消されませんが、新しいトークンを取得する際に古いトークンを安全に削除することをお勧めします。 

Confidential クライアントとパブリック クライアントを区別することは重要です。更新トークンを使用できる時間に影響するためです。 さまざまな種類のクライアントの詳細については、[RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1) を参照してください。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Confidential クライアントの更新トークンの有効期間
Confidential クライアントは、クライアント パスワード (シークレット) を安全に格納できるアプリケーションです。 それらは、要求が悪意のあるアクターからではなく、セキュリティで保護されたクライアント アプリケーションから送信されていることを証明できます。 たとえば、Web アプリは、Web サーバーにクライアント シークレットを格納できるため、Confidential クライアントです。 これは公開されません。 これらのフローは安全性をより高めているため、フローに対して発行される更新トークンの既定の有効期間は `until-revoked` で、ポリシーを使用して変更することはできず、任意にパスワードをリセットしても取り消されません。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>パブリック クライアントの更新トークンの有効期間

パブリック クライアントは、クライアントのパスワード (シークレット) を安全に格納できません。 たとえば、iOS や Android アプリは、リソース所有者のシークレットを難読化できないため、パブリック クライアントとみなされます。 リソースにポリシーを設定して、指定した期間よりも古いパブリック クライアントの更新トークンが、新しいアクセス トークンと更新トークン ペアを取得しないようにできます (これを行うには、Refresh Token Max Inactive Time プロパティ (`MaxInactiveTime`) を使用します)。それを超えると更新トークンを受け付けなくなる期間を設定するポリシーを使用することもできます (これを行うには、Refresh Token Max Age プロパティを使用します)。更新トークンの有効期間を調整して、パブリック クライアント アプリケーションの使用時に、ユーザーが自動的に再認証されるのではなく、資格情報を再入力する必要があるタイミングと頻度を制御できます。

### <a name="id-tokens"></a>ID トークン
ID トークンは、Web サイトとネイティブ クライアントに渡されます。 ID トークンは、ユーザーに関するプロファイル情報を格納します。 ID トークンは、ユーザーとクライアントの特定の組み合わせにバインドされます。 ID トークンは、それらの有効期限まで有効とみなされます。 通常、Web アプリケーションは、アプリケーションにおけるユーザーのセッションの有効期間と、ユーザーに対して発行された ID トークンの有効期間を照合します。 ID トークンの有効期間を調整して、Web アプリケーションがアプリケーション セッションを期限切れにする頻度、および Azure AD でユーザーを再認証する (自動的にまたは対話形式で) ように要求する頻度を制御できます。

### <a name="single-sign-on-session-tokens"></a>シングル サインオン セッション トークン
ユーザーが Azure AD で認証すると、ユーザーのブラウザーと Azure AD でシングル サインオン (SSO) セッションが確立されます。 SSO トークンは、Cookie の形式でこのセッションを表します。 SSO セッション トークンは特定のリソース/クライアント アプリケーションにバインドされていません。 SSO セッション トークンは失効させることができ、使用時に必ず有効性がチェックされます。

Azure AD は永続的と非永続的の 2 つの種類の SSO セッション トークンを使用します。 永続的セッション トークンは、ブラウザーで永続的な Cookie として保存されます。 非永続的セッション トークンは、セッション Cookie として保存されます (セッション Cookie は、ブラウザーを閉じると破棄されます)。通常は、非永続的セッション トークンが保存されます。 ただし、ユーザーが認証時に **[サインインしたままにする]** チェック ボックスをオンにした場合は、永続的セッション トークンが保存されます。

非永続的セッション トークンには、24 時間の有効期間があります。 永続的トークンには、180 日間の有効期間があります。 有効期間内に SSO セッション トークンを使用した時点で、有効期間はトークンの種類に応じて、さらに 24 時間または 180 日間延長されます。 SSO セッション トークンが有効期間内に使用されない場合は、期限切れとみなされ、受け付けられなくなります。

ポリシーを使用して最初のセッション トークンが発行された後の時間を設定し、それを超えるとセッションのトークンを受け付けないようにすることができます (これを行うには、Session Token Max Age プロパティを使用します)。セッション トークンの有効期間を調整して、Web アプリケーションの使用時に、ユーザーが自動的に再認証されるのではなく、資格情報を再入力する必要があるタイミングと頻度を制御できます。

### <a name="token-lifetime-policy-properties"></a>トークンの有効期間ポリシーのプロパティ
トークンの有効期間ポリシーとは、トークンの有効期間の規則が含まれる一種のポリシー オブジェクトです。 ポリシーのプロパティは、指定したトークンの有効期間の制御に使用します。 ポリシーが設定されていない場合は、既定の有効期間の値が適用されます。

### <a name="configurable-token-lifetime-properties"></a>構成可能なトークンの有効期間のプロパティ
| プロパティ | ポリシーのプロパティ文字列 | 影響 | 既定値 | 最小値 | 最大値 |
| --- | --- | --- | --- | --- | --- |
| アクセス トークンの有効期間 |AccessTokenLifetime |アクセス トークン、ID トークン、SAML2 トークン |1 時間 |10 分 |1 日 |
| 更新トークンの最大非アクティブ時間 |MaxInactiveTime |更新トークン |90 日間 |10 分 |90 日間 |
| 単一要素更新トークンの最長有効期間 |MaxAgeSingleFactor |更新トークン (すべてのユーザー向け) |Until-revoked |10 分 |Until-revoked<sup>1</sup> |
| 多要素更新トークンの最長有効期間 |MaxAgeMultiFactor |更新トークン (すべてのユーザー向け) |Until-revoked |10 分 |Until-revoked<sup>1</sup> |
| 単一要素セッション トークンの最長有効期間 |MaxAgeSessionSingleFactor<sup>2</sup> |セッション トークン (永続的および非永続的) |Until-revoked |10 分 |Until-revoked<sup>1</sup> |
| 多要素セッション トークンの最長有効期間 |MaxAgeSessionMultiFactor<sup>3</sup> |セッション トークン (永続的および非永続的) |Until-revoked |10 分 |Until-revoked<sup>1</sup> |

* <sup>1</sup>これらの属性に対して明示的に設定できる最大期間は 365 日です。

### <a name="exceptions"></a>例外
| プロパティ | 影響 | 既定値 |
| --- | --- | --- |
| Refresh Token Max Age (失効情報が不十分なフェデレーション ユーザーに発行<sup>1</sup>) |Refresh Token (失効情報が不十分なフェデレーション ユーザーに発行<sup>1</sup>) |12 時間 |
| Refresh Token Max Inactive Time (Confidential クライアントに発行) |更新トークン (Confidential クライアントに発行) |90 日間 |
| Refresh token Max Age (Confidential クライアントに発行) |更新トークン (Confidential クライアントに発行) |Until-revoked |

* <sup>1</sup>失効情報が不十分なフェデレーション ユーザーには、"LastPasswordChangeTimestamp" 属性が同期されないすべてのユーザーが含まれます。 これらのユーザーにはこの短い Max Age が与えられます。その理由は、AAD は、古い資格情報 (変更済みのパスワードなど) に関連付けられたトークンを取り消すタイミングを確認できず、ユーザーおよび関連付けられているトークンがまだ良好であることをより頻繁に確認する必要があるためです。 このエクスペリエンスを向上させるには、テナント管理者は、"LastPasswordChangeTimestamp" 属性 (Powershell または AADSync を使用してユーザー オブジェクトに設定できる) が同期されていることを確認する必要があります。

### <a name="policy-evaluation-and-prioritization"></a>ポリシーの評価と優先順位付け
トークン有効期間ポリシーを作成して、特定のアプリケーション、組織、およびサービス プリンシパルに割り当てることができます。 複数のポリシーを、特定のアプリケーションに適用できます。 有効なトークン有効期間ポリシーは、次の規則に従います。

* ポリシーが明示的にサービス プリンシパルに割り当てられている場合は、そのポリシーが適用されます。
* 明示的にサービス プリンシパルに割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。
* サービス プリンシパルまたは組織に明示的に割り当てられているポリシーがない場合、アプリケーションに割り当てられているポリシーが適用されます。
* サービス プリンシパル、組織、またはアプリケーション オブジェクトに割り当てられているポリシーがない場合は、既定値が適用されます ([構成可能なトークンの有効期間のプロパティ](#configurable-token-lifetime-properties)の表を参照してください)。

アプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。

トークンの有効性は、トークンの使用時に評価されます。 アクセスされているアプリケーションに対して、最も優先度が高いポリシーが有効になります。

ここで使用されるすべての時間帯は、C# [TimeSpan](/dotnet/api/system.timespan) オブジェクト (D.HH:MM:SS) 形式に従って書式設定されます。  つまり、80 日と 30 分は `80.00:30:00` になります。  0 の場合、先頭の D を削除することができますので 90 分は `00:90:00` になります。  

> [!NOTE]
> シナリオの例を次に示します。
>
> ユーザーは次の 2 つの Web アプリケーションに接続したいものとします:Web アプリケーション A と Web アプリケーション B。
> 
> 考慮すべき要素:
> * 両方の Web アプリケーションとも、同じ親組織にあります。
> * Session Token Max Age を 8 時間に設定したトークンの有効期間ポリシー 1 が、親組織の既定値として設定されています。
> * Web アプリケーション A は、日常的に使用する Web アプリケーションで、どのポリシーにもリンクされていません。
> * Web アプリケーション B は、機密性の高いプロセスに使用されます。 そのサービス プリンシパルは、30 分の　Session Token Max Age が設定されているトークンの有効期間ポリシー 2 にリンクされています。
>
> 正午に、ユーザーは新しいブラウザー セッションを開始し、Web アプリケーション A へのアクセスを試みます。ユーザーは Azure AD にリダイレクトされ、サインインするよう指示されます。 これによって、ブラウザーにセッション トークンを含む Cookie が作成されます。 ユーザーは、Web アプリケーション A にアクセスするための ID トークンによって、Web アプリケーション A にリダイレクトされます。
>
> 午後 0 時 15 分に、ユーザーは Web アプリケーション B へのアクセスを試みます。ブラウザーは Azure AD にリダイレクトされ、そこでセッション Cookie が検出されます。 Web アプリケーション B のサービス プリンシパルは、トークンの有効期間ポリシー 2 にリンクされているだけでなく、既定のトークンの有効期間ポリシー 1 が設定されている親組織の一部にもなっています。 サービス プリンシパルにリンクされているポリシーは、組織の既定のポリシーより優先順位が高いため、トークンの有効期間ポリシー 2 が有効になります。 セッション トークンが最初に発行されたのは、過去 30 分以内であるため、このトークンは有効とみなされます。 ユーザーは、アクセス権限を与える ID トークンによって Web アプリケーション B にリダイレクトされます。
>
> 午後 1 時に、ユーザーは Web アプリケーション A へのアクセスを試みます。ユーザーは、Azure AD にリダイレクトされます。 Web アプリケーション A は、どのポリシーにもリンクされていませんが、既定のトークンの有効期間ポリシー 1 が設定された組織内にあるため、そのポリシーが有効になります。 過去 8 時間以内で最初に発行されたセッション Cookie が検出されます。 ユーザーは自動的に新しい ID トークンで Web アプリケーション A にリダイレクトされます。 ユーザーは認証する必要がありません。
>
> その後直ちにユーザーは Web アプリケーション B へのアクセスを試みます。ユーザーは、Azure AD にリダイレクトされます。 以前と同様、トークンの有効期間ポリシー 2 が有効になります。 トークンは 30 分以上前に発行されたため、ユーザーはサインイン資格情報を再入力するように求められます。 新しいセッション トークンと ID トークンが発行されます。 これで、ユーザーは Web アプリケーション B にアクセスできます。
>
>

## <a name="configurable-policy-property-details"></a>構成可能なポリシーのプロパティの詳細
### <a name="access-token-lifetime"></a>アクセス トークンの有効期間
**文字列:** AccessTokenLifetime

**影響:** アクセス トークン、ID トークン

**概要:** このポリシーは、このリソースのアクセス トークンと ID トークンが有効とみなされる期間を制御します。 Access Token Lifetime プロパティを減らすと、悪意のあるアクターによって、長時間にわたってアクセス トークンや ID トークンが使用されるリスクが軽減します (これらのトークンは取り消しできません)。このトレードオフは、トークンを頻繁に交換する必要があるため、パフォーマンスが影響を受けることです。

### <a name="refresh-token-max-inactive-time"></a>更新トークンの最大非アクティブ時間
**文字列:** MaxInactiveTime

**影響:** 更新トークン

**概要:** このポリシーは、更新トークンがどの程度古くなると、クライアントがこのリソースにアクセスするときに、新しいアクセス トークンと更新トークンのペアの取得に更新トークンを使用できなくするのかを制御します。 更新トークンを使用すると、通常は新しい更新トークンが返されるため、クライアントが指定した期間に、現在の更新トークンを使用して、リソースにアクセスを試みた場合に、このポリシーによって、アクセスが妨げられます。

このポリシーは、クライアントでアクティブでなかったユーザーを強制的に再認証し、新しい更新トークンを取得させることができます。

Refresh Token Max Inactive Time プロパティは Single-Factor Token Max Age および Multi-Factor Refresh Token Max Age プロパティよりも小さな値に設定する必要があります。

### <a name="single-factor-refresh-token-max-age"></a>単一要素更新トークンの最長有効期間
**文字列:** MaxAgeSingleFactor

**影響:** 更新トークン

**概要:** このポリシーは、ユーザーが前回単一要素のみで認証に成功した後に、新しいアクセス トークンと更新トークンのペアを取得するために更新トークンを使用できる期間を制御します。 ユーザーが認証し、新しい更新トークンを受け取ると、ユーザーは、指定した期間のあいだ更新トークン フローを使用できます (これは現在の更新トークンが失効しておらず、非アクティブ時間より長く未使用のままにされていない場合にあてはまります)。その時点で、ユーザーは新しい更新トークンを受け取るために再認証するよう強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Multi-Factor Refresh Token Max Age プロパティ以下の値に設定することをお勧めします。

### <a name="multi-factor-refresh-token-max-age"></a>多要素更新トークンの最長有効期間
**文字列:** MaxAgeMultiFactor

**影響:** 更新トークン

**概要:** このポリシーは、ユーザーが前回多要素で認証に成功した後に、新しいアクセス トークンと更新トークンのペアを取得するために更新トークンを使うことができる期間を制御します。 ユーザーが認証し、新しい更新トークンを受け取ると、ユーザーは、指定した期間のあいだ更新トークン フローを使用できます (これは現在の更新トークンが失効しておらず、非アクティブ時間より長く未使用のままにされていない場合にあてはまります)。その時点で、ユーザーは新しい更新トークンを受け取るために再認証を強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Single-Factor Refresh Token Max Age プロパティ以上の値に設定することをお勧めします。

### <a name="single-factor-session-token-max-age"></a>単一要素セッション トークンの最長有効期間
**文字列:** MaxAgeSessionSingleFactor

**影響:** セッション トークン (永続的および非永続的)

**概要:** このポリシーは、ユーザーが前回単一要素のみで認証に成功した後に、新しい ID とセッション トークンを取得するためにセッショントークンを使用できる期間を制御します。 ユーザーが認証し、新しいセッション トークンを受け取ると、ユーザーは、指定した期間のあいだセッション トークン フローを使用できます (これは、現在のセッション トークンが失効しておらず、有効期限が切れていない場合にあてはまります)。指定した期間の後、ユーザーは新しいセッション トークンを受け取るために再認証が強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Multi-Factor Session Token Max Age プロパティ以下の値に設定することをお勧めします。

### <a name="multi-factor-session-token-max-age"></a>多要素セッション トークンの最長有効期間
**文字列:** MaxAgeSessionMultiFactor

**影響:** セッション トークン (永続的および非永続的)

**概要:** このポリシーは、ユーザーが前回多要素で認証に成功した後に、新しい ID とセッション トークンを取得するためにセッショントークンを使用できる期間を制御します。 ユーザーが認証し、新しいセッション トークンを受け取ると、ユーザーは、指定した期間のあいだセッション トークン フローを使用できます (これは、現在のセッション トークンが失効しておらず、有効期限が切れていない場合にあてはまります)。指定した期間の後、ユーザーは新しいセッション トークンを受け取るために再認証が強制されます。

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このプロパティは、Single-Factor Session Token Max Age プロパティ以上の値に設定することをお勧めします。

## <a name="example-token-lifetime-policies"></a>トークンの有効期間ポリシーの例
アプリ、サービス プリンシパル、および組織全体のトークンの有効期間を作成および管理できる場合は、Azure AD で多くのシナリオを実行できます。 このセクションでは新しい規則を適用する場合に役立つ、いくつかの一般的なポリシー シナリオについて説明します。

* トークンの有効期間
* トークンの最大非アクティブ時間
* トークンの最長有効期間

例では、次の方法を説明します。

* 組織の既定のポリシーを管理する
* Web サインインのポリシーを作成する
* Web API を呼び出すネイティブ アプリケーションのポリシーを作成する
* 詳細なポリシーを管理する

### <a name="prerequisites"></a>前提条件
次の例では、アプリ、サービス プリンシパル、および組織全体のポリシーを作成、更新、リンク、および削除します。 Azure AD に慣れていない場合は、これらの例を続行する前に、[Azure AD テナントを取得する方法](quickstart-create-new-tenant.md)について学習することをお勧めします。  

使用を開始するには、次の手順を実行します。

1. 最新版の [Azure AD PowerShell モジュール パブリック プレビュー リリース](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。
2. `Connect` コマンドを実行して、Azure AD 管理者アカウントにサインインします。 新しいセッションを開始するたびにこのコマンドを実行します。

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 組織に作成されているすべてのポリシーを表示するには、次のコマンドを実行します。 このコマンドは、次のシナリオでほとんどの操作の後に実行します。 コマンドの実行は、ポリシーの ** ** を取得する場合にも役立ちます。

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>例:組織の既定のポリシーを管理する
この例では、組織全体でユーザーがサインインする頻度を少なくするポリシーを作成します。 そのためには、組織全体に適用される単一要素の更新トークンに対してトークンの有効期間ポリシーを作成します。 このポリシーは、組織内のすべてのアプリケーションと、ポリシーがまだ設定されていない各サービス プリンシパルに適用されます。

1. トークンの有効期間ポリシーを作成します。

    1. 単一要素の更新トークンを "until-revoked" に設定します。 トークンは、アクセスが取り消されるまで期限切れになりません。 次のポリシー定義を作成します。

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. ポリシーを作成するには、次のコマンドを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. 新しいポリシーを表示し、ポリシーの **ObjectId** を取得するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. ポリシーを更新します。

    この例で設定する最初のポリシーは、サービスに求められるほど厳密にしないようにすることもできます。 単一要素更新トークンを 2 日で期限が切れるように設定するには、次のコマンドを実行します。

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>例:Web サインインのポリシーを作成する

この例では、ユーザーが、Web アプリで頻繁に認証を必要とするポリシーを作成します。 このポリシーは、アクセス トークンと ID トークンの有効期間と、多要素セッション トークンの最長有効期間を Web アプリのサービス プリンシパルに設定します。

1. トークンの有効期間ポリシーを作成します。

    この Web サインイン用のポリシーでは、アクセス トークンと ID トークンの有効期間と、単一要素セッション トークンの最長有効期間を 2 時間に設定します。

    1. ポリシーを作成するには、このコマンドを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. 新しいポリシーを表示し、ポリシーの **ObjectId** を取得するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの **ObjectId** も取得する必要があります。

    1. [Get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用して、組織のすべてのサービス プリンシパルまたは 1 つのサービス プリンシパルを表示します。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. サービス プリンシパルがある場合、次のコマンドを実行します。
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>例:Web API を呼び出すネイティブ アプリケーションのポリシーを作成する
この例では、ユーザーに必要とする認証の頻度を少なくするポリシーを作成します。 ポリシーにより、ユーザーが再認証を必要とするまでの非アクティブでいられる時間も長くなります。 ポリシーは、Web API に適用されます。 ネイティブ アプリがリソースとして Web API を要求すると、このポリシーが適用されます。

1. トークンの有効期間ポリシーを作成します。

    1. Web API の厳密なポリシーを作成するには、次のコマンドを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. 新しいポリシーを表示するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Web API にポリシーを割り当てます。 アプリケーションの **ObjectId** を取得する必要もあります。 [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) コマンドレットを使用して、アプリの **ObjectId** を見つけるか、[Azure portal](https://portal.azure.com/) を使用します。

    アプリの **ObjectId** を取得し、ポリシーを割り当てます。

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>例:詳細なポリシーを管理する
この例では、いくつかのポリシーを作成して、優先度システムのしくみを説明します。 複数のオブジェクトに適用される複数のポリシーを管理する方法も説明します。

1. トークンの有効期間ポリシーを作成します。

    1. 単一要素の更新トークンの有効期間を 30 日間に設定する組織の既定のポリシーを作成するには、次のコマンドを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. 新しいポリシーを表示するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. サービス プリンシパルにポリシーを割り当てます。

    これで、組織全体に適用されるポリシーが得られます。 特定のサービス プリンシパルに対してはこの 30 日間のポリシーを保持しますが、組織の既定のポリシーを "until-revoked" の上限となるよう変更するとします。

    1. 組織のすべてのサービス プリンシパルを表示するには、[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用します。

    2. サービス プリンシパルがある場合、次のコマンドを実行します。

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. `IsOrganizationDefault` フラグを false に設定します。

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. 新しい組織の既定のポリシーを作成します。

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    これで、元のポリシーがサービス プリンシパルにリンクされ、新しいポリシーが組織の既定のポリシーとして設定されます。 サービス プリンシパルに適用されるポリシーが、組織の既定のポリシーよりも優先されることに注意してください。

## <a name="cmdlet-reference"></a>コマンドレット リファレンス

### <a name="manage-policies"></a>ポリシーの管理

次のコマンドレットを使用して、ポリシーを管理することができます。

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

新しいポリシーを作成します。

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Definition</code> |ポリシーのすべてのルールが含まれる文字列化された JSON の配列。 | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |ポリシー名の文字列。 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |True の場合は、組織の既定のポリシーとして、ポリシーを設定します。 False の場合は、何もしません。 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |ポリシーの種類。 トークンの有効期間に対しては、常に "TokenLifetimePolicy" を使用します。 | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [省略可能] |ポリシーの代替 ID を設定します。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
すべての AzureAD ポリシーまたは指定されたポリシーを取得します。

```powershell
Get-AzureADPolicy
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> [省略可能] |目的のポリシーの **ObjectId (ID)** 。 |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get AzureADPolicyAppliedObject
ポリシーにリンクされたすべてのアプリとサービス プリンシパルを取得します。

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |目的のポリシーの **ObjectId (ID)** 。 |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
既存のポリシーを更新します。

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |目的のポリシーの **ObjectId (ID)** 。 |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |ポリシー名の文字列。 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [省略可能] |ポリシーのすべてのルールが含まれる文字列化された JSON の配列。 |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [省略可能] |True の場合は、組織の既定のポリシーとして、ポリシーを設定します。 False の場合は、何もしません。 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [省略可能] |ポリシーの種類。 トークンの有効期間に対しては、常に "TokenLifetimePolicy" を使用します。 |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [省略可能] |ポリシーの代替 ID を設定します。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
指定したポリシーを削除します。

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |目的のポリシーの **ObjectId (ID)** 。 | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>アプリケーション ポリシー
アプリケーション ポリシーには次のコマンドレットを使用できます。</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
指定したポリシーをアプリケーションにリンクします。

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |アプリケーションの **ObjectId (ID)** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |ポリシーの **ObjectId**。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
アプリケーションに割り当てられているポリシーを取得します。

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |アプリケーションの **ObjectId (ID)** 。 | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
アプリケーションからポリシーを削除します。

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |アプリケーションの **ObjectId (ID)** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |ポリシーの **ObjectId**。 | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>サービス プリンシパル ポリシー
サービス プリンシパル ポリシーには次のコマンドレットを使用できます。

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
指定したポリシーをサービス プリンシパルにリンクします。

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |アプリケーションの **ObjectId (ID)** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |ポリシーの **ObjectId**。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
指定したサービス プリンシパルにリンクされている任意のポリシーを取得します。

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |アプリケーションの **ObjectId (ID)** 。 | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
指定したサービス プリンシパルからポリシーを削除します。

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| parameters | 説明 | 例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |アプリケーションの **ObjectId (ID)** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |ポリシーの **ObjectId**。 | `-PolicyId <ObjectId of Policy>` |
