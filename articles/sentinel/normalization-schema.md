---
title: Azure Sentinel データ正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Azure Sentinel データ正規化スキーマを示します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806937"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Azure Sentinel データ正規化スキーマ リファレンス

## <a name="terminology"></a>用語

次の用語が、Azure Sentinel のスキーマで使用されます。

| 項目 | 定義 |
| ---- | ---------- |
| レポート デバイス | レコードを Azure Sentinel に送信するシステム。 レコードのサブジェクト システムではない場合があります。 |
| Record | レポート デバイスから送信されるデータの単位。 これは多くの場合、"ログ"、"イベント"、または "アラート" と呼ばれますが、必ずしもこれらのいずれかである必要はありません。 |
|

## <a name="data-types-and-formats"></a>データ型と形式

値は、以下のガイドラインに基づいて正規化する必要があります。 これは、正規化されたフィールドには必須で、他のフィールドには推奨です。 

| データ型 | 物理型 | 形式と値 |
| --------- | ------------- | ---------------- |
| **日付/時刻** | 取り込み方法の機能に応じて、降順の優先順位で使用します。<ul><li>Log Analytics 組み込みの datetime 型</li><li>Log Analytics の datetime の数値表現を使用した整数フィールド</li><li>Log Analytics の datetime の数値表現を使用した文字列フィールド</li></ul> | Log Analytics の datetime の表現。 <br></br>Log Analytics 日付と時刻の表現は、Unix の時刻表現と性質は似ていますが異なります。 以下の変換ガイドラインを参照してください。 <br></br>日付と時効はタイム ゾーンの調整を行う必要があります。 |
| **MAC アドレス** | String | コロン 16 進表記 |
| **IP アドレス** | IP アドレス | スキーマには、IPv4 アドレスと IPv6 アドレスが別々に含まれているわけではありません。 どの IP アドレス フィールドにも、IPv4 アドレスか IPv6 アドレスのどちらかを含めることができます。<ul><li>ドット 10 進表記の IPv4</li><li>IPv6 は 8 ヘクテット表記で、ここで説明する短い形式を使用できます。</li></ul> |
| **User** | String | 次の 3 つのユーザー フィールドを使用できます。<ul><li>ユーザー名</li><li>ユーザー UPN</li><li>ユーザー ドメイン</li></ul> |
| **[ユーザー ID]** | String | 次の 2 つのユーザー ID が現在サポートされています。<ul><li>ユーザー SID</li><li>Azure Active Directory ID</li></ul> |
| **[デバイス]** | String | 次の 3 つのデバイス/ホスト列がサポートされています。<ul><li>id</li><li>名前</li><li>完全修飾ドメイン名 (FQDN)</li></ul> |
| **Country (国)** | String | 次の優先順位に従って ISO 3166-1 を使用した文字列:<ul><li>2 英文字コード (米国の場合は US)</li><li>3 英文字コード (米国の場合は USA)</li><li>短い名前</li></ul> |
| **リージョン** | String | ISO 3166-2 使用した都道府県名 |
| **City (市)** | String | |
| **Longitude (経度)** | Double | ISO 6709 座標表現 (符号付き 10 進数) |
| **Latitude (緯度)** | Double | ISO 6709 座標表現 (符号付き 10 進数) |
| **ハッシュ アルゴリズム** | String | 次の 4 つのハッシュ列がサポートされています。<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **ファイルの種類** | String | このファイル タイプの種類:<ul><li>拡張子</li><li>クラス</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>ネットワーク セッション テーブル スキーマ

次に、バージョン 1.0.0 のネットワーク セッション テーブルのスキーマを示します

