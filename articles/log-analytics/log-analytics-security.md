---
title: "Log Analytics のデータ セキュリティ | Microsoft Docs"
description: "Log Analytics でプライバシーを保護し、データをセキュリティで保護する方法について説明します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: d0f4323c22858300d95af7407cdb14995507ee60
ms.openlocfilehash: 952697dff7dca2779a6eb5375afa41c0b130aad4


---
# <a name="log-analytics-data-security"></a>Log Analytics データのセキュリティ
Microsoft は、組織の IT インフラストラクチャの管理に役立つソフトウェアやサービスを提供すると同時に、お客様のプライバシーとデータの保護に努めています。 他者にデータを委託する場合、信頼には厳格なセキュリティが必要であることを把握しております。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。

Microsoft においてデータの保護は最優先事項になります。 セキュリティ ポリシーを含め、次の情報に関するご質問やご提案、問題がある場合は、[Azure のサポート オプション](http://azure.microsoft.com/support/options/)に関するページを参照してください。

この記事は、Operations Management Suite (OMS) の Log Analytics によるデータの収集、処理、および保護の方法について説明します。 エージェントを使用して Web サービスに接続し、System Center Operations Manager を使用して運用データを収集し、Azure 診断からデータを取得して Log Analytics に使用することができます。 収集したデータは、証明書ベースの認証と SSL 3 を使用して、Microsoft Azure でホストされている Log Analytics サービスにインターネット経由で送信されます。 データは、送信される前にエージェントによって圧縮されます。

Log Analytics サービスは次の方法でクラウドベースのデータを安全に管理しています。

* データの分離
* データの保持
* 物理的なセキュリティ
* インシデント管理
* コンプライアンス
* セキュリティ基準認定

## <a name="data-segregation"></a>データの分離
顧客データは OMS サービスを通じて各コンポーネントで論理的に個別に保存されます。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。 各顧客には、長期的なデータを格納する専用の Azure BLOB が提供されます。

## <a name="data-retention"></a>データの保持
インデックス設定済みのログの検索データは、価格プランに従って保持されます。 詳細については、「 [ログ分析の価格](https://azure.microsoft.com/pricing/details/log-analytics/)」を参照してください。

顧客データは、OMS ワークスペースが閉じられてから 30 日後に削除されます。 データが存在する Azure Storage アカウントも削除されます。 顧客データが削除されるときに、物理ドライブは破棄されません。

次の表では、OMS で利用できるソリューションの一部と、収集するデータの種類の例を示しています。

| **ソリューション** | **データ型** |
| --- | --- |
| 構成の評価 |構成データ、メタデータ、状態データ |
| 容量計画 |パフォーマンス データとメタデータ |
| マルウェア対策 |構成データとメタデータ |
| システムの更新の評価 |メタデータと状態のデータ |
| ログの管理 |ユーザー定義のイベント ログ、Windows イベント ログ、IIS ログ |
| 変更の追跡 |ソフトウェア インベントリと Windows サービスのメタデータ |
| SQL と Active Directory の評価 |WMI データ、レジストリ データ、パフォーマンス データ、SQL Server の動的管理の表示結果 |

次のテーブルでは、データの種類の例を示しています。

| **データの種類** | **フィールド** |
| --- | --- |
| アラート: |アラート名、アラートの説明、BaseManagedEntityId、問題 ID、IsMonitorAlert、RuleId、ResolutionState、優先度、重大度、カテゴリ、所有者、ResolvedBy、TimeRaised、TimeAdded、LastModified、LastModifiedBy、LastModifiedExceptRepeatCount、TimeResolved、TimeResolutionStateLastModified、TimeResolutionStateLastModifiedInDB、RepeatCount |
| 構成 |CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate |
| イベント |EventId、EventOriginalID、BaseManagedEntityInternalId、RuleId、PublisherId、PublisherName、FullNumber、番号、カテゴリ、ChannelLevel、LoggingComputer、EventData、EventParameters、TimeGenerated、TimeAdded <br>**注:** カスタム フィールドを使ってイベントを Windows のイベント ログに書き込むと、それらのイベントは OMS によって収集されます。 |
| Metadata |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、 NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| パフォーマンス |ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded |
| 状態 |StateChangeEventId、StateId、NewHealthState、OldHealthState、コンテキスト、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

## <a name="physical-security"></a>物理的なセキュリティ
OMS サービスの Log Analytics は Microsoft の担当者によって運営されており、すべてのアクティビティ ログは記録され、監査することができます。 このサービスはすべて Azure で実行され、Azure の一般的なエンジニアリングの条件に準拠しています。 Azure の資産の物理的なセキュリティに関する詳細は、「 [Microsoft Azure セキュリティの概要](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)」の 18 ページを参照してください。 転送や終了を含む OMS サービスへの責任がなくなったユーザに対する、領域をセキュリティで保護する物理的なアクセス権は、1 営業日以内に変更されます。 使用されるグローバルな物理インフラストラクチャについては、[Microsoft データ センター](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx)を参照してください。

## <a name="incident-management"></a>インシデント管理
OMS には、すべての Microsoft サービスが準拠するインシデント管理プロセスがあります。 まとめると次のようになります。

* セキュリティ責任の一部が Microsoft に属し、一部が顧客に属する責任の分担モデルの使用
* Azure のセキュリティ インシデントの管理
  * インシデントの検出によって開始される調査
  * 待機中のインシデント対応チームメンバーによる、インシデントの影響と重要度の評価。 証拠に基づいて、評価をセキュリティ対応チームに報告する必要があるかどうかが決定されます。
  * 技術的な、またはフォレンジックな調査の実施、コンテインメント、緩和策、および回避策の戦略の特定を行うための、セキュリティ対応エキスパートによるインシデントの診断。 顧客データが不正なまたは権限のない個人にさらされている可能性があるとセキュリティ チームが考えている場合、顧客インシデント通知プロセスの実行が並列で開始されます。  
  * 安定化とインシデントからの復旧。 インシデント対応チームは、問題を緩和するために、復旧計画を作成します。 影響を受けるシステムの隔離などの危機管理手順は直ちに、診断と並列して実行される可能性があります。 直近のリスクが収まった後に実行される、長期間の緩和策が計画される可能性があります。  
  * インシデントの終了と事後分析の実施。 インシデント対応チームは、イベントの再発防止を目的として、ポリシー、手順、プロセスを変更するため、問題の詳細を示す事後分析を作成します。
* 顧客へのセキュリティ インシデントの通知
  * 影響を受ける顧客の範囲の特定、および該当の顧客への可能な限り詳細な通知の提供
  * 顧客に詳細な情報を提供し、顧客側で調査を実行してエンドユーザー側で作成されたコミットメントを満たして、通知プロセスを過度に遅延させないようにするための通知の作成
  * 必要に応じたインシデントの確認と宣言
  * 不適切な待機時間のない、法的コミットメントまたは契約コミットメントに従ったインシデント通知による顧客への通知。 セキュリティ インシデントは、Microsoft が選択した (電子メールを含む) 方法によって、1 人以上の顧客側の管理者に配信されます。
* チームの準備とトレーニングの実施
  * Microsoft の担当者は、疑いのあるセキュリティ上の問題の特定と報告に役立つ、セキュリティと認識のトレーニングを完了する必要があります。  
  * Microsoft Azure サービスで作業しているオペレーターは、顧客データをホストする機密性の高いシステムへのアクセスに関連する、追加のトレーニングを受ける義務があります。
  * Microsoft セキュリティ対応担当者は、役割に対する専門的なトレーニングを受けます。

顧客のデータの損失が発生した場合、1 日以内に顧客に通知します。 ただし、OMS で顧客データの損失が発生したことはありません。 また、Microsoft では、作成されたデータのコピーを保持しており、地理的に分散しています。

Microsoft のセキュリティ インシデントへの対応の詳細については、「[Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf) (クラウドでの Microsoft Azure のセキュリティ対応)」を参照してください。

## <a name="compliance"></a>コンプライアンス
OMS ソフトウェア開発およびサービス チームの情報セキュリティとガバナンスのプログラムは、ビジネス要件をサポートしており、法律および [Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/) と [Microsoft セキュリティ センターのコンプライアンス](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)で説明されている規定を順守しています。 そこでは、OMS によるセキュリティ要件の確立方法、セキュリティ制御の識別方法、リスクの管理と監視方法についても説明されています。 Microsoft では、ポリシー、標準、手順、およびガイドラインのレビューを毎年行っています。

OMS 開発チームの各メンバーは、正規のアプリケーション セキュリティのトレーニングを受けています。 内部的には、ソフトウェア開発用に、バージョン管理システムを使用しています。 各ソフトウェア プロジェクトは、バージョン管理システムによって保護されています。

Microsoft には、Microsoft のすべてのサービスを監視して評価する、セキュリティとコンプライアンスのチームがあります。 情報セキュリティ責任者がチームを構成します。彼らは、OMS を開発するエンジニアリング部門とは関係ありません。 セキュリティ責任者には独自の管理チェーンがあり、製品とサービスについて独立した評価を行い、セキュリティとコンプライアンスを確保します。

Microsoft の取締役会には、Microsoft におけるすべての情報セキュリティ プログラムに関する年次報告書が提出されます。

OMS ソフトウェアの開発とサービスのチームは、Microsoft の法律および法令遵守チーム、その他の業界パートナーと積極的に連携し、さまざまな認定資格を取得しています。

## <a name="certifications-and-attestations"></a>認定と認証
OMS Log Analytics は、次の要件を満たしています。

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) および [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) 準拠
* PCI Security Standards Council による [Payment Card Industry (PCI 準拠) データ セキュリティ基準 (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI)。
* [Service Organization Controls (SOC) 1 Type 1 および SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) 準拠
* [HIPAA および HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) (HIPAA Business Associate Agreement を締結している企業向け)
* Windows の一般的なエンジニアリング条件
* Microsoft の信頼できるコンピューティング
* Azure のサービスとしては、OMS で使用されるコンポーネントは、Azure の準拠要件に従います。 詳細については、[Microsoft セキュリティ センターのコンプライアンス](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)を参照してください。

> [!NOTE]
> Log Analytics は、一部の認定および認証において、以前の名前である *Operational Insights* として表示されています。
>
>


## <a name="cloud-computing-security-data-flow"></a>クラウド コンピューティングのセキュリティ データ フロー
次の図は、企業の情報フローを例にしたクラウドのセキュリティ アーキテクチャです。情報が Log Analytics サービスまで移動する間にどのように保護され、最終的に OMS ポータルでユーザーに表示されるかを示しています。 各手順の詳細は図の後で説明します。

![Image of OMS data collection and security](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1.Log Analytics にサインアップし、データを収集する
組織から Log Analytics にデータを送信する場合、Windows エージェント、Azure Virtual Machines 上で実行されるエージェント、または Linux 用 OMS エージェントを構成します。 Operations Manager エージェントを使用する場合、オペレーション コンソールで構成ウィザードを使用して構成します。 ユーザー (あなた、他の個人、または複数人のグループの場合があります) は、次のアカウントのいずれかを使用して 1 つ以上の OMS アカウント (OMS ワークスペース) を作成し、エージェントを登録します。

* [組織 ID](../active-directory/sign-up-organization.md)
* [Microsoft アカウント - Outlook、Office Live、MSN](http://www.microsoft.com/account/default.aspx)

OMS ワークスペースは、データが収集、集計、分析、表示される場所になります。 ワークスペースは、主にデータをパーティション分割するための手段として使用されます。各ワークスペースは一意になります。 たとえば、実稼働データを OMS ワークスペースの 1 つで管理し、テスト データを別のワークスペースで管理する場合があります。 ワークスペースは、ユーザーのデータへのアクセスを管理者が制御する際にも役立ちます。 ワークスペースごとに複数のユーザーを関連付けることができます。また、各ユーザー アカウントは複数の OMS ワークスペースにアクセスできます。 データ センターのリージョンに基づいてワークスペースを作成します。 各ワークスペースは、主に OMS サービスの可用性のために、リージョン内の他のデータ センターにレプリケートされます。

Operations Manager の場合、構成ウィザードが完了したら、各 Operations Manager 管理グループは、Log Analytics サービスとの接続を確立します。 その後、Add Computers (コンピューターの追加) ウィザードを使用します。ウィザードではサービスへのデータの送信が許可される管理グループを選択します。 他の種類のエージェントでは、それぞれが安全な方法で OMS サービスに接続します。

接続されたシステムと Log Analytics サービスの間の通信はすべて暗号化されます。  暗号化には TLS (HTTPS) プロトコルが使用されます。  Log Analytics が最も最近の暗号化プロトコルを使用して最新の状態であるようにするため、続けて Microsoft SDL の手順が行われます。

各エージェントが Log Analytics のデータを収集します。 収集されるデータの種類は、使用するソリューションの種類によって異なります。 データ収集の概要については「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」を参照してください。 また、ほぼすべてのソリューションについて、より詳細な収集情報も提供されています。 ソリューションは、定義済みビュー、ログ検索クエリ、データの収集ルール、処理ロジックのバンドルになります。 Log Analytics を使用してソリューションをインポートできるのは管理者のみです。 ソリューションはインポート後、Operations Manager 管理サーバー (使用する場合) に移動され、選択した任意のエージェントに移動されます。 その後、エージェントはデータを収集します。

## <a name="2-send-data-from-agents"></a>2.エージェントからデータを送信する
登録キーを使用してすべての種類のエージェントを登録すると、証明書ベースの認証とポート 443 による SSL が使用され、エージェントと Log Analytics サービス間にセキュリティで保護された接続が確立します。 OMS では、キーの生成と管理にシークレット ストアを使用します。 秘密キーは 90 日ごとに交換されて Azure に格納され、厳密な規制およびコンプライアンス手順に従う Azure オペレーターによって管理されます。

Operations Manager では Log Analytics サービスにワークスペースを登録すると、Operations Manager 管理サーバーとの間にセキュリティで保護された HTTPS 接続が確立します。

Azure Virtual Machines 上で実行される Windows エージェントの場合、Azure テーブルの診断イベントを読み取るために、読み取り専用のストレージ キーが使用されます。

エージェントが何かの理由でサービスと通信できない場合、収集したデータはローカルの一時的なキャッシュに格納されます。管理サーバーでは 2 時間にわたって 8 分ごとにデータの再送信を試みます。 エージェントのキャッシュされたデータは、オペレーティング システムの資格情報ストアによって保護されています。 2 時間が経過してもサービスがデータを処理できない場合、エージェントはデータをキューに格納します。 キューがいっぱいになると、OMS はパフォーマンス データから順にデータ型を削除し始めます。 エージェントのキューの上限はレジストリ キーであるため、必要に応じて変更できます。 収集されたデータは圧縮され、負荷を追加しないようにオンプレミス データベースをバイパスして、サービスに送信されます。 収集したデータが送信されると、データはキャッシュから削除されます。

前述のように、エージェントからのデータが SSL 経由で Microsoft Azure データ センターに送信されます。 必要に応じて、ExpressRoute を使用してデータのセキュリティを強化できます。 ExpressRoute は、ネットワーク サービス プロバイダーによって提供されるマルチ プロトコル ラベル スイッチング (MPLS) VPN などの、既存の WAN ネットワークから Azure に直接接続する方法です。 詳細については、[ExpressRoute](https://azure.microsoft.com/services/expressroute/) に関するページを参照してください。

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3.Log Analytics サービスでデータを受信して処理する
Log Analytics サービスでは、Azure 認証で証明書とデータの整合性を検証することにより、入力されるデータが信頼できる発行元からのものであることを確認します。 未処理の生データはその後、[Microsoft Azure Storage](../storage/storage-introduction.md) で BLOB として暗号化されていない状態で保存されます。 ただし、各 Azure Storage BLOB には、そのユーザーのみがアクセスできる一意のキーのセットがあります。 保存されているデータの種類は、インポートしてデータを収集するために使用したソリューションの種類によって異なります。 次に、Log Analytics サービスは、Azure Storage BLOB 用の生データを処理します。

## <a name="4-use-log-analytics-to-access-the-data"></a>4.Log Analytics を使用してデータにアクセスする
OMS ポータルで Log Analytics にサインインするには、設定済みの組織アカウントまたは Microsoft アカウントを使用します。 OMS ポータルと OMS の Log Analytics 間のすべてのトラフィックは、セキュリティで保護された HTTPS チャネル経由で送信されます。 OMS ポータルを使用する場合、セッション ID がユーザーのクライアント (Web ブラウザー) で生成され、データはセッションが終了するまでローカル キャッシュに保存されます。 セッションが終了すると、キャッシュが削除されます。 個人を特定できる情報が含まれないクライアント側の Cookie は、自動的に削除されません。 セッションの Cookie は HTTPOnly としてマークされ、セキュリティで保護されます。 あらかじめ決められたアイドル期間の後は、OMS ポータル セッションが終了します。

OMS ポータルを使用してデータを CSV ファイルにエクスポートし、検索 API を使用してデータにアクセスすることができます。 CSV エクスポートはエクスポートあたり 50,000 行に制限され、API のデータは検索あたり 5,000 行に制限されます。

## <a name="next-steps"></a>次のステップ
* [Log Analytics の起動と開始](log-analytics-get-started.md) 」では、Log Analytics の詳細と、分単位で起動および実行する方法について説明します。



<!--HONumber=Nov16_HO4-->


