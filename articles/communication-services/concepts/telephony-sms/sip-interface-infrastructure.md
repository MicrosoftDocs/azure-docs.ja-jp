---
title: SIP インターフェイスのインフラストラクチャ要件 - Azure Communication Services
description: Azure Communication Services SIP インターフェイス構成のインフラストラクチャ要件を確認します
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ede650ae072ef53ed40a9372a292ab69fe8cc1af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492729"
---
# <a name="sip-interface-infrastructure-requirements"></a>SIP インターフェイスのインフラストラクチャ要件 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
この記事では、SIP インターフェイスのデプロイを計画する際に留意すべきインフラストラクチャ、ライセンス、セッション ボーダー コントローラー (SBC) 接続について詳しく説明します。


## <a name="infrastructure-requirements"></a>インフラストラクチャの要件
次の表は、SIP インターフェイスをデプロイするためのインフラストラクチャ要件として、サポートされる SBC やドメインなど、各種ネットワーク接続の要件が記載されています。  

|インフラストラクチャ要件|必要なもの|
|:--- |:--- |
|セッション ボーダー コントローラー (SBC)|サポートされている SBC。 詳細については、[サポートされる SBC](#supported-session-border-controllers-sbcs) に関するページを参照してください。|
|SBC に接続されたテレフォニー トランク|SBC に接続された 1 つまたは複数のテレフォニー トランク。 SBC の片側は、SIP インターフェイスを介して Azure Communication Services に接続されます。 PBX や Analog Telephony Adapter など、サードパーティのテレフォニー エンティティに SBC を接続することもできます。 SBC に接続された任意の PSTN 接続オプションを使用できます (SBC に対する PSTN トランクの構成については、SBC ベンダーまたはトランク プロバイダーにお問い合わせください)。|
|Azure サブスクリプション|ACS リソースを作成したり、SBC の構成と SBC への接続を作成したりする際に使用する Azure サブスクリプション。|
|Communication Services のアクセス トークン|電話をかけるには、`voip` スコープの有効なアクセス トークンが必要です。 「[アクセス トークン](../identity-model.md#access-tokens)」を参照してください。|
|SBC のパブリック IP アドレス|SBC への接続に使用できるパブリック IP アドレス。 SBC の種類によっては NAT を使用できます。|
|SBC の完全修飾ドメイン名 (FQDN)|SBC の FQDN。この FQDN のドメイン部分は、Microsoft 365 や Office 365 組織の登録済みドメインとは一致しません。 詳細については、「[SBC ドメイン名](#sbc-domain-names)」を参照してください。|
|SBC のパブリック DNS エントリ |SBC の FQDN をパブリック IP アドレスにマップするパブリック DNS エントリ。 |
|SBC の信頼済みの公開証明書 |SIP インターフェイスとのすべての通信に使用される SBC の証明書。 詳細については、「[SBC の信頼済みの公開証明書](#public-trusted-certificate-for-the-sbc)」を参照してください。|
|ファイアウォールの IP アドレスとポート (SIP シグナリングとメディア用) |SBC は、クラウド内の次のサービスと通信を行います。<br/><br/>SIP プロキシ: シグナリングを処理します<br/>メディア プロセッサ: メディアを処理します<br/><br/>Microsoft Cloud では、この 2 つのサービスに別々の IP アドレスが割り当てられます。この点については、このドキュメントの中で後述します。


## <a name="sbc-domain-names"></a>SBC ドメイン名

Office 365 を利用していない場合は、公開証明書を取得できる任意のドメイン名を使用できます。

次の表では、テナント用に登録されている DNS 名の例、その名前が SBC の完全修飾ドメイン名 (FQDN) として使用できるかどうか、および有効な FQDN 名の例を示しています。

|DNS name|SBC の FQDN として使用できるか|FQDN 名の例|
|:--- |:--- |:--- |
contoso.com|はい|**有効な名前:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|いいえ|SBC の名前に *.onmicrosoft.com ドメインを使用することはできません

Office 365 をご利用の場合は、SBC のドメイン名が Office 365 テナントの登録ドメインと一致しないようにしてください。 次に、Office 365 と Azure Communication Services が共存する例を示します。

|Office 365 の登録ドメイン|Teams における SBC の FQDN の例|ACS における SBC の FQDN 名の例|
|:--- |:--- |:--- |
**contoso.com** (第 2 レベル ドメイン)|**sbc.contoso.com** (第 2 レベル ドメインの名前)|**sbc.acs.contoso.com** (第 3 レベル ドメインの名前)<br/>**sbc.fabrikam.com** (別のドメイン内の任意の名前)|
|**o365.contoso.com** (第 3 レベル ドメイン)|**sbc.o365.contoso.com** (第 3 レベル ドメインの名前)|**sbc.contoso.com** (第 2 レベル ドメインの名前)<br/>**sbc.acs.o365.contoso.com** (第 4 レベル ドメインの名前)<br/>**sbc.fabrikam.com** (別のドメイン内の任意の名前)

SBC のペアリングは、Communication Services リソース レベルで機能します。つまり、1 つの Communication Services リソースに多数の SBC をペアリングすることはできますが、複数の Communication Services リソースに 1 つの SBC をペアリングすることはできません。 複数の異なるリソースにペアリングするには、重複しない SBC の FQDN が必要です。

## <a name="public-trusted-certificate-for-the-sbc"></a>SBC の信頼済みの公開証明書

SBC の証明書は、証明書署名要求 (CSR) を生成することによって要求することをお勧めします。 SBC の CSR を生成する具体的な手順については、ご利用の SBC ベンダーから提供されている相互接続の手順やドキュメントを参照してください。 

  > [!NOTE]
  > ほとんどの証明機関 (CA) では、秘密キーのサイズを 2048 以上にすることが求められます。 CSR を生成するときは、この点に留意してください。

証明書には、共通名 (CN) またはサブジェクトの別名 (SAN) フィールドとして SBC FQDN が必要です。 証明書は、中間プロバイダーからではなく証明機関から直接発行してもらう必要があります。

また、Communication Services の SIP インターフェイスは、CN や SAN におけるワイルドカードをサポートしています。ワイルドカードは標準の [RFC HTTP OVER TLS](https://tools.ietf.org/html/rfc2818#section-3.1) に準拠している必要があります。 

たとえば、`\*.contoso.com` は、SBC FQDN `sbc.contoso.com` と一致しますが、`sbc.test.contoso.com` とは一致しません。

証明書は、次のいずれかのルート証明機関に生成してもらう必要があります。

- AffirmTrust
- AddTrust External CA Root
- Baltimore CyberTrust Root*
- Buypass
- Cybertrust
- Class 3 Public Primary Certification Authority
- Comodo Secure Root CA
- Deutsche Telekom 
- DigiCert Global Root CA
- DigiCert High Assurance EV Root CA
- Entrust
- GlobalSign
- Go Daddy
- GeoTrust
- Verisign, Inc. 
- SSL.com
- Starfield
- Symantec Enterprise Mobile Root for Microsoft 
- SwissSign
- Thawte Timestamping CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

Microsoft はお客様からのご要望に基づき、さらなる証明機関の追加に取り組んでいます。 

## <a name="sip-signaling-fqdns"></a>SIP シグナリング: FQDN 

次の 3 つの FQDN が、Communication Services の SIP インターフェイスの接続ポイントとなります。

- **sip.pstnhub.microsoft.com** - グローバル FQDN。最初に試行される必要があります。 この名前の解決要求を SBC が送信すると、SBC に割り当てられたプライマリ Azure データセンターを指す IP アドレスが Microsoft Azure DNS サーバーから返されます。 この割り当ては、データセンターのパフォーマンス メトリックと SBC との地理的近接性に基づいています。 返される IP アドレスは、プライマリ FQDN に相当します。
- **sip2.pstnhub.microsoft.com** - 第 2 の FQDN。優先度が 2 番目のリージョンに地理的にマップされます。
- **sip3.pstnhub.microsoft.com** - 第 3 の FQDN。優先度が 3 番目のリージョンに地理的にマップされます。

この 3 つの FQDN を正しい順序で並べることは、次の目的で必要になります。

- 最適なエクスペリエンスを提供する。最初の FQDN を照会すると、負荷が減り、割り当てられる SBC データセンターまでの距離が最短になります。
- 一時的な問題が発生しているデータセンターに対して SBC からの接続を確立する際にフェールオーバーが実行される。 詳細については、以下の[フェールオーバーのメカニズム](#failover-mechanism-for-sip-signaling)に関するセクションを参照してください。  

これらの FQDN (sip.pstnhub.microsoft.com、sip2.pstnhub.microsoft.com、sip3.pstnhub.microsoft.com) は、次のいずれかの IP アドレスに解決されます。

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

これらの IP アドレスに対するファイアウォール ポートを開放して、シグナリングを目的にこれらのアドレスとの間の受信トラフィックと送信トラフィックを許可してください。 ファイアウォールが DNS 名をサポートしていれば、上記のどの IP アドレスも、`sip-all.pstnhub.microsoft.com` という FQDN で解決されます。 

## <a name="sip-signaling-ports"></a>SIP シグナリング: Port

Communication Services の SIP インターフェイスには、次のポートを使用します。

|トラフィック|ソース|終了|送信元ポート|宛先ポート|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|SIP プロキシ|SBC|1024 – 65535|SBC で定義 (Office 365 GCC High/DoD では、ポート 5061 のみを使用する必要があります)|
SIP/TLS|SBC|SIP プロキシ|SBC で定義|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>SIP シグナリングのフェールオーバー メカニズム

SBC は DNS クエリを実行して、sip.pstnhub.microsoft.com を解決します。 プライマリ データセンターは、SBC の場所とデータセンターのパフォーマンス メトリックに基づいて選択されます。 プライマリ データセンターで問題が発生している場合、SBC は、sip2.pstnhub.microsoft.com の名前解決を試みます。これが 2 番目に割り当てられるデータセンターに解決されます。まれなケースとして、2 つのリージョンのデータセンターが使用できない場合、SBC は最後の FQDN (sip3.pstnhub.microsoft.com) の名前解決を試み、その結果、第 3 のデータセンターの IP が得られます。

## <a name="media-traffic-ip-and-port-ranges"></a>メディア トラフィック: IP とポート範囲

メディア トラフィックは、メディア プロセッサと呼ばれる独立したサービスとの間で送受信されます。 ACS のメディア プロセッサは発行時に、任意の Azure IP アドレスを使用できます。 [アドレスの全一覧](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードしてください。

### <a name="port-range"></a>ポート範囲
メディア プロセッサのポート範囲を次の表に示します。 

|トラフィック|ソース|終了|送信元ポート|宛先ポート|
|:--- |:--- |:--- |:--- |:--- |
|UDP または SRTP|メディア プロセッサ|SBC|3478 から 3481 および 49152 から 53247|SBC で定義|
|UDP または SRTP|SBC|メディア プロセッサ|SBC で定義|3478 から 3481 および 49152 から 53247|

  > [!NOTE]
  > Microsoft では、SBC での同時通話ごとに少なくとも 2 つのポートを推奨しています。


## <a name="media-traffic-media-processors-geography"></a>メディア トラフィック: メディア プロセッサの地理的位置

メディア トラフィックは、メディア プロセッサと呼ばれるコンポーネントを経由します。 メディア プロセッサは、SIP プロキシと同じデータセンターに配置されます。 また、メディア フローを最適化するためのメディア プロセッサもあります。 たとえば、SIP プロキシ コンポーネントは現在オーストラリアに存在しません (SIP はシンガポールや香港特別行政区を経由します) が、メディア プロセッサはオーストラリア国内に存在します。 国内にメディア プロセッサが必要かどうかは、長距離トラフィック (オーストラリアからシンガポール、オーストラリアから香港特別行政区など) を送信する際にユーザーが体感する待ち時間によって決まります。 オーストラリアから香港特別行政区またはオーストラリアからシンガポールに流れるトラフィックの例で言えば、良好な通話品質を維持するうえで、SIP トラフィックの待ち時間は許容されますが、リアルタイム メディア トラフィックの待ち時間は許容されません。

SIP プロキシとメディア プロセッサの両方のコンポーネントがデプロイされている場所は次のとおりです。
- 米国 (米国西部と米国東部データセンターの 2 つ)
- ヨーロッパ (アムステルダムとダブリンのデータセンター)
- アジア (シンガポールと香港特別行政区のデータセンター)
- オーストラリア (オーストラリア東部と南東部のデータセンター)

メディア プロセッサのみがデプロイされている場所は次のとおりです (SIP は、上記のうち最も近いデータセンターを経由します)。
- 日本 (東日本と西日本のデータセンター)


## <a name="media-traffic-codecs"></a>メディア トラフィック: コーデック

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>SBC とクラウド メディア プロセッサ (または Microsoft Teams クライアント) の区間
メディア バイパスのケースとメディア バイパスでないケースの両方が対象となります。

セッション ボーダー コントローラーとクラウド メディア プロセッサの区間のダイレクト ルーティング インターフェイスでは、次のコーデックを使用できます。

- SILK、G.711、G.722、G.729

セッション ボーダー コントローラーで、望ましくないコーデックをプランから除外することにより、特定のコーデックの使用を強制することができます。

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>ACS SDK アプリとクラウド メディア プロセッサの区間

クラウド メディア プロセッサと ACS SDK アプリの区間では、SILK または G.722 が使用されます。 この区間のコーデックは、Microsoft のアルゴリズムにより複数のパラメーターを考慮して選択されます。 

## <a name="supported-session-border-controllers-sbcs"></a>サポートされるセッション ボーダー コントローラー (SBC)

現在、認定作業を進めています。 その間は、[Teams の認定セッション ボーダー コントローラー](/MicrosoftTeams/direct-routing-border-controllers)をご利用ください。 

## <a name="next-steps"></a>次の手順

### <a name="conceptual-documentation"></a>概念説明のドキュメント

- [テレフォニーの概念](./telephony-concept.md)
- [Azure Communication Services での電話番号の種類](./plan-solution.md)
- [料金](../pricing.md)

### <a name="quickstarts"></a>クイックスタート

- [電話の呼び出し](../../quickstarts/voice-video-calling/pstn-call.md)