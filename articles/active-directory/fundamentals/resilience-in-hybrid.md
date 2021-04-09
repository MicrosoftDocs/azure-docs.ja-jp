---
title: Azure Active Directory でより回復性があるハイブリッド認証を構築する
description: 回復性があるハイブリッド インフラストラクチャの構築に関する、アーキテクトおよび IT 管理者向けのガイド。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724761"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>ハイブリッド アーキテクチャで回復性を強化する

ハイブリッド認証を使用すると、ユーザーはオンプレミスで管理されている ID を使用して、クラウドベースのリソースにアクセスできます。 ハイブリッド インフラストラクチャには、クラウドとオンプレミスの両方のコンポーネントが含まれています。

* クラウド コンポーネントとしては、Azure AD、Azure リソースとサービス、組織のクラウドベース アプリ、SaaS アプリケーションなどがあります。

* オンプレミス コンポーネントには、オンプレミス アプリケーション、SQL データベースなどのリソース、および Windows Server Active Directory のような ID プロバイダーが含まれます。 

> [!IMPORTANT]
> ハイブリッド インフラストラクチャでの回復性を計画する際は、依存関係と単一障害点の数を最小限にすることが重要です。 

Microsoft は、ハイブリッド認証用として 3 つのメカニズムを提供しています。 これらのオプションは、回復性の順序に従って一覧表示されています。 可能な場合は、パスワード ハッシュ同期を実装することをお勧めします。

* [パスワード ハッシュ同期](../hybrid/whatis-phs.md) (PHS) では、Azure AD Connect を使用して ID とパスワードのハッシュを Azure AD に同期させることで、ユーザーがオンプレミスで管理されているパスワードを使用してクラウドベースのリソースにサインインできるようにします。 PHS には、認証のためではなく、同期のためだけにオンプレミスの依存関係があります。

* [パススルー認証](../hybrid/how-to-connect-pta.md) (PTA) では、ユーザーはサインインのために Azure AD にリダイレクトされます。 次に、企業ネットワークにデプロイされているエージェントを介して、ユーザー名とパスワードがオンプレミスの Active Directory に対して検証されます。 PTA には、オンプレミスのサーバー上に存在する Azure AD PTA エージェントのオンプレミス占有領域があります。

* [フェデレーション](../hybrid/whatis-fed.md)のお客様が AD FS などのフェデレーション サービスをデプロイし、その後、フェデレーション サービスによって生成された SAML アサーションが Azure AD によって検証されます。 フェデレーションは、オンプレミス インフラストラクチャへの依存関係が最も大きいため、障害点も多くなります。 

   
‎組織では、これらの方法の 1 つ以上が使用されている可能性があります。 詳細については、「[Azure AD ハイブリッド ID ソリューションの適切な認証方法を選択する](../hybrid/choose-ad-authn.md)」をご覧ください。 この記事には、方法を決定するのに役立つデシジョン ツリーが含まれています。

## <a name="password-hash-synchronization"></a>パスワード ハッシュの同期

Azure AD の最も単純で回復性があるハイブリッド認証オプションは、[パスワードハッシュ同期](../hybrid/whatis-phs.md)です。これには、認証要求を処理する際にオンプレミス ID インフラストラクチャへの依存関係はありません。 パスワード ハッシュを持つ ID が Azure AD に同期されると、ユーザーはオンプレミスの ID コンポーネントに依存せずに、クラウド リソースに対して認証を行うことができます。 

