---
title: "Operations Management Suite (OMS) の Office 365 ソリューション | Microsoft Docs"
description: "この記事では、OMS での Office 365 ソリューションの構成と使用について詳しく説明します。  Log Analytics で作成された Office 365 レコードの詳細な説明が含まれています。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: dcc44986acbb76eafc3cfacb79acf237802de021
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>Operations Management Suite (OMS) の Office 365 ソリューション

![Office 365 のロゴ](media/oms-solution-office-365/icon.png)

Operations Management Suite (OMS) 用の Office 365 ソリューションでは、Log Analytics で Office 365 環境を監視できます。  

- Office 365 アカウント上でのユーザー アクティビティを監視し、使用パターンを分析したり、行動傾向を識別したりします。 たとえば、組織の外で共有されるファイルや、最も人気のある SharePoint サイトといった特定の使用シナリオを抽出することができます。
- 管理者のアクティビティを監視し、構成変更や高権限操作を追跡します。
- 不必要なユーザーの行動を検出および調査します。これは、組織のニーズに合わせてカスタマイズできます。
- 監査とコンプライアンスを実証します。 たとえば、機密ファイルに対するファイル アクセス操作を監視でき、これは監査とコンプライアンスのプロセスに役立ちます。
- 組織の Office 365 アクティビティ データに対して OMS 検索を使用し、運用上のトラブルシューティングを実行します。

## <a name="prerequisites"></a>前提条件
このソリューションをインストールして構成する前に、次のものが必要です。

