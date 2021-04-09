---
title: Azure Sentinel エンティティ型リファレンス | Microsoft Docs
description: この記事では、Azure Sentinel のエンティティ型とその必須識別子について説明します。
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456185"
---
# <a name="azure-sentinel-entity-types-reference"></a>Azure Sentinel エンティティ型リファレンス

## <a name="entity-types-and-identifiers"></a>エンティティ型と識別子

次の表では、現在 Azure Sentinel でマッピングに使用できる **エンティティ型** と、各エンティティ型の **識別子** として使用できる **属性** を示します。これは、[分析ルール ウィザード](tutorial-detect-threats-custom.md)の [エンティティ マッピング](map-data-fields-to-entities.md) セクションの **[識別子]** ドロップダウンの一覧に表示されます。

「**必須識別子**」列の各識別子は、そのエンティティを識別するために最低限必要なものです。 ただし、必須識別子だけでは、"*一意の*" ID を提供するのに十分ではない場合があります。 使用する識別子が多いほど、ID が一意になる可能性が高くなります。 1 つのエンティティ マッピングに最大で 3 つの識別子を使用できます。

最適な結果が得られるようにするには (保証された一意の識別)、可能な限り、「**最も強い識別子**」列の識別子を使用する必要があります。 強い識別子を複数使用すると、さまざまなデータ ソースやスキーマの強い識別子の相関関係が可能になります。 これにより、Azure Sentinel で特定のエンティティについてより包括的な分析情報を提供できます。

