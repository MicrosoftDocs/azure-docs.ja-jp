---
title: 移行後に単一データベースとプールされたデータベースを管理する - Azure SQL Database | Microsoft Docs
description: Azure SQL Database への移行後のデータベースの管理方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 73bc2d9889727a1633986e12642bd06cf2714632
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357327"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>クラウドの新しい DBA – Azure SQL Database での単一データベースとプールされたデータベースの管理

従来の自己管理型で自己制御型の環境から PaaS 環境への移行は、最初は少し面倒に思えるかもしれません。 アプリの開発者や DBA であれば、常に、アプリケーションの高い可用性、パフォーマンス、セキュリティ、回復力を維持するのに役立つプラットフォームのコア機能を知りたいと思うでしょう。 この記事の目的はまさにそれです。 この記事では、リソースを簡潔に整理すると共に、アプリケーションの効率的な実行を管理および維持し、クラウドで最善の結果を得るために、単一データベースとプールされたデータベースで SQL Database の主要な機能を最大限に活用する方法のガイダンスを示します。 この記事は、主に以下のような読者を対象に書かれています。

- Azure SQL Database へのアプリケーションの移行を評価している – アプリケーションの現代化。
- アプリケーションの移行を行っている – 進行中の移行シナリオ。
- Azure SQL DB への移行を最近完了した – クラウドの新しい DBA。

この記事では、単一データベースおよびエラスティック プール内のプールされたデータベースを使用するときにすぐに利用できる、プラットフォームとしての Azure SQL Database の主要な特性の一部について説明します。 次のような内容です。

- Azure portal を使用したデータベースの監視
- ビジネス継続性とディザスター リカバリー (BCDR)
- セキュリティとコンプライアンス
- インテリジェントなデータベースの監視とメンテナンス
- データの移動

> [!NOTE]
> この記事は、Azure SQL Database での次のデプロイ オプションに適用されます: 単一データベースとエラスティック プール。 SQL Database のマネージド インスタンスのデプロイ オプションには適用されません。

## <a name="monitor-databases-using-the-azure-portal"></a>Azure ポータルを使用したデータベースの監視

