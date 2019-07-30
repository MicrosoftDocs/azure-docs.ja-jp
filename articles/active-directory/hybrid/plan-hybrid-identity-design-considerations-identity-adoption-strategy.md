---
title: ハイブリッド ID 設計 - 導入戦略 - Azure | Microsoft Docs
description: 条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。 条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109331"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>ハイブリッド ID 導入戦略の定義
このタスクでは、次のトピックで検討したビジネス要件を満たすために、ハイブリッド ID ソリューションのハイブリッド ID 導入戦略を定義します。

* [ビジネス ニーズの決定](plan-hybrid-identity-design-considerations-business-needs.md)
* [ディレクトリ同期要件の決定](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [多要素認証要件の決定](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>ビジネス ニーズ戦略の定義
最初のタスクでは、組織のビジネス ニーズの決定に対応します。  組織のビジネス ニーズは非常に多岐にわたり、注意を怠るとスコープ クリープが発生する可能性があります。  最初はシンプルさを心がけながら、今後の変更に対応し、容易にするような設計の計画を常に意識してください。  設計がシンプルか複雑かを問わず、Azure Active Directory は、Office 365、Microsoft Online Services、クラウド対応アプリケーションをサポートする Microsoft ID プラットフォームです。

## <a name="define-an-integration-strategy"></a>統合戦略の定義
マイクロソフトでは、クラウド ID、同期済み ID、フェデレーション ID の 3 つの主な統合シナリオを用意しています。  これらの統合戦略の 1 つを導入する計画を行う必要があります。  選択する戦略はさまざまで、選択の決定要因には、どのようなタイプのユーザー エクスペリエンスを提供するか、既存のインフラストラクチャがあるか、何が最もコスト効率に優れているか、などがあります。  

![統合シナリオ](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

上の図で定義されているシナリオは、次のとおりです。

* **クラウド ID**: クラウドのみに存在する ID です。  Azure AD の場合は、具体的に Azure AD ディレクトリ内に存在します。
* **同期済み**: オンプレミスとクラウドに存在する ID です。  Azure AD Connect を使用すると、これらのユーザーは作成されるか、既存の Azure AD アカウントと結合されます。  ユーザーのパスワードは、パスワード ハッシュと呼ばれるものを使用して、オンプレミスの環境からクラウドに同期されます。  同期済みのものを使用する場合の注意点として、オンプレミス環境でユーザーが無効にされた場合に、そのアカウントの状態が Azure AD に反映されるまでに最大 3 時間かかることがあります。  同期時間の間隔がその原因です。
* **フェデレーション**: これらの ID は、オンプレミスとクラウドの両方に存在します。  Azure AD Connect を使用すると、これらのユーザーは作成されるか、既存の Azure AD アカウントと結合されます。  

> [!NOTE]
> 同期オプションの詳細については、[オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)に関するページをお読みください。
> 
> 

次の表は、各戦略の長所と短所を把握するのに役立ちます。

| 戦略 | 長所 | 短所 |
| --- | --- | --- |
| **クラウド ID** |小規模な組織の管理がさらに容易になります。 <br> オンプレミスでインストールするものはありません。 追加のハードウェアは必要ありません<br>ユーザーが退職した場合、容易に無効にすることができます |ユーザーは、クラウド内のワークロードにアクセスするときにサインインする必要があります <br> クラウド ID とオンプレミスの ID で、パスワードが同じ場合と異なる場合があります |
| **同期済み** |オンプレミスのパスワードにより、オンプレミスとクラウドの両方のディレクトリを認証します <br>小規模、中規模、または大規模の組織での管理がさらに容易になります <br>ユーザーは一部のリソースにシングル サインオン (SSO) できます <br> マイクロソフトが推奨する同期方法です <br> 管理がさらに容易になります |お客様によっては、企業ごとのポリシーにより、クラウドと自社ディレクトリの同期を積極的に行わない場合があります。 |
| **フェデレーション** |ユーザーはシングル サインオン (SSO) できます <br>ユーザーが解雇されたり退職したりした場合、アカウントを即座に無効にしてアクセスを取り消すことができます<br> 同期では実現できない高度なシナリオをサポートします |設定および構成の手順が多くなります <br> 高度なメンテナンスが必要です <br> STS インフラストラクチャ用に追加のハードウェアが必要な場合があります <br> フェデレーション サーバーをインストールするために、追加のハードウェアが必要な場合があります。 AD FS を使用する場合は、追加のソフトウェアが必要です <br> SSO の広範な設定が必要です <br> フェデレーション サーバーがダウンした場合の障害点が重大で、ユーザーの認証ができなくなります |

### <a name="client-experience"></a>クライアント エクスペリエンス
使用する戦略は、ユーザーのサインイン エクスペリエンスに影響します。  次の表に、ユーザーがサインイン エクスペリエンスに何を求めるかに関する情報を示します。  すべてのフェデレーション ID プロバイダーが、すべてのシナリオで SSO をサポートするわけではありません。

**ドメインに参加しているプライベート ネットワーク アプリケーション**:

|  | 同期済み ID | フェデレーション ID |
| --- | --- | --- |
| Web ブラウザー |フォーム ベース認証 |シングル サインオン。組織 ID の提示が必要な場合があります |
| Outlook |資格情報の要求 |資格情報の要求 |
| Skype for Business (Lync) |資格情報の要求 |Lync の場合はシングル サインオン。Exchange の場合は資格情報が要求されます |
| OneDrive for Business |資格情報の要求 |シングル サインオン |
| Office Pro Plus サブスクリプション |資格情報の要求 |シングル サインオン |

**外部ソースまたは信頼されていないソース**:

|  | 同期済み ID | フェデレーション ID |
| --- | --- | --- |
| Web ブラウザー |フォーム ベース認証 |フォーム ベース認証 |
| Outlook、Skype for Business (Lync)、OneDrive for Business、Office サブスクリプション |資格情報の要求 |資格情報の要求 |
| Exchange ActiveSync |資格情報の要求 |Lync の場合はシングル サインオン。Exchange の場合は資格情報が要求されます |
| モバイル アプリ |資格情報の要求 |資格情報の要求 |

タスク 1 で、サードパーティ IdP があるか、サード パーティ IdP を使用して Azure AD とフェデレーションを行うことを決定した場合は、次のサポートされる機能を理解しておく必要があります。

* SP-Lite プロファイルに準拠している SAML 2.0 プロバイダーは、Azure AD および関連するアプリケーションに対する認証をサポートできます
* パッシブ認証がサポートされます。これにより、OWA や SPO などに対する認証が容易になります。
* SAML 2.0 Enhanced Client Profile (ECP) を使用して Exchange Online クライアントをサポートできます。

また、どの機能が使用できないかについても注意が必要です。

* WS-Trust/WS-Federation のサポートがない場合、他のすべてのアクティブなクライアントが停止します
  * つまり、Lync クライアント、OneDrive クライアント、Office サブスクリプション、Office 2016 以前の Office Mobile がなくなります。
* Office からパッシブ認証に移行することで、これらのクライアントで純正の SAML 2.0 IdP をサポートできますが、あくまでもクライアント間ベースでのサポートになります

> [!NOTE]
> 最新の一覧については、「[Azure AD のフェデレーション互換性リスト](how-to-connect-fed-compatibility.md)」の記事をご覧ください。
> 
> 

## <a name="define-synchronization-strategy"></a>同期戦略の定義
このタスクでは、組織のオンプレミス データとクラウドの同期に使用するツールと、どのようなトポロジを使用する必要があるかを定義します。  ほとんどの組織で Active Directory を使用しているため、ここでは上記の質問に回答するために、Azure AD Connect の使用に関する詳細情報を示します。  Active Directory がない環境向けには、この戦略を計画するための FIM 2010 R2 または MIM 2016 の使用に関する情報を提供します。  ただし、Azure AD Connect の今後のリリースで LDAP ディレクトリがサポートされるため、計画のスケジュールによってはこの情報が役に立つ場合があります。

### <a name="synchronization-tools"></a>同期ツール
長年にわたり、いくつかの同期ツールが登場し、さまざまなシナリオで使用されています。  現在、サポートされているすべてのシナリオで、Azure AD Connect が主要なツールとして選ばれています。  AAD Sync や DirSync もまだまだ健在で、現在の環境にも見られます。 

> [!NOTE]
> 各ツールでサポートされる機能の最新情報については、記事「 [ディレクトリ統合ツールの比較](plan-hybrid-identity-design-considerations-tools-comparison.md) 」をお読みください。  
> 
> 

### <a name="supported-topologies"></a>サポートされているトポロジ
同期戦略を定義する場合、使用するトポロジを決定する必要があります。 手順 2. で決定した情報に基づいて、使用に適したトポロジを決定できます。 単一のフォレスト、単一の Azure AD トポロジが最も一般的で、単一の Active Directory フォレストと Azure AD の単一のインスタンスで構成されます。  このトポロジは、多くのシナリオで使用されており、次の図に示すように、Azure AD Connect の高速インストールを使用する場合に想定されるトポロジです。

![サポートされるトポロジ](./media/plan-hybrid-identity-design-considerations/single-forest.png)単一フォレスト シナリオでは、図 5 に示すとおり、大規模な組織はもちろんのこと小規模な組織でも複数のフォレストを持つことは一般的です。

> [!NOTE]
> Azure AD Connect の同期を使用したオンプレミス AD や Azure AD のさまざまなトポロジの詳細については、記事「 [Azure AD Connect のトポロジ](plan-connect-topologies.md)」をお読みください。
> 
> 

![複数フォレスト トポロジ](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

複数フォレストのシナリオ

次の項目に該当する場合は、複数のフォレストで構成される単一の Azure AD トポロジを検討してください。

* ユーザーの ID がフォレスト全体で 1 つのみである: 詳細については、ユーザーの一意の識別子に関するセクションで後述します。
* ユーザー ID が配置されているフォレストに対してユーザーの認証を行う。
* UPN とソース アンカー (変更できない ID) がこのフォレストから取得される。
* Azure AD Connect からすべてのフォレストにアクセスできる: つまり、ドメインに参加する必要がない。この項目を満たしやすくなる場合は Azure AD Connect を DMZ に配置できます。
* ユーザーのメールボックスが 1 つのみである。
* ユーザーのメールボックスをホストするフォレストが、Exchange グローバル アドレス一覧 (GAL) に表示される属性に最適なデータ品質を備えている。
* ユーザーにメールボックスがない場合、任意のフォレストを使用してこれらの値を提供できる。
* リンクされたメールボックスがある場合、サインインに使用される別のアカウントが異なるフォレストにある。

> [!NOTE]
> クラウドとオンプレミスの両方に存在するオブジェクトは、一意の識別子を使用して "接続" されます。 ディレクトリ同期の場合は、この一意の識別子を SourceAnchor といいます。 シングル サインオンの場合は、これを ImmutableId といいます。 [Azure AD Connect の設計概念](plan-connect-design-concepts.md#sourceanchor) 」を参照してください。
> 
> 

上記に該当しない場合で、かつアクティブなアカウントまたはメールボックスが複数ある場合は、Azure AD Connect により 1 つだけ選択され、それ以外は無視されます。  リンクされたメールボックスはあるが、その他のアカウントがない場合、それらのアカウントは Azure AD にはエクスポートされず、そのユーザーはどのグループのメンバーにもなりません。  これは、以前に DirSync で実施されていた処理とは異なります。このような複数フォレストのシナリオのサポートを向上するための措置です。 次の図に、複数フォレストのシナリオを示します。

![複数の Azure AD テナント](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**複数のフォレストと複数の Azure AD のシナリオ**

組織で使用する Azure AD のディレクトリは 1 つのみにすることをお勧めしますが、Azure AD Connect の同期サーバーと Azure AD ディレクトリの間で 1:1 の関係が維持されていれば、複数使用するシナリオもサポートされます。  Azure AD のインスタンスごとに、Azure AD Connect をインストールする必要があります。  また、Azure AD は設計上分離されているため、Azure AD の 1 つのインスタンス内のユーザーは、別のインスタンス内のユーザーを表示できません。

次の図に示すように、Active Directory の単一のオンプレミス インスタンスと複数の Azure AD ディレクトリの接続も可能でサポートされています。

![単一フォレストのフィルター処理](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**単一フォレストのフィルター処理のシナリオ**

これを行うには、次の項目に該当する必要があります。

* 各 Azure AD Connect 同期サーバーは、操作対象のオブジェクトのセットが相互排他的になるようなフィルター処理を構成する必要があります。  たとえば、各サーバーが特定のドメインまたは OU をスコープにするようにします。
* DNS ドメインは 1 つの Azure AD ディレクトリにしか登録できないため、オンプレミス AD のユーザーの UPN で個別の名前空間を使用する必要があります。
* Azure AD の 1 つのインスタンスのユーザーは、そのインスタンスのユーザーのみを表示できます。  他のインスタンスのユーザーを表示することはできません。
* 1 つの Azure AD ディレクトリだけが、オンプレミスの AD を持つ Exchange ハイブリッドを有効にできます。
* 相互排他性は書き戻しにも適用されます。  そのため、単一のオンプレミス構成を前提としている一部の書き戻し機能は、このトポロジではサポートされません。  次のトピックがあります。
  * 既定の構成によるグループの書き戻し
  * デバイスの書き戻し

次はサポートされていません。実装として選択しないでください。

* 同じ Azure AD ディレクトリに対する複数の Azure AD Connect 同期サーバーの接続は、同期サーバーが相互に排他的な一連のオブジェクトを同期するように構成されている場合でもサポートされません。
* 複数の Azure AD ディレクトリへの同じユーザーの同期は、サポートされていません。 
* 1 つの Azure AD のユーザーを他の Azure AD ディレクトリで連絡先として表示するような構成の変更もサポートされていません。 
* 複数の Azure AD ディレクトリに接続するような Azure AD Connect Sync の変更もサポートされていません。
* Azure AD のディレクトリは、分離するように設計されています。 ディレクトリ間に共通の統合された GAL を構築しようとする際に他の Azure AD ディレクトリからデータを読み取るような、Azure AD Connect Sync の構成の変更はサポートされていません。 Azure AD Connect Sync を使用している別のオンプレミス AD の連絡先としてユーザーをエクスポートすることもサポートされていません。

> [!NOTE]
> 組織でネットワーク上のコンピューターからインターネットへの接続が制限されている場合、次の記事の一覧にあるエンドポイント (FQDN、IPv4 と IPv6 のアドレス範囲) を、クライアント コンピューターの送信許可リストと Internet Explorer の信頼済みサイト ゾーンに追加して、コンピューターで Office 365 を正常に使用できるようにしてください。 詳細については、「 [Office 365 の URL と IP アドレスの範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)」をお読みください。
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>多要素認証戦略の定義
このタスクでは、使用する多要素認証戦略を定義します。  Azure Multi-Factor Authentication では、次の 2 種類のバージョンが用意されています。  1 つはクラウドベースで、もう 1 つは Azure MFA Server を使用するオンプレミス ベースです。  上記で行った評価に基づいて、戦略に適したソリューションを決定できます。  次の表を使用して、どの設計オプションが企業のセキュリティ要件を最適に満たすかを判断します。

多要素設計オプション:

| セキュリティで保護する資産 | クラウドでの MFA | オンプレミスでの MFA |
| --- | --- | --- |
| Microsoft アプリ |はい |はい |
| アプリ ギャラリー内の SaaS アプリ |はい |はい |
| Azure AD アプリケーション プロキシ経由で公開される IIS アプリケーション |はい |はい |
| Azure AD アプリケーション プロキシ経由で公開されない IIS アプリケーション |× |はい |
| VPN、RDG などのリモート アクセス |× |はい |

戦略のソリューションが決定した場合でも、さらに上記の評価を使用して、ユーザーの配置を検討する必要があります。  これにより、ソリューションが変更される可能性があります。  次の表を使用すると、この決定に役立ちます。

| ユーザーの配置 | 推奨される設計オプション |
| --- | --- |
| Azure Active Directory |クラウドでの Multi-Factor Authentication |
| AD FS によるフェデレーションを使用した Azure AD とオンプレミスの AD |両方 |
| Azure AD Connect を使用した Azure AD とオンプレミスの AD。パスワード同期なし |両方 |
| Azure AD Connect を使用した Azure AD とオンプレミスの AD。パスワード同期あり |両方 |
| オンプレミスの AD |Multi-Factor Authentication Server |

> [!NOTE]
> また、選択した多要素認証の設計オプションで、設計に必要な機能がサポートされていることを確認する必要があります。  詳細については、「 [ユーザーに適した多要素のセキュリティ ソリューションの選択](../authentication/concept-mfa-howitworks.md)」をお読みください。
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor Auth Provider
Azure Active Directory テナントを持つグローバル管理者は、既定で多要素認証を使用できます。 ただし、すべてのユーザーに多要素認証を拡張する場合や、グローバル管理者が管理ポータル、カスタムの案内応答、レポートなどの機能を利用できるようにする場合は、Multi-Factor Authentication Provider を購入して構成する必要があります。

> [!NOTE]
> また、選択した多要素認証の設計オプションで、設計に必要な機能がサポートされていることを確認する必要があります。 
> 
> 

## <a name="next-steps"></a>次の手順
[データ保護要件の決定](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>関連項目
[設計上の考慮事項の概要](plan-hybrid-identity-design-considerations-overview.md)

