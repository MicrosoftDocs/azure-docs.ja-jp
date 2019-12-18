---
title: Log Analytics のデータ セキュリティ | Microsoft Docs
description: Log Analytics でプライバシーを保護し、データをセキュリティで保護する方法について説明します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/04/2019
ms.openlocfilehash: 3ff69928f4d6aa1692cdb1d4fd7e846b3a6b7a5c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932547"
---
# <a name="log-analytics-data-security"></a>Log Analytics データのセキュリティ
このドキュメントでは、[Azure セキュリティ センター](../../security/fundamentals/trust-center.md)の情報に加えて、Azure Monitor の機能である Log Analytics に固有の情報を提供することを目的としています。  

この記事は、Log Analytics によるデータの収集、処理、および保護の方法について説明します。 エージェントを使用して Web サービスに接続し、System Center Operations Manager を使用して運用データを収集し、Azure Diagnostics からデータを取得して Log Analytics に使用することができます。 

Log Analytics サービスは次の方法でクラウドベースのデータを安全に管理しています。

* データの分離
* データの保持
* 物理的なセキュリティ
* インシデント管理
* コンプライアンス
* セキュリティ基準認定

セキュリティ ポリシーを含め、次の情報に関するご質問やご提案、問題がある場合は、[Azure のサポート オプション](https://azure.microsoft.com/support/options/)に関するページを参照してください。

## <a name="sending-data-securely-using-tls-12"></a>TLS 1.2 を使用して安全にデータを送信する 

Log Analytics へのデータの転送時のセキュリティを保証するため、少なくとも Transport Layer Security (TLS) 1.2 を使用するようにエージェントを構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。さらに、業界はこれらの以前のプロトコルのサポートを中止する方向へ急速に動いています。 

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) は、[2018 年 6 月 30 日を期限として](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf)、TLS/SSL の以前のバージョンを無効にし、より安全なプロトコルにアップグレードすることを求めています。 Azure がレガシー サポートを廃止した場合、エージェント/クライアントが TLS 1.2 以上で通信できないと Log Analytics にデータを送信できなくなります。 

エージェントで TLS 1.2 のみを使用するように明示的に設定することは、絶対に必要な場合を除いてお勧めしません。なぜなら、そうすることで、TLS 1.3 などのより新しいよくより安全なプロトコルを自動的に検出して利用できるようにするプラットフォーム レベルのセキュリティ機能が無効になる可能性があるためです。 

### <a name="platform-specific-guidance"></a>プラットフォーム固有のガイダンス