- 組織の Office 365 サブスクリプション。
- グローバル管理者であるユーザー アカウントの資格情報。
- 監査データを受信するには、Office 365 サブスクリプションで[監査を構成する](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)必要があります。  [メールボックスの監査](https://technet.microsoft.com/library/dn879651.aspx)は個別に構成されることに注意してください。  監査が構成されていない場合でも、ソリューションをインストールしてその他のデータを収集することは可能です。
 


## <a name="management-packs"></a>管理パック
このソリューションでは、接続されている管理グループに管理パックがインストールされることはありません。
  

## <a name="configuration"></a>構成
[Office 365 ソリューションをサブスクリプションに追加](../log-analytics/log-analytics-add-solutions.md)したら、ソリューションを Office 365 サブスクリプションに接続する必要があります。

1. [ソリューションの追加](../log-analytics/log-analytics-add-solutions.md)に関するページの手順に従って、アラート管理ソリューションを OMS ワークスペースに追加します。
2. OMS ポータルで **[Settings] \(設定)** に進みます。
3. **[Connected Sources] \(接続済みソース)** で、**[Office 365]** を選択します。
4. **[Connect Office 365] \(Office 365 を接続)** をクリックします。<br>![Office 365 を接続する](media/oms-solution-office-365/configure.png)
5. サブスクリプションのグローバル管理者であるアカウントで Office 365 にサインインします。 
6. ソリューションが監視するワークロードと共に、サブスクリプションが一覧表示されます。<br>![Office 365 を接続する](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>データ収集
### <a name="supported-agents"></a>サポートされているエージェント
Office 365 ソリューションは、どの [OMS エージェント](../log-analytics/log-analytics-data-sources.md)からもデータを取得しません。  Office 365 から直接データを取得します。

### <a name="collection-frequency"></a>収集の頻度
レコードが作成されるたびに、Office 365 は [webhook 通知](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications)と詳細なデータを Log Analytics に送信します。

## <a name="using-the-solution"></a>ソリューションの使用
OMS ワークスペースに Office 365 ソリューションを追加すると、OMS ダッシュボードに **[Office 365]** タイルが追加されます。 このタイルには、ご利用の環境におけるコンピューターの数と更新プログラムの対応状態が数字とグラフで表示されます。<br><br>
![Office 365 の概要タイル](media/oms-solution-office-365/tile.png)  

**[Office 365]** タイルをクリックして **[Office 365]** ダッシュボードを開きます。

![Office 365 ダッシュボード](media/oms-solution-office-365/dashboard.png)  

ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定のスコープと時間範囲について、その列の基準に該当するアラート数の上位 10 件が表示されます。 ログ検索を実行してアラート全件を取得するには、列の一番下にある [See all] \(すべて表示) をクリックするか、列ヘッダーをクリックします。

| 分割 | Description |
|:--|:--|
| [操作] | すべての監視対象 Office 365 サブスクリプションから、アクティブ ユーザーに関する情報を提供します。 時間の経過と共に発生するアクティビティの数を見ることもできます。
| Exchange | Add-Mailbox Permission、または Set-Mailbox などの Exchange Server アクティビティの内訳を示します。 |
| SharePoint | SharePoint ドキュメントに対してユーザーが実行する最上位のアクティビティを示します。 このタイルからドリル ダウンすると、ターゲット ドキュメントやこのアクティビティの場所など、これらのアクティビティの詳細が検索ページに表示されます。 たとえば、File Accessed イベントの場合、アクセスされているドキュメント、それと関連付けられたアカウント名、および IP アドレスを見ることができます。 |
| Azure Active Directory | ユーザー パスワードのリセットやログイン試行など、最上位のユーザー アクティビティが含まれます。 ドリルダウンすると、結果の状態など、これらのアクティビティの詳細を見ることができます。 これは主に、Azure Active Directory 上の不審なアクティビティを監視する場合に便利です。 |




## <a name="log-analytics-records"></a>Log Analytics のレコード

Log Analytics ワークスペースで Office 365 ソリューションによって作成されたすべてのレコードは、**型** が **OfficeActivity** です。  **OfficeWorkload**プロパティは、レコードが参照する Office 365 サービス (Exchange、AzureActiveDirectory、SharePoint、または OneDrive) を決定します。  **RecordType** プロパティは操作の種類を指定します。  プロパティは操作の種類ごとに異なり、次の表に示しています。

### <a name="common-properties"></a>共通のプロパティ
次のプロパティは、Office 365 のすべてのレコードに共通です。

| プロパティ | 説明 |
|:--- |:--- |
| 型 | *OfficeActivity* |
| ClientIP | アクティビティが記録されたときに使用されたデバイスの IP アドレス。 IP アドレスは IPv4 または IPv6 アドレスの形式で表示されます。 |
| OfficeWorkload | レコードが参照する Office 365 サービス。<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| 操作 | ユーザーまたは管理者アクティビティの名前。  |
| OrganizationId | 組織の Office 365 テナントの GUID。 どの Office 365 サービスで発生するかにかかわらず、この値は組織に対して常に同じになります。 |
| RecordType | 実行する操作の種類。 |
| ResultStatus | (Operation プロパティで指定された) アクションが正常に終了したかどうかを示します。 値は Succeeded、PartiallySucceded、Failed のいずれかです。 Exchange 管理者アクティビティの場合、値は True または False です。 |
| UserId | レコードがログに記録される結果になったアクションを実行したユーザーの UPN (ユーザー プリンシパル名)。たとえば、my_name@my_domain_name。 (SHAREPOINT\system や NTAUTHORITY\SYSTEM などの) システム アカウントによって実行されるアクティビティのレコードも含まれることに注意してください。 | 
| UserKey | UserId プロパティで識別されるユーザーの代替 ID。  たとえば、SharePoint、OneDrive for Business、および Exchange でユーザーによって実行されたイベントの Passport 一意識別子 (PUID) が、このプロパティの値になります。 このプロパティは、他のサービスで発生するイベントや、システム アカウントによって実行されるイベントの UserID プロパティと同じ値を指定する場合もあります。|
| UserType | 操作を実行したユーザーの種類。<br><br>[Admin]<br>アプリケーション<br>DcAdmin<br>通常 <br>予約済み<br>ServicePrincipal<br>システム |


### <a name="azure-active-directory-base"></a>Azure Active Directory ベース
次のプロパティは、Azure Active Directory のすべてのレコードに共通です。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD イベントの種類。 |
| ExtendedProperties | Azure AD イベントの拡張プロパティ。 |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory アカウント ログオン
これらのレコードは、Active Directory のユーザーがログオンを試みたときに作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| アプリケーション | アカウント ログイン イベントをトリガーするアプリケーション (Office 15 など)。 |
| クライアント | アカウント ログイン イベントで使用されたクライアント デバイス、デバイス OS、およびデバイス ブラウザーの詳細。 |
| LoginStatus | このプロパティは OrgIdLogon.LoginStatus に直接由来します。 アルゴリズムを変えることにより、さまざまな興味深いログオン失敗のマッピングを実行できます。 |
| UserDomain | テナント ID 情報 (TII)。 | 


### <a name="azure-active-directory"></a>Azure Active Directory
これらのレコードは、変更または追加が Azure Active Directory オブジェクトに行われたときに作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | (Operation プロパティによって識別される) アクションの実行対象だったユーザー。 |
| アクター | アクションを実行したユーザーまたはサービス プリンシパル。 |
| ActorContextId | アクターが所属する組織の GUID。 |
| ActorIpAddress | アクターの IP アドレス。IPv4 または IPv6 アドレスの形式。 |
| InterSystemsId | Office 365 サービス内のコンポーネント間でアクションを追跡する GUID。 |
| IntraSystemId |   アクションを追跡するために Azure Active Directory によって生成される GUID。 |
| SupportTicketId | "代理操作" の状況におけるアクションのカスタマー サポート チケット ID。 |
| TargetContextId | ターゲットのユーザーが所属する組織の GUID。 |


### <a name="data-center-security"></a>データ センター セキュリティ
これらのレコードは、データ センター セキュリティの監査データから作成されます。  

| プロパティ | Description |
|:--- |:--- |
| EffectiveOrganization | 昇格/コマンドレットのターゲットだったテナントの名前。 |
| ElevationApprovedTime | 昇格が承認されたときのタイムスタンプ。 |
| ElevationApprover | Microsoft マネージャーの名前。 |
| ElevationDuration | 昇格がアクティブだった期間。 |
| ElevationRequestId |  昇格要求の一意識別子。 |
| ElevationRole | 昇格が要求されたロール。 |
| ElevationTime | 昇格の開始時刻。 |
| Start_Time | コマンドレット実行の開始時刻。 |


### <a name="exchange-admin"></a>Exchange 管理者
これらのレコードは、Exchange 構成が変更されたときに作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  組織内のユーザー、Microsoft のデータセンター担当者またはデータセンター サービス アカウント、委任された管理者のいずれによってコマンドレットが実行されたかを指定します。 False の値は、組織内の人物によってコマンドレットが実行されたことを示します。 True の値は、データセンター担当者、データセンター サービス アカウント、または委任管理者によってコマンドレットが実行されたことを示します。 |
| ModifiedObjectResolvedName |  コマンドレットによって変更されたオブジェクトのユーザー フレンドリ名。 コマンドレットがオブジェクトを変更する場合にのみ記録されます。 |
| OrganizationName | テナントの名前。 |
| OriginatingServer | コマンドレットの実行元だったサーバーの名前。 |
| parameters | Operations プロパティで識別されるコマンドレットと共に使用されたすべてのパラメーターの名前と値。 |


### <a name="exchange-mailbox"></a>Exchange メールボックス
これらのレコードは、変更または追加が Exchange メールボックスに行われたときに作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | ブラウザーのバージョン、Outlook のバージョン、モバイル デバイス情報など、操作を実行するために使用された電子メール クライアントに関する情報。 |
| Client_IPAddress | 操作が記録されたときに使用されたデバイスの IP アドレス。 IP アドレスは IPv4 または IPv6 アドレスの形式で表示されます。 |
| ClientMachineName | Outlook クライアントをホストするマシン名。 |
| ClientProcessName | メールボックスへのアクセスに使用された電子メール クライアント。 |
| ClientVersion | 電子メール クライアントのバージョン。 |
| InternalLogonType | 内部使用のために予約されています。 |
| Logon_Type | メールボックスにアクセスし、記録された操作を実行したユーザーの種類を示します。 |
| LogonUserDisplayName |    操作を実行したユーザーのユーザー フレンドリ名。 |
| LogonUserSid | 操作を実行したユーザーの SID。 |
| MailboxGuid | アクセスされたメールボックスの Exchange GUID。 |
| MailboxOwnerMasterAccountSid | メールボックス所有者アカウントのマスター アカウント SID。 |
| MailboxOwnerSid | メールボックス所有者の SID。 |
| MailboxOwnerUPN | アクセスされたメールボックスを所有する人物の電子メール アドレス。 |


### <a name="exchange-mailbox-audit"></a>Exchange メールボックス監査
これらのレコードは、メールボックス監査エントリが作成されるときに作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| 項目 | 操作が実行された対象の項目を表します。 | 
| SendAsUserMailboxGuid | その名前で電子メールを送信するためにアクセスされたメールボックスの Exchange GUID。 |
| SendAsUserSmtp | 偽装されているユーザーの SMTP アドレス。 |
| SendonBehalfOfUserMailboxGuid | 代理でメールを送信するためにアクセスされたメールボックスの Exchange GUID。 |
| SendOnBehalfOfUserSmtp | その代理でメールが送信されるユーザーの SMTP アドレス。 |


### <a name="exchange-mailbox-audit-group"></a>Exchange メールボックス監査グループ
これらのレコードは、変更または追加が Exchange グループに行われたときに作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | グループ内の各項目に関する情報。 |
| CrossMailboxOperations | 複数のメールボックスが操作に関係したかどうかを示します。 |
| DestMailboxId | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックス GUID を指定します。 |
| DestMailboxOwnerMasterAccountSid | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックス所有者のマスター アカウント SID の SID を指定します。 |
| DestMailboxOwnerSid | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックスの SID を指定します。 |
| DestMailboxOwnerUPN | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックスの所有者の UPN を指定します。 |
| DestFolder | 移動などの操作の宛先フォルダー。 |
| フォルダー | 項目のグループが位置しているフォルダー。 |
| フォルダー |     操作に関係したソース フォルダーに関する情報。たとえば、フォルダーが選択後に削除されるかどうか。 |


### <a name="sharepoint-base"></a>SharePoint ベース
これらのプロパティは、SharePoint のすべてのレコードに共通です。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | SharePoint で発生したイベントを識別します。 値は SharePoint または ObjectModel です。 |
| ItemType | アクセスまたは変更されたオブジェクトの種類。 オブジェクトの種類の詳細については、ItemType の表を参照してください。 |
| MachineDomainInfo | デバイス同期操作に関する情報。 この情報は、要求に存在する場合にのみ報告されます。 |
| MachineId |   デバイス同期操作に関する情報。 この情報は、要求に存在する場合にのみ報告されます。 |
| Site_ | ユーザーがアクセスするファイルまたはフォルダーがあるサイトの GUID。 |
| Source_Name | 監査対象の操作をトリガーしたエンティティ。 値は SharePoint または ObjectModel です。 |
| UserAgent | ユーザーのクライアントまたはブラウザーに関する情報。 この情報は、クライアントまたはブラウザーによって提供されます。 |


### <a name="sharepoint-schema"></a>SharePoint スキーマ
これらのレコードは、SharePoint の構成変更が行われたときに作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | カスタム イベントに関する省略可能文字列。 |
| Event_Data |  カスタム イベントに関する省略可能ペイロード。 |
| ModifiedProperties | サイトまたはサイト コレクション管理者グループのメンバーとしてユーザーを追加するなどの管理者イベントに含まれるプロパティ。 プロパティには、変更されたプロパティの名前 (サイト管理者グループなど)、変更されたプロパティの新しい値 (サイト管理者として追加されたユーザーなど)、変更されたオブジェクトの以前の値が含まれます。 |


### <a name="sharepoint-file-operations"></a>SharePoint ファイル操作
これらのレコードは、SharePoint でのファイル操作に応答して作成されます。

| プロパティ | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | コピーまたは移動されるファイルのファイル拡張子。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| DestinationFileName | コピーまたは移動されるファイルの名前。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| DestinationRelativeUrl | ファイルのコピーまたは移動先フォルダーの URL。 SiteURL、DestinationRelativeURL、および DestinationFileName パラメーターの値の組み合わせは、(コピーされたファイルの完全パス名である) ObjectID プロパティの値と同じです。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| SharingType | リソースの共有相手だったユーザーに割り当てられていた共有アクセス許可の種類。 このユーザーは UserSharedWith パラメーターによって識別されます。 |
| Site_Url | ユーザーがアクセスするファイルまたはフォルダーがあるサイトの URL。 |
| SourceFileExtension | ユーザーがアクセスしたファイルのファイル拡張子。 アクセスしたオブジェクトがフォルダーの場合、このプロパティは空です。 |
| SourceFileName |  ユーザーがアクセスしたファイルまたはフォルダーの名前。 |
| SourceRelativeUrl | ユーザーがアクセスするファイルが含まれているフォルダーの URL。 SiteURL、SourceRelativeURL、および SourceFileName パラメーターの値の組み合わせは、(ユーザーがアクセスするファイルの完全パス名である) ObjectID プロパティの値と同じです。 |
| UserSharedWith |  リソースの共有相手だったユーザー。 |




## <a name="sample-log-searches"></a>サンプル ログ検索
次の表は、このソリューションによって収集された更新レコードを探すログ検索の例です。

| クエリ | Description |
| --- | --- |
|Office 365 サブスクリプションでのすべての操作のカウント |`Type = OfficeActivity | measure count() by Operation` |
|SharePoint サイトの使用率|`Type=OfficeActivity OfficeWorkload=sharepoint | measure count() as Count by SiteUrl | sort Count asc`|
|ユーザーの種類別のファイル アクセス操作|`Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed | measure count() by UserType`|
|特定のキーワードで検索する|`Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"`|
|Exchange 上の外部アクションを監視する|`Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true`|



## <a name="troubleshooting"></a>トラブルシューティング

Office 365 ソリューションで期待どおりにデータが収集されない場合、**[Settings] \(設定)** -> **[Connected Sources] \(接続済みソース)** -> **[Office 365]** から OMS ポータルにアクセスし、ソリューションの状態を確認します。 次の表は、それぞれの状態の説明です。

| 状態 | Description |
|:--|:--|
| アクティブ | Office 365 サブスクリプションがアクティブであり、ワークロードは OMS ワークスペースに正常に接続しています。 |
| Pending | Office 365 サブスクリプションはアクティブですが、ワークロードはまだ OMS ワークスペースに正常に接続していません。 Office 365 サブスクリプションを初めて接続するとき、すべてのワークロードは、正常に接続されるまでの間はこの状態になります。 すべてのワークロードがアクティブに切り替わるまで、最大 24 時間かかります。 |
| 非アクティブ | Office 365 サブスクリプションは非アクティブ状態です。 Office 365 管理ページで詳細を確認してください。 Office 365 サブスクリプションをアクティブ化した後、データの受信を開始するには、OMS ワークスペースからサブスクリプションをリンク解除して再度リンクします。 |



## <a name="next-steps"></a>次のステップ
* [Log Analytics](../log-analytics/log-analytics-log-searches.md) でログ検索を使用して、詳細な更新プログラムデータを確認します。
* [独自のダッシュボードを作成](../log-analytics/log-analytics-dashboards.md)して、お気に入りの Office 365 検索クエリを表示します。
* [アラートを作成](../log-analytics/log-analytics-alerts.md)して、重要な Office 365 アクティビティがあらかじめ通知されるようにします。  