[Azure portal](https://portal.azure.com/) で、ご利用のデータベースを選択し、 **[監視]** グラフをクリックすることによって、個々のデータベースの使用率を監視できます。 これにより、 **[メトリック]** ウィンドウが表示されます。 **[グラフの編集]** ボタンをクリックすると、内容を編集できます。 次のメトリックを追加します。

- CPU の割合
- DTU の割合
- データ IO の割合
- データベース サイズの割合

これらのメトリックを追加したら、これらを **[監視]** グラフに引き続き表示し、 **[メトリック]** ウィンドウにはさらに詳細な情報を表示できます。 4 つのメトリックはいずれも、データベースの **DTU** を基準とする平均使用率を示しています。 サービス レベルの詳細については、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。  

![データベース パフォーマンスのサービス階層の監視](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

また、パフォーマンス メトリックに対してアラートを構成することができます。 **[メトリック]** ウィンドウの **[アラートの追加]** ボタンをクリックします。 ウィザードの指示に従ってアラートを構成します。 メトリックが特定のしきい値を超えた場合、またはメトリックが特定のしきい値を下回った場合に警告するオプションがあります。

たとえば、データベースのワークロードが増加する見込みの場合、データベースのいずれかのパフォーマンス メトリックが 80% に達すると電子メールのアラートを送信するように構成できます。 これは、次に大きいコンピューティング サイズにいつ切り替える必要があるかを見つけるための早期警告として使用できます。

下位のコンピューティング サイズにダウングレードできるかどうかを判断するために、パフォーマンス メトリックを利用することもできます。 たとえば、Standard S2 データベースを使用していて、すべてのパフォーマンス メトリックは、どの時点でもデータベースの平均的な使用率が 10% を超えないとします。 この場合、データベースは Standard S1 で快適に動作します。 ただし、下位のコンピューティング サイズへの移行を決定する前に、急上昇や変動するワークロードに注意してください。

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>ビジネス継続性とディザスター リカバリー (BCDR)

ビジネス継続性とディザスター リカバリーの機能を使うと、障害が発生した場合でも、通常どおりビジネスを継続できます。 この場合の障害は、データベース レベルのイベント (たとえば、ユーザーが誤って重要なテーブルを削除した) や、データ センター レベルのイベント (津波などの地域災害) です。

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>SQL Database でバックアップを作成し管理するにはどうすればよいですか

Azure SQL DB ではバックアップを作成しません。なぜなら必要ないからです。 データベースのバックアップは SQL Database が自動的に行うため、バックアップのスケジュール、取得、管理について心配する必要はもうありません。 プラットフォームは、完全バックアップを 1 週間ごと、差分バックアップを数時間ごと、ログ バックアップを 5 分ごとに取得して、効率的なディザスター リカバリーと最小限のデータ損失を保証します。 データベースを作成するとすぐに、最初の完全バックアップが行われます。 これらのバックアップは "リテンション期間" と呼ばれる一定の期間だけ利用でき、この期間は選ばれているサービス レベルによって異なります。 SQL Database では、[特定の時点への復旧 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) を使って、この保有期間内の任意の時点に復元することができます。

|サービス階層|保有期間の日数|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

さらに、[長期保存 (LTR)](sql-database-long-term-retention.md) 機能を使うと、はるかに長期 (最大 10 年) にわたりバックアップ ファイルを保持することができ、その期間内の任意の時点でこれらのバックアップからデータを復元できます。 また、データベース バックアップは geo レプリケートされたストレージに保持されるので、リージョン レベルの災害からの回復力が保証されます。 Azure リージョン内のバックアップを保有期間の任意の時点に復元することもできます。 [ビジネス継続性の概要](sql-database-business-continuity.md)に関するページを参照してください。

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>データセンター レベルの障害または局所的大災害が発生した場合、ビジネス継続性を確保するにはどうすればよいですか

データベース バックアップは geo レプリケートされた記憶域に格納されるので、リージョン レベルの災害が発生した場合でも、別の Azure リージョンにバックアップを復元することができます。 これは geo リストアと呼ばれます。 一般に、geo リストアの RPO (目標復旧時点) は 1 時間以内、ERT (推定復旧時間) は数分から数時間です。

ミッション クリティカルなデータベースに対しては、Azure SQL DB はアクティブ geo レプリケーションを提供します。 アクティブ geo レプリケーションは、基本的に、元のデータベースの geo レプリケートされたセカンダリ コピーを別のリージョンに作成します。 たとえば、もともと Azure 米国西部リージョンでホストされているデータベースにリージョン災害回復力を持たせるには、 米国西部のデータベースのアクティブ geo レプリカを米国東部に作成します。 米国西部で災害が発生した場合は、米国東部リージョンにフェールオーバーできます。 これらを自動フェールオーバー グループ内に構成すると、災害時にはデータベースが米国東部のセカンダリに自動的にフェールオーバーするので、さらに回復力が高くなります。 この場合の RPO は 5 秒以下、ERT は 30 秒以下です。

自動フェールオーバー グループを構成していない場合、アプリケーションでは障害を絶えず監視し、セカンダリ データベースへのフェールオーバーを開始する必要があります。 このようなアクティブ geo レプリカは、異なる Azure リージョンに最大 4 個まで作成できます。 回復力がさらに向上します。 これらのセカンダリ アクティブ geo レプリカには読み取り専用でアクセスすることもできます。 これは、geo 分散アプリケーション シナリオの待機時間の削減に非常に便利です。

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>オンプレミスのディザスター リカバリー プランをどのように SQL Database に変更すればよいですか

要約すると、従来のオンプレミス SQL Server のセットアップでは、フェールオーバー クラスタリング、データベース ミラーリング、トランザクション レプリケーション、ログ配布などの機能を使って可用性をアクティブに管理し、バックアップを維持および管理して、ビジネス継続性を確保する必要がありました。 SQL Database ではプラットフォームがこれらを自動的に管理するので、ユーザーはデータベース アプリケーションの開発と最適化に集中でき、同じくらい災害管理に配慮する必要はありません。 ユーザーは、Azure Portal で数回クリックして (または PowerShell API で数個のコマンドを入力して) バックアップ プランとディザスター リカバリー プランを構成し、使用することができます。

ディザスター リカバリーの詳細については、「[Azure SQL Db Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)」 (Azure SQL Db ディザスター リカバリー 101) を参照してください。

## <a name="security-and-compliance"></a>セキュリティとコンプライアンス

SQL Database ではセキュリティとプライバシーが非常に重視されています。 SQL Database 内のセキュリティはデータベース レベルとプラットフォーム レベルで利用でき、最も理解しやすいのは複数のレイヤーに分類されているときです。 各レイヤーで、アプリケーションを制御して最善のセキュリティを提供します。 以下のレイヤーがあります。

- ID と認証 ([Windows/SQL 認証および Azure Active Directory (AAD) 認証](sql-database-control-access.md))。
- アクティビティの監視 ([監査](sql-database-auditing.md)および[脅威の検出](sql-database-threat-detection.md))。
- 実際のデータの保護 ([Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) および [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine))。
- 機密データおよび特権データの制御 ([行レベル セキュリティ](/sql/relational-databases/security/row-level-security)および[動的データ マスク](/sql/relational-databases/security/dynamic-data-masking))。

[Azure Security Center](https://azure.microsoft.com/services/security-center/): Azure、オンプレミス、他のクラウドで実行されているワークロード全体でのセキュリティ管理を一元化します。 [監査](sql-database-auditing.md)や [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) などの SQL Database の主要な保護機能がすべてのリソースに対して構成されているかどうかを確認し、独自の要件に応じてポリシーを作成できます。

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>SQL Database ではどのようなユーザー認証方法が提供されますか

SQL Database では [2 種類の認証方法](sql-database-control-access.md#authentication)が提供されています。

- [Azure Active Directory 認証](sql-database-aad-authentication.md)
- SQL 認証

従来の Windows 認証はサポートされていません。 Azure Active Directory (AD) は、ID とアクセスの集中管理サービスです。 組織内のすべての担当者にシングル サインオン (SSO) アクセスを非常に簡単に提供できます。 つまり、認証が簡単なように、資格情報はすべての Azure サービスで共有されます。 AAD は [MFA (多要素認証)](sql-database-ssms-mfa-authentication.md) をサポートし、[数クリック](../active-directory/hybrid/how-to-connect-install-express.md)で AAD を Windows Server Active Directory と統合できます。 SQL 認証はこれまでとまったく同じように動作します。 ユーザー名とパスワードを提供すると、特定の SDL Database サーバーの任意のデータベースでユーザーを認証できます。 このサービスを使用すると、SQL Database および SQL Data Warehouse で、Azure AD ドメイン内において多要素認証とゲスト ユーザー アカウントを提供できます。 既に Active Directory をオンプレミスで使用している場合、そのディレクトリを Azure Active Directory とフェデレーションして Azure へ拡張できます。

|**ユーザーの条件**|**SQL Database/SQL Data Warehouse**|
|---|---|
|Azure で Azure Active Directory (AD) を使用しない|[SQL 認証](sql-database-security-overview.md)を使用します。|
|オンプレミスの SQL Server で AD を使用している|[AD と Azure AD をフェデレーションして](../active-directory/hybrid/whatis-hybrid-identity.md)、Azure AD 認証を使用します。 これにより、シングル サインオンを使用できます。|
|多要素認証 (MFA) を適用する必要がある|[Microsoft 条件付きアクセス](sql-database-conditional-access.md)で MFA をポリシーとして必須にして、[MFA サポート付きの Azure AD ユニバーサル認証](sql-database-ssms-mfa-authentication.md)を使用します。|
|Microsoft アカウント (live.com、outlook.com) または他のドメイン (gmail.com など) のゲスト アカウントを持っている|SQL Database/Data Warehouse で、[Azure AD B2B コラボレーション](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)を利用する [Azure AD ユニバーサル認証](sql-database-ssms-mfa-authentication.md)を使用します。|
|フェデレーション ドメインから Azure AD の資格情報を使用して Windows にログオンしている|[Azure AD 統合認証](sql-database-aad-authentication-configure.md)を使用します。|
|Azure にフェデレーションされていないドメインから資格情報を使用して Windows にログオンしている|[Azure AD 統合認証](sql-database-aad-authentication-configure.md)を使用します。|
|SQL Database または SQL Data Warehouse への接続が必要な中間層サービスを使用している|[Azure AD 統合認証](sql-database-aad-authentication-configure.md)を使用します。|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>データベースへの接続アクセスを制限または制御する方法

複数の方法から適切なものを選んで、アプリケーションの最適な接続編成を実現できます。

- ファイアウォール ルール
- VNet サービス エンドポイント
- 予約済み IP

#### <a name="firewall"></a>ファイアウォール

ファイアウォールは、SDL Database サーバーへのアクセスを特定のエンティティだけに許可することで、外部エンティティからサーバーへのアクセスを防ぎます。 既定では、他の Azure サービスからの接続を除き、SDL Database サーバー内の接続とデータベースはすべて拒否されます。 ファイアウォール ルールでは、ファイアウォールを通過できるコンピューターの IP アドレスを設定することで、特定のエンティティ (たとえば、開発者コンピューター) にのみサーバーへのアクセスを開くことができます。 また、SDL Database サーバーへのアクセスを許可する IP の範囲を指定することもできます。 たとえば、ファイアウォール設定ページで範囲を指定することにより、組織内の開発者コンピューターの IP アドレスを一度に追加できます。

サーバー レベルまたはデータベース レベルで、ファイアウォール ルールを作成できます。 サーバー レベルの IP ファイアウォール規則は、Azure portal または SSMS で作成できます。 サーバー レベルとデータベース レベルでファイアウォール ルールを設定する方法について詳しくは、[SQL Database での IP ファイアウォール規則の作成](sql-database-security-tutorial.md#create-firewall-rules)に関するページをご覧ください。

#### <a name="service-endpoints"></a>サービス エンドポイント

既定では、SQL Database は、"Azure サービスがサーバーにアクセスできる" ように構成されます。これは、Azure 内の任意の仮想マシンがデータベースへの接続を試行できることを意味します。 これらの試みも認証を受ける必要があります。 ただし、Azure IP がデータベースにアクセスできるようにしたくない場合は、"サーバーへの Azure サービスのアクセスの許可" を無効にできます。 さらに、[VNet サービス エンドポイント](sql-database-vnet-service-endpoint-rule-overview.md)を構成できます。

サービス エンドポイント (SE) を使うと、Azure 内の自分のプライベート仮想ネットワークのみに、重要な Azure リソースを公開できます。 これにより、基本的に、リソースへのパブリック アクセスが除去されます。 仮想ネットワークと Azure の間のトラフィックは、Azure のバックボーン ネットワーク上に留まります。 SE を使わない場合は、強制トンネリング パケット ルーティングを使います。 仮想ネットワークはインターネット トラフィックを強制的に組織に送り、Azure サービス トラフィックは同じルートを経由します。 サービス エンドポイントを使うと、パケットは仮想ネットワークから Azure バックボーン ネットワーク上のサービスに直接流れるので、これを最適化できます。

![VNet サービス エンドポイント](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>予約済み IP

他の選択肢としては、VM に[予約済み IP](../virtual-network/virtual-networks-reserved-public-ip.md) をプロビジョニングし、サーバーのファイアウォール設定でこれらの VM IP アドレスをホワイトリストに登録します。 予約済み IP アドレスを割り当てると、IP アドレスを変更した場合にファイアウォールの規則を更新する手間が省けます。

### <a name="what-port-do-i-connect-to-sql-database-on"></a>SQL Database への接続はどのポートで行われますか

ポート 1433 です。 SQL Database の通信はこのポートで行われます。 企業ネットワーク内から接続するには、組織のファイアウォール設定に送信ルールを追加する必要があります。 ガイドラインとして、Azure 境界の外部にはポート 1433 を公開しないでください。

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>サーバーおよび SQL Database のデータベースでのアクティビティを監視および規制するにはどうすればよいですか

#### <a name="sql-database-auditing"></a>SQL Database 監査

SQL Database では、監査を有効にしてデータベース イベントを追跡することができます。 [SQL Database Auditing](sql-database-auditing.md) は、データベース イベントを記録して、Azure Storage アカウントの監査ログ ファイルに書き込みます。 セキュリティとポリシーの違反の可能性について洞察を得たり、法令順守を維持したりするには、監査が特に有効です。監査が必要であると考えられる特定のイベント カテゴリを定義および構成し、それを基にして、あらかじめ構成されたレポートやダッシュボードを使ってデータベースで発生したイベントの概要を把握できます。 これらの監査ポリシーは、データベース レベルまたはサーバー レベルで適用できます。 サーバー/データベースの監査を有効にする方法については、[SQL Database の監査を有効にする](sql-database-security-tutorial.md#enable-security-features)方法に関するトピックをご覧ください。

#### <a name="threat-detection"></a>脅威の検出

[脅威検出](sql-database-threat-detection.md)を使うと、監査によって発見されたセキュリティやポリシーの違反に、とても簡単に対処できます。 セキュリティの専門化でなくても、システム内の潜在的な脅威や違反に対処できます。 脅威検出には、SQL インジェクションの検出など、組み込み機能もいくつかあります。 SQL インジェクションとは、データを変更または侵害する試みであり、データベース アプリケーションを攻撃する非常に一般的な手段です。 脅威検出機能では、潜在的な脆弱性と SQL インジェクション攻撃、および異常なデータベース アクセス パターン (通常とは異なる場所または未知のプリンシパルからのアクセスなど) を検出するアルゴリズムを複数セット実行します。 データベースで脅威が検出された場合、セキュリティ責任者またはその他の指定された管理者に電子メール通知が送られます。 各通知には、不審なアクティビティの詳細情報と、脅威に対して推奨されるさらなる調査方法や軽減策が記載されています。 脅威検出を有効にする方法については、[脅威検出の有効化](sql-database-security-tutorial.md#enable-security-features)に関するページを参照してください。

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>一般に SQL Database 上のデータを保護するにはどうすればよいですか

暗号化は、機微なデータをセキュリティで保護して侵入者から守るための強力なメカニズムを提供します。 復号化キーがなくては、侵入者は暗号化されたデータを使用できません。 したがって、暗号化は SQL Database に組み込まれている既存のセキュリティ レイヤーの上に別の保護レイヤーを追加します。 SQL Database のデータの保護には 2 つの側面があります。

- データ ファイルとログ ファイルに保存されているデータ
- 使用中のデータ

SQL Database では、既定により、記憶域サブシステムのデータ ファイルとログ ファイルに保存されているデータは、[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) によって常に完全に暗号化されます。 バックアップも暗号化されます。 TDE を使う場合、このデータにアクセスするアプリケーション側を変更する必要はありません。 名前のとおり、暗号化と復号化は透過的に行われます。
使用中と保存中の機微なデータを保護するため、SQL Database には [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) という名前の機能が用意されています。 AE はクライアント側暗号化の 1 種であり、データベースの機密列を暗号化します (このため、これらの列は、データベース管理者および未承認ユーザーに対しては暗号化テキストになります)。 サーバーは、最初に暗号化されたデータを受け取ります。 Always Encrypted のキーもクライアント側に格納されるため、機密列の暗号化を解除できるのは承認済みクライアントのみに限られます。 暗号化キーはクライアントに格納されるため、サーバー管理者およびデータ管理者は機微なデータを確認することはできません。 AE は、未承認のクライアントから物理ディスクまで、テーブル内の機密列をエンド ツー エンドで暗号化します。 AE では現在等値比較がサポートされているため、DBA では引き続き、SQL コマンドの一環として暗号化された列に対してクエリを行うことができます。 Always Encrypted は、[Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)、Windows 証明書ストア、ローカル ハードウェアのセキュリティ モジュールなどの多様なキー ストア オプションと組み合わせて使用できます。

|**特性**|**常に暗号化**|**透過的なデータ暗号化**|
|---|---|---|
|**暗号化の範囲**|End-to-end|保存データ|
|**データベース サーバーは機微なデータにアクセスできる**|いいえ|はい (暗号化は保存データに対するものであるため)|
|**許可される T-SQL の操作**|等値比較|T-SQL のすべての公開されている部分を使用できます|
|**機能を使うために必要なアプリの変更**|最小限|極めて最小限|
|**暗号化の細分性**|列レベル|データベース レベル|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>データベース内の機密データへのアクセスを制限するにはどうすればよいですか

どのようなアプリケーションでも、データベースには機微なデータがある程度の量存在し、無制限にアクセスされないように防ぐ必要があります。 組織内の特定の担当者はこのデータを見る必要がありますが、他のユーザーはこのデータを見ることができてはなりません。 従業員の給与などはその一例です。 管理者は自分の直属の部下の給与情報にアクセスする必要がありますが、個々のチーム メンバーは同僚の給与情報にアクセスできる必要はありません。 もう 1 つのシナリオは、開発段階またはテストの間に機微なデータ (顧客の SSN など) を使用するデータ開発者です。 やはり、この情報は開発者に公開する必要はありません。 このような場合、機微なデータはマスクするか、まったく公開しないようにする必要があります。 SQL Database には、機微なデータを未承認ユーザーが表示できないようにする 2 つのアプローチがあります。

[動的データ マスク](sql-database-dynamic-data-masking-get-started.md)はデータ マスク機能であり、アプリケーション レイヤー上の権限のないユーザーに対して機微なデータをマスクすることでこれらのデータの公開を制限できます。 マスク パターンを作成できるマスク ルールを定義し (たとえば、XXX-XX-0000 のように、国民 ID である SSN の最後の 4 桁のみを表示しそれ以外の多くを X で隠す)、マスク ルールから除外されるユーザーを識別します。 マスクの適用は処理中に行われ、さまざまなデータ カテゴリにさまざまなマスク関数を使用できます。 動的データ マスクを使うと、データベースの機微なデータを自動的に検出して、マスクを適用できます。

[行レベル セキュリティ](/sql/relational-databases/security/row-level-security)を使うと、行レベルでアクセスを制御することができます。 つまり、クエリを実行するユーザー (グループ メンバーシップまたは実行コンテキスト) に基づいて、データベース テーブル内の特定の行が隠ぺいされます。 このアクセス制限は、アプリのロジックを簡素化するため、アプリケーション層ではなくデータベース層で行われます。 最初に公開されない行を排除するフィルター述語を作成し、次にこれらの行にアクセスできるユーザーを定義するセキュリティ ポリシーを作成します。 最後に、エンド ユーザーがクエリを実行すると、ユーザーの特権に応じて、制限された行が表示されるか、またはまったく表示されません。

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>暗号化キーはクラウドでどのように管理すればよいですか

Always Encrypted (クライアント側暗号化) および Transparent Data Encryption (保存時暗号化) ではともに、キー管理オプションが用意されています。 暗号化キーを定期的にローテーションすることをお勧めします。 ローテーションの頻度は、組織内部の規制とコンプライアンス要件の両方に合わせる必要があります。

#### <a name="transparent-data-encryption-tde"></a>透過的なデータ暗号化 (TDE)

TDE は 2 キー階層になっており、各ユーザー データベースのデータは対称的でデータベース固有の AES-256 データベース暗号化キー (DEK) で暗号化され、さらにサーバー固有の非対称の RSA 2048 マスター キーで暗号化されます。 マスター キーは次のどちらかで管理できます。

- SQL Database プラットフォームで自動的。
- キー ストアとして [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) を使用。

既定では、Transparent Data Encryption のマスター キーは利便性のために SQL Database サービスにより管理されます。 組織でマスター キーを管理する必要がある場合、 \[Azure Key Vault] \(sql-database-always-encrypted-azure-key-vault.md) をキー ストアとして使用できます。 Azure Key Vault を使用する場合、組織でキーのプロビジョニング、ローテーション、および権限管理を制御することになります。 TDE マスター キーは DEK を再暗号化しただけのものであるため、[このキーのローテーションおよび種類の切り替え](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)は短時間で行うことができます。 セキュリティとデータ管理で役割が分けられている組織の場合、セキュリティ管理者が TDE マスター キーの主要な素材を Azure Key Vault にプロビジョニングし、サーバーでの保存時暗号化用に使用する Azure Key Vault キー ID をデータベース管理者に提供できます。 Key Vault は、マイクロソフトが暗号化キーを確認または抽出しないように作られています。 また、組織のキーを一元的に管理できます。

#### <a name="always-encrypted"></a>Always Encrypted

Always Encrypted は [2 キー階層](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)になっており、機密データの列は AES 256 列暗号化キー (CEK) で暗号化され、さらに列マスター キー (CMK) で暗号化されます。 Always Encrypted で提供されるクライアント ドライバーには、CMK の長さに関する制限はありません。 CEK の暗号化値はデータベースに格納され、CMK は Windows 証明書ストア、Azure Key Vault、ハードウェア セキュリティ モジュールなどの信頼できるキー ストアに格納されます。

- [CEK と CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) はともにローテーションさせることができます。
- CEK のローテーションはデータ操作のサイズであり、暗号化された列を含むテーブルのサイズによっては時間がかかる場合があります。 そのため、CEK のローテーションを適切に計画することをお勧めします。
- ただし、CMK のローテーションはデータベースのパフォーマンスに影響しないため、個別のロールを使用して行うことができます。

次の図に、Always Encrypted の列マスター キーのキー ストア オプションを示します。

![Always Encrypted の CMK ストア プロバイダー](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>組織と SQL Database の間のトラフィックを最適化してセキュリティ保護するにはどうすればよいですか

組織と SQL Database の間のネットワーク トラフィックは、一般に、パブリック ネットワーク経由でルーティングされます。 ただし、このパスを最適化してセキュリティを強化する場合は、ExpressRoute を検討できます。 ExpressRoute を使うと、基本的に、プライベート接続経由で企業ネットワークを Azure プラットフォームに拡張できます。 これにより、パブリック インターネットを使う必要がなくなります。 また、パブリック インターネット経由の場合より、セキュリティと信頼性が強化され、ルーティングの最適化によりネットワーク待機時間が短縮されてはるかに高速になります。 組織と Azure の間で大きなデータ チャンクを転送することを計画している場合、ExpressRoute を使うとコストの点でメリットがあります。 組織 から Azure への接続は、3 つの異なる接続モデルから選ぶことができます。

- [クラウドの Exchange で同一場所配置](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [任意の環境間](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [ポイント ツー ポイント](../expressroute/expressroute-connectivity-models.md#Ethernet)

また、ExpressRoute では、購入した帯域幅制限の最大 2 倍を追加料金なしで利用できます。 また、ExpressRoute を使用してリージョン間接続を構成することもできます。 ER 接続プロバイダーの一覧については、「[Express Route パートナーとピアリングの場所](../expressroute/expressroute-locations.md)」を参照してください。 次の記事では、ExpressRoute についてさらに詳しく説明されています。

- [ExpressRoute の概要](../expressroute/expressroute-introduction.md)
- [前提条件](../expressroute/expressroute-prerequisites.md)
- [ワークフロー](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL Database はすべての規制要件に準拠していますか。また、SQL Database は組織のコンプライアンスにどのように役立ちますか

SQL Database は、さまざまな規制に準拠しています。 SQL Database が準拠するコンプライアンスの最新の一覧を参照するには、[Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) にアクセスしてご自分の組織に重要なコンプライアンスを細かく調査し、SQL Database が準拠する Azure サービスに含まれているかどうかを確認してください。 SQL Database はコンプライアンスに準拠しているサービスとして認証を受けていますが、これは組織のサービスのコンプライアンスを支援するものであり、自動的にコンプライアンスを保証するものではないことに注意してください。

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>移行後のインテリジェントなデータベースの監視とメンテナンス

データベースを SQL Database に移行した後は、データベースを監視し (たとえば、リソースの使用状況の確認や DBCC のチェック)、定期的にメンテナンス (たとえば、インデックスの再構築や再構成、統計情報など) を行います。 幸いにも、SQL Database はこの点でインテリジェントであり、履歴の傾向と記録されたメトリックや統計情報を使って、データベースの監視とメンテナンスを積極的に支援し、アプリケーションが常に最適な状態で稼働できるようにします。 構成のセットアップによっては、Azure SQL DB はメンテナンス タスクを自動的に実行できる場合もあります。 SQL Database のデータベースの監視には、3 つの側面があります。

- パフォーマンスの監視と最適化。
- セキュリティの最適化。
- コストの最適化。

### <a name="performance-monitoring-and-optimization"></a>パフォーマンスの監視と最適化

Query Performance Insight を使うと、データベースのワークロードに合わせて調整された推奨事項が得られ、アプリケーションは常に最適なレベルで実行できます。 推奨事項を自動的に適用し、面倒なメンテナンス タスクを実行しなくてよいように設定することもできます。 Index Advisor を使うと、ワークロードに基づくインデックスの推奨設定を自動的に実装できます。これは自動チューニングと呼ばれます。 推奨事項はアプリケーション ワークロードの変化に応じて進化し、最も重要な推奨事項が提供されます。 また、手動でこれらの推奨事項を確認し、自分で判断して適用することもできます。  

### <a name="security-optimization"></a>セキュリティの最適化

SQL Database では、データの保護に役立つ実践的なセキュリティの推奨事項と、データベースに対する潜在的な脅威になる可能性のある不審なデータベース アクティビティを識別して調査する脅威検出が提供されます。 [脆弱性評価](sql-vulnerability-assessment.md): データベース スキャンおよびレポート サービスであり、大規模なデータベースのセキュリティ状態を監視して、セキュリティ上のリスクおよびユーザーが定義したセキュリティ ベースラインからのずれを特定します。 各スキャン後には、ユーザーに合わせた実行可能な手順の一覧と修復スクリプト、およびコンプライアンス要件に準拠するために使用可能な評価レポートが提供されます。

Azure Security Center では、全体的なセキュリティ推奨事項を確認し、シングル クリックでそれらを適用できます。

### <a name="cost-optimization"></a>コストの最適化

Azure SQL プラットフォームにより、サーバーのデータベース全体の使用履歴が分析され、お客様に適したコストの最適化オプションが評価および推奨されます。 この分析により実践的な推奨事項を分析して作成するには、通常 2 週間かかります。 エラスティック プールはそのようなオプションの 1 つです。 推奨事項は、バナーとしてポータルに表示されます。

![エラスティック プールの推奨事項](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

[Advisor] セクションでこの分析を見ることもできます。

![エラスティック プールの推奨事項 - Advisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>SQL Database のパフォーマンスとリソース使用率を監視するにはどうすればよいですか

SQL Database では、プラットフォームのインテリジェントな洞察を利用して、パフォーマンスを監視し、それに応じて調整することができます。 SQL Database のパフォーマンスとリソース使用率は、次の方法で監視できます。

#### <a name="azure-portal"></a>Azure ポータル

Azure portal でデータベースを選択し [概要] ペインのグラフをクリックすると、データベースの使用率が表示されます。 このグラフは、CPU 使用率、DTU の割合、データ IO の割合、セッションの割合、データベース サイズの割合など、複数のメトリックを表示するように変更できます。

![監視グラフ](./media/sql-database-manage-after-migration/monitoring-chart.png)

![監視グラフ 2](./media/sql-database-manage-after-migration/chart.png)

このグラフでは、リソース別のアラートを構成することもできます。 これらのアラートを使用することで、リソースの状態に電子メールで対応したり、HTTPS/HTTP エンドポイントに書き込んだり、アクションを実行することができます。 詳細については、[アラートの作成](sql-database-insights-alerts-portal.md)に関するページをご覧ください。

#### <a name="dynamic-management-views"></a>動的管理ビュー

リソース使用率の統計について、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 動的管理ビューでクエリを実行すると過去 1 時間における履歴を確認でき、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) システム カタログ ビューでクエリを実行すると過去 14 日間の履歴を確認できます。

#### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) では、指定したデータベースについて、リソース使用率が上位のクエリおよび実行時間が長いクエリの履歴を確認できます。 リソース使用率、期間、および実行頻度で、上位のクエリをすばやく識別できます。 クエリを追跡し、回帰を検出できます。 この機能を使用するには、データベースで[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を有効化しアクティブにする必要があります。

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure Monitor ログの Azure SQL Analytics (プレビュー)

[Azure Monitor ログ](../azure-monitor/insights/azure-sql.md)では、主要な SQL Azure パフォーマンス メトリックを収集して視覚化することができます。ワークスペースごとに SQL Database は 150,000 個、SQL エラスティック プールは 5,000 個がサポートされています。 これにより、監視を行い、通知を受け取ることができます。 SQL Database およびエラスティック プールのメトリックは、複数の Azure サブスクリプションとエラスティック プールにわたって監視でき、これらのメトリックを利用してアプリケーション スタックの各層における問題を特定できます。

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>パフォーマンスに問題があります。SQL Database と SQL Server のトラブルシューティングの方法の違いはどのようなものですか

クエリおよびデータベース パフォーマンスの問題の診断に使用するトラブルシューティング手法の主要な部分は、変わりません。 クラウドで利用されている SQL Server エンジンは同じものです。 ただし、Azure SQL DB プラットフォームには "インテリジェンス" が組み込まれています。 それにより、パフォーマンスの問題のトラブルシューティングと診断が容易になります。 ユーザーに代わって是正措置の一部を実行することもでき、場合によっては事前に問題を自動的に修復します。

パフォーマンスの問題のトラブルシューティングに向けたアプローチには、[Query Performance Insight (QPI)](sql-database-query-performance.md) や [Database Advisor](sql-database-advisor.md) などのインテリジェントな機能を組み合わせて使うと大きなメリットがあり、方法においてはその点が異なります。問題のトラブルシューティングに役立つかもしれない重要な詳細を手作業で調べる必要はもうありません。 難しい作業はプラットフォームがやってくれます。 その 1 つの例は QPI です。 QPI では、クエリ レベルまでドリルダウンし、過去の傾向を見て、クエリが回帰したときを正確に識別できます。 Database Advisor では、インデックスの欠落、インデックスの削除、クエリのパラメーター化など、全体的なパフォーマンスの向上に役立つ推奨事項が提供されます。

パフォーマンスのトラブルシューティングでは、アプリケーションだけの問題であるか、それともデータベースの問題がアプリケーションのパフォーマンスに影響しているのかを、識別することが重要です。 多くの場合、パフォーマンスの問題はアプリケーション層にあります。 アーキテクチャまたはデータ アクセス パターンが原因である可能性があります。 たとえば、ネットワーク待機時間の影響を受けやすい、頻繁に通信を行うアプリケーションについて考えます。 この場合、アプリケーションとサーバーの間で多数の短い要求がやり取りされ、混雑したネットワークではこれらのラウンドトリップが急速に蓄積されるため、アプリケーションに悪影響があります。 この場合にパフォーマンスを向上させるには、[バッチ クエリ](sql-database-performance-guidance.md#batch-queries)を使うことができます。 バッチを使うと、要求が一括処理されるようになるので非常に有効です。ラウンドトリップ遅延時間を削減し、アプリケーションのパフォーマンスを向上させることができます。

さらに、データベースの全体的なパフォーマンスの低下が生じている場合は、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) および [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) の各動的管理ビューを監視して、CPU、IO、メモリ使用率を把握できます。 パフォーマンスは、データベースのリソース不足のために影響を受けている可能性があります。 ワークロードの需要の増減に応じて、コンピューティング サイズやサービス レベルの変更が必要になる場合があります。

パフォーマンスのチューニングに関する問題の包括的な推奨事項については、「[データベースの調整](sql-database-performance-guidance.md#tune-your-database)」をご覧ください。

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>適切なサービス レベルとコンピューティング サイズを使用していることを確認するにはどうすればよいですか

SQL Database には、Basic、Standard、Premium というさまざまなサービス レベルが用意されています。 各サービス レベルに対応した予測可能なパフォーマンスが保証されます。 ワークロードによっては、アクティビティのバーストにより、リソース使用率が現在のコンピューティング サイズの上限に達する可能性があります。 このような場合は、最初にチューニングが役に立つかどうかを評価するのが有効です (たとえば、インデックスの追加や変更など)。 まだ制限の問題が発生する場合は、上位のサービス レベルまたはコンピューティング サイズへの移行を検討します。

|**サービス レベル**|**一般的なユース ケース シナリオ**|
|---|---|
|**Basic**|コンカレンシー、スケール、パフォーマンスに関する要件が高くない、ユーザーとデータベースの数が少ないアプリケーション。 |
|**Standard**|コンカレンシー、スケール、パフォーマンスの要件が高く、IO の要求が低から中程度であるアプリケーション。 |
|**Premium**|同時実行ユーザー数が多く、CPU/メモリの要件が高く、IO 要求が高いアプリケーション。 コンカレンシーが高く、スループットが高く、待機時間の影響を受けやすいアプリは、Premium レベルを利用できます。 |
|||

コンピューティング サイズが適切であることを確認するには、「SQL Database のパフォーマンスとリソース使用率を監視するにはどうすればよいですか?」で説明したいずれかの方法で、クエリとデータベース リソース使用量を監視できます。 クエリ/データベースによるCPU/メモリ使用量が一貫して高い場合は、上位のコンピューティング サイズへのスケールアップを検討します。 同様に、ピーク時間帯でもリソースがあまり使われていない場合は、現在のコンピューティング サイズからのスケールダウンを検討します。

SaaS アプリ パターンまたはデータベース統合シナリオがある場合は、エラスティック プールを使ってコストを最適化することを検討します。 エラスティック プールは、データベースの統合とコストの最適化を実現する優れた方法です。 エラスティック プールを使った複数のデータベースの管理については、[プールとデータベースの管理](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)に関するページをご覧ください。

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>どのくらいの頻度でデータベースの整合性チェックを実行する必要がありますか

SQL Database では、特定のクラスのデータ破損にデータを失うことなく自動的に対処できるスマートな手法が使われています。 これらの手法はサービスに組み込まれており、必要に応じてサービスで利用されます。 サービス間のデータベース バックアップは、定期的に復元して DBCC CHECKDB を実行することでテストされます。 問題がある場合、SQL Database は事前にそれらに対処します。 [ページの自動修復](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)は、破損している、またはデータの整合性に問題があるページの修正に利用されます。 データベースのページは常に、ページの整合性を検証する既定の CHECKSUM 設定で検証されます。 SQL Database は、データベースのデータの整合性を先行的に監視および確認し、問題が発生した場合は高い優先順位で対処します。 これらに加えて、必要に応じて、独自の整合性チェックを実行することもできます。  詳しくは、「[Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)」(Azure SQL Database のデータ整合性) をご覧ください。

## <a name="data-movement-after-migration"></a>移行後のデータの移動

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>SQL Database のデータを BACPAC ファイルとしてエクスポートおよびインポートするにはどうすればよいですか

- **エクスポート**:Azure portal から Azure SQL データベースを BACPAC ファイルとしてエクスポートできます。

   ![データベースのエクスポート](./media/sql-database-export/database-export1.png)

- **インポート**:Azure portal を使って、データを BACPAC ファイルとしてデータベースにインポートすることもできます。

   ![データベースのインポート](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>SQL Database と SQL Server の間でデータを同期するにはどうすればよいですか

これを行うには、いくつかの方法があります。

- **[データ同期](sql-database-sync-data.md)** – この機能を使うと、複数のオンプレミス SQL Server データベースと SQL Database の間でデータを双方向に同期することができます。 オンプレミスの SQL Server データベースと同期するには、ローカル コンピューターに同期エージェントをインストールして構成し、発信 TCP ポート 1433 を開く必要があります。
- **[トランザクション レプリケーション](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – トランザクション レプリケーションを使うと、オンプレミスをパブリッシャー、Azure SQL DB をサブスクライバーにして、オンプレミスから Azure SQL DB にデータを同期できます。 現時点では、このセットアップのみがサポートされています。 最小限のダウンタイムでオンプレミスから Azure SQL にデータを移行する方法の詳細については、「[トランザクション レプリケーションの使用](sql-database-single-database-migrate.md#method-2-use-transactional-replication)」を参照してください。

## <a name="next-steps"></a>次の手順

[SQL Database](sql-database-technical-overview.md) についての詳細情報。