|プラットフォーム/言語 | サポート | 詳細情報 |
| --- | --- | --- |
|Linux | Linux ディストリビューションでは、TLS 1.2 のサポートに関して [OpenSSL](https://www.openssl.org) に依存する傾向があります。  | [OpenSSL の Changelog](https://www.openssl.org/news/changelog.html) を参照して、使用している OpenSSL のバージョンがサポートされていることを確認してください。|
| Windows 8.0 - 10 | サポートされています。既定で有効になっています。 | [既定の設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには。  |
| Windows Server 2012 - 2016 | サポートされています。既定で有効になっています。 | [既定の設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには |
| Windows 7 SP1 および Windows Server 2008 R2 SP1 | サポートされていますが、既定では有効になっていません。 | 有効にする方法の詳細については、「[トランスポート層セキュリティ (TLS) のレジストリ設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)」を参照してください。  |

## <a name="data-segregation"></a>データの分離
Log Analytics サービスによってデータが取り込まれた後、データはサービス全体のコンポーネントごとに論理的に分離されます。 すべてのデータはワークスペースごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。 データは、選択したリージョンのストレージ クラスター内の専用データベースに格納されます。

## <a name="data-retention"></a>データの保持
インデックス設定済みのログの検索データは、価格プランに従って保持されます。 詳細については、「 [ログ分析の価格](https://azure.microsoft.com/pricing/details/log-analytics/)」を参照してください。

[サブスクリプション契約](https://azure.microsoft.com/support/legal/subscription-agreement/)の一環として、Microsoft は契約の条項に従ってデータを保持します。  顧客データが削除されるときに、物理ドライブは破棄されません。  

次の表では、利用できるソリューションの一部と、収集するデータの種類の例を示しています。

| **ソリューション** | **データ型** |
| --- | --- |
| 容量とパフォーマンス |パフォーマンス データとメタデータ |
| 更新管理 |メタデータと状態のデータ |
| ログの管理 |ユーザー定義のイベント ログ、Windows イベント ログ、IIS ログ |
| 変更の追跡 |ソフトウェア インベントリ、Windows サービスと Linux デーモン メタデータ、Windows/Linux ファイルのメタデータ |
| SQL と Active Directory の評価 |WMI データ、レジストリ データ、パフォーマンス データ、SQL Server の動的管理の表示結果 |

次のテーブルでは、データの種類の例を示しています。

| **データの種類** | **フィールド** |
| --- | --- |
| アラート: |アラート名、アラートの説明、BaseManagedEntityId、問題 ID、IsMonitorAlert、RuleId、ResolutionState、優先度、重大度、カテゴリ、所有者、ResolvedBy、TimeRaised、TimeAdded、LastModified、LastModifiedBy、LastModifiedExceptRepeatCount、TimeResolved、TimeResolutionStateLastModified、TimeResolutionStateLastModifiedInDB、RepeatCount |
| 構成 |CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate |
| Event |EventId、EventOriginalID、BaseManagedEntityInternalId、RuleId、PublisherId、PublisherName、FullNumber、番号、カテゴリ、ChannelLevel、LoggingComputer、EventData、EventParameters、TimeGenerated、TimeAdded <br>**注:** カスタム フィールドを使ってイベントを Windows のイベント ログに書き込むと、それらのイベントは Log Analytics によって収集されます。 |
| Metadata |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、 NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| パフォーマンス |ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded |
| State |StateChangeEventId、StateId、NewHealthState、OldHealthState、コンテキスト、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

## <a name="physical-security"></a>物理的なセキュリティ
Log Analytics サービスは Microsoft の担当者によって管理されており、すべてのアクティビティ ログは記録され、監査することができます。 Log Analytics は、Azure サービスとして動作し、すべての Azure コンプライアンスとセキュリティの要件を満たしています。 Azure の資産の物理的なセキュリティに関する詳細は、「 [Microsoft Azure セキュリティの概要](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)」の 18 ページを参照してください。 転送や終了を含む Log Analytics サービスへの責任がなくなったユーザーに対する、領域をセキュリティで保護する物理的なアクセス権は、1 営業日以内に変更されます。 使用されるグローバルな物理インフラストラクチャについては、[Microsoft データ センター](https://azure.microsoft.com/global-infrastructure/)を参照してください。

## <a name="incident-management"></a>インシデント管理
Log Analytics には、すべての Microsoft サービスが準拠するインシデント管理プロセスがあります。 まとめると次のようになります。

* セキュリティ責任の一部が Microsoft に属し、一部が顧客に属する責任の分担モデルの使用
* Azure のセキュリティ インシデントの管理:
  * インシデントの検出によって開始される調査
  * 待機中のインシデント対応チームメンバーによる、インシデントの影響と重要度の評価。 証拠に基づいて、評価をセキュリティ対応チームに報告する必要があるかどうかが決定されます。
  * 技術的な、またはフォレンジックな調査の実施、コンテインメント、緩和策、および回避策の戦略の特定を行うための、セキュリティ対応エキスパートによるインシデントの診断。 顧客データが不正なまたは権限のない個人にさらされている可能性があるとセキュリティ チームが考えている場合、顧客インシデント通知プロセスの実行が並列で開始されます。  
  * 安定化とインシデントからの復旧。 インシデント対応チームは、問題を緩和するために、復旧計画を作成します。 影響を受けるシステムの隔離などの危機管理手順は直ちに、診断と並列して実行される可能性があります。 直近のリスクが収まった後に実行される、長期間の緩和策が計画される可能性があります。  
  * インシデントの終了と事後分析の実施。 インシデント対応チームは、イベントの再発防止を目的として、ポリシー、手順、プロセスを変更するため、問題の詳細を示す事後分析を作成します。
* 顧客へのセキュリティ インシデントの通知:
  * 影響を受ける顧客の範囲の特定、および該当の顧客への可能な限り詳細な通知の提供
  * 顧客に詳細な情報を提供し、顧客側で調査を実行してエンドユーザー側で作成されたコミットメントを満たして、通知プロセスを過度に遅延させないようにするための通知の作成
  * 必要に応じたインシデントの確認と宣言
  * 不適切な待機時間のない、法的コミットメントまたは契約コミットメントに従ったインシデント通知による顧客への通知。 セキュリティ インシデントは、Microsoft が選択した (電子メールを含む) 方法によって、1 人以上の顧客側の管理者に配信されます。
* チームの準備とトレーニングの実施:
  * Microsoft の担当者は、疑いのあるセキュリティ上の問題の特定と報告に役立つ、セキュリティと認識のトレーニングを完了する必要があります。  
  * Microsoft Azure サービスで作業しているオペレーターは、顧客データをホストする機密性の高いシステムへのアクセスに関連する、追加のトレーニングを受ける義務があります。
  * Microsoft セキュリティ対応担当者は、役割に対する専門的なトレーニングを受けます。

顧客のデータの損失が発生した場合、1 日以内に顧客に通知します。 ただし、サービスで顧客データの損失が発生したことはありません。 

Microsoft のセキュリティ インシデントへの対応の詳細については、「[Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf) (クラウドでの Microsoft Azure のセキュリティ対応)」を参照してください。

## <a name="compliance"></a>コンプライアンス
Log Analytics ソフトウェア開発およびサービス チームの情報セキュリティとガバナンスのプログラムは、ビジネス要件をサポートしており、法律および [Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)と [Microsoft セキュリティ センターのコンプライアンス](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)で説明されている規定を順守しています。 そこでは、Log Analytics によるセキュリティ要件の確立方法、セキュリティ制御の識別方法、リスクの管理と監視方法についても説明されています。 Microsoft では、ポリシー、標準、手順、およびガイドラインのレビューを毎年行っています。

開発チームの各メンバーは、正規のアプリケーション セキュリティのトレーニングを受けています。 内部的には、ソフトウェア開発用に、バージョン管理システムを使用しています。 各ソフトウェア プロジェクトは、バージョン管理システムによって保護されています。

Microsoft には、Microsoft のすべてのサービスを監視して評価する、セキュリティとコンプライアンスのチームがあります。 情報セキュリティ責任者がチームを構成します。彼らは、Log Analytics を開発するエンジニアリング チームとは関係ありません。 セキュリティ責任者には独自の管理チェーンがあり、製品とサービスについて独立した評価を行い、セキュリティとコンプライアンスを確保します。

Microsoft の取締役会には、Microsoft におけるすべての情報セキュリティ プログラムに関する年次報告書が提出されます。

Log Analytics ソフトウェアの開発とサービスのチームは、Microsoft の法律および法令遵守チーム、その他の業界パートナーと積極的に連携し、さまざまな認定資格を取得しています。

## <a name="certifications-and-attestations"></a>認定と認証
Azure Log Analytics は、次の要件を満たしています。

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* PCI Security Standards Council による [Payment Card Industry (PCI 準拠) データ セキュリティ基準 (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI)。
* [Service Organization Controls (SOC) 1 Type 1 および SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) に準拠している
* [HIPAA および HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) (HIPAA Business Associate Agreement を締結している企業向け)
* Windows の一般的なエンジニアリング条件
* Microsoft の信頼できるコンピューティング
* Azure のサービスとしては、Log Analytics で使用されるコンポーネントは、Azure の準拠要件に従います。 詳細については、[Microsoft セキュリティ センターのコンプライアンス](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)を参照してください。

> [!NOTE]
> Log Analytics は、一部の認定および認証において、以前の名前である *Operational Insights* として表示されています。
>
>

## <a name="cloud-computing-security-data-flow"></a>クラウド コンピューティングのセキュリティ データ フロー
次の図は、企業の情報フローを例にしたクラウドのセキュリティ アーキテクチャです。情報が Log Analytics サービスまで移動する間にどのように保護され、最終的に Azure portal でユーザーに表示されるかを示しています。 各手順の詳細は図の後で説明します。

![Log Analytics データ収集とセキュリティの図](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1.Log Analytics にサインアップし、データを収集する
組織が Log Analytics にデータを送信できるようにするには、Azure 仮想マシン、または自身の環境や他のクラウド プロバイダーの仮想または物理コンピューターで実行される Windows または Linux エージェントを構成します。  Operations Manager を使用する場合は、管理グループから Operations Manager エージェントを構成します。 ユーザー (あなた、他のユーザー、または複数ユーザーのグループの場合があります) は、次のアカウントのいずれかを使用して 1 つ以上の Log Analytics ワークスペースを作成し、エージェントを登録します。

* [組織 ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft アカウント - Outlook、Office Live、MSN](https://account.microsoft.com/account)

Log Analytics ワークスペースは、データが収集、集計、分析、表示される場所になります。 ワークスペースは、主にデータをパーティション分割するための手段として使用されます。各ワークスペースは一意になります。 たとえば、実稼働データをワークスペースの 1 つで管理し、テスト データを別のワークスペースで管理する場合があります。 ワークスペースは、ユーザーのデータへのアクセスを管理者が制御する際にも役立ちます。 ワークスペースごとに複数のユーザー アカウントを関連付けることができます。また、各ユーザー アカウントは複数の Log Analytics ワークスペースにアクセスできます。 データ センターのリージョンに基づいてワークスペースを作成します。

Operations Manager の場合、Operations Manager 管理グループは、Log Analytics サービスとの接続を確立します。 管理グループ内のエージェントで管理されるどのシステムがデータの収集とサービスへのデータ送信を許可されるかを構成します。 ソリューションからのデータは、有効にしているソリューションに応じて、Operations Manager 管理サーバーから Log Analytics サービスに直接送信される場合と、エージェント管理システムで収集されるデータ量の関係で、エージェントからサービスに直接送信される場合があります。 Operations Manager によって監視されていないシステムでは、それぞれ安全に Log Analytics サービスに直接接続します。

接続されたシステムと Log Analytics サービスの間の通信はすべて暗号化されます。 暗号化には TLS (HTTPS) プロトコルが使用されます。  Log Analytics が最も最近の暗号化プロトコルを使用して最新の状態であるようにするため、続けて Microsoft SDL の手順が行われます。

各エージェントが Log Analytics のデータを収集します。 収集されるデータの種類は、使用するソリューションの種類によって異なります。 データ収集の概要については「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](../../azure-monitor/insights/solutions.md)」を参照してください。 また、ほぼすべてのソリューションについて、より詳細な収集情報も提供されています。 ソリューションは、定義済みビュー、ログ検索クエリ、データの収集ルール、処理ロジックのバンドルになります。 Log Analytics を使用してソリューションをインポートできるのは管理者のみです。 ソリューションはインポート後、Operations Manager 管理サーバー (使用する場合) に移動され、選択した任意のエージェントに移動されます。 その後、エージェントはデータを収集します。

## <a name="2-send-data-from-agents"></a>2.エージェントからデータを送信する
登録キーを使用してすべての種類のエージェントを登録すると、証明書ベースの認証とポート 443 による SSL が使用され、エージェントと Log Analytics サービス間にセキュリティで保護された接続が確立します。 Log Analytics では、キーの生成と管理にシークレット ストアを使用します。 秘密キーは 90 日ごとに交換されて Azure に格納され、厳密な規制およびコンプライアンス手順に従う Azure オペレーターによって管理されます。

Operations Manager では、Log Analytics ワークスペースに登録されている管理グループは、Operations Manager 管理サーバーとのセキュアな HTTPS 接続を確立します。

Azure Virtual Machines 上で実行される Windows または Linux エージェントの場合、Azure テーブルの診断イベントを読み取るために、読み取り専用のストレージ キーが使用されます。  

Log Analytics に統合されている Operations Manager 管理グループに報告するエージェントでは、管理サーバーがなんらかの理由でサービスと通信できない場合、収集されたデータは管理サーバー上の一時的なキャッシュにローカルに格納されます。   2 時間にわたって 8 分ごとにデータを再送信しようとします。  管理サーバーをバイパスし、Log Analytics に直接送信されるデータの場合、動作は Windows エージェントと一貫性があります。  

Windows または管理サーバー エージェントのキャッシュされたデータは、オペレーティング システムの資格情報ストアによって保護されています。 2 時間が経過してもサービスがデータを処理できない場合、エージェントはデータをキューに格納します。 キューがいっぱいになると、エージェントはパフォーマンス データから順にデータ型を削除し始めます。 エージェントのキューの上限はレジストリ キーであるため、必要に応じて変更できます。 収集されたデータは圧縮され、負荷を追加しないように Operations Manager 管理グループ データベースをバイパスして、サービスに送信されます。 収集したデータが送信されると、データはキャッシュから削除されます。

前述のように、管理サーバーまたは直接接続エージェントからのデータが SSL 経由で Microsoft Azure データセンターに送信されます。 必要に応じて、ExpressRoute を使用してデータのセキュリティを強化できます。 ExpressRoute は、ネットワーク サービス プロバイダーによって提供されるマルチ プロトコル ラベル スイッチング (MPLS) VPN などの、既存の WAN ネットワークから Azure に直接接続する方法です。 詳細については、[ExpressRoute](https://azure.microsoft.com/services/expressroute/) に関するページを参照してください。

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3.Log Analytics サービスでデータを受信して処理する
Log Analytics サービスでは、Azure 認証で証明書とデータの整合性を検証することにより、入力されるデータが信頼できる発行元からのものであることを確認します。 未処理の生データは、リージョンの Azure Event Hub に格納され、データは最終的に保存されます。 保存されているデータの種類は、インポートしてデータを収集するために使用したソリューションの種類によって異なります。 次に、Log Analytics サービスは、生データを処理してデータベースに取り込みます。

データベースに格納されている収集済みデータのリテンション期間は、選択された料金プランによって異なります。 *無料*プランの場合、収集されたデータは 7 日間使用できます。 "*有料*" プランの場合、収集したデータは既定で 31 日間利用でき、730 日まで延長できます。 データは、データの機密性を保証するために、Azure ストレージに暗号化され保存されます。そして、そのデータは、ローカル冗長ストレージ (LRS) を使用してローカルのリージョン内にレプリケートされます。 過去 2 週間以内のデータは SSD ベースのキャッシュにも格納されます。このキャッシュは暗号化されます。

## <a name="4-use-log-analytics-to-access-the-data"></a>4.Log Analytics を使用してデータにアクセスする
Log Analytics ワークスペースにアクセスするには、設定済みの組織アカウントまたは Microsoft アカウントを使用して Azure Portal にサインインします。 ポータルと Log Analytics サービス間のすべてのトラフィックは、セキュリティで保護された HTTPS チャネル経由で送信されます。 ポータルを使用する場合、セッション ID がユーザーのクライアント (Web ブラウザー) で生成され、データはセッションが終了するまでローカル キャッシュに保存されます。 セッションが終了すると、キャッシュが削除されます。 個人を特定できる情報が含まれないクライアント側の Cookie は、自動的に削除されません。 セッションの Cookie は HTTPOnly としてマークされ、セキュリティで保護されます。 あらかじめ決められたアイドル期間の後は、Azure Portal セッションが終了します。

## <a name="next-steps"></a>次の手順
* Log Analytics で Azure VM のデータを収集する方法については、[Azure VM のクイック スタート](../../azure-monitor/learn/quick-collect-azurevm.md)に関するページをご覧ください。  

*  環境内の物理または仮想の Windows または Linux コンピューターからデータを収集する場合は、[Linux コンピューターのクイック スタート](../../azure-monitor/learn/quick-collect-linux-computer.md)または [Windows コンピューターのクイック スタート](../../azure-monitor/learn/quick-collect-windows-computer.md)に関するページをご覧ください

