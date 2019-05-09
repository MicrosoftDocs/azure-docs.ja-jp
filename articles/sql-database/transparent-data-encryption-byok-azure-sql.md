---
title: TDE - Azure Key Vault の統合または Bring Your Own Key (BYOK) - Azure SQL Database | Microsoft Docs
description: SQL Database と Data Warehouse の Transparent Data Encryption (TDE) で Azure Key Vault を使用した Bring Your Own Key (BYOK) がサポートされます。 BYOK 対応 TDE の概要、利点、しくみ、考慮事項、および推奨事項。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 28bb6fbc3b6b9552850244d608e6587e8a9052de
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57891003"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure Key Vault のユーザー管理キーを使用した Azure SQL Transparent Data Encryption:Bring Your Own Key のサポート

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) と Azure Key Vault の統合により、TDE 保護機能と呼ばれる、ユーザーが管理する非対称キーを使用してデータベース暗号化キー (DEK) を暗号化することが可能になります。 これは、一般に、Transparent Data Encryption に対する Bring Your Own Key (BYOK) のサポートとも呼ばれます。  BYOK シナリオでは、TDE 保護機能は、ユーザーが所有および管理する [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (Azure のクラウドベースの外部キー管理システム) 内に格納されます。 TDE 保護機能はキー コンテナーによって [生成する](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) か、オンプレミスの HSM デバイスからキー コンテナーに [転送](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) することができます。 TDE の DEK はデータベースのブート ページに格納され、Azure Key Vault に格納されている TDE 保護機能によって暗号化と暗号化解除が実行されます。TDE 保護機能はそれ以外の場所で使用されることはありません。  SQL Database には、DEK の暗号化解除と暗号化のために、ユーザーが所有するキー コンテナーへのアクセス許可が付与されている必要があります。 論理 SQL サーバーからキー コンテナーへのアクセス許可が取り消されると、データベースはアクセス不可となり、すべてのデータが暗号化されます。 Azure SQL Database の場合、TDE 保護機能は論理 SQL サーバー レベルで設定され、そのサーバーに関連付けられているすべてのデータベースによって継承されます。 [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance) の場合、TDE 保護機能はインスタンス レベルで設定され、そのインスタンス上のすべての*暗号化された*データベースによって継承されます。 *サーバー*という言葉は、別途明記されていない限り、このドキュメントではサーバーとインスタンスの両方を指します。

TDE と Azure Key Vault の統合により、ユーザーは Azure Key Vault 機能を使用して、キーの交換、キー コンテナーのアクセス許可、キーのバックアップ、すべての TDE 保護機能の監査/レポートの有効化などのキー管理タスクを制御できます。 Key Vault はキーの一元管理を提供し、厳しく監視されたハードウェア セキュリティ モジュール (HSM) を利用します。また、キー管理とデータ管理の職務の分離を可能にすることでセキュリティ ポリシーの遵守を支援します。  

TDE と Azure Key Vault の統合には次のような利点があります。

- TDE 保護機能を自己管理できるので、透明性が向上し、きめ細かく制御できる
- データベースをアクセス不可にするために、いつでもアクセス許可を取り消すことができる
- Key Vault でホストすることにより、TDE 保護機能 (および他の Azure サービスで使用されている他のキーやシークレット) を一元管理できる
- 組織内でのキー管理とデータ管理の責任を分離することで、職務の分離を支援する
- Key Vault は Microsoft が暗号化キーを参照または抽出しないように設計されているので、ユーザー独自のクライアントからの信頼が向上する
- キーの交換がサポートされている

> [!IMPORTANT]
> サービス管理 TDE を使用しているユーザーが Key Vault を使い始める場合、Key Vault の TDE 保護機能に切り替えるプロセス中も TDE は引き続き有効です。 ダウンタイムも、データベース ファイルの再暗号化もありません。 サービス管理キーから Key Vault キーへの切り替えに必要なのは、データベース暗号化キー (DEK) の再暗号化だけであり、これは高速のオンライン操作です。

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>TDE と Azure Key Vault の統合によるサポートの仕組み

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

![Key Vault に対するサーバーの認証](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Key Vault の TDE 保護機能を使用するように TDE が最初に構成されたときに、サーバーは各 TDE 対応データベースの DEK を Key Vault に送信してキーのラップを要求します。 Key Vault から、暗号化されたデータベース暗号化キーが返され、ユーザー データベースに格納されます。  

> [!IMPORTANT]
> **TDE 保護機能は、Azure Key Vault に一度格納されると、Azure Key Vault の外部に移動されることはない**ことに注意してください。 サーバーが実行できるのは、Key Vault 内の TDE 保護機能のキー マテリアルにキー操作要求を送信することだけであり、**TDE 保護機能にアクセスしたり、TDE 保護機能をキャッシュしたりすることはありません**。 Key Vault 管理者には、サーバーの Key Vault アクセス許可をいつでも取り消すことができる権限があります。アクセス許可を取り消した場合、サーバーへのすべての接続が切断されます。

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>TDE と Azure Key Vault の構成に関するガイドライン

### <a name="general-guidelines"></a>一般的なガイドライン

- Azure Key Vault と Azure SQL Database/Managed Instance が必ず同じテナントに属するようにします。  キー コンテナーとサーバーのテナント間の対話は**サポートされていません**。
- 必要なリソースに使用するサブスクリプションを決定します。後でサーバーをサブスクリプション間で移動するには、BYOK 対応 TDE の新しいセットアップが必要になります。 リソースの移動の詳細については、[こちら](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)をご覧ください。
- TDE と Azure Key Vault を構成するときは、ラップ/ラップ解除操作の繰り返しによってキー コンテナーにかかる負荷を考慮することが重要です。 たとえば、SQL Database サーバーに関連付けられているすべてのデータベースで同じ TDE 保護機能が使用されるため、そのサーバーのフェールオーバーが発生すると、サーバー内のデータベースと同じ数のキー操作がコンテナーに対してトリガーされます。 これまでの経験と文書化されている [Key Vault サービスの制限](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)に基づき、コンテナー内の TDE 保護機能にアクセスするときに高可用性を常に確保するために、1 つのサブスクリプションの 1 つの Azure Key Vault に関連付けるデータベースの数を、Standard/General Purpose データベースの場合は最大 500 個、Premium/Business Critical データベースの場合は最大 200 個にすることをお勧めします。
- 推奨:TDE 保護機能のコピーをオンプレミスに保持します。  これには、TDE 保護機能をローカルに作成するための HSM デバイスと、TDE 保護機能のローカル コピーを格納するためのキー エスクロー システムが必要です。  ローカル HSM から Azure Key Vault にキーを転送する方法については、[こちら](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)をご覧ください。

### <a name="guidelines-for-configuring-azure-key-vault"></a>Azure Key Vault の構成に関するガイドライン

- キー (またはキー コンテナー) を誤って削除した場合に、データの損失から保護するために、[論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)を有効にしてキー コンテナーを作成します。  [PowerShell を使用して、キー コンテナーの "soft-delete" プロパティを有効にする](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)必要があります (このオプションは AKV ポータルからはまだ使用できませんが、Azure SQL で必要です)。  
  - 論理的に削除されたリソースは、復旧または消去されない限り、一定期間 (90 日間) 保持されます。
  - **recover** アクションと **purge** アクションには、キー コンテナーのアクセス ポリシーに独自のアクセス許可が関連付けられています。
- キー コンテナーにリソース ロックを設定して、この重要なリソースを削除できるユーザーを制御し、誤削除や許可されていない削除を防ぎます。  リソース ロックの詳細については、[こちら](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)をご覧ください。

- Azure Active Directory (Azure AD) ID を使用して、SQL Database サーバーにキー コンテナーへのアクセス権を付与します。  ポータル UI を使用すると、Azure AD ID が自動的に作成され、キー コンテナーのアクセス許可がサーバーに付与されます。  PowerShell を使用して BYOK 対応 TDE を構成する場合は、Azure AD ID を作成し、完了を確認する必要があります。 PowerShell を使用するときの詳細な手順については、[BYOK 対応 TDE の構成](transparent-data-encryption-byok-azure-sql-configure.md)と [Managed Instance 用 BYOK 対応 TDE の構成](https://aka.ms/sqlmibyoktdepowershell)に関する記事を参照してください。

  > [!NOTE]
  > Azure AD ID が**誤って削除された場合や、サーバーのアクセス許可が取り消された場合** (キー コンテナーのアクセス ポリシーを使用)、サーバーはキー コンテナーへのアクセスを失い、TDE で暗号化されたデータベースは 24 時間以内にアクセスできなくなります。

- Azure Key Vault でファイアウォールと仮想ネットワークを使用するときは、次を構成する必要があります。 
  - [Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\) - [はい] を選択

  > [!NOTE]
  > TDE で暗号化された SQL データベースがファイアウォールをバイパスできないために、キー コンテナーへのアクセスを失った場合、データベースは 24 時間以内にアクセスできなくなります。

- すべての暗号化キーの監査およびレポートを有効にします。Key Vault が提供するログは、他のセキュリティ情報およびイベント管理 (SIEM) ツールに簡単に挿入できます。 Operations Management Suite (OMS) [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) は、既に統合されているサービスの一例です。
- 暗号化されたデータベースの高可用性を確保するには、異なるリージョンに存在する 2 つの Azure Key Vault で各 SQL Database サーバーを構成します。

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>TDE 保護機能 (非対称キー) の構成に関するガイドライン

- ローカル HSM デバイス上で暗号化キーをローカルに作成します。 Azure Key Vault に格納できるように、非対称の RSA 2048 キーであることを確認します。
- キー エスクロー システムにキーをエスクローします。  
- 暗号化キー ファイル (.pfx、.byok、または .backup) を Azure Key Vault にインポートします。

   > [!NOTE]
   > テストのために、Azure Key Vault を使用してキーを作成することは可能ですが、秘密キーはキー コンテナーの外部に移動できないため、このキーをエスクローすることはできません。  キーを失うと (キー コンテナーでの誤削除、期限切れなど)、データが完全に失われるので、実稼働データの暗号化に使用するキーを常にバックアップし、エスクローしてください。

- 有効期限のないキーを使用する (既に使用中のキーに有効期限を設定しない): **キーの有効期限が切れると、暗号化されたデータベースは TDE 保護機能へのアクセスを失い、24 時間以内にアクセスできなくなります**。
- キーが有効であり、"*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作を実行するためのアクセス許可があることを確認します。
- Azure Key Vault のキーを初めて使用する前に、Azure Key Vault キーのバックアップを作成します。 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) コマンドの詳細については、こちらをご覧ください。
- キーに変更を加えるたびに (ACL の追加、タグの追加、キー属性の追加など) 新しいバックアップを作成します。
- データベースの古いバックアップを復元できるように、キーを交換するときは、キー コンテナーにキーの**以前のバージョンを保持**します。 データベースの TDE 保護機能が変更されても、データベースの古いバックアップが、最新の TDE 保護機能を使用するように**更新されることはありません**。  各バックアップの復元時には、バックアップの作成時に使用された TDE 保護機能が必要になります。 キーの交換を実行するには、[PowerShell を使用した Transparent Data Encryption 保護機能の交換](transparent-data-encryption-byok-azure-sql-key-rotation.md)に関する記事の手順に従います。
- サービス管理キーに戻したら、以前に使用したすべてのキーを Azure Key Vault に保持します。  これにより、Azure Key Vault に格納された TDE 保護機能を使用してデータベースのバックアップを復元できます。  保存されているすべてのバックアップが、サービス管理キーを使用して作成されるまで、Azure Key Vault を使用して作成された TDE 保護機能を保持する必要があります。  
- [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) を使用して、これらのキーの回復可能なバックアップ コピーを作成します。
- セキュリティ インシデントの発生時に、データ損失のリスクを伴わずに、侵害された可能性のあるキーを削除するには、[侵害された可能性のあるキーの削除](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)に関する記事の手順に従ってください。

## <a name="high-availability-geo-replication-and-backup--restore"></a>高可用性、geo レプリケーション、バックアップ/復元

### <a name="high-availability-and-disaster-recovery"></a>高可用性と障害復旧

Azure Key Vault で高可用性を構成する方法は、使用しているデータベースと SQL Database サーバーの構成によって異なります。ここでは、2 つの異なるケースの推奨される構成について説明します。  1 つ目のケースは、geo 冗長が構成されていないスタンドアロンのデータベースまたは SQL Database サーバーです。  2 つ目のケースは、フェールオーバー グループまたは geo 冗長で構成されたデータベースまたは SQL Database サーバーです。この場合、geo フェールオーバーが確実に機能するように、各 geo 冗長コピーには、フェールオーバー グループ内のローカル Azure Key Vault が含まれている必要があります。

最初のケースで、geo 冗長が構成されていないデータベースおよび SQL Database サーバーの高可用性が必要な場合は、同じキー マテリアルで、2 つの異なるリージョンの 2 つの異なるキー コンテナーを使用するようにサーバーを構成することを強くお勧めします。 これを実現するには、SQL Database サーバーと同じリージョンに併置されたプライマリ キー コンテナーを使用して TDE 保護機能を作成し、別の Azure リージョンのキー コンテナーにキーを複製します。これにより、データベースの稼働中にプライマリ キー コンテナーが停止した場合に、サーバーはもう 1 つのキー コンテナーにアクセスできます。 Backup-AzKeyVaultKey コマンドレットを使用して、プライマリ キー コンテナーから暗号化された形式のキーを取得し、Restore-AzKeyVaultKey コマンドレットを使用して、2 つ目のリージョンのキー コンテナーを指定します。

![1 台のサーバーの HA (Geo-DR なし)](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Azure Key Vault で Geo-DR を構成する方法

暗号化されたデータベースの TDE 保護機能の高可用性を維持するには、既存のあるいは必要な SQL Database フェールオーバー グループまたはアクティブ geo レプリケーション インスタンスに基づいて、冗長 Azure Key Vault を構成する必要があります。  geo レプリケートされたサーバーごとに個別のキー コンテナーが必要であり、同じ Azure リージョンにサーバーと併置する必要があります。 1 つのリージョンの停止によってプライマリ データベースにアクセスできなくなり、フェールオーバーがトリガーされた場合は、セカンダリ データベースがセカンダリ キー コンテナーを使用して引き継ぐことができます。

geo レプリケートされた Azure SQL Database の場合、Azure Key Vault の次の構成が必要です。

- リージョン内にキー コンテナーがあるプライマリ データベースが 1 つと、リージョン内にキー コンテナーがあるセカンダリ データベースが 1 つ。
- 少なくとも 1 つのセカンダリが必要です。最大 4 つのセカンダリがサポートされています。
- セカンダリのセカンダリ (チェーン) はサポートされていません。

次のセクションでは、セットアップと構成の手順について詳しく説明します。

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault の構成手順

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) をインストールする
- 2 つの Azure Key Vault を 2 つの異なるリージョンに作成します。作成時に、[PowerShell を使用して各キー コンテナーの "soft-delete" プロパティを有効にします](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) (このオプションは AKV ポータルからはまだ使用できませんが、SQL で必要です)。
- キーのバックアップと復元が機能するように、2 つの Azure Key Vault は、同じ Azure Geo で利用できる 2 つのリージョンに配置する必要があります。  SQL Geo-DR の要件を満たすために、2 つのキー コンテナーを異なる Geo に配置する必要がある場合は、オンプレミスの HSM からキーをインポートできる [BYOK プロセス](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)に従います。
- 1 つ目のキー コンテナーに次のような新しいキーを作成します。  
  - RSA/RSA-HSA 2048 キー
  - 有効期限なし
  - キーが有効化され、取得、キーのラップ、キーのラップ解除の各操作を実行するためのアクセス許可がある
- 主キーをバックアップし、2 つ目のキー コンテナーにキーを復元します。  「[Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)」および「[Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey)」をご覧ください。

### <a name="azure-sql-database-configuration-steps"></a>Azure SQL Database の構成手順

次の構成手順は、新しい SQL デプロイから開始するか、既存の SQL Geo-DR デプロイを使用するかによって異なります。  まず、新しいデプロイの構成手順を概説します。次に、Azure Key Vault に格納されている TDE 保護機能を、Geo-DR リンクが既に確立されている既存のデプロイに割り当てる方法を説明します。

**新しいデプロイの手順**:

- 以前に作成したキー コンテナーと同じ 2 つのリージョンに、2 つの SQL Database サーバーを作成します。
- SQL Database サーバーの TDE ウィンドウを選択し、SQL Database サーバーごとに次の操作を行います。  
  - 同じリージョン内の AKV を選択します。
  - TDE 保護機能として使用するキーを選択します。各サーバーでは、TDE 保護機能のローカル コピーが使用されます。
  - ポータルでこれを行うと、SQL Database サーバーの [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) が作成されます。この ID は、キー コンテナーにアクセスするために、SQL Database サーバーのアクセス許可を割り当てるときに使用されるので、削除しないでください。 アクセスを取り消すには、SQL Database サーバーのアクセス許可ではなく、Azure Key Vault のアクセス許可を削除します。これは、キー コンテナーにアクセスするために SQL Database サーバーのアクセス許可を割り当てるときに使用されます。
- プライマリ データベースを作成します。
- [アクティブ geo レプリケーションのガイダンス](sql-database-geo-replication-overview.md)に従ってシナリオを完了します。この手順により、セカンダリ データベースが作成されます。

![フェールオーバー グループと Geo-DR](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> データベース間の geo リンクの確立に進む前に、両方のキー コンテナーに同じ TDE 保護機能が存在することを確認することが重要です。

**Geo-DR デプロイを使用した既存の SQL DB の手順**:

SQL Database サーバーが既に存在し、プライマリ データベースとセカンダリ データベースが既に割り当てられているため、Azure Key Vault の構成手順は、次の順序で実行する必要があります。

- セカンダリ データベースをホストする SQL Database サーバーから始めます。
  - 同じリージョンにあるキー コンテナーを割り当てる
  - TDE 保護機能を割り当てる
- 次に、プライマリ データベースをホストする SQL Database サーバーに移ります。
  - セカンダリ DB に使用したものと同じ TDE 保護機能を選択する

![フェールオーバー グループと Geo-DR](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>キー コンテナーをサーバーに割り当てるときには、セカンダリ サーバーから始めることが重要です。  2 番目の手順では、キー コンテナーをプライマリ サーバーに割り当て、TDE 保護機能を更新します。この時点で、レプリケートされたデータベースで使用される TDE 保護機能を両方のサーバーで使用できるため、Geo-DR リンクは引き続き機能します。

SQL Database Geo-DR シナリオで、Azure Key Vault のユーザー管理のキーを使用して TDE を有効にする前に、SQL Database の geo レプリケーションに使用されるリージョンに、同じ内容の 2 つの Azure Key Vault を作成して管理することが重要です。  "同じ内容" とは、具体的には、すべてのデータベースで使用される TDE 保護機能に両方のサーバーがアクセスできるように、どちらのキー コンテナーにも同じ TDE 保護機能のコピーが含まれている必要があることを意味します。  今後、2 つのキー コンテナーを同期させておく必要があります。つまり、キーの交換後に 2 つのキー コンテナーに TDE 保護機能の同じコピーが含まれており、ログ ファイルまたはバックアップに使用されるキーの古いバージョンが保持されている必要があります。また、TDE 保護機能は同じキー プロパティを保持する必要があり、キー コンテナーは SQL の同じアクセス許可を保持する必要があります。  

[アクティブ geo レプリケーションの概要](sql-database-geo-replication-overview.md)に関する記事の手順に従ってテストを行い、フェールオーバーをトリガーします。2 つのキー コンテナーに対する SQL のアクセス許可が保持されていることを確認するために、この作業を定期的に行う必要があります。

### <a name="backup-and-restore"></a>バックアップと復元

Key Vault のキーを使用して TDE でデータベースを暗号化すると、生成されるバックアップも同じ TDE 保護機能で暗号化されます。

Key Vault の TDE 保護機能で暗号化されたバックアップを復元するには、キー マテリアルが元のキー名で元のコンテナーに引き続き存在することを確認します。 データベースの TDE 保護機能が変更されても、データベースの古いバックアップが、最新の TDE 保護機能を使用するように更新されることは**ありません**。 そのため、データベースのバックアップを復元できるように、TDE 保護機能の古いバージョンをすべて Key Vault に保持しておくことをお勧めします。

バックアップの復元に必要なキーが元のキー コンテナーに残っていない場合は、"Target server `<Servername>` does not have access to all AKV Uris created between <Timestamp #1> and <Timestamp #2>. Please retry operation after restoring all AKV Uris." (ターゲット サーバー <Servername> は <Timestamp #1> から <Timestamp #2> までの間に作成されたどの AKV URI にもアクセスできません。 すべての AKV URI を復元してから操作をやり直してください\) というエラー メッセージが返されます。

これを軽減するには、[Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) コマンドレットを実行して、サーバーに追加された Key Vault のキーのリストを取得します (ユーザーによって削除された場合を除きます)。 すべてのバックアップを復元できるようにするには、バックアップのターゲット サーバーがこれらのキーのすべてにアクセスできることを確認します。

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

SQL Database のバックアップの回復の詳細については、[Azure SQL Database の復旧](sql-database-recovery-using-backups.md)に関する記事をご覧ください。 SQL Data Warehouse のバックアップの回復の詳細については、[Azure SQL Data Warehouse の復元](../sql-data-warehouse/backup-and-restore.md)に関する記事をご覧ください。

バックアップ ログ ファイルに関するその他の考慮事項:TDE 保護機能が交換され、データベースが新しい TDE 保護機能を使用している場合でも、バックアップ ログ ファイルは元の TDE 暗号化機能で暗号化されたままになっています。  復元時に、データベースを復元するには両方のキーが必要になります。  サービス管理 TDE を使用するようにデータベースが変更された場合でも、Azure Key Vault に格納されている TDE 保護機能がログ ファイルで使用されている場合は、復元時にこのキーが必要になります。