![PHS のアーキテクチャ図](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

この認証オプションを選択した場合は、オンプレミスの ID コンポーネントが使用不可になっても中断が発生することはありません。 オンプレミスの中断は、ハードウェア障害、停電、自然災害、マルウェアによる攻撃など、さまざまな理由で発生する可能性があります。 

### <a name="how-do-i-implement-phs"></a>PHS の実装方法

PHS を実装するには、次のリソースを参照してください。

* [Azure AD Connect を使用してパスワード ハッシュ同期を実装する](../hybrid/how-to-connect-password-hash-synchronization.md)

* [パスワード ハッシュ同期を有効にする](../hybrid/how-to-connect-password-hash-synchronization.md)

PHS を使用できないという要件がある場合は、パススルー認証を使用してください。

## <a name="pass-through-authentication"></a>パススルー認証

パススルー認証は、サーバー上にオンプレミスで存在する認証エージェントに依存します。 Azure AD とオンプレミスの PTA エージェントの間には、永続的な接続 (Service Bus) が存在します。 ファイアウォール、認証エージェントをホストするサーバー、およびオンプレミスの Windows Server Active Directory (またはその他の ID プロバイダー) はすべて潜在的な障害点です。 

![PTA のアーキテクチャ図](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>PTA の実装方法

パススルー認証を実装するには、次のリソースを参照してください。

* [パススルー認証のしくみ](../hybrid/how-to-connect-pta-how-it-works.md)

* [パススルー認証のセキュリティの詳細](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Azure AD パススルー認証をインストールする](../hybrid/how-to-connect-pta-quick-start.md)

* PTA を使用する場合は、[高可用性トポロジ](../hybrid/how-to-connect-pta-quick-start.md)を定義します。

 ## <a name="federation"></a>フェデレーション

フェデレーションでは、Azure AD とフェデレーション サービスの間に信頼関係を作成する必要があります。これには、エンドポイント、トークン署名証明書、およびその他のメタデータの交換が含まれます。 要求が Azure AD に到着すると、構成が読み取られ、構成されているエンドポイントにユーザーがリダイレクトされます。 その時点で、ユーザーはフェデレーション サービスと対話し、それによって、Azure AD によって検証された SAML アサーションが発行されます。 

次の図は、企業の Active Directory フェデレーション サービス (AD FS) のトポロジを示しています。これは、複数のオンプレミス データ センターにまたがる冗長フェデレーションおよび Web アプリケーション プロキシ・サーバーが含まれたデプロイです。 この構成は、DNS、geo アフィニティ機能を備えたネットワーク負荷分散、ファイアウォールなどのエンタープライズ ネットワーク インフラストラクチャ コンポーネントに依存します。オンプレミスのコンポーネントと接続はすべて、障害の影響を受けやすくなります。 詳細については、[AD FS のキャパシティ プランニングに関するドキュメント](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)を参照してください。

> [!NOTE]
>  フェデレーションでは、オンプレミスの依存関係の数が最も多いため、潜在的な障害点の数も最も多くなります。 こちらの図は AD FS を示していますが、他のオンプレミスの ID プロバイダーも、高可用性、スケーラビリティ、およびフェールオーバーを実現するために、同様の設計上の考慮事項の対象となります。

![フェデレーションのアーキテクチャ図](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>フェデレーションの実装方法

フェデレーション認証戦略を実装している場合、またはその回復性を向上させたい場合は、次のリソースを参照してください。

* [フェデレーション認証とは](../hybrid/whatis-fed.md)

* [フェデレーションのしくみ](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD のフェデレーション互換性リスト](../hybrid/how-to-connect-fed-compatibility.md)

* [AD FS のキャパシティ プランニングに関するドキュメント](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)に従います

* [Azure IaaS での AD FS のデプロイ](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* フェデレーションと共に [PHS を有効](../hybrid/tutorial-phs-backup.md)にします

## <a name="next-steps"></a>次のステップ
管理者とアーキテクト向けの回復性に関するリソース
 
* [資格情報管理を使用して回復性を強化する](resilience-in-credentials.md)

* [デバイスの状態を使用して回復性を強化する](resilience-with-device-states.md)

* [継続的アクセス評価 (CAE) を使用して回復性を強化する](resilience-with-continuous-access-evaluation.md)

* [外部ユーザー認証で回復性を強化する](resilience-b2b-authentication.md)

* [アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する](resilience-on-premises-access.md)

開発者向けの回復性に関するリソース

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [CIAM システムで回復性を強化する](resilience-b2c.md)