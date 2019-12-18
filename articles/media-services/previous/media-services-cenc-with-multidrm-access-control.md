---
title: Azure Media Services のアクセス制御を使用したコンテンツ保護システムの設計 | Microsoft Docs
description: Microsoft Smooth Streaming Client Porting Kit のライセンスを取得する方法について説明します。
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: b0fec44a59bd70c6f1d0236861d93e81aaba033c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969447"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Azure Media Services のアクセス制御を使用したコンテンツ保護システムの設計 

## <a name="overview"></a>概要

Over-the-Top (OTT) 用またはオンライン ストリーミング ソリューション用のデジタル著作権管理 (DRM) サブシステムの設計と構築は、複雑なタスクです。 通常、運営会社/オンライン ビデオ プロバイダーはこのタスクを専門の DRM サービス プロバイダーに外部委託します。 このドキュメントでは、OTT またはオンライン ストリーミング ソリューションでのエンド ツー エンドの DRM サブシステムの参照設計と実装について説明します。

このドキュメントの対象読者は、OTT またはオンライン ストリーミング/マルチスクリーン ソリューションの DRM サブシステムに関する作業を行っているエンジニア、または DRM サブシステムに興味のあるすべての読者です。 前提として、読者は、PlayReady、Widevine、FairPlay、Adobe Access など、市販されている DRM テクノロジの少なくとも 1 つには精通している必要があります。

