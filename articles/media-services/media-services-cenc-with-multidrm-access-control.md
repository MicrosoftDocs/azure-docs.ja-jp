---
title: "CENC とマルチ DRM および Access Control: Azure および Azure Media Services での参照設計と実装 | Microsoft Docs"
description: "Microsoft® Smooth Streaming Client Porting Kit のライセンスを取得する方法について説明します。"
services: media-services
documentationcenter: 
author: willzhan
manager: erikre
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2016
ms.author: willzhan;kilroyh;yanmf;juliako
translationtype: Human Translation
ms.sourcegitcommit: e65393c9582056f84530a32804e0d82fd451b688
ms.openlocfilehash: 1ea286a04c84d031fcefa8dc771cbdef9d8a9b72
ms.lasthandoff: 01/18/2017


---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC とマルチ DRM および Access Control: Azure および Azure Media Services での参照設計と実装
 
## <a name="introduction"></a>はじめに
OTT またはオンライン ストリーミング ソリューション用の DRM サブシステムを設計および構築するのは複雑な作業であることはよく知られています。 そして、運営会社/オンライン ビデオ プロバイダーがこの部分を専門の DRM サービス プロバイダーに外部委託するのはよくあることです。 このドキュメントでは、OTT またはオンライン ストリーミング ソリューションでのエンド ツー エンドの DRM サブシステムの参照設計と実装について説明します。

このドキュメントの対象読者は、OTT またはオンライン ストリーミング/マルチスクリーン ソリューションの DRM サブシステムに関する作業を行っているエンジニア、または DRM サブシステムに興味のあるすべての読者です。 前提として、読者は、PlayReady、Widevine、FairPlay、Adobe Access など、市販されている DRM テクノロジの少なくとも&1; つには精通している必要があります。