| フィールド名 | 値の種類 | 例 | 説明 | 関連付けられた OSSEM エンティティ |
|-|-|-|-|-|
| EventType | String | トラフィック | 収集されているイベントの種類 | event |
| EventSubType | String | 認証 | 種類の追加説明 (該当する場合) | event |
| EventCount | Integer  | 10 | 集計されたイベントの数 (該当する場合)。 | event |
| EventEndTime | 日付/時刻 | 「データ型」をご覧ください | イベントが終了した時刻 | event |
| EventMessage | string |  アクセス拒否 | レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明 | event |
| DvcIpAddr | IP アドレス |  23.21.23.34 | レコードを生成しているデバイスの IP アドレス | デバイス<br>IP |
| DvcMacAddr | String | 06:10:9f:eb:8f:14 | イベントの送信元のレポート デバイスのネットワーク インターフェイスの MAC アドレス。 | デバイス<br>Mac |
| DvcHostname | デバイス名 (文字列) | syslogserver1.contoso.com | メッセージを生成しているデバイスのデバイス名。 | Device |
| EventProduct | String | OfficeSharepoint | イベントを生成している製品。 | event |
| EventProductVersion | string | 9.0 |  イベントを生成している製品のバージョン。 | event |
| EventResourceId | デバイス ID (文字列) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | メッセージを生成しているデバイスのリソース ID。 | event |
| EventReportUrl | String | https://192.168.1.1/repoerts/ae3-56.htm | レポート デバイスによって作成された完全なレポートへのリンク | event |
| EventVendor | String | Microsoft | イベントを生成している製品のベンダー。 | event |
| EventResult | 複数値:Success、Partial、Failure、[空] (文字列) | Success | アクティビティについて報告された結果。 該当しない場合は空の値。 | event |
| EventResultDetails | String | パスワードが違います | EventResult で報告された結果の理由または詳細 | event |
| EventSchemaVersion | Real | 0.1 | Azure Sentinel スキーマのバージョン。 現時点では 0.1。 | event |
| EventSeverity | String | 低 | 報告されたアクティビティがセキュリティに影響を与える場合、影響の重大度を示します。 | event |
| EventOriginalUid | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | レポート デバイスからのレコード ID。 | event |
| EventStartTime | 日付/時刻 | 「データ型」をご覧ください | イベントが開始した時刻 | event |
| TimeGenerated | 日付/時刻 | 「データ型」をご覧ください | レポート ソースによって報告された、イベントが発生した時刻。 | カスタム フィールド |
| EventTimeIngested | 日付/時刻 | 「データ型」をご覧ください | イベントが Azure Sentinel に取り込まれたされた時刻。 Azure Sentinel によって追加されます。 | event |
| EventUid | Guid (文字列) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Azure Sentinel が行をマークするために使用する一意の識別子。 | event |
| NetworkApplicationProtocol | String | HTTPS | 接続またはセッションで使用されるアプリケーション レイヤー プロトコル。 | ネットワーク |
| DstBytes | INT | 32455 | 接続またはセッションで送信先から送信元に送信されたバイト数。 | 宛先 |
| SrcBytes | INT | 46536 | 接続またはセッションで送信元から送信先に送信されたバイト数。 | source |
| NetworkBytes | INT | 78991 | 両方向に送信されたバイト数。 BytesReceived と BytesSent が両方とも存在する場合、BytesTotal はその合計と同じである必要があります。 | ネットワーク |
| NetworkDirection | 複数値:受信、送信 (文字列) | 着信 | 組織に対する接続またはセッションの方向。 | ネットワーク |
| DstGeoCity | String | Burlington | 送信先 IP アドレスに関連付けられた都市 | 送信先、<br>ジオ (主要地域) |
| DstGeoCountry | 国 (文字列) | 米国 | 送信元 IP アドレスに関連付けられた国 | 送信先、<br>ジオ (主要地域) |
| DstDvcHostname | デバイス名 (文字列) |  victim_pc | 送信先デバイスのデバイス名 | 宛先<br>Device |
| DstDvcFqdn | String | victim_pc.contoso.local | ログが作成されたホストの完全修飾ドメイン名 | 送信先、<br>Device |
| DstDomainHostname | string | CONTOSO | たとえば、DNS 参照や NS 参照用の送信先のドメイン、送信先ホストのドメイン (Web サイト、ドメイン名など) | 宛先 |
| DstInterfaceName | string | Microsoft Hyper-V ネットワーク アダプター | 送信先デバイスによる接続またはセッションに使用されるネットワーク インターフェイス。 | 宛先 |
| DstInterfaceGuid | string | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | 認証要求に使用されたネットワーク インターフェイスの GUID  | 宛先 |
| DstIpAddr | IP アドレス | 2001:db8::ff00:42:8329 | 接続またはセッションの送信先の IP アドレス。通常、ネットワーク パケットで送信先 IP と呼ばれます | 送信先、<br>IP |
| DstDvcIpAddr | IP アドレス | 75.22.12.2 | ネットワークパケットに直接関連付けられていないデバイスの送信先 IP アドレス | 送信先、<br>デバイス<br>IP
| DstGeoLatitude | 緯度 (倍精度) | 44.475833 | 送信先 IP アドレスに関連付けられている地理的座標の緯度 | 送信先、<br>ジオ (主要地域) |
| DstMacAddr | String | 06:10:9f:eb:8f:14 | 接続またはセッションが終了するネットワーク インターフェイスの MAC アドレス。通常は、ネットワーク パケットでの送信先 MAC と呼ばれます | 送信先、<br>MAC |
| DstDvcMacAddr | String | 06:10:9f:eb:8f:14 | ネットワーク パケットに直接関連付けられていないデバイスの送信先 MAC アドレス。 | 送信先、<br>デバイス<br>MAC |
| DstDvcDomain | String | CONTOSO | 送信先デバイスのドメイン。 | 送信先、<br>Device |
| DstPortNumber | Integer | 443 | 送信先 IP ポート。 | 送信先、<br>ポート |
| DstGeoRegion | リージョン (文字列) | バーモント | 送信先 IP アドレスに関連付けられたリージョン | 送信先、<br>ジオ (主要地域) |
| DstResourceId | デバイス ID (文字列) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | 送信先デバイスのリソース。 | 宛先 |
| DstNatIpAddr | IP アドレス | 2::1 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信元との通信に使用する IP アドレス。 | 送信先 NAT、<br>IP |
| DstNatPortNumber | INT | 443 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信元との通信に使用するポート。 | 送信先 NAT、<br>ポート |
| DstUserSid | ユーザー SID |  S-12-1445 | セッションの送信先に関連付けられている ID のユーザー ID。 通常、サーバーの認証に使用される ID。 詳細については、「データ型」をご覧ください。 | 送信先、<br>User |
| DstUserAadId | 文字列 (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | セッションの送信先側にいるユーザーの Azure AD アカウント オブジェクト ID | 送信先、<br>User |
| DstUserName | ユーザー名 (文字列) | johnd | セッションの送信先に関連付けられている ID のユーザー名。  | 送信先、<br>User |
| DstUserUpn | string | johnd@anon.com | セッションの送信先に関連付けられている ID の UPN。 | 送信先、<br>User |
| DstUserDomain | string | WORKGROUP | セッションの送信先側にあるアカウントのドメインまたはコンピューター名 | 送信先、<br>User |
| DstZone | String | Dmz | レポート デバイスにより定義された、送信先のネットワーク ゾーン。 | 宛先 |
| DstGeoLongitude | 経度 (倍精度) | -73.211944 | 送信先 IP アドレスに関連付けられている地理的座標の経度 | 送信先、<br>ジオ (主要地域) |
| DvcAction | 複数値:Allow、Deny、Drop (文字列) | Allow | ファイアウォールなどの中間デバイスによって報告された場合に、デバイスによって実行されるアクション。 | Device |
| DvcInboundInterface | String | eth0 | ファイアウォールなどの中間デバイスによって報告された場合に、送信元デバイスへの接続に使用されるネットワーク インターフェイス。 | Device |
| DvcOutboundInterface | String  | ギガビット アダプター イーサネット 4 | ファイアウォールなどの中間デバイスによって報告された場合に、送信先デバイスへの接続に使用されるネットワーク インターフェイス。 | Device |
| NetworkDuration | Integer | 1500 | ネットワーク セッションまたは接続の完了にかかる時間 (ミリ秒単位) | ネットワーク |
| NetworkIcmpCode | Integer | 34 | ICMP メッセージの場合、ICMP メッセージの種類の数値 (RFC 2780 または RFC 4443)。 | ネットワーク |
| NetworkIcmpType | String | 宛先は到達不能です | ICMP メッセージの場合は、ICMP メッセージの種類のテキスト表現 (RFC 2780 または RFC 4443)。 | ネットワーク |
| DstPackets | INT  | 446 | 接続またはセッションで送信先から送信元に送信されたパケット数。 パケットの意味はレポート デバイスで定義されます。 | 宛先 |
| SrcPackets | INT  | 6478 | 接続またはセッションで送信元から送信先に送信されたパケット数。 パケットの意味はレポート デバイスで定義されます。 | source |
| NetworkPackets | INT  | 0 | 両方向に送信されたパケット数。 PacketsReceived と PacketsSent が両方とも存在する場合、BytesTotal はその合計と同じである必要があります。 | ネットワーク |
| HttpRequestTime | Integer | 700 | 要求がサーバーに送信されるまでにかかった時間 (該当する場合)。 | Http |
| HttpResponseTime | Integer | 800 | サーバーで応答を受け取るまでにかかった時間 (該当する場合)。 | Http |
| NetworkRuleName | String | AnyAnyDrop | DeviceAction を決定したときに使用されたルールの名前または ID | ネットワーク |
| NetworkRuleNumber | INT |  23 | 一致したルール番号  | ネットワーク |
| NetworkSessionId | string | 172_12_53_32_4322__123_64_207_1_80 | レポート デバイスによって報告されたセッション識別子。 たとえば、認証後の特定のアプリケーションの L7 セッション識別子 | ネットワーク |
| SrcGeoCity | String | Burlington | 送信元 IP アドレスに関連付けられた都市 | 送信元、<br>ジオ (主要地域) |
| SrcGeoCountry | 国 (文字列) | 米国 | 送信元 IP アドレスに関連付けられた国 | 送信元、<br>ジオ (主要地域) |
| SrcDvcHostname | デバイス名 (文字列) |  villain | 送信元デバイスのデバイス名 | 送信元、<br>Device |
| SrcDvcFqdn | string | Villain.malicious.com | ログが作成されたホストの完全修飾ドメイン名 | 送信元、<br>Device |
| SrcDvcDomain | string | EVILORG | セッションを開始したデバイスのドメイン | 送信元、<br>Device |
| SrcDvcOs | String | iOS | 送信元デバイスの OS | 送信元、<br>Device |
| SrcDvcModelName | String | Samsung Galaxy Note | 送信元デバイスのモデル名 | 送信元、<br>Device |
| SrcDvcModelNumber | String | 10.0 | 送信元デバイスのモデル番号 | 送信元、<br>Device |
| SrcDvcType | String | モバイル | 送信元デバイスの種類 | 送信元、<br> Device |
| SrcIntefaceName | String | eth01 | 送信元デバイスで接続またはセッションに使用されるネットワーク インターフェイス。 | source |
| SrcInterfaceGuid | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | 使用されるネットワーク インターフェイスの GUID | source |
| SrcIpAddr | IP アドレス | 77.138.103.108 | 接続またはセッションの開始元の IP アドレス。 | 送信元、<br>IP |
| SrcDvcIpAddr | IP アドレス | 77.138.103.108 | ネットワーク パケットに直接関連付けられていないデバイスの送信元 IP アドレス (プロバイダーによって収集されるか、明示的に計算されます)。 | 送信元、<br>デバイス<br>IP |
| SrcGeoLatitude | 緯度 (倍精度) | 44.475833 | 送信元 IP アドレスに関連付けられている地理的座標の緯度 | 送信元、<br>ジオ (主要地域) |
| SrcGeoLongitude | 経度 (倍精度) | -73.211944 | 送信元 IP アドレスに関連付けられている地理的座標の経度 | 送信元、<br>ジオ (主要地域) |
| SrcMacAddr | String | 06:10:9f:eb:8f:14 | 接続またはセッションの開始元のネットワーク インターフェイスの MAC アドレス。 | 送信元、<br>Mac |
| SrcDvcMacAddr | String | 06:10:9f:eb:8f:14 | ネットワーク パケットに直接関連付けられていないデバイスの送信元 MAC アドレス。 | 送信元、<br>デバイス<br>Mac |
| SrcPortNumber | Integer | 2335 | 接続元の IP ポート。 複数の接続を構成するセッションに関連しないことがあります。 | 送信元、<br>ポート |
| SrcGeoRegion | リージョン (文字列) | バーモント | 送信元 IP アドレスに関連付けられた国内のリージョン | 送信元、<br>ジオ (主要地域) |
| SrcResourceId | String | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | メッセージを生成しているデバイスのリソース ID。 | source |
| SrcNatIpAddr | IP アドレス | 4.3.2.1 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信先との通信に使用する IP アドレス。 | 送信元 NAT、<br>IP |
| SrcNatPortNumber | Integer | 345 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信先との通信に使用するポート。 | 送信元 NAT、<br>ポート |
| SrcUserSid | ユーザー ID (文字列) | S-15-1445 | セッションの送信元に関連付けられている識別子のユーザー ID。 通常、クライアントに対してアクションを実行するユーザー。 詳細については、「データ型」をご覧ください。 | 送信元、<br>User |
| SrcUserAadId | 文字列 (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | セッションの送信元側にいるユーザーの Azure AD アカウント オブジェクト ID | 送信元、<br>User |
| SrcUserName | ユーザー名 (文字列) | bob | セッションの送信元に関連付けられている識別子のユーザー名。 通常、クライアントに対してアクションを実行するユーザー。 詳細については、「データ型」をご覧ください。 | source<br>User |
| SrcUserUpn | string | bob@alice.com | セッションを開始するアカウントの UPN | 送信元、<br>User |
| SrcUserDomain | string | デスクトップ | セッションを開始するアカウントのドメイン | 送信元、<br>User |
| SrcZone | String | タップ | レポート デバイスにより定義された、送信元のネットワーク ゾーン。 | source |
| NetworkProtocol | String | TCP | 接続またはセッションで使用される IP プロトコル。 通常は、TCP、UDP、または ICMP | ネットワーク |
| CloudAppName | String | Facebook | プロキシによって識別される HTTP アプリケーションの送信先アプリケーションの名前。 | クラウド |
| CloudAppId | String | 124 | プロキシによって識別される HTTP アプリケーションの送信先アプリケーションの ID。 この値は、通常、使用されるプロキシに固有です。 | クラウド |
| CloudAppOperation | String | DeleteFile | プロキシによって識別される HTTP アプリケーションの送信先アプリケーションのコンテキストでユーザーが実行した操作。 この値は、通常、使用されるプロキシに固有です。 | クラウド |
| CloudAppRiskLevel | String | 3 | プロキシによって識別される HTTP アプリケーションに関連付けられているリスク レベル。 この値は、通常、使用されるプロキシに固有です。 | クラウド |
| FileName | 文字列型 | ImNotMalicious.exe | ファイル名情報を提供する FTP や HTTP などのプロトコルのネットワーク接続で送信されるファイル名。 | ファイル |
| FilePath | String | C:\Malicious\ImNotMalicious.exe | ファイル名を含んだ、ファイルの完全パス | ファイル |
| FileHashMd5 | String | 51BC68715FC7C109DCEA406B42D9D78F | プロトコルのネットワーク接続を介して送信されるファイルの MD5 ハッシュ値。 | ファイル |
| FileHashSha1 | String | 491AE3…C299821476F4 | プロトコルのネットワーク接続を介して送信されるファイルの SHA1 ハッシュ値。 | ファイル |
| FileHashSha256 | String | 9B8F8EDB…C129976F03 | プロトコルのネットワーク接続を介して送信されるファイルの SHA256 ハッシュ値。 | ファイル |
| FileHashSha512 | String | 5E127D…F69F73F01F361 | プロトコルのネットワーク接続を介して送信されるファイルの SHA512 ハッシュ値。 | ファイル |
| FileExtension |  String | exe | FTP や HTTP などのプロトコルのネットワーク接続を介して送信されるファイルの SHA256 ハッシュ値。 | ファイル
| FileMimeType | String | application/msword | FTP や HTTP などのプロトコルのネットワーク接続を介して送信されるファイルの MIME の種類 | ファイル |
| FileSize | Integer | 23500 | プロトコルのネットワーク接続を介して送信されるファイルのファイルサイズ (バイト単位)。 | ファイル |
| HttpVersion | String | 2.0 | HTTP/HTTPS ネットワーク接続の HTTP 要求バージョン。 | Http |
| HttpRequestMethod | String | GET | HTTP/HTTPS ネットワーク セッションの HTTP メソッド。 | Http |
| HttpStatusCode | String | 404 | HTTP/HTTPS ネットワーク セッションの HTTP 状態コード。 | Http |
| HttpContentType | String | multipart/form-data; boundary=something | HTTP/HTTPS ネットワーク セッションの HTTP 応答のコンテンツ タイプのヘッダー。 | Http |
| HttpReferrerOriginal | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS ネットワーク セッションの HTTP 参照元ヘッダー。 | Http |
| HttpUserAgentOriginal | String | Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML、like Gecko) Chrome/83.0.4103.97 Safari/537.36 | HTTP/HTTPS ネットワーク セッションの HTTP ユーザー エージェント ヘッダー。 | Http |
| HttpRequestXff | String | 120.12.41.1 | HTTP/HTTPS ネットワーク セッションの HTTP X-Forwarded-For ヘッダー。 | Http |
| UrlCategory | String | 検索エンジン | その内容 (成人向け、ニュース、広告、パーク ドメインなど) に関連する、URL の定義済みのグループ (または URL 内のドメインに基づいているだけの場合もあります) | url |
| UrlOriginal | String | https:// contoso.com/fo/?k=v&q=u#f | HTTP/HTTPS ネットワーク セッションの HTTP 要求 URL。 | url |
| UrlHostname | String | contoso.com | HTTP/HTTPS ネットワーク セッションの HTTP 要求 URL のドメイン部分。 | url |
| ThreatCategory | String | Trojan | IPS の Web Security Gateway などのセキュリティ システムによって識別され、このネットワーク セッションに関連付けられている脅威のカテゴリ。 | 脅威 |
| ThreatId | String | Tr.124 | IP の Web セキュリティゲートウェイなどのセキュリティシステムによって識別され、このネットワークセッションに関連付けられている脅威の ID。 | 脅威 |
| ThreatName | String | EICAR テスト ファイル | 識別された脅威またはマルウェアの名前 | 脅威 |
| AdditionalFields | 動的 (JSON バッグ) | {<br>Property1: "val1"、<br>Property2: "val2"<br>} | スキーマ内のそれぞれの列が一致しない場合、追加フィールドを JSON バッグに格納できます。<br>クエリ時の解析の場合、JSON へのデータのパッキングによりクエリのパフォーマンスが低下するため、この方法を使用しないことをお勧めします。 代わりに、追加の列のレベルを上げることをお勧めします。<br>今後の取り込み時の解析シナリオでは、この JSON バッグ列に追加データが収集されます。 | カスタム フィールド |
| 