| エンティティ型 | 識別子 | 必須識別子 | 最も強い識別子 |
| - | - | - | - |
| [**ユーザー アカウント**](#user-account)<br>*(Account)* | 名前<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>名前<br>AadUserId<br>PUID<br>ObjectGuid | Name + NTDomain<br>Name + UPNSuffix<br>AADUserId<br>Sid |
| [**Host**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | HostName + NTDomain<br>HostName + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP アドレス**](#ip-address)<br>*(IP)* | Address | Address | |
| [**マルウェア**](#malware) | 名前<br>カテゴリ | 名前 | |
| [**ファイル**](#file) | ディレクトリ<br>名前 | 名前 | |
| [**Process**](#process) | ProcessId<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>ProcessId | |
| [**クラウド アプリケーション**](#cloud-application)<br>*(CloudApplication)* | AppId<br>名前<br>InstanceName | AppId<br>名前 | |
| [**ドメイン名**](#domain-name)<br>*(DNS)* | DomainName | DomainName | |
| [**Azure リソース**](#azure-resource) | ResourceId | ResourceId | |
| [**ファイル ハッシュ**](#file-hash)<br>*(FileHash)* | アルゴリズム<br>[値] | Algorithm + Value | |
| [**レジストリ キー**](#registry-key) | Hive<br>キー | Hive<br>キー | Hive + Key |
| [**レジストリ値**](#registry-value) | 名前<br>[値]<br>ValueType | 名前 | |
| [**セキュリティ グループ**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | url | url | |
| [**IoT デバイス**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>source<br>SourceRef<br>製造元<br>モデル<br>OperatingSystem<br>IpAddress<br>MacAddress<br>プロトコル<br>SerialNumber | IoTHub<br>deviceId | IoTHub + DeviceId |
| [**Mailbox**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Upn<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**メール クラスター**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>脅威<br>クエリ<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>source<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | クエリ<br>source | Query + Source |
| [**メール メッセージ**](#mail-message) | Recipient<br>Urls<br>脅威<br>送信者<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>サブジェクト<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>言語<br>ThreatDetectionMethods | NetworkMessageId<br>Recipient | NetworkMessageId + Recipient |
| [**送信メール**](#submission-mail) | SubmissionId<br>SubmissionDate<br>申請者<br>NetworkMessageId<br>Timestamp<br>Recipient<br>送信者<br>SenderIp<br>サブジェクト<br>ReportType | SubmissionId<br>NetworkMessageId<br>Recipient<br>申請者 |  |
|

## <a name="entity-type-schemas"></a>エンティティ型スキーマ

以下では、各エンティティ型の完全なスキーマの詳細について説明します。 これらのスキーマの多くには、他のエンティティ型へのリンクが含まれています。たとえば、ユーザー アカウント スキーマには、ホスト エンティティ型へのリンクが含まれています。これは、ユーザー アカウントの 1 つの属性は、それが定義されているホストであるということです。 これらの外部リンクされたエンティティは、エンティ ティマッピングのための識別子としては使用できませんが、エンティティ ページと調査グラフでエンティティの詳細を提供する場合に非常に便利です。

> [!NOTE]
> **型** 列の値の後に疑問符が付いている場合は、フィールドが null 許容であることを示します。

## <a name="user-account"></a>ユーザー アカウント

*エンティティ名: Account*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "account" |
| 名前 | String | アカウントの名前。 このフィールドには、ドメインが追加されていない名前のみを設定する必要があります。 |
| *FullName* | *N/A* | "*スキーマの一部ではありません。エンティティ マッピングの古いバージョンとの下位互換性のために含まれます。* "
| NTDomain | String | アラート形式 domain\username で表される NETBIOS ドメイン名。 例: Finance, NT AUTHORITY |
| DnsDomain | String | 完全修飾ドメイン DNS 名。 例: finance.contoso.com |
| UPNSuffix | String | アカウントのユーザー プリンシパル名サフィックス。 これはドメイン名の場合もあります。 例: contoso.com |
| Host | Entity | ローカル アカウントの場合は、アカウントが含まれているホスト。 |
| Sid | String | アカウントのセキュリティ識別子 (例: S-1-5-18)。 |
| AadTenantId | Guid? | Azure AD テナント ID (既知の場合)。 |
| AadUserId | Guid? | Azure AD アカウント オブジェクト ID (既知の場合)。 |
| PUID | Guid? | Azure AD Passport ユーザー ID (既知の場合)。 |
| IsDomainJoined | Bool? | これがドメイン アカウントであるかどうかを決定します。 |
| DisplayName | String | アカウントの表示名。 |
| ObjectGuid | Guid? | ObjectGUID 属性は、Active Directory によって割り当てられる、オブジェクトの一意識別子である単一値の属性です。 |
|

アカウント エンティティの強い識別子:

- Name + UPNSuffix
- AadUserId
- Sid + Host (ビルトイン アカウントの Sid に必要)
- Sid (ビルトイン アカウントの Sid を除く)
- Name + NTDomain (NTDomain がビルトイン ドメイン (たとえば "Workgroup") ではない場合)
- Name + Host (NTDomain がビルトイン ドメイン (たとえば "Workgroup") である場合)
- Name + DnsDomain
- PUID
- ObjectGuid

アカウント エンティティの弱い識別子:

- 名前

## <a name="host"></a>Host

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "host" |
| DnsDomain | String | このホストが属している DNS ドメイン。 既知の場合は、ドメインの完全な DNS サフィックスが含まれている必要があります。 |
| NTDomain | String | このホストが属している NT ドメイン。 |
| HostName | String | ドメイン サフィックスを除いたホスト名。 |
| *FullName* | *N/A* | "*スキーマの一部ではありません。エンティティ マッピングの古いバージョンとの下位互換性のために含まれます。* "
| NetBiosName | String | ホスト名 (Windows 2000 より前)。 |
| IoTDevice | Entity | IoT デバイス エンティティ (このホストが IoT デバイスを表している場合)。 |
| AzureID | String | VM の Azure リソース ID (既知の場合)。 |
| OMSAgentID | String | OMS エージェント ID (ホストに OMS エージェントがインストールされている場合)。 |
| OSFamily | Enum? | 次のいずれかの値です。 <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | String | オペレーティング システムの自由記載表現。<br>このフィールドは、OSFamily より細かい特定のバージョン、または OSFamily 列挙型でサポートされていない将来の値を保持することを意図しています。 |
| IsDomainJoined | Bool | このホストがドメインに属しているかどうかを指定します。 |
|

ホスト エンティティの強い識別子:
- HostName + NTDomain
- HostName + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (エンティティ マッピングではサポートされていません)

ホスト エンティティの弱い識別子:
- HostName
- NetBiosName

## <a name="ip-address"></a>IP アドレス

*エンティティ名: IP*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "ip" |
| Address | String | 文字列としての IP アドレス (例: 127.0.0.1、IPv4 または IPv6 のいずれか)。 |
| 場所 | GeoLocation | IP エンティティに関連付けられている地理的な場所のコンテキスト。 |
|

IP エンティティの強い識別子:
- Address

## <a name="malware"></a>マルウェア

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "malware" |
| 名前 | String | ベンダーによるマルウェアの名前 (例: `Win32/Toga!rfn`)。 |
| カテゴリ | String | ベンダーによるマルウェアのカテゴリ (例: Trojan)。 |
| ファイル | リスト\<Entity> | マルウェアが検出された、リンクされたファイル エンティティのリスト。 ファイル エンティティをインラインまたは参照として含めることができます。<br>構造の詳細については、ファイル エンティティを参照してください。 |
| プロセス | リスト\<Entity> | マルウェアが検出された、リンクされたプロセス エンティティのリスト。 これは、ファイルレス アクティビティでアラートがトリガーされたときによく使用されます。<br>構造の詳細については、[Process](#process) エンティティを参照してください。 |
|

マルウェア エンティティの強い識別子:

- Name + Category

## <a name="file"></a>ファイル

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "file" |
| ディレクトリ | String | ファイルへの完全なパスです。 |
| 名前 | String | パスを除いたファイル名 (一部のアラートにはパスが含まれない場合があります)。 |
| Host | Entity | ファイルが格納されたホスト。 |
| FileHashes | List&lt;Entity&gt; | このファイルに関連付けられているファイル ハッシュ。 |
|

ファイル エンティティの強い識別子:
- Name + Directory
- Name + FileHash
- Name + Directory + FileHash

## <a name="process"></a>Process

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "process" |
| ProcessId | String | プロセス ID。 |
| CommandLine | String | プロセスを作成するために使用されるコマンド ライン。 |
| ElevationToken | Enum? | プロセスに関連付けられた昇格トークン。<br>指定できる値<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | プロセスの実行が開始された日時。 |
| ImageFile | Entity (File) | ファイル エンティティをインラインまたは参照として含めることができます。<br>構造の詳細については、ファイル エンティティを参照してください。 |
| Account | Entity | プロセスが実行されているアカウント。<br>[Account](#user-account) エンティティをインラインまたは参照として含めることができます。<br>構造の詳細については、[Account](#user-account) エンティティを参照してください。 |
| ParentProcess | Entity (Process) | 親プロセス エンティティ。 <br>部分的なデータ (つまり、PID だけ) を含めることができます。 |
| Host | Entity | プロセスが実行されていたホスト。 |
| LogonSession | Entity (HostLogonSession) | セッションが実行されていたホスト。 |
|

プロセス エンティティの強い識別子:

- Host + ProcessId + CreationTimeUtc
- Host + ParentProcessId + CreationTimeUtc + CommandLine
- Host + ProcessId + CreationTimeUtc + ImageFile
- Host + ProcessId + CreationTimeUtc + ImageFile.FileHash

プロセス エンティティの弱い識別子:

- ProcessId + CreationTimeUtc + CommandLine (Host はなし)
- ProcessId + CreationTimeUtc + ImageFile (Host はなし)

## <a name="cloud-application"></a>クラウド アプリケーション

*エンティティ名: CloudApplication*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "cloud-application" |
| AppId | int | アプリケーションの技術的 ID。 これは、[クラウド アプリケーション識別子](#cloud-application-identifiers)のリストで定義されている値のいずれかである必要があります。 AppId フィールドの値は省略可能です。 |
| 名前 | String | 関連するクラウド アプリケーションの名前。 アプリケーション名の値は省略可能です。 |
| InstanceName | String | クラウド アプリケーションのユーザー定義のインスタンス名。 多くの場合、顧客が持っている同じ種類の複数のアプリケーションを区別するために使用されます。 |
|

クラウド アプリケーション エンティティの強い識別子:
 - AppId (InstanceName なし)
 - Name (InstanceName なし)
 - AppId + InstanceName
 - Name + InstanceName

## <a name="domain-name"></a>ドメイン名

*エンティティ名: DNS*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "dns" |
| DomainName | String | アラートに関連付けられている DNS レコードの名前。 |
| IpAddress | List&lt;Entity (IP)&gt; | 解決された IP アドレスに対応するエンティティ。 |
| DnsServerIp | Entity (IP) | 要求を解決する DNS サーバーを表すエンティティ。 |
| HostIpAddress | Entity (IP) | DNS 要求クライアントを表すエンティティ。 |
|

DNS エンティティの強い識別子:
- DomainName + DnsServerIp + HostIpAddress

DNS エンティティの弱い識別子:
- DomainName + HostIpAddress

## <a name="azure-resource"></a>Azure リソース

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "azure-resource" |
| ResourceId | String | リソースの Azure リソース ID。 |
| SubscriptionId | String | リソースのサブスクリプション ID。 |
| TryGetResourceGroup | Bool | リソース グループの値 (存在する場合)。 |
| TryGetProvider | Bool | プロバイダーの値 (存在する場合)。 |
| TryGetName | Bool | 名前の値 (存在する場合)。 |
|

Azure リソース エンティティの強い識別子:
- ResourceId

## <a name="file-hash"></a>ファイル ハッシュ

*エンティティ名: FileHash*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "filehash" |
| アルゴリズム | 列挙型 | ハッシュ アルゴリズムの種類。 指定できる値<li>Unknown<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| [値] | String | ハッシュ値。 |
|

ファイル ハッシュ エンティティの強い識別子:
- Algorithm + Value

## <a name="registry-key"></a>レジストリ キー

*エンティティ名: RegistryKey*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "registry-key" |
| Hive | Enum? | 次のいずれかの値です。<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| キー | String | レジストリ キーのパス |
|

レジストリ キー エンティティの強い識別子:
- Hive + Key

## <a name="registry-value"></a>レジストリ値

*エンティティ名: RegistryValue*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "registry-value" |
| キー | Entity (RegistryKey) | レジストリ キーのエンティティ。 |
| 名前 | String | レジストリ値の名前。 |
| [値] | String | 値データの文字列形式の表現。 |
| ValueType | Enum? | 次のいずれかの値です。<li>String<li>Binary<li>DWord<li>Qword<li>MultiString<li>ExpandString<li>なし<li>Unknown<br>値は、Microsoft.Win32.RegistryValueKind 列挙型に準拠している必要があります。 |
|

レジストリ値エンティティの強い識別子:
- Key + Name

レジストリ値エンティティの弱い識別子:
- Name (Key なし)

## <a name="security-group"></a>セキュリティ グループ

*エンティティ名: SecurityGroup*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "security-group" |
| DistinguishedName | String | グループの識別名。 |
| SID | String | SID 属性は、グループのセキュリティ識別子 (SID) を指定する単一値の属性です。 |
| ObjectGuid | Guid? | ObjectGUID 属性は、Active Directory によって割り当てられる、オブジェクトの一意識別子である単一値の属性です。 |
|

セキュリティ グループ エンティティの強い識別子:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "url" |
| url | Uri | エンティティが指している完全な URL。 |
|

URL エンティティの強い識別子:
- Url (絶対 URL の場合)

URL エンティティの弱い識別子:
- Url (相対 URL の場合)

## <a name="iot-device"></a>IoT デバイス

*エンティティ名: IoTDevice*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "iotdevice" |
| IoTHub | Entity (AzureResource) | デバイスが属している IoT Hub を表す AzureResource エンティティ。 |
| deviceId | String | IoT Hub のコンテキストでのデバイスの ID。 |
| DeviceName | String | デバイスのフレンドリ名。 |
| IoTSecurityAgentId | Guid? | デバイスで実行されている *Defender for IoT* エージェントの ID。 |
| DeviceType | String | デバイスの種類 (例: "temperature sensor"、"freezer"、"wind turbine")。 |
| source | String | デバイス エンティティのソース (Microsoft/Vendor)。 |
| SourceRef | Entity (Url) | デバイスが管理されているソース項目への URL 参照。 |
| 製造元 | String | デバイスの製造元。 |
| モデル | String | デバイスのモデル。 |
| OperatingSystem | String | デバイスで実行されているオペレーティング システム。 |
| IpAddress | Entity (IP) | デバイスの現在の IP アドレス。 |
| MacAddress | String | デバイスの MAC アドレス。 |
| プロトコル | List&lt;String&gt; | デバイスでサポートされているプロトコルのリスト。 |
| SerialNumber | String | デバイスのシリアル番号。 |
|

IoT デバイス エンティティの強い識別子:
- IoTHub + DeviceId

IoT デバイス エンティティの弱い識別子:
- DeviceId (IoTHub なし)

## <a name="mailbox"></a>Mailbox

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "mailbox" |
| MailboxPrimaryAddress | String | メールボックスのプライマリ アドレス。 |
| DisplayName | String | メールボックスの表示名。 |
| Upn | String | メールボックスの UPN。 |
| RiskLevel | Enum? | このメールボックスのリスク レベル。 指定できる値<li>なし<li>低<li>Medium<li>高 |
| ExternalDirectoryObjectId | Guid? | メールボックスの AzureAD 識別子。 Account エンティティの AadUserId に似ていますが、このプロパティは Office 側でのメールボックス オブジェクトに固有です。 |
|

メールボックス エンティティの強い識別子:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>メール クラスター

*エンティティ名: MailCluster*

> [!NOTE]
> **MDO** = **Microsoft Defender for Office 365**、旧称 Office 365 Advanced Threat Protection (O365 ATP)。

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "mail-cluster" |
| NetworkMessageIds | IList&lt;String&gt; | メール クラスターの一部であるメール メッセージ ID。 |
| CountByDeliveryStatus | IDictionary&lt;String,Int&gt; | DeliveryStatus 文字列表現別のメール メッセージの数。 |
| CountByThreatType | IDictionary&lt;String,Int&gt; | ThreatType 文字列表現別のメール メッセージの数。 |
| CountByProtectionStatus | IDictionary&lt;String,long&gt; | Threat Protection の状態別のメール メッセージの数。 |
| 脅威 | IList&lt;String&gt; | メール クラスターの一部であるメール メッセージの脅威。 |
| クエリ | String | メール クラスターのメッセージの識別に使用されたクエリ。 |
| QueryTime | DateTime? | クエリの日時。 |
| MailCount | Int? | メール クラスターの一部であるメール メッセージの数。 |
| IsVolumeAnomaly | Bool? | これがボリューム異常メール クラスターかどうかを示します。 |
| source | String | メール クラスターのソース (既定値は "O365 ATP")。 |
| ClusterSourceIdentifier | String | このメール クラスターのソースであるメールのネットワーク メッセージ ID。 |
| ClusterSourceType | String | メール クラスターのソースの種類。 これは、MDO の MailClusterSourceType の設定に対応します (上記のメモを参照)。 |
| ClusterQueryStartTime | DateTime? | クラスター開始日時 - クラスター カウント クエリの開始日時として使用されます。 通常、終了日時から MDO の DaysToLookBack の設定を引いたものの日付 (上のメモを参照)。 |
| ClusterQueryEndTime | DateTime? | クラスター終了日時 - クラスター カウント クエリの終了日時として使用されます。 通常、メール データの受信日時。 |
| ClusterGroup | String | MDO で使用される Kusto クエリ キーに対応します (上のメモを参照)。 |
|

メール クラスター エンティティの強い識別子:
- Query + Source

## <a name="mail-message"></a>メール メッセージ

*エンティティ名: MailMessage*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "mail-message" |
| ファイル | IList&lt;File&gt; | このメール メッセージの添付ファイルのファイル エンティティ。 |
| Recipient | String | このメール メッセージの受信者。 複数の受信者がいる場合、メール メッセージがコピーされ、コピーごとに 1 人の受信者になります。 |
| Urls | IList&lt;String&gt; | このメール メッセージに含まれている URL。 |
| 脅威 | IList&lt;String&gt; | このメール メッセージに含まれている脅威。 |
| 送信者 | String | 送信者の電子メール アドレス。 |
| P1Sender | String | "P2 (プライマリ) ユーザーの代わりに" このメールを送信した (委任された) ユーザーのメール ID。 委任によって送信されていないメールの場合、この値は P2Sender と等しくなります。 |
| P1SenderDisplayName | String | "P2 (プライマリ) ユーザーの代わりに" このメールを送信した (委任された) ユーザーの表示名。 メール ヘッダーでは "OnbehalfofSenderDisplayName" プロパティによって表されます。 |
| P1SenderDomain | String | "P2 (プライマリ) ユーザーの代わりに" このメールを送信した (委任された) ユーザーのメール ドメイン。 委任によって送信されていないメールの場合、この値は P2SenderDomain と等しくなります。 |
| P2Sender | String | このメールが代わりに送信された (プライマリ) ユーザーのメール。 |
| P2SenderDisplayName | String | このメールが代わりに送信された (プライマリ) ユーザーの表示名。 メールが委任によって送信されなかった場合、これは送信者の表示名を表します。 |
| P2SenderDomain | String | このメールが代わりに送信された (プライマリ) ユーザーのメール ドメイン。 メールが委任によって送信されなかった場合、これは送信者のドメインを表します。 |
| SenderIP | String | 送信者の IP アドレス。 |
| ReceivedDate | DateTime | このメッセージの受信日時。 |
| NetworkMessageId | Guid? | このメール メッセージのネットワーク メッセージ ID。 |
| InternetMessageId | String | このメール メッセージのインターネット メッセージ ID。 |
| サブジェクト | String | このメール メッセージの件名。 |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | 一致または類似するメール メッセージを検索するため、Microsoft Defender for Office 365 によって使用されます。 |
| AntispamDirection | Enum? | このメール メッセージの方向。 指定できる値<li>Unknown<li>受信<li>送信<li>Intraorg (内部) |
| DeliveryAction | Enum? | このメール メッセージの配信アクション。 指定できる値<li>Unknown<li>DeliveredAsSpam<li>配信済み<li>［ブロック済み］<li>置換後 |
| DeliveryLocation | Enum? | このメール メッセージの配信場所。 指定できる値<li>Unknown<li>Inbox<li>JunkFolder<li>DeletedFolder<li>検疫<li>外部<li>失敗<li>Dropped<li>Forwarded |
| Language | String | メールの内容が書き込まれる言語。 |
| ThreatDetectionMethods | IList&lt;String&gt; | このメールに適用される脅威検出方法のリスト。 |
|

メール メッセージ エンティティの強い識別子:
- NetworkMessageId + Recipient

## <a name="submission-mail"></a>送信メール

*エンティティ名: SubmissionMail*

| フィールド | Type | 説明 |
| ----- | ---- | ----------- |
| Type | String | "SubmissionMail" |
| SubmissionId | Guid? | 申請 ID。 |
| SubmissionDate | DateTime? | この申請が報告された日時。 |
| 申請者 | String | 申請者のメール アドレス。 |
| NetworkMessageId | Guid? | 申請が属しているメールのネットワーク メッセージ ID。 |
| Timestamp | DateTime? | メッセージが受信されたときのタイムスタンプ (メール)。 |
| Recipient | String | メールの受信者。 |
| 送信者 | String | メールの送信者。 |
| SenderIp | String | 送信者の IP。 |
| サブジェクト | String | 申請メールの件名。 |
| ReportType | String | 特定のインスタンスの申請の種類。 これは、Junk、Phish、Malware、または NotJunk に対応します。 |
|

SubmissionMail エンティティの強い識別子:
- SubmissionId、Submitter、NetworkMessageId、Recipient

## <a name="cloud-application-identifiers"></a>クラウド アプリケーションの識別子

次の一覧では、既知のクラウド アプリケーションの識別子を定義します。 アプリ ID の値は、[クラウド アプリケーション](#cloud-application)のエンティティ識別子として使用されます。

|アプリ ID|名前|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|ボックス|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|cornerstone ondemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive Software|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|アマゾン ウェブ サービス|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive for Business|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk Fusion Lifecycle|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype for Business|
|25988|Google Docs|
|26055|Microsoft Office 365 管理センター|
|26060|OPSWAT Gears|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Google ドライブ|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft フォーム|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS Proxy Emulator|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel でのエンティティの構造、識別子、およびスキーマについて説明しました。

[エンティティ](entities-in-azure-sentinel.md)と[エンティティ マッピング](map-data-fields-to-entities.md)に関する詳細をご確認ください。 