ここでは、マルチ DRM での共通暗号化 (CENC) についても説明します。 オンライン ストリーミングと OTT 業界での大きなトレンドは、さまざまなクライアント プラットフォームのマルチ ネイティブ DRM で CENC を使う方法です。 その前は、さまざまなクライアント プラットフォームに 1 つの DRM とそのクライアント SDK を使う方法がトレンドでした。 マルチ ネイティブ DRM で CENC を使うと、PlayReady と Widevine はいずれも [共通暗号化 (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 仕様に従って暗号化されます。

マルチ DRM での CENC の利点は次のとおりです。

* さまざまなプラットフォームとそのネイティブ DRM に対して単一の暗号化処理が使用されるので、暗号化のコストが減ります。
* 必要な暗号化資産のコピーが 1 つだけで済むので、暗号化資産の管理コストが減ります。
* ネイティブ DRM クライアントはネイティブ プラットフォームでは通常無料なので、DRM クライアント ライセンスのコストがかかりません。

Microsoft は、他の主要企業と共に DASH および CENC を積極的に後援しています。 Azure Media Services は、DASH と CENC をサポートしています。 最近のお知らせについては、次のブログをご覧ください。

*  [Azure Media Services での Google Widevine ライセンス配信サービスのお知らせ](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services はマルチ DRM ストリームを配信するための Google Widevine パッケージを追加します](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>この記事の目標

この記事の目標を以下に示します。

* マルチ DRM で CENC を使う DRM サブシステムの参照設計を提供します。
* Azure/Media Services プラットフォームでの参照実装を提供します。
* 設計と実装に関するいくつかのトピックについて説明します。

この記事では、"マルチ DRM" という用語には次の製品が含まれます。

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

次の表は、各 DRM でサポートされるネイティブ プラットフォーム/ネイティブ アプリおよびブラウザーをまとめたものです。

| **クライアント プラットフォーム** | **ネイティブ DRM のサポート** | **ブラウザー/アプリ** | **ストリーミング形式** |
| --- | --- | --- | --- |
| **スマート TV、オペレーター STB、OTT STB** |PlayReady (プライマリ)、Widevine、その他 |Linux、Opera、WebKit、その他 |各種形式 |
| **Windows 10 デバイス (Windows PC、Windows タブレット、Windows Phone、Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>ユニバーサル Windows プラットフォーム |DASH (HLS の場合 PlayReady は非対応)<br/><br/>DASH、Smooth Streaming (HLS の場合 PlayReady は非対応) |
| **Android デバイス (電話、タブレット、TV)** |Widevine |Chrome/EME |DASH、HLS |
| **iOS (iPhone、iPad)、OS X クライアント、Apple TV** |FairPlay |Safari 8+/EME |HLS |

各 DRM の現在のデプロイメント状態を考慮すると、サービスでは通常 2 ～ 3 種類の DRM を実装し、すべての種類のエンドポイントに最適な方法で対応できるようにする必要があります。

さまざまなクライアントで一定レベルのユーザー エクスペリエンスを実現するには、サービス ロジックの複雑さとクライアント側での複雑さの間にトレードオフが発生します。

選択するときは、次の点に注意してください。

* PlayReady はすべての Windows デバイスと一部の Android デバイスにネイティブ実装され、事実上すべてのプラットフォームでソフトウェア SDK を介して利用できます。
* Widevine はすべての Android デバイス、Chrome、その他の一部のデバイスにネイティブ実装されます。
* FairPlay は iOS と Mac OS クライアント、または iTunes を介してのみ使用できます。

一般的なマルチ DRM には 2 つのオプションがあります。

* PlayReady、Widevine
* PlayReady、Widevine、FairPlay

## <a name="a-reference-design"></a>参照設計
このセクションでは、実装に使われるテクノロジに依存しない参照設計を示します。

DRM サブシステムに含まれる可能性のあるコンポーネントは次のとおりです。

* キー管理
* DRM パッケージ化
* DRM ライセンス配信
* 権利チェック
* 認証/承認
* プレーヤー
* 配信元/コンテンツ配信ネットワーク (CDN)

次の図では、DRM サブシステムのコンポーネント間の相互関係の概要を示します。

![CENC を使用する DRM サブシステム](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **セキュリティ トークン サービス (STS)** |Azure AD |
| **DRM 保護ワークフロー** |Media Services の動的保護 |
| **DRM ライセンス配信** |* Media Services のライセンス配信 (PlayReady、Widevine、FairPlay) <br/>* Axinom ライセンス サーバー <br/>* カスタム PlayReady ライセンス サーバー |
| **元のドメイン** |Media Services ストリーミング エンドポイント |
| **キー管理** |参照実装には必要ありません |
| **コンテンツ管理** |C# コンソール アプリケーション |

つまり、IDP と STS の両方が Azure AD で使われます。 プレーヤーには [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) が使われます。 Media Services と Media Player はどちらも、マルチ DRM での DASH および CENC をサポートします。

次の図では、このテクノロジ マッピングでの全体的な構造とフローを示します。

![Media Services での CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

動的 CENC 暗号化を設定するため、コンテンツ管理ツールは次の入力を使います。

* オープン コンテンツ
* キー生成/管理からのコンテンツ キー
* ライセンス取得 URL
* Azure AD からの情報のリスト

コンテンツ管理ツールの出力は次のとおりです。

* ContentKeyAuthorizationPolicy には、ライセンス配信が JSON Web トークン (JWT) を検証する方法についての仕様および DRM ライセンス仕様が含まれます。
* AssetDeliveryPolicy には、ストリーミング形式、DRM 保護、およびライセンス取得 URL についての仕様が含まれます。

実行時のフローを次に示します。

* ユーザーが認証されると、JWT が生成されます。
* JWT に含まれる要求の 1 つは、グループ オブジェクト ID EntitledUserGroup を含むグループ要求です。 この要求は権利チェックを渡すために使われます。
* プレーヤーは CENC で保護されたコンテンツのクライアント マニフェストをダウンロードして、以下を識別します。
   * キー ID。
   * コンテンツが CENC で保護されていること。
   * ライセンス取得 URL
* プレーヤーは、サポートされている DRM/ブラウザーに基づいてライセンス取得要求を作成します。 ライセンス取得要求では、キー ID と JWT も送信されます。 ライセンス配信サービスは、含まれる JWT と要求を確認した後、必要なライセンスを発行します。

## <a name="implementation"></a>実装
### <a name="implementation-procedures"></a>実装手順
実装の手順は次のとおりです。

1. テスト資産を準備します。 テスト ビデオを Media Services のマルチビットレートの Fragmented MP4 にエンコード/パッケージ化します。 この資産は、DRM では保護され "*ません*"。 DRM 保護は、後で動的保護によって行われます。

2. キー ID および (必要に応じてキー シードから) コンテンツ キーを作成します。 この例では、2 つのテスト資産のためにただ 1 つのキー ID とコンテンツ キーを使うので、キー管理システムは必要ありません。

3. Media Services API を使って、テスト資産用のマルチ DRM ライセンス配信サービスを構成します。 Media Services のライセンス サービスではなく、会社または会社のベンダーによるカスタム ライセンス サーバーを使用している場合は、このステップを省略できます。 ライセンス配信を構成するステップで、ライセンス取得 URL を指定できます。 承認ポリシーの制限、異なる DRM ライセンス サービスのライセンス応答テンプレートなど、一部の詳細構成を指定するには Media Services API が必要です。 現在の Azure Portal では、この構成に必要な UI は提供されていません。 API レベルの情報とサンプル コードについては、「[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-playready-widevine.md)」をご覧ください。

4. Media Services API を使って、テスト資産の資産配信ポリシーを構成します。 API レベルの情報とサンプル コードについては、「[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-playready-widevine.md)」をご覧ください。

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

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* アプリケーションの **[構成]** タブで、Azure AD のアプリケーションへのアクセス許可を追加します。 アクセス許可は、ローカル バージョンとデプロイ バージョン両方のアプリケーションごとに必要です。

    ![アクセス許可](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

## <a name="additional-topics-for-implementation"></a>実装に関する他のトピック
ここでは、設計と実装に関する他のトピックについて説明します。

### <a name="http-or-https"></a>HTTP か HTTPS か
ASP.NET MVC プレーヤー アプリケーションは、以下をサポートする必要があります。

* Azure AD によるユーザー認証 (HTTPS の場合)。
* クライアントと Azure AD 間での JWT の交換 (HTTPS の場合)。
* クライアントによる DRM ライセンスの取得。ライセンス配信が Media Services によって提供される場合は、HTTPS で必要です。 PlayReady 製品スイートでは、ライセンス配信に HTTPS を必要としません。 PlayReady ライセンス サーバーが Media Services の外部にある場合は、HTTP または HTTPS を使うことができます。

ASP.NET のプレーヤー アプリケーションはベスト プラクティスとして HTTPS を使うので、Media Player はページの HTTPS の下にあります。 ただし、ストリーミングには HTTP が優先されるので、混合コンテンツの問題を考慮する必要があります。

* ブラウザーでは混合コンテンツは許可されません。 ただし、Silverlight や、Smooth および DASH 用の OSMF プラグインでは許可されます。 顧客データが危険にさらされる可能性がある悪意のある JavaScript を挿入する機能の脅威にため、混合コンテンツにはセキュリティ上の問題があります。 ブラウザーは、既定でこの機能をブロックします。 これを回避する唯一の方法は、サーバー (配信元) 側で (HTTPS か HTTP かに関係なく) すべてのドメインを許可することです。 これはおそらくよい方法ではありません。
* 混合コンテンツは避ける必要があります。 プレーヤー アプリケーションと Media Player の両方で、HTTP または HTTPS を使う必要があります。 混合コンテンツを再生するとき、silverlightSS テクノロジは混合コンテンツの警告をクリアする必要があります。 flashSS テクノロジは、混合コンテンツ警告なしで混合コンテンツを処理します。
* 2014 年 8 月より前に作成されたストリーミング エンドポイントは、HTTPS をサポートしません。 その場合は、HTTPS 用に新しいストリーミング エンドポイントを作成して使います。

参照実装では、DRM で保護されたコンテンツに対し、アプリケーションとストリーミングのどちらも HTTPS を使います。 オープン コンテンツの場合、プレーヤーは認証またはライセンスを必要としないので、HTTP でも HTTPS でも使うことができます。

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory の署名キーのロールオーバー
署名キーのロールオーバーは、実装で考慮する必要のある重要なポイントです。 これを無視すると、長くても 6 週間以内に、完成したシステムは最終的に完全に機能を停止します。

Azure AD は、業界標準を使い、それ自体と Azure AD を使うアプリケーションの間の信頼を確立します。 具体的には、Azure AD は公開キーと秘密キーのペアで構成される署名キーを使用します。 Azure AD がユーザーに関する情報を含むセキュリティ トークンを作成するとき、このトークンは、アプリケーションに送信される前に、秘密キーで Azure AD によって署名されます。 トークンが有効であり、Azure AD から発行されたことを確認するには、アプリケーションでトークンの署名を検証する必要があります。 アプリケーションは、テナントのフェデレーション メタデータ ドキュメントに含まれる Azure AD によって公開された公開キーを使います。 この公開キーとその派生元である署名キーは、Azure AD のすべてのテナントに対して同じものが使われます。

Azure AD でのキーのロールオーバーに関して詳しくは、[Azure AD での署名キー ロールオーバーに関する重要な情報](../../active-directory/active-directory-signing-key-rollover.md)のページをご覧ください。

[公開/秘密キーのペア](https://login.microsoftonline.com/common/discovery/keys/)は次のように使われます。

* 秘密キーは、JWT を生成するために Azure AD によって使われます。
* 公開キーは、JWT を検証するために、Media Services の DRM ライセンス配信サービスなどのアプリケーションによって使われます。

セキュリティのため、Azure AD は定期的に (6 週間ごと) 証明書をローテーションします。 セキュリティ侵害が発生した場合、キーのロールオーバーはいつでも行われる可能性があります。 したがって、Media Services のライセンス配信サービスは、Azure AD がキーのペアをローテーションしたら、使われる公開キーを更新する必要があります。 更新しないと、Media Services でのトークン認証は失敗し、ライセンスは発行されません。

このサービスを設定するには、DRM ライセンス配信サービスを構成するときに、TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument を設定します。

JWT の流れを次に示します。

* Azure AD は、認証されたユーザーの現在の秘密キーで JWT を発行します。
* プレーヤーは、マルチ DRM で保護されたコンテンツを含む CENC を認識すると、最初に Azure AD によって発行された JWT を探します。
* プレーヤーは、JWT を含むライセンス取得要求を、Media Services のライセンス配信サービスに送信します。
* Media Services のライセンス配信サービスは、Azure AD からの最新の有効な公開キーを使って JWT を検証した後、ライセンスを発行します。

DRM ライセンス配信サービスは、Azure AD からの最新の有効な公開キーを常にチェックします。 Azure AD によって提示される公開キーは、Azure AD によって発行される JWT の検証に使われます。

Azure AD が JWT を生成した後、プレイヤーが検証のために Media Services の DRM ライセンス配信サービスに JWT を送信する前に、キーのロールオーバーが発生するとどうなるでしょうか。

キーはいつでもロールオーバーされる可能性があるため、フェデレーション メタデータ ドキュメントでは常に複数の有効な公開キーを使用できます。 Media Services のライセンス配信は、ドキュメントで指定されているどのキーでも使うことができます。 これは、1 つのキーがすぐにロールされて別のキーに置き換えられる可能性があるためです。

### <a name="where-is-the-access-token"></a>アクセス トークンの場所
Web アプリが API アプリを呼び出す場合の認証フローは次のようになります (「[アプリケーション ID と OAuth 2.0 クライアント資格情報付与](../../active-directory/develop/web-api.md)」を参照)。

* ユーザーが Web アプリケーションで Azure AD にサインインします。 詳しくは、「[Web ブラウザー対 Web アプリケーション](../../active-directory/develop/web-app.md)」をご覧ください。
* Azure AD 認証エンドポイントは、承認コードを付けてクライアント アプリケーションにユーザー エージェントをリダイレクトします。 ユーザー エージェントは、クライアント アプリケーションのリダイレクト URI に承認コードを返します。
* Web アプリケーションは、Web API に対して認証し、目的のリソースを取得できるように、アクセス トークンを取得する必要があります。 Web アプリケーションは、Azure AD のトークン エンドポイントに要求を送信して、資格情報、クライアント ID、Web API のアプリケーション ID の URI を提供します。 Web アプリケーションは、承認コードを示してユーザーが同意したことを証明します。
* Azure AD がアプリケーションを認証し、Web API の呼び出しに使う JWT アクセス トークンを返します。
* Web アプリケーションは、返された JWT アクセス トークンを HTTPS 経由で使って、Web API への要求の "Authorization" ヘッダーに、"Bearer" を指定した JWT 文字列を追加します。 その後、Web API は JWT を検証します。 検証が正常に行われると、目的のリソースが返されます。

このアプリケーション ID フローでは、Web API は Web アプリケーションがユーザーを認証済みであることを信頼します。 そのため、このパターンは信頼されたサブシステムと呼ばれます。 [承認フローの図](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code)では、承認コード付与フローがどのように動作するかが説明されています。

トークン制限のあるライセンス取得は、同じ信頼されたサブシステムのパターンに従います。 Media Services のライセンス配信サービスは、Web API リソース、つまり Web アプリケーションがアクセスする必要のある "バックエンド リソース" です。 それでは、アクセス トークンはどこにあるのでしょうか。

アクセス トークンは、Azure AD から取得されます。 ユーザー認証が成功すると、承認コードが返されます。 その後、承認コードをクライアント ID およびアプリ キーと共に使って、アクセス トークンに交換します。 アクセス トークンは、Media Services ライセンス配信サービスを指すか表している "ポインター" アプリケーションにアクセスするために使われます。

Azure AD でポインター アプリを登録して構成するには、次の手順のようにします。

1. Azure AD テナントで以下のことを行います。

   * サインオン URL https://<リソース名>.azurewebsites.net/ で、アプリケーション (リソース) を追加します。 
   * URL https://<AAD テナント名>.onmicrosoft.com/<リソース名> で、アプリ ID を追加します。

2. リソース アプリ用の新しいキーを追加します。

3. アプリ マニフェスト ファイルを更新し、groupMembershipClaims プロパティの値を "groupMembershipClaims":"All" に変更します。

4. プレーヤー Web アプリを参照する Azure AD アプリの **[他のアプリケーションに対するアクセス許可]** セクションで、手順 1 で追加したリソース アプリを追加します。 **[デリゲートされたアクセス許可]** で **[<リソース名> へのアクセス]** をオンにします。 このオプションは、リソース アプリにアクセスするアクセス トークンを作成するためのアクセス許可を Web アプリに付与します。 Visual Studio と Azure Web アプリで展開している場合は、Web アプリのローカル バージョンと展開バージョンの両方でこれを行います。

Azure AD によって発行された JWT は、ポインター リソースへのアクセスに使われるアクセス トークンです。

### <a name="what-about-live-streaming"></a>ライブ ストリーミングの場合
上の説明は、オンデマンド資産に注目したものでした。 ライブ ストリーミングの場合

プログラムに関連付けられた資産を VOD 資産として扱うことにより、Media Services のライブ ストリーミングでもまったく同じ設計と実装を使用できます。

具体的には、Media Services でライブ ストリーミングを行うには、チャネルを作成した後、チャネルの下にプログラムを作成する必要があります。 プログラムを作成するには、プログラムのライブ アーカイブを含む資産を作成する必要があります。 CENC にライブ コンテンツのマルチ DRM 保護を提供するには、プログラムを開始する前に、VOD 資産の場合と同じセットアップ/処理を資産に適用します。

### <a name="what-about-license-servers-outside-media-services"></a>Media Services の外部にあるライセンス サーバーの場合
多くの場合、ユーザーは自前のデータ センター内にあるライセンス サーバー ファーム、または DRM サービス プロバイダーによってホストされているライセンス サーバー ファームを運用しています。 Media Services Content Protection の場合、ハイブリッド モードで運用できます。 コンテンツは Media Services でホストして動的に保護することができ、DRM ライセンスは Media Services の外部のサーバーによって配信されます。 この例では、次のように変更することを考えます。

* STS は、ライセンス サーバー ファームによって許容可能で検証できるトークンを発行する必要があります。 たとえば、Axinom によって提供される Widevine ライセンス サーバーには、権利メッセージを含む特定の JWT が必要です。 そのため、STS でそのような JWT を発行する必要があります。 この種の実装については、[Azure ドキュメント センター](https://azure.microsoft.com/documentation/)で「[Axinom を使用して Azure Media Services に Widevine ライセンスを配信する](media-services-axinom-integration.md)」をご覧ください。
* Media Services でライセンス配信サービス (ContentKeyAuthorizationPolicy) を構成する必要はもうありません。 必要なのは、マルチ DRM を使う CENC の設定で AssetDeliveryPolicy を構成するときに、ライセンス取得 URL を (PlayReady、Widevine、FairPlay に) 提供することです。

### <a name="what-if-i-want-to-use-a-custom-sts"></a>カスタム STS を使用する場合
ユーザーは、カスタム STS を使って JWT を提供することがあります。 次のような理由が考えられます。

* ユーザーが利用している IDP が、STS をサポートしていない場合。 この場合は、カスタム STS が選択肢になります。
* ユーザーが、STS とユーザーのサブスクライバー請求システムの統合において、より柔軟性の高い、または厳密な制御を必要とする場合。 たとえば、MVPD 運営会社は、プレミアム、ベーシック スポーツなど、複数の OTT サブスクライバー パッケージを提供する場合があります。 運営会社は、特定のパッケージのコンテンツだけが利用可能になるように、トークンの要求をサブスクライバーのパッケージと一致させる必要があります。 この場合、カスタム STS は必要な柔軟性と制御を提供します。

カスタム STS を使うときは、2 つの変更を行う必要があります。

* 資産のライセンス配信サービスを構成するときに、Azure AD からの現在のキーではなく、カスタム STS での検証に使うセキュリティ キーを指定する必要があります (詳細は後述)。 
* JTW トークンが生成されるときに、現在の Azure AD での X 509 証明書の秘密キーではなく、セキュリティ キーを指定します。

セキュリティ キーには次の 2 種類があります。

* 対称キー:JWT の生成と検証に同じキーが使われます。
* 非対称キー:X509 証明書の公開/秘密キー ペアの秘密キーが JWT の暗号化/生成に使われ、公開キーがトークンの検証に使われます。

> [!NOTE]
> 開発プラットフォームとして .NET Framework/C# を使う場合、非対称セキュリティ キーに使われる X509 証明書のキーの長さは 2048 ビット以上でなければなりません。 これは、.NET Framework の System.IdentityModel.Tokens.X509AsymmetricSecurityKey クラスの要件です。 そうでない場合は、次の例外がスローされます。
> 
> IDX10630:署名の "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" は "2048" ビット以上でなければなりません。

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

![カスタム Azure AD テナント ドメインのアカウント](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**スマート カードを使用する Microsoft ドメインのアカウント**:2 要素認証を使用する Microsoft 企業 IT によってカスタマイズされたサインイン ページ。

![カスタム Azure AD テナント ドメインのアカウント](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft アカウント**:Microsoft アカウントのコンシューマー向けサインイン ページ。

![カスタム Azure AD テナント ドメインのアカウント](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>PlayReady に対する Encrypted Media Extensions の使用
Windows 8.1 での Internet Explorer 11 以降や、Windows 10 での Microsoft Edge ブラウザーなど、Encrypted Media Extensions (EME)/PlayReady をサポートする最新のブラウザーでは、PlayReady が EME の基になる DRM です。

![PlayReady に対する EME の使用](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

プレーヤー領域が暗くなっているのは、PlayReady の保護機能が、保護されたビデオのスクリーン キャプチャを防いでいるためです。

次のスクリーンショットでは、プレーヤーのプラグインと Microsoft Security Essentials (MSE)/EME のサポートを示します。

![PlayReady 用のプレーヤー プラグイン](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10 の Microsoft Edge や Internet Explorer 11 の EME は、[PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) を対応する Windows 10 デバイス上で呼び出すことができます。 PlayReady SL3000 により、拡張プレミアム コンテンツ (4K、HDR) の配信や新しいコンテンツ配信モデル (拡張コンテンツの場合) の利用が可能になります。

Windows デバイスにフォーカスするには、PlayReady が Windows デバイスで利用可能なハードウェアで唯一の DRM です (PlayReady SL3000)。 ストリーミング サービスは EME またはユニバーサル Windows プラットフォーム アプリケーションを介して PlayReady を使用でき、PlayReady SL3000 を使って他の DRM よりも高い画質を提供することができます。 通常、2K までのコンテンツは Chrome または Firefox に配信され、4K までのコンテンツは同じデバイス上の Microsoft Edge/Internet Explorer 11 またはユニバーサル Windows プラットフォーム アプリケーションに配信されます。 量は、サービスの設定と実装に依存します。

#### <a name="use-eme-for-widevine"></a>Widevine に対する EME の使用
Windows 10、Windows 8.1、Mac OSX Yosemite の Chrome 41 以降や、Android 4.4.4 の Chrome など、EME/Widevine をサポートする最新のブラウザーでは、Google Widevine が EME の背後にある DRM です。

![Widevine に対する EME の使用](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine では、保護されたビデオのスクリーン キャプチャが防止されません。

![Widevine 用のプレーヤー プラグイン](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>権利のないユーザー
ユーザーが "Entitled Users" グループのメンバーではない場合、ユーザーは権利チェックにパスしません。 次に示すように、マルチ DRM ライセンス サービスは要求されたライセンスの発行を拒否します。 詳細な説明は "ライセンスを取得できませんでした" という内容であり、これは設計どおりです。

![権利のないユーザー](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>カスタム セキュリティ トークン サービスの実行
カスタム STS を実行する場合、JWT は、対称キーまたは非対称キーを使ってカスタム STS により発行されます。

次のスクリーンショットは、対称キーを使ったシナリオのものです (Chrome を使用)。

![対称キーを使ったカスタム STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

次のスクリーンショットは、X509 証明書で非対称キーを使うシナリオのものです (Microsoft の最新ブラウザーを使用)。

![非対称キーを使ったカスタム STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

どちらの場合も、ユーザー認証は同じです。 Azure AD によって行われます。 唯一の違いは、JWT が Azure AD ではなくカスタム STS によって発行されることです。 動的 CENC 保護を構成するときは、ライセンス配信サービスの制限で JWT の種類として対称キーまたは非対称キーが指定されます。

## <a name="summary"></a>まとめ

このドキュメントでは、トークン認証によるマルチ ネイティブ DRM およびアクセス制御を使用する CENC、Azure を使用したその設計と実装、Media Services、Media Player について説明しました。

* DRM/CENC サブシステムのすべての必要なコンポーネントを含む参照設計を示しました。
* Azure、Media Services、Media Player での参照実装を示しました。
* 設計と実装に直接関係するいくつかのトピックについても説明しました。

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. のサービス使用条件とプライバシー ポリシーが適用されます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