DRM には、マルチ DRM での CENC (共通暗号化) も含まれます。 オンライン ストリーミングおよび OTT 業界での大きなトレンドは、従来のさまざまなクライアント プラットフォームに単一の DRM とそのクライアント SDK を使用する方法から、さまざまなクライアント プラットフォームで CENC とマルチ ネイティブ DRM を使用する方法に変化しています。 マルチ ネイティブ DRM で CENC を使用すると、PlayReady と Widevine はいずれも [共通暗号化 (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 仕様に従って暗号化されます。

マルチ DRM での CENC の利点は次のとおりです。

1. さまざまなプラットフォームとそのネイティブ DRM に対して単一の暗号化処理が使用されるので、暗号化のコストが減ります。
2. 必要な暗号化資産のコピーが&1; つだけで済むので、暗号化資産の管理コストが減ります。
3. ネイティブ DRM クライアントはネイティブ プラットフォームでは通常無料なので、DRM クライアント ライセンスのコストがかかりません。

Microsoft は、他の主要企業と共に DASH および CENC を積極的にプロモートしてきました。 Microsoft Azure Media Services は、DASH と CENC をサポートしています。 最近の発表については、Mingfei のブログ「[Announcing Google Widevine license delivery services in Azure Media Services  (Azure Media Services での Google Widevine ライセンス配信サービスのご案内)](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)」および「 [Azure Media Services adds Google Widevine packaging for delivering multi-DRM stream (Azure Media Services によるマルチ DRM ストリーム配信用 Google Widevine パッケージの追加)](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)」を参照してください。  

### <a name="overview-of-this-article"></a>この記事の概要
この記事の目的は次のとおりです。

1. マルチ DRM で CENC を使用する DRM サブシステムの参照設計を提供します。
2. Microsoft Azure/Azure Media Services プラットフォームでの参照実装を提供します。
3. 設計と実装に関するいくつかのトピックについて説明します。

この記事の「マルチ DRM」には次のものが含まれます。

1. Microsoft PlayReady
2. Google Widevine
3. Apple FairPlay 

次の表は、各 DRM でサポートされるネイティブ プラットフォーム/ネイティブ アプリおよびブラウザーをまとめたものです。

| **クライアント プラットフォーム** | **ネイティブ DRM のサポート** | **ブラウザー/アプリ** | **ストリーミング形式** |
| --- | --- | --- | --- |
| **スマート TV、オペレーター STB、OTT STB** |PlayReady (プライマリ)、Widevine、その他 |Linux、Opera、WebKit、その他 |各種形式 |
| **Windows 10 デバイス (Windows PC、Windows タブレット、Windows Phone、Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>UWP |DASH (HLS の場合 PlayReady は非対応)<br/><br/>DASH、Smooth Streaming (HLS の場合 PlayReady は非対応) |
| **Android デバイス (電話、タブレット、TV)** |Widevine |Chrome/EME |DASH |
| **iOS (iPhone、iPad)、OS X クライアント、Apple TV** |FairPlay |Safari 8+/EME |HLS |


各 DRM の現在のデプロイメント状態を考慮すると、サービスでは通常 2 ～ 3 種類の DRM を実装し、すべての種類のエンドポイントに最適な方法で対応できるようにする必要があります。

さまざまなクライアントで一定レベルのユーザー エクスペリエンスを実現するには、サービス ロジックの複雑さとクライアント側での複雑さの間にトレードオフが発生します。

選択の際には、次の点に注意してください。

* PlayReady はすべての Windows デバイスと一部の Android デバイスにネイティブ実装され、事実上すべてのプラットフォームでソフトウェア SDK を介して利用できます。
* Widevine はすべての Android デバイス、Chrome、その他の一部のデバイスにネイティブ実装されます。
* FairPlay は iOS と Mac OS クライアント、または iTunes を介してのみ使用できます。

そのため、一般的なマルチ DRM は次のようになります。

* オプション 1: PlayReady、Widevine
* オプション 2: PlayReady、Widevine、FairPlay

## <a name="a-reference-design"></a>参照設計
このセクションでは、実装に使用されるテクノロジに依存しない参照設計を示します。

DRM サブシステムに含まれる可能性のあるコンポーネントは次のとおりです。

1. キー管理
2. DRM パッケージ化
3. DRM ライセンス配信
4. 権利チェック
5. 認証/承認
6. プレーヤー
7. 配信元/CDN

次の図では、DRM サブシステムのコンポーネント間の相互関係の概要を示します。

![CENC を使用する DRM サブシステム](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

設計には&3; つの基本的な「階層」があります。

1. バック オフィス階層 (黒): 外部には公開されません。
2. “DMZ” 階層 (青): パブリックに接続するすべてのエンドポイントを含みます。
3. パブリック インターネット階層 (明るい青): CDN およびプレーヤーとパブリック インターネット上のトラフィックを含みます。

静的暗号化か動的暗号化かにかかわらず、DRM の保護を制御するためのコンテンツ管理ツールが必要です。 DRM 暗号化には次のものを提供する必要があります。

1. MBR ビデオ コンテンツ
2. コンテンツ キー
3. ライセンス取得 URL

再生時のフローの概要は次のとおりです。

1. ユーザーが認証されます。
2. 承認トークンがユーザーに対して作成されます。
3. DRM で保護されたコンテンツ (マニフェスト) がプレーヤーにダウンロードされます。
4. プレーヤーが、キー ID および承認トークンと共にライセンス取得要求をライセンス サーバーに送信します。

次のトピックに移る前に、キー管理の設計に関して少し説明します。

| **コンテンツ キー – 資産** | **シナリオ** |
| --- | --- |
| 1 –&1; |最も簡単なケースです。 最も細かい制御を提供します。 ただし、一般に、ライセンス配信コストは最も高くなります。 保護された資産ごとに少なくとも&1; つのライセンス要求が必要です。 |
| 1 – 多 |複数の資産に対して同じコンテンツ キーを使用できます。 たとえば、ジャンルやジャンルのサブセット (つまり Movie Gene) などの論理グループのすべての資産が、1 つのコンテンツ キーを使用します。 |
| 多 –&1; |各資産に複数のコンテンツ キーが必要です。 <br/><br/>たとえば、マルチ DRM の動的 CENC 保護を MPEG-DASH に対して適用し、動的 AES-128 暗号化を HLS に対して適用する必要がある場合、それぞれが固有の ContentKeyType を持つ異なる&2; つのコンテンツ キーが必要です(動的 CENC 保護に使用されるコンテンツ キーには ContentKeyType.CommonEncryption を使用する必要があり、動的 AES-128 暗号化に使用されるコンテンツ キーには ContentKeyType.EnvelopeEncryption を使用する必要があります)。 (動的 CENC 保護に使用するコンテンツ キーには、ContentKeyType.CommonEncryption を使う必要があります。一方、動的 AES-128 暗号化に使用するコンテンツ キーには、ContentKeyType.EnvelopeEncryption を使用する必要があります)。<br/><br/>もう&1; つの例として、DASH コンテンツの CENC 保護では、理論上は、あるコンテンツ キーを使用してビデオ ストリームを保護し、別のコンテンツ キーでオーディオ ストリームを保護できます。 |
| 多 – 多 |上記の&2; つのシナリオの組み合わせです。コンテンツ キーの&1; つのセットが、同じ資産 「グループ」の複数の資産のそれぞれに使用されます。 |

考慮すべきもう&1; つの重要な要素は、永続的ライセンスと非永続的ライセンスの使用です。

これらの考慮事項は、

ライセンス配信にパブリック クラウドを使用する場合のライセンス配信コストに直接影響します。 次の&2; つの異なる設計について考えてみます。

1. 月単位のサブスクリプション: 永続的ライセンスを使用し、コンテンツ キーと資産のマッピングは 1 – 多です。 例: すべての子供向けムービーの暗号化に&1; つのコンテンツ キーを使用します。 この場合、次のようになります。

    すべての子供向けムービーに対して要求されるデバイス当たりのライセンス合計数 = 1
2. 月単位のサブスクリプション: 非永続的ライセンスを使用し、コンテンツ キーと資産のマッピングは 1 – 1 です。 この場合、次のようになります。

    すべての子供向けムービーに対して要求されるデバイス当たりのライセンス合計数 = (視聴ムービー数) × (セッション数)

簡単にわかるように、ライセンス配信サービスが Azure Media Services などのパブリック クラウドで提供される場合、これら&2; 種類の設計でのライセンス要求パターン、したがってライセンス配信コストは、大きく異なるものになります。

## <a name="mapping-design-to-technology-for-implementation"></a>実装テクノロジへの設計のマッピング
次に、各構成要素に対して使用するテクノロジを指定することにより、汎用的な設計を、Microsoft Azure/Azure Media Services プラットフォームでのテクノロジにマップします。

そのマッピングを次の表に示します。

| **構成要素** | **テクノロジ** |
| --- | --- |
| **プレーヤー** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **ID プロバイダー (IDP)** |Azure Active Directory |
| **Secure Token Service (STS)** |Azure Active Directory |
| **DRM 保護ワークフロー** |Azure Media Services の動的保護 |
| **DRM ライセンス配信** |1.Azure Media Services のライセンス配信 (PlayReady、Widevine、FairPlay)、または <br/>2.Axinom ライセンス サーバー、 <br/>3.カスタム PlayReady ライセンス サーバー |
| **元のドメイン** |Azure Media Services ストリーミング エンドポイント |
| **キー管理** |参照実装には必要ありません |
| **コンテンツ管理** |C# コンソール アプリケーション |

つまり、ID プロバイダー (IDP) と Secure Token Service (STS) はどちらも Azure AD です。 プレーヤーについては、 [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/)を使用します。 Azure Media Services と Azure Media Player はどちらも、マルチ DRM での DASH および CENC をサポートします。

次の図では、このテクノロジ マッピングでの全体的な構造とフローを示します。

![AMS での CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

動的 CENC 暗号化を設定するため、コンテンツ管理ツールは次の入力を使用します。

1. オープン コンテンツ
2. キー生成/管理からのコンテンツ キー
3. ライセンス取得 URL
4. Azure AD からの情報のリスト

コンテンツ管理ツールの出力は次のとおりです。

1. ContentKeyAuthorizationPolicy: ライセンス配信が JWT トークンを検証する方法についての仕様および DRM ライセンス仕様を含みます。
2. AssetDeliveryPolicy: ストリーミング形式、DRM 保護、およびライセンス取得 URL についての仕様を含みます。

実行時のフローは次のようになります。

1. ユーザーが認証されると、JWT トークンが生成されます。
2. JWT トークンに含まれる要求の&1; つは、“EntitledUserGroup” のグループ オブジェクト ID を含む "groups" 要求です。 この要求は、「権利チェック」に合格するために使用されます。
3. プレーヤーは CENC で保護されたコンテンツのクライアント マニフェストをダウンロードして、以下を「確認」します。

   1. キー ID
   2. コンテンツが CENC で保護されていること
   3. ライセンス取得 URL
4. プレーヤーは、サポートされている DRM/ブラウザーに基づいてライセンス取得要求を作成します。 ライセンス取得要求では、キー ID と JWT トークンも送信されます。 ライセンス配信サービスは、含まれる JWT トークンと要求を確認した後、必要なライセンスを発行します。

## <a name="implementation"></a>実装
### <a name="implementation-procedures"></a>実装手順
実装には次の手順が含まれます。

1. テスト資産を準備します。テスト ビデオを Azure Media Services のマルチビットレートの Fragmented MP4 にエンコード/パッケージ化します。 この資産は、DRM では保護されません。 DRM 保護は、後で動的保護によって行われます。
2. キー ID および (必要に応じてキー シードから) コンテンツ キーを作成します。 このトピックでは、2 つのテスト資産のために&1; セットのキー ID とコンテンツ キーだけを使用するので、キー管理システムは必要ありません。
3. AMS API を使用して、テスト資産用のマルチ DRM ライセンス配信サービスを構成します。 Azure Media Services のライセンス サービスではなく、会社または会社のベンダーによるカスタム ライセンス サーバーを使用している場合は、この手順を省略し、ライセンス配信を構成するステップでライセンス取得 URL を指定できます。 承認ポリシーの制限、異なる DRM ライセンス サービスのライセンス応答テンプレートなど、一部の詳細構成を指定するには AMS API が必要です。現在の Azure ポータルでは、この構成に必要な UI はまだ提供されていません。 API レベルの情報およびサンプル コードについては、「 [PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)」を参照してください。
4. AMS API を使用して、テスト資産の資産配信ポリシーを構成します。 API レベルの情報およびサンプル コードについては、「 [PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)」を参照してください。
5. Azure で Azure Active Directory テナントを作成して構成します。
6. Azure Active Directory テナントでユーザー アカウントとグループをいくつか作成します。少なくとも、"EntitledUser" グループを作成し、このグループにユーザーを追加する必要があります。 このグループのユーザーはライセンス取得での権利チェックに合格しますが、このグループに属さないユーザーは合格せず、ライセンスを取得できません。 この "EntitledUser" グループのメンバーであることは、Azure AD によって発行される JWT トークンにおいて必要な "groups" 要求です。 この要求要件は、マルチ DRM ライセンス配信サービスを構成するステップで指定する必要があります。
7. ビデオ プレーヤーをホストする ASP.NET MVC アプリを作成します。 この ASP.NET アプリは、Azure Active Directory テナントに対するユーザー認証で保護されます。 ユーザー認証後に取得されるアクセス トークンには、適切な要求が含まれます。 このステップには、OpenID Connect API を使用することが推奨されます。 次の NuGet パッケージをインストールする必要があります。
   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
8. [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/)を使用してプレーヤーを作成します。 [Azure Media Player の ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) を使用すると、異なる DRM プラットフォームで使用する DRM テクノロジを指定できます。
9. テストのマトリックス:

| **DRM** | **ブラウザー** | **権利のあるユーザーの結果** | **権利のないユーザーの結果** |
| --- | --- | --- | --- |
| **PlayReady** |Windows 10 の MS Edge または IE11 |合格 |不合格 |
| **Widevine** |Windows 10 の Chrome |合格 |不合格 |
| **FairPlay** |TBD | | |

ASP.NET MVC プレーヤー アプリ用に Azure Active Directory を設定する詳細な手順については、「 [Azure Media Services OWIN MVC ベースのアプリを Azure Active Directory と統合し、JWT クレームに基づいてコンテンツ キーの配信を制限する](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)」を参照してください。

また、「[JWT token Authentication in Azure Media Services and Dynamic Encryption (Azure Media Services と動的暗号化における JWT トークン認証)](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)」および [Azure AD と Azure Media Services キー配信の統合のサンプル](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/)も参照してください。

Azure Active Directory については以下を参照してください。

* 「 [Azure Active Directory 開発者ガイド](../active-directory/active-directory-developers-guide.md)」: 開発者向けの情報。
* 「 [Azure AD ディレクトリの管理](../active-directory/active-directory-administer.md)」: 管理者向けの情報。

### <a name="some-gotchas-in-implementation"></a>実装に関する注意事項
実装には「注意事項」がいくつかあります。 問題が発生した場合は、以下の「注意事項」をトラブルシューティングの参考にしてください。

1. **Issuer** URL の末尾には **"/"** が必要です。  

    **Audience** はプレーヤー アプリケーションのクライアント ID でなければならず、やはり発行者 URL の末尾に **"/"** を追加する必要があります。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    [JWT デコーダー](http://jwt.calebb.net/)では、JWT トークンの **aud** と **iss** が次のようになっている必要があります。

    ![第&1; の注意事項](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. (アプリケーションの [構成] タブの) AAD でアプリケーションにアクセス許可を追加します。 これは、(ローカル バージョンとデプロイ バージョンの) 各アプリケーションに必要です。

    ![第&2; の注意事項](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. 動的 CENC 保護の設定では適切な発行者を使用します。

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    次の設定では動作しません。

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID は AAD テナント ID です。 GUID は、Azure ポータルの [エンドポイント] ポップアップで確認できます。
4. グループ メンバーシップ要求特権を付与します。 AAD アプリケーション マニフェスト ファイルは次のようになります。

    "groupMembershipClaims": "All" (既定値は null)
5. 制限要件を作成するときに、適切な TokenType を設定します。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    SWT (ACS) に加えて JWT (AAD) のサポートを追加するので、既定の TokenType は TokenType.JWT です。 SWT/ACS を使用する場合は、TokenType.SWT に設定する必要があります。

## <a name="additional-topics-for-implementation"></a>実装に関する他のトピック
次に、設計と実装に関する他のトピックについて説明します。

### <a name="http-or-https"></a>HTTP か HTTPS か
作成した ASP.NET MVC プレーヤー アプリケーションは以下をサポートする必要があります。

1. HTTPS を使用する必要がある、Azure AD によるユーザー認証
2. HTTPS を使用する必要がある、クライアントと Azure AD 間での JWT トークンの交換
3. クライアントによる DRM ライセンスの取得。ライセンス配信が Azure Media Services によって提供される場合は HTTPS の使用が必須です。 PlayReady 製品スイートではライセンス配信に HTTPS を必要としません。 PlayReady ライセンス サーバーが Azure Media Services の外部にある場合は、HTTP か HTTPS のいずれかを使用できます。

したがって、ASP.NET のプレーヤー アプリケーションは、ベスト プラクティスとして HTTPS を使用します。 つまり、Azure Media Player は HTTPS 下のページで提供されます。 ただし、ストリーミングの場合は HTTP が望ましいので、混合コンテンツの問題を考慮する必要があります。

1. ブラウザーでは混合コンテンツは許可されません。 ただし、Silverlight や、Smooth および DASH 用の OSMF プラグインでは許可されます。 混合コンテンツにはセキュリティ上の問題があります。これは、顧客データが危険にさらされる可能性がある悪意のある JS を挿入する機能の脅威によるものです。  ブラウザーは既定でこれをブロックするので、これを回避する唯一の方法は、サーバー (配信元) 側で (https か http かに関係なく) すべてのドメインを許可することです。 これはおそらくよい方法ではありません。
2. 混合コンテンツは避ける必要があります。両方 HTTP を使用するか、両方 HTTPS を使用します。 混合コンテンツを再生するとき、silverlightSS テクノロジは混合コンテンツの警告をクリアする必要があります。 flashSS テクノロジは、混合コンテンツ警告なしで混合コンテンツを処理します。
3. 2014 年 8 月より前に作成されたストリーミング エンドポイントは、HTTPS をサポートしません。 その場合は、HTTPS 用に新しいストリーミング エンドポイントを作成して使用してください。

参照実装では、DRM で保護されたコンテンツに対し、アプリケーションとストリーミングのどちらも HTTTPS を使用します。 オープン コンテンツの場合、プレーヤーは認証またはライセンスを必要としないので、HTTP でも HTTPS でも自由に使用できます。

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory の署名キーのロールオーバー
これは、実装で考慮する必要のある重要なポイントです。 実装でこれを考慮しないと、完成したシステムは最大 6 週間以内に完全に動作を停止します。

Azure AD は、業界標準を使用して、それ自体と Azure AD を使用するアプリケーションの間の信頼を確立します。 具体的には、Azure AD は公開キーと秘密キーのペアで構成される署名キーを使用します。 Azure AD がユーザーに関する情報を含むセキュリティ トークンを作成するとき、このトークンは、アプリケーションに送信される前に、秘密キーを使用して Azure AD によって署名されます。 トークンが有効であり、実際に Azure AD から発行されたことを確認するには、アプリケーションは、テナントのフェデレーション メタデータ ドキュメントに含まれる Azure AD によって公開された公開キーを使用して、トークンの署名を検証する必要があります。 この公開キーとその派生元である署名キーは、Azure AD のすべてのテナントに対して同じものが使用されます。

Azure AD でのキーのロールオーバーに関する詳細については、 [Azure AD での署名キー ロールオーバーの重要な情報](../active-directory/active-directory-signing-key-rollover.md)に関するページをご覧ください。

[公開/秘密キーのペア](https://login.windows.net/common/discovery/keys/)は次のように使用されます。

* 秘密キーは、JWT トークンを生成するために Azure Active Directory によって使用されます。
* 公開キーは、JWT トークンを検証するために、AMS の DRM ライセンス配信サービスなどのアプリケーションによって使用されます。

セキュリティのため、Azure Active Directory はこの証明書を定期的 (6 週間ごと) に変更します。 セキュリティ侵害が発生した場合、キーのロールオーバーはいつでも行われる可能性があります。 したがって、AMS のライセンス配信サービスは、Azure AD がキーのペアを変更したら、使用する公開キーを更新する必要があります。更新しないと、AMS でのトークン認証は失敗し、ライセンスは発行されません。

そのためには、DRM ライセンス配信サービスを構成するときに、TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument を設定します。

JWT トークンのフローは次のとおりです。

1. Azure AD は、認証されたユーザーの現在の秘密キーで JWT トークンを発行します。
2. プレーヤーは、マルチ DRM で保護されたコンテンツを含む CENC を認識すると、最初に Azure AD によって発行された JWT トークンを探します。
3. プレーヤーは、JWT トークンを含むライセンス取得要求を、AMS のライセンス配信サービスに送信します。
4. AMS のライセンス配信サービスは、Azure AD からの最新の有効な公開キーを使用して JWT トークンを検証した後、ライセンスを発行します。

DRM ライセンス配信サービスは、Azure AD からの最新の有効な公開キーを常にチェックします。 Azure AD によって提示される公開キーは、Azure AD によって発行される JWT トークンの検証に使用されます。

AAD が JWT トークンを生成した後、プレイヤーが検証のために AMS の DRM ライセンス配信サービスに JWT トークンを送信する前に、キーのロールオーバーが発生するとどうなるでしょうか。

キーはいつでもロールオーバーされる可能性があるため、フェデレーション メタデータ ドキュメントでは常に複数の有効な公開キーを使用できます。 Azure Media Services ライセンス配信は、ドキュメントで指定されているどのキーでも使用できます。あるキーがすぐにロールオーバーしても、別のキーを代わりに使用できます。

### <a name="where-is-the-access-token"></a>アクセス トークンの場所
Web アプリが API アプリを呼び出す場合の認証フローは次のようになります (「 [アプリケーション ID と OAuth 2.0 クライアント資格情報付与](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api)」を参照)。

1. ユーザーは、Web アプリケーションで Azure AD にサインインします (「 [Web ブラウザー対 Web アプリケーション](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application)」を参照)。
2. Azure AD 認証エンドポイントは、承認コードを付けてクライアント アプリケーションにユーザー エージェントをリダイレクトします。 ユーザー エージェントは、クライアント アプリケーションのリダイレクト URI に承認コードを返します。
3. Web アプリケーションは、Web API に対して認証し、目的のリソースを取得できるように、アクセス トークンを取得する必要があります。 Web アプリケーションは、資格情報、クライアント ID、Web API のアプリケーション ID の URI を提供して、Azure AD のトークン エンドポイントに要求を送信します。 Web アプリケーションは、承認コードを示してユーザーが同意したことを証明します。
4. Azure AD がアプリケーションを認証し、Web API の呼び出しに使用する JWT アクセス トークンを返します。
5. Web アプリケーションは、返された JWT アクセス トークンを HTTPS 経由で使用して、Web API への要求の承認ヘッダーに、"Bearer" を指定した JWT 文字列を追加します。 その後、Web API が JWT を検証します。検証が正常に行われると、目的のリソースが返されます。

この「アプリケーション ID」フローでは、Web API は Web アプリケーションがユーザーを認証済みであることを信頼します。 そのため、このパターンは信頼されたサブシステムと呼ばれます。 [このページの図](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) では、承認コード付与フローがどのように動作するかが説明されています。

トークン制限のあるライセンス取得でも、同じ信頼されたサブシステムのパターンに従っています。 Azure Media Services のライセンス配信サービスは、Web アプリケーションがアクセスする必要のある Web API リソース (バックエンド リソース) です。 それでは、アクセス トークンはどこにあるのでしょうか。

実際には、Azure AD からアクセス トークンを取得します。 ユーザー認証が成功すると、承認コードが返されます。 その後、承認コードをクライアント ID およびアプリ キーと共に使用して、アクセス トークンに交換します。 アクセス トークンは、Azure Media Services ライセンス配信サービスを指すか表している「ポインター」アプリケーションにアクセスするためのものです。

次の手順に従って、Azure AD で「ポインター」アプリを登録して構成する必要があります。

1. Azure AD テナントで

   * サインオン URL を含むアプリケーション (リソース) を追加します。

   https://[resource_name].azurewebsites.net/ および

   * アプリ ID URL:

   https://[aad_tenant_name].onmicrosoft.com/[resource_name]
2. リソース アプリ用の新しいキーを追加します。
3. アプリ マニフェスト ファイルを更新し、groupMembershipClaims プロパティの値を "groupMembershipClaims": "All" に変更します。  
4. プレーヤー Web アプリを参照する Azure AD アプリの [他のアプリケーションに対するアクセス許可] セクションで、上の手順 1 で追加したリソース アプリを追加します。 [デリゲートされたアクセス許可] で [<リソース名> へのアクセス] をオンにします。 これにより、リソース アプリにアクセスするためのアクセス トークンを作成するアクセス許可が Web アプリに付与されます。 Visual Studio と Azure Web アプリでデプロイしている場合は、Web アプリのローカル バージョンとデプロイ バージョンの両方で、これを行う必要があります。

したがって、Azure AD によって発行される JWT トークンは、実際にはこの「ポインター」リソースにアクセスするためのアクセス トークンです。

### <a name="what-about-live-streaming"></a>ライブ ストリーミングの場合
これまではオンデマンド資産について説明しました。 ライブ ストリーミングの場合

プログラムに関連付けられた資産を「VOD 資産」として扱うことにより、Azure Media Services のライブ ストリーミングでもまったく同じ設計と実装を使用できます。

具体的には、Azure Media Services でライブ ストリーミングを行うには、チャネルを作成した後、チャネルの下にプログラムを作成する必要があることはよく知られています。 プログラムを作成するには、プログラムのライブ アーカイブを含む資産を作成する必要があります。 CENC にライブ コンテンツのマルチ DRM 保護を提供するには、プログラムを開始する前に、「VOD 資産」の場合と同じセットアップ/処理を資産に適用する必要があります。

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Azure Media Services の外部にあるライセンス サーバーの場合
多くの場合、ユーザーは自前のデータ センター内にある、または DRM サービス プロバイダーによってホストされている、ライセンス サーバー ファームを使用します。 幸い、Azure Media Services Content Protection を使用すると、ハイブリッド モードで運用できます。つまり、コンテンツは Azure Media Services でホストして動的に保護しながら、Azure Media Services の外部のサーバーで DRM ライセンスを配信できます。 この場合、次の変更を考慮する必要があります。

1. Secure Token Service は、ライセンス サーバー ファームによって許容可能で検証できるトークンを発行する必要があります。 たとえば、Axinom によって提供される Widevine ライセンス サーバーには、"権利メッセージ" を含む特定の JWT トークンが必要です。 そのため、STS でそのような JWT トークンを発行する必要があります。 そのような実装の詳細については、[Azure ドキュメント センター](https://azure.microsoft.com/documentation/)の「[Axinom を使用して Azure Media Services に Widevine ライセンスを配信する](media-services-axinom-integration.md)」を参照してください。
2. Azure Media Services でライセンス配信サービス (ContentKeyAuthorizationPolicy) を構成する必要はもうありません。 必要なのは、マルチ DRM を使用する CENC の設定で AssetDeliveryPolicy を構成するときに、ライセンス取得 URL を (PlayReady、Widevine、FairPlay に) 提供することです。

### <a name="what-if-i-want-to-use-a-custom-sts"></a>カスタム STS を使用する場合
JWT トークンを提供するためにユーザーがカスタム STS (Secure Token Service) の使用を選択するにはいくつかの理由が考えられます。 次に示すのはその一部です。

1. ユーザーが使用する ID プロバイダー (IDP) が STS をサポートしていない場合。 この場合は、カスタム STS が選択肢になります。
2. ユーザーが、STS とユーザーのサブスクライバー請求システムの統合において、より柔軟性の高い、または厳密な制御を必要とする場合。 たとえば、MVPD 運営会社は、プレミアム、ベーシック スポーツなど、複数の OTT サブスクライバー パッケージを提供する場合があります。運営会社は、適切なパッケージのコンテンツだけが利用可能になるように、トークンの要求をサブスクライバーのパッケージと一致させる必要があります。 この場合、カスタム STS は必要な柔軟性と制御を提供します。

カスタム STS を使用する場合は、2 つの変更を行う必要があります。

1. 資産のライセンス配信サービスを構成するときに、Azure Active Directory からの現在のキーではなく、カスタム STS での検証に使用するセキュリティ キーを指定する必要があります (詳細は後述)。
2. JTW トークンが生成されるときに、現在の Azure Active Directory での X&509; 証明書の秘密キーではなく、セキュリティ キーを指定します。

セキュリティ キーには次の&2; 種類があります。

1. 対称キー: JWT トークンの生成と検証の両方に同じキーが使用されます。
2. 非対称キー: X509 証明書の公開/秘密キー ペアの秘密キーが JWT トークンの暗号化/生成に使用され、公開キーがトークンの検証に使用されます。

#### <a name="tech-note"></a>テクニカル ノート
開発プラットフォームとして .NET Framework/C# を使用する場合、非対称セキュリティ キーに使用される X509 証明書のキーの長さは 2048 ビット以上でなければなりません。 これは、.NET Framework の System.IdentityModel.Tokens.X509AsymmetricSecurityKey クラスの要件です。 そうでない場合は、次の例外がスローされます。

IDX10630: 署名の 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' は '2048' ビット以上でなければなりません。

## <a name="the-completed-system-and-test"></a>完成したシステムとテスト
読者がログイン アカウントを取得する前にシステムの基本的な動作を理解できるように、完成したエンド ツー エンド システムのいくつかのシナリオを説明します。

プレーヤー Web アプリケーションとそのログインは、 [こちら](https://openidconnectweb.azurewebsites.net/)にあります。

「統合されていない」シナリオが必要な場合: Azure Media Services でホストされているビデオ資産が保護されていない場合、または DRM で保護されていてもトークン認証がない (要求するすべてのユーザーにライセンスを発行する) 場合は、ログインなしでテストできます (ビデオ ストリーミングが HTTP 経由の場合は HTTP に切り替えることによって)。

エンド ツー エンドの統合シナリオが必要な場合: ビデオ資産が Azure Media Services による動的な DRM 保護下にあり、トークン認証と、Azure AD によって生成される JWT トークンを使用する場合は、ログインが必要です。

### <a name="user-login"></a>ユーザー ログイン
エンド ツー エンドの統合 DRM システムをテストするには、「アカウント」を作成または追加する必要があります。

どのようなアカウントでしょうか。

もともと Azure では Microsoft アカウント ユーザーにのみアクセスが許可されていましたが、現在では両方のシステムのユーザーがアクセスできます。 これは、すべての Azure プロパティが認証のために Azure AD を信頼し、Azure AD が組織のユーザーを認証し、Azure AD が Microsoft アカウント コンシューマー ID システムを信頼してコンシューマー ユーザーを認証するフェデレーション関係を作成することによって実現されました。 その結果、Azure AD は「ゲスト」 Microsoft アカウントだけでなく「ネイティブ」 Azure AD アカウントも認証できます。

Azure AD は Microsoft アカウント (MSA) ドメインを信頼するので、次のどのドメインのアカウントでもカスタム Azure AD テナントに追加し、そのアカウントを使用してログインできます。

| **ドメイン名** | **ドメイン** |
| --- | --- |
| **カスタム Azure AD テナント ドメイン** |somename.onmicrosoft.com |
| **企業ドメイン** |microsoft.com |
| **Microsoft アカウント (MSA) ドメイン** |outlook.com、live.com、hotmail.com |

いずれかの著者に連絡していただけば、アカウントを作成し、追加してさしあげます。

異なるドメイン アカウントで使用されるログイン ページのスクリーンショットを以下に示します。

**カスタム Azure AD テナント ドメインのアカウント**: カスタム Azure AD テナント ドメインのカスタマイズされたログイン ページが表示されます。

![カスタム Azure AD テナント ドメインのアカウント](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**スマート カードを使用する Microsoft ドメインのアカウント**:&2; 要素認証を使用する Microsoft 企業 IT によってカスタマイズされたログイン ページが表示されます。

![カスタム Azure AD テナント ドメインのアカウント](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft アカウント (MSA)**: Microsoft アカウントのコンシューマー向けログイン ページが表示されます。

![カスタム Azure AD テナント ドメインのアカウント](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>PlayReady に対する Encrypted Media Extensions の使用
Windows 8.1 での IE 11 以上や、Windows 10 での Microsoft Edge ブラウザーなど、Encrypted Media Extensions (EME)/PlayReady をサポートする最新のブラウザーでは、PlayReady が EME の基になる DRM です。

![PlayReady に対する EME の使用](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

プレーヤー領域が暗くなっているのは、PlayReady の保護機能が、保護されたビデオのスクリーン キャプチャを防いでいるためです。

次の画面では、プレーヤーのプラグインと MSE/EME サポートを示します。

![PlayReady に対する EME の使用](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10 の Microsoft Edge や IE 11 の EME は、 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) を対応する Windows 10 デバイス上で呼び出すことができます。 PlayReady SL3000 により、拡張プレミアム コンテンツ (4 K や HDR など) の配信や新しいコンテンツ配信モデル (拡張コンテンツ向けのアーリー ウィンドウ) の利用が可能になります。

Windows デバイスにフォーカス: PlayReady は、Windows デバイスで利用可能なハードウェアベースの唯一の DRM です (PlayReady SL3000)。 ストリーミング サービスは EME または UWP アプリケーションを介して PlayReady を使用でき、PlayReady SL3000 を使って他の DRM よりも高い画質を提供することができます。 通常、(サービスの設定や実装に応じて) 2 K コンテンツは Chrome または Firefox へ、4 K コンテンツは Microsoft Edge/IE11 または同じデバイス上の UWP アプリケーションへ配信されます。

#### <a name="using-eme-for-widevine"></a>Widevine に対する EME の使用
Windows 10、Windows 8.1、Mac OSX Yosemite の Chrome 41 以降や、Android 4.4.4 の Chrome など、EME/Widevine をサポートする最新のブラウザーでは、Google Widevine が EME の背後にある DRM です。

![Widevine に対する EME の使用](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine では保護されたビデオのスクリーン キャプチャが防止されないことに注意してください。

![Widevine に対する EME の使用](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>権利のないユーザー
ユーザーが "Entitled Users" グループのメンバーではない場合、ユーザーは "権利チェック" をパスできず、次に示すように、マルチ DRM ライセンス サービスは要求されたライセンスの発行を拒否します。 詳細な説明は「ライセンスを取得できませんでした」であり、これは設計どおりです。

![権利のないユーザー](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>カスタム セキュリティ トークン サービスの実行
カスタム セキュリティ トークン サービス (STS) を実行するシナリオでは、JWT トークンは対称キーまたは非対称キーを使用してカスタム STS によって発行されます。

対象キーを使用する場合 (Chrome を使用):

![カスタム STS の実行](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

X509 証明書で非対称キーを使用する場合 (Microsoft の最新のブラウザーを使用):

![カスタム STS の実行](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

どちらの場合も、ユーザー認証は変わらず、Azure AD によって行われます。 唯一の違いは、JWT トークンが Azure AD ではなくカスタム STS によって発行されることです。 もちろん、動的 CENC 保護を構成するときは、ライセンス配信サービスの制限で JWT トークンの種類として対称キーまたは非対称キーが指定されます。

## <a name="summary"></a>まとめ
このドキュメントでは、トークン認証によるマルチ ネイティブ DRM およびアクセス制御を使用する CENC、Azure を使用したその設計と実装、Azure Media Services、Azure Media Player について説明しました。

* DRM/CENC サブシステムのすべての必要なコンポーネントを含む参照設計を示しました。
* Azure、Azure Media Services、Azure Media Player での参照実装を示しました。
* 設計と実装に直接関係するいくつかのトピックについても説明しました。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
