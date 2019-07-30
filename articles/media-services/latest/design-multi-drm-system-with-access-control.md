---
title: アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計 - Azure Media Services | Microsoft Docs
description: Microsoft Smooth Streaming Client Porting Kit のライセンスの取得について説明します。
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: ffbf53c0bb0aaf2832afecc2d0df935f04eeff19
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310325"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計 

Over-the-Top (OTT) 用またはオンライン ストリーミング ソリューション用のデジタル著作権管理 (DRM) サブシステムの設計と構築は、複雑なタスクです。 通常、運営会社/オンライン ビデオ プロバイダーはこのタスクを専門の DRM サービス プロバイダーに外部委託します。 このドキュメントでは、OTT またはオンライン ストリーミング ソリューションでのエンド ツー エンドの DRM サブシステムの参照設計と参照実装について説明します。

このドキュメントの対象読者は、OTT またはオンライン ストリーミング/マルチスクリーン ソリューションの DRM サブシステムに関する作業を行っているエンジニア、または DRM サブシステムに興味のあるすべての読者です。 前提として、読者は、PlayReady、Widevine、FairPlay、Adobe Access など、市販されている DRM テクノロジの少なくとも 1 つには精通している必要があります。

ここでは、Azure Media Services でサポートされている 3 つの DRM をマルチ DRM に使用します。PlayReady と Widevine に対応した Common Encryption (CENC)、FairPlay、および AES-128 クリア キー暗号化です。 オンライン ストリーミングと OTT 業界での大きなトレンドは、さまざまなクライアント プラットフォームのネイティブ DRM を使う方法です。 その前は、さまざまなクライアント プラットフォームに 1 つの DRM とそのクライアント SDK を使う方法がトレンドでした。 マルチ ネイティブ DRM で CENC を使うと、PlayReady と Widevine はいずれも [共通暗号化 (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 仕様に従って暗号化されます。

コンテンツ保護にネイティブのマルチ DRM を使用することには、次のような利点があります。

* さまざまなプラットフォームとそのネイティブ DRM に対して単一の暗号化処理が使用されるので、暗号化のコストが減ります。
* ストレージ内に必要な資産のコピーが 1 つだけで済むので、資産の管理コストが減ります。
* ネイティブ DRM クライアントはネイティブ プラットフォームでは通常無料なので、DRM クライアント ライセンスのコストがかかりません。

### <a name="goals-of-the-article"></a>この記事の目標

この記事の目標を以下に示します。

* 3 つの DRM (DASH 用の CENC、HLS 用の FairPlay、およびスムーズ ストリーミング用の PlayReady) をすべて使用する DRM サブシステムの参照設計を示します。
* Azure および Azure Media Services プラットフォームでの参照実装を示します。
* 設計と実装に関するいくつかのトピックについて説明します。

次の表は、各種プラットフォームでのネイティブ DRM サポートと、各種ブラウザーでの EME サポートをまとめたものです。

| **クライアント プラットフォーム** | **ネイティブ DRM** | **EME** |
| --- | --- | --- |
| **スマート TV、STB** | PlayReady、Widevine など | PlayReady や Widevine に対応した埋め込みブラウザー/EME|
| **Windows 10** | PlayReady | PlayReady に対応した Microsoft Edge/IE11|
| **Android デバイス (電話、タブレット、TV)** |Widevine |Widevine に対応した Chrome |
| **iOS** | FairPlay | FairPlay に対応した Safari (iOS 11.2 以降) |
| **macOS** | FairPlay | FairPlay に対応した Safari (Mac OS X 10.11+ El Capitan 上の Safari 9+ 以降)|
| **tvOS** | FairPlay | |

各 DRM の現在のデプロイメント状態を考慮すると、サービスでは通常 2 ～ 3 種類の DRM を実装し、すべての種類のエンドポイントに最適な方法で対応できるようにする必要があります。

さまざまなクライアントで一定レベルのユーザー エクスペリエンスを実現するには、サービス ロジックの複雑さとクライアント側での複雑さの間にトレードオフが発生します。

選択するときは、次の点に注意してください。

* PlayReady はすべての Windows デバイスと一部の Android デバイスにネイティブ実装され、事実上すべてのプラットフォームでソフトウェア SDK を介して利用できます。
* Widevine はすべての Android デバイス、Chrome、その他の一部のデバイスにネイティブ実装されます。 また、Widevine は DASH を介して Firefox や Opera ブラウザーでもサポートされます。
* FairPlay は、iOS、macOS、tvOS で使用できます。


## <a name="a-reference-design"></a>参照設計
このセクションでは、実装に使われるテクノロジに依存しない参照設計を示します。

DRM サブシステムに含まれる可能性のあるコンポーネントは次のとおりです。

* キー管理
* DRM 暗号化パッケージング
* DRM ライセンス配信
* 権利チェック/アクセス制御
* ユーザー認証/承認
* プレーヤー アプリ
* 配信元/コンテンツ配信ネットワーク (CDN)

次の図では、DRM サブシステムのコンポーネント間の相互関係の概要を示します。

![CENC を使用する DRM サブシステム](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

設計には 3 つの基本レイヤーがあります。

* バックオフィス レイヤー (黒) は、外部には公開されません。
* DMZ レイヤー (濃い青) には、パブリックに公開されるすべてのエンドポイントが含まれます。
* パブリック インターネット レイヤー (明るい青) には、CDN およびプレーヤーとパブリック インターネット上のトラフィックが含まれます。

また、静的暗号化か動的暗号化かにかかわらず、DRM の保護を制御するためのコンテンツ管理ツールが必要です。 DRM 暗号化への入力には次のものが含まれます。

* MBR ビデオ コンテンツ
* コンテンツ キー
* ライセンス取得 URL

再生時のフローの概要は次のとおりです。

* ユーザーが認証されます。
* 承認トークンがユーザーに対して作成されます。
* DRM で保護されたコンテンツ (マニフェスト) がプレーヤーにダウンロードされます。
* プレーヤーが、キー ID および承認トークンと共にライセンス取得要求をライセンス サーバーに送信します。

次のセクションでは、キー管理の設計について説明します。

| **コンテンツ キー対資産** | **シナリオ** |
| --- | --- |
| 1 対 1 |最も簡単なケースです。 最も細かい制御を提供します。 ただし、一般に、ライセンス配信コストは最も高くなります。 保護された資産ごとに少なくとも 1 つのライセンス要求が必要です。 |
| 1 対多 |複数の資産に対して同じコンテンツ キーを使用できます。 たとえば、ジャンルやジャンルのサブセット (つまり Movie Gene) などの論理グループのすべての資産が、1 つのコンテンツ キーを使用します。 |
| 多対 1 |各資産に複数のコンテンツ キーが必要です。 <br/><br/>たとえば、マルチ DRM の動的 CENC 保護を MPEG-DASH に対して適用し、動的 AES-128 暗号化を HLS に対して適用する必要がある場合、2 つのコンテンツ キーが必要です。 各コンテンツ キーに固有の ContentKeyType が必要です (動的 CENC 保護に使われるコンテンツ キーには ContentKeyType.CommonEncryption を使い、 動的 AES-128 暗号化に使われるコンテンツ キーには ContentKeyType.EnvelopeEncryption を使います)。<br/><br/>もう 1 つの例として、DASH コンテンツの CENC 保護では、理論上は、あるコンテンツ キーを使ってビデオ ストリームを保護し、別のコンテンツ キーでオーディオ ストリームを保護できます。 |
| 多対多 |前の 2 つのシナリオの組み合わせです。 コンテンツ キーの 1 つのセットが、同じ資産グループの複数の資産のそれぞれに使われます。 |

考慮すべきもう 1 つの重要な要素は、永続的ライセンスと非永続的ライセンスの使用です。

これらの考慮事項は、

ライセンス配信にパブリック クラウドを使用する場合のライセンス配信コストに直接影響します。 次の 2 つの異なる設計でそれを示します。

* 月単位のサブスクリプション:永続的ライセンスを使用し、コンテンツ キーと資産のマッピングは 1 対多です。 たとえば、すべての子供向けムービーの暗号化に 1 つのコンテンツ キーを使用します。 この場合、次のようになります。

    すべての子供向けムービーに対して要求されるデバイス当たりのライセンス合計数 = 1

* 月単位のサブスクリプション:非永続的ライセンスを使用し、コンテンツ キーと資産のマッピングは 1 対 1 です。 この場合、次のようになります。

    すべての子供向けムービーに対して要求されるデバイス当たりのライセンス合計数 = (視聴ムービー数) × (セッション数)

2 つの異なる設計は、非常に異なるライセンス要求のパターンになります。 ライセンス配信サービスが Media Services などのパブリック クラウドで提供される場合、パターンが異なると、ライセンス配信コストも異なるものになります。

## <a name="map-design-to-technology-for-implementation"></a>実装テクノロジへの設計のマッピング
次に、各構成要素に対して使用するテクノロジを指定することにより、汎用的な設計を、Azure/Media Services プラットフォームでのテクノロジにマップします。

そのマッピングを次の表に示します。

| **構成要素** | **テクノロジ** |
| --- | --- |
| **プレーヤー** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **ID プロバイダー (IDP)** |Azure Active Directory (Azure AD) |
| **Secure Token Service (STS)** |Azure AD |
| **DRM 保護ワークフロー** |Azure Media Services の動的保護 |
| **DRM ライセンス配信** |* Media Services のライセンス配信 (PlayReady、Widevine、FairPlay) <br/>* Axinom ライセンス サーバー <br/>* カスタム PlayReady ライセンス サーバー |
| **元のドメイン** |Azure Media Services ストリーミング エンドポイント |
| **キー管理** |参照実装には必要ありません |
| **コンテンツ管理** |C# コンソール アプリケーション |

つまり、IDP と STS の両方が Azure AD によって提供されます。 プレーヤーには [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) が使われます。 Azure Media Services と Azure Media Player のいずれにおいても、DASH 対応の CENC、HLS 対応の FairPlay、スムーズ ストリーミング対応の PlayReady、およびDASH、HLS、スムーズ対応の AES-128 暗号化がサポートされます。

次の図では、このテクノロジ マッピングでの全体的な構造とフローを示します。

![Media Services での CENC](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

DRM コンテンツ保護を設定するため、コンテンツ管理ツールは次の入力を使います。

* オープン コンテンツ
* キー管理からのコンテンツ キー
* ライセンス取得 URL
* Azure AD からの情報の一覧 (対象ユーザー、発行者、トークン要求など)

コンテンツ管理ツールの出力は次のとおりです。

* ContentKeyPolicy: 使用されている各種 DRM に対応した DRM ライセンス テンプレートを示します
* ContentKeyPolicyRestriction: DRM ライセンスが発行される前のアクセス制御を示します
* Streamingpolicy: ストリーミングに使用される DRM のさまざまな組み合わせを示します (暗号化モード - ストリーミング プロトコル - コンテナー形式)
* StreamingLocator: 暗号化に使用されるコンテンツ キー/IV と、ストリーミング URL を示します 

実行時のフローを次に示します。

* ユーザーが認証されると、JWT が生成されます。
* JWT に含まれる要求の 1 つは、グループ オブジェクト ID EntitledUserGroup を含むグループ要求です。 この要求は権利チェックを渡すために使われます。
* プレーヤーは CENC で保護されたコンテンツのクライアント マニフェストをダウンロードして、以下を識別します。
   * キー ID。
   * コンテンツが DRM で保護されていること。
   * ライセンス取得 URL
* プレーヤーは、サポートされている DRM/ブラウザーに基づいてライセンス取得要求を作成します。 ライセンス取得要求では、キー ID と JWT も送信されます。 ライセンス配信サービスは、含まれる JWT と要求を確認した後、必要なライセンスを発行します。

## <a name="implementation"></a>実装
### <a name="implementation-procedures"></a>実装手順
実装の手順は次のとおりです。

1. テスト資産を準備します。 テスト ビデオを Media Services のマルチビットレートの Fragmented MP4 にエンコード/パッケージ化します。 この資産は、DRM では保護され "*ません*"。 DRM 保護は、後で動的保護によって行われます。

2. キー ID および (必要に応じてキー シードから) コンテンツ キーを作成します。 この例では、2 つのテスト資産のためにただ 1 つのキー ID とコンテンツ キーを使うので、キー管理システムは必要ありません。

3. Media Services API を使って、テスト資産用のマルチ DRM ライセンス配信サービスを構成します。 Media Services のライセンス サービスではなく、会社または会社のベンダーによるカスタム ライセンス サーバーを使用している場合は、このステップを省略できます。 ライセンス配信を構成するステップで、ライセンス取得 URL を指定できます。 承認ポリシーの制限、異なる DRM ライセンス サービスのライセンス応答テンプレートなど、一部の詳細構成を指定するには Media Services API が必要です。 現在の Azure Portal では、この構成に必要な UI は提供されていません。 API レベルの情報とサンプル コードについては、「[PlayReady または Widevine の動的共通暗号化を使用する](protect-with-drm.md)」をご覧ください。

4. Media Services API を使って、テスト資産の資産配信ポリシーを構成します。 API レベルの情報とサンプル コードについては、「[PlayReady または Widevine の動的共通暗号化を使用する](protect-with-drm.md)」をご覧ください。

5. Azure で Azure AD テナントを作成して構成します。

6. Azure AD テナントでユーザー アカウントとグループをいくつか作成します。 少なくとも、"Entitled User" グループを作成し、このグループにユーザーを追加する必要があります。 このグループのユーザーは、ライセンス取得での権利チェックに合格します。 このグループに属さないユーザーは認証チェックに合格せず、ライセンスを取得できません。 この "Entitled User" グループのメンバーシップは、Azure AD によって発行される JWT において必要なグループ要求です。 マルチ DRM ライセンス配信サービスを構成するステップで、この要求要件を指定します。

7. ビデオ プレーヤーをホストするための ASP.NET MVC アプリを作成します。 この ASP.NET アプリは、Azure AD テナントに対するユーザー認証で保護されます。 ユーザー認証後に取得されるアクセス トークンには、適切な要求が含まれます。 このステップには、OpenID Connect API を使うことをお勧めします。 次の NuGet パッケージをインストールします。

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) を使ってプレーヤーを作成します。 異なる DRM プラットフォームで使う DRM テクノロジを指定するには、[Azure Media Player の ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) を使います。

9. テスト マトリックスを次の表に示します。

    | **DRM** | **ブラウザー** | **権利のあるユーザーの結果** | **権利のないユーザーの結果** |
    | --- | --- | --- | --- |
    | **PlayReady** |Windows 10 の Microsoft Edge または Internet Explorer 11 |合格 |不合格 |
    | **Widevine** |Chrome、Firefox、Opera |合格 |不合格 |
    | **FairPlay** |macOS 上の Safari      |合格 |不合格 |
    | **AES-128** |最新のブラウザー  |合格 |不合格 |

ASP.NET MVC プレーヤー アプリ用に Azure AD をセットアップする方法については、「[Azure Media Services OWIN MVC ベースのアプリを Azure Active Directory と統合し、JWT 要求に基づいてコンテンツ キーの配信を制限する](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)」をご覧ください。

詳しくは、「[JWT token Authentication in Azure Media Services and Dynamic Encryption](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)」(Azure Media Services と動的暗号化における JWT トークン認証) をご覧ください。  

Azure AD に関する情報:

* 開発者向けの情報については、「[開発者のための Azure Active Directory](../../active-directory/develop/v1-overview.md)」をご覧ください。
* 管理者向けの情報については、「[Azure AD ディレクトリの管理](../../active-directory/fundamentals/active-directory-administer.md)」をご覧ください。

### <a name="some-issues-in-implementation"></a>実装での問題

実装の問題については、以下のトラブルシューティング情報を参考にしてください。

* 発行者 URL は "/" で終わっている必要があります。 対象ユーザーは、プレーヤー アプリケーション クライアント ID である必要があります。 また、発行者 URL の末尾に "/" を追加します。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    [JWT デコーダー](http://jwt.calebb.net/)では、JWT の **aud** と **iss** が次のように表示されます。

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* アプリケーションの **[構成]** タブで、Azure AD のアプリケーションへのアクセス許可を追加します。 アクセス許可は、ローカル バージョンとデプロイ バージョン両方のアプリケーションごとに必要です。

    ![アクセス許可](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* 動的 CENC 保護を設定するときは、正しい発行者を使う必要があります。

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    次のように指定すると動作しません。

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID は Azure AD テナント ID です。 GUID は、Azure Portal の **[エンドポイント]** ポップアップ メニューで確認できます。

* グループ メンバーシップ要求特権を付与します。 Azure AD アプリケーション マニフェスト ファイルに次の指定が含まれることを確認します。 

    "groupMembershipClaims":"All"    (既定値は null)

* 制限要件を作成するときに、適切な TokenType を設定します。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    SWT (ACS) に加えて JWT (Azure AD) のサポートを追加するので、既定の TokenType は TokenType.JWT です。 SWT/ACS を使う場合は、トークンを TokenType.SWT に設定する必要があります。

## <a name="the-completed-system-and-test"></a>完成したシステムとテスト

ここでは、読者がサインイン アカウントを取得する前にシステムの基本的な動作を理解できるように、完成したエンド ツー エンド システムについて以下のシナリオを説明します。

* 統合されていないシナリオが必要な場合:

    * Media Services でホストされているビデオ資産が、保護されていない場合、または DRM で保護されていてもトークン認証がない (要求するすべてのユーザーにライセンスを発行する) 場合は、サインインなしでテストできます。 ビデオ ストリーミングが HTTP 経由の場合は、HTTP に切り替えます。

* エンド ツー エンドの統合シナリオが必要な場合:

    * ビデオ資産が Media Services による動的な DRM 保護下にあり、トークン認証と、Azure AD によって生成された JWT を使っている場合は、サインインが必要です。

プレーヤー Web アプリケーションとそのサインインについては、[こちらの Web サイト](https://openidconnectweb.azurewebsites.net/)をご覧ください。

### <a name="user-sign-in"></a>ユーザーのサインイン
エンド ツー エンドの統合 DRM システムをテストするには、アカウントを作成または追加する必要があります。

どのようなアカウントでしょうか。

もともと Azure では Microsoft アカウント ユーザーにのみアクセスが許可されていましたが、現在では両方のシステムのユーザーがアクセスできます。 すべての Azure プロパティが認証で Azure AD を信頼し、Azure AD は組織のユーザーを認証するようになっています。 フェデレーション関係が作成され、Azure AD は Microsoft アカウント コンシューマー ID システムを信頼して、コンシューマー ユーザーを認証します。 その結果、Azure AD はゲスト Microsoft アカウントだけでなく、ネイティブ Azure AD アカウントも認証できます。

Azure AD は Microsoft アカウント ドメインを信頼するので、次のどのドメインのアカウントでもカスタム Azure AD テナントに追加し、そのアカウントを使ってサインインできます。

| **ドメイン名** | **ドメイン** |
| --- | --- |
| **カスタム Azure AD テナント ドメイン** |somename.onmicrosoft.com |
| **企業ドメイン** |microsoft.com |
| **Microsoft アカウント ドメイン** |outlook.com、live.com、hotmail.com |

いずれかの著者に連絡していただけば、アカウントを作成し、追加してさしあげます。

異なるドメイン アカウントで使われるサインイン ページのスクリーンショットを以下に示します。

**カスタム Azure AD テナント ドメインのアカウント**:カスタム Azure AD テナント ドメインのカスタマイズされたサインイン ページ。

![カスタム Azure AD テナント ドメインのアカウント 1](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**スマート カードを使用する Microsoft ドメインのアカウント**:2 要素認証を使用する Microsoft 企業 IT によってカスタマイズされたサインイン ページ。

![カスタム Azure AD テナント ドメインのアカウント 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft アカウント**:Microsoft アカウントのコンシューマー向けサインイン ページ。

![カスタム Azure AD テナント ドメインのアカウント 3](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>PlayReady に対する Encrypted Media Extensions の使用

Windows 8.1 での Internet Explorer 11 以降や、Windows 10 での Microsoft Edge ブラウザーなど、Encrypted Media Extensions (EME)/PlayReady をサポートする最新のブラウザーでは、PlayReady が EME の基になる DRM です。

![PlayReady に対する EME の使用](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

プレーヤー領域が暗くなっているのは、PlayReady の保護機能が、保護されたビデオのスクリーン キャプチャを防いでいるためです。

次のスクリーンショットでは、プレーヤーのプラグインと Microsoft Security Essentials (MSE)/EME のサポートを示します。

![PlayReady 用のプレーヤー プラグイン](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Windows 10 の Microsoft Edge や Internet Explorer 11 の EME は、[PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) を対応する Windows 10 デバイス上で呼び出すことができます。 PlayReady SL3000 により、拡張プレミアム コンテンツ (4K、HDR) の配信や新しいコンテンツ配信モデル (拡張コンテンツの場合) の利用が可能になります。

Windows デバイスにフォーカスするには、PlayReady が Windows デバイスで利用可能なハードウェアで唯一の DRM です (PlayReady SL3000)。 ストリーミング サービスは EME またはユニバーサル Windows プラットフォーム アプリケーションを介して PlayReady を使用でき、PlayReady SL3000 を使って他の DRM よりも高い画質を提供することができます。 通常、2K までのコンテンツは Chrome または Firefox に配信され、4K までのコンテンツは同じデバイス上の Microsoft Edge/Internet Explorer 11 またはユニバーサル Windows プラットフォーム アプリケーションに配信されます。 量は、サービスの設定と実装に依存します。

#### <a name="use-eme-for-widevine"></a>Widevine に対する EME の使用

Windows 10、Windows 8.1、Mac OSX Yosemite の Chrome 41 以降や、Android 4.4.4 の Chrome など、EME/Widevine をサポートする最新のブラウザーでは、Google Widevine が EME の背後にある DRM です。

![Widevine に対する EME の使用](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine では、保護されたビデオのスクリーン キャプチャが防止されません。

![Widevine 用のプレーヤー プラグイン](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>FairPlay に対する EME の使用

同様に、macOS や iOS 11.2 以降の Safari でも、FairPlay で保護されたコンテンツをこのテスト プレーヤーでテストできます。

その場合は、protectionInfo.type として "FairPlay" を指定し、FPS AC Path (FairPlay ストリーミング アプリケーション証明書パス) にアプリケーション証明書の適切な URL を入力します。

### <a name="unentitled-users"></a>権利のないユーザー

ユーザーが "Entitled Users" グループのメンバーではない場合、ユーザーは権利チェックにパスしません。 次に示すように、マルチ DRM ライセンス サービスは要求されたライセンスの発行を拒否します。 詳細な説明は "ライセンスを取得できませんでした" という内容であり、これは設計どおりです。

![権利のないユーザー](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>カスタム セキュリティ トークン サービスの実行

カスタム STS を実行する場合、JWT は、対称キーまたは非対称キーを使ってカスタム STS により発行されます。

次のスクリーンショットは、対称キーを使ったシナリオのものです (Chrome を使用)。

![対称キーを使ったカスタム STS](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

次のスクリーンショットは、X509 証明書で非対称キーを使うシナリオのものです (Microsoft の最新ブラウザーを使用)。

![非対称キーを使ったカスタム STS](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

どちらの場合も、ユーザー認証は同じです。 Azure AD によって行われます。 唯一の違いは、JWT が Azure AD ではなくカスタム STS によって発行されることです。 動的 CENC 保護を構成するときは、ライセンス配信サービスの制限で JWT の種類として対称キーまたは非対称キーが指定されます。

## <a name="next-steps"></a>次の手順

* [よく寄せられる質問](frequently-asked-questions.md)
* [コンテンツ保護の概要](content-protection-overview.md)
* [DRM によるコンテンツの保護](protect-with-drm.md)
