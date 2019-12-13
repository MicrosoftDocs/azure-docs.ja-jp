---
title: カスタマー マネージド Transparent Data Encryption (TDE)
description: SQL Database と Data Warehouse の Transparent Data Encryption (TDE) で Azure Key Vault を使用した Bring Your Own Key (BYOK) がサポートされます。 BYOK 対応 TDE の概要、利点、しくみ、考慮事項、および推奨事項。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 11/19/2019
ms.openlocfilehash: c8a1e2a19fa3c8691cdb381669dc3d4db189c42d
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995849"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>カスタマー マネージド キーを使用した Azure SQL Transparent Data Encryption

カスタマー マネージド キーを使用した Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) を使用すると、保存データの保護に Bring Your Own Key (BYOK) シナリオが可能になり、組織がキーとデータの管理における職務の分離を実施できるようになります。 カスタマー マネージド Transparent Data Encryption を使用すると、キー ライフサイクル管理 (キーの作成、アップロード、ローテーション、削除)、キーの使用権限、およびキーに対する操作の監査は、顧客の責任となり、顧客がこれらを完全に制御できます。

このシナリオでは、TDE 保護機能と呼ばれるデータベース暗号化キー (DEK) の暗号化に使用されるキーは、顧客が所有し、顧客が管理する [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (クラウドベースの外部キー管理システム) に格納されている、カスタマー マネージド非対称キーです。 Key Vault は、FIPS 140-2 Level 2 認定のハードウェア セキュリティ モジュール (HSM) をオプションで使用できる、RSA 暗号化キー向けの、可用性が高くスケーラブルでセキュリティで保護されたストレージです。 格納されているキーに直接アクセスすることはできませんが、キーを使用して暗号化/復号化のサービスを認証されたエンティティに提供します。 キーは、キー コンテナーによって生成、インポート、または[オンプレミスの HSM デバイスからキー コンテナーに転送](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)することができます。

Azure SQL Database と Azure SQL Data Warehouse の場合、TDE 保護機能は論理サーバー レベルで設定され、そのサーバーに関連付けられているすべての暗号化されたデータベースによって継承されます。 Azure SQL Managed Instance の場合、TDE 保護機能はインスタンス レベルで設定され、そのインスタンス上のすべての暗号化されたデータベースによって継承されます。 *サーバー*という用語は、別途明記されていない限り、このドキュメントでは SQL Database 論理サーバーとマネージド インスタンスの両方を指します。 

> [!IMPORTANT]
> サービス マネージド TDE を使用していて、カスタマー マネージド TDE の使用を開始する場合は、切り替え処理中はデータが暗号化されたままとなり、データベース ファイルのダウンタイムが生じたり再暗号化が行われたりすることはありません。 サービス マネージド キーからカスタマー マネージド キーへの切り替えに必要なのは、高速のオンライン操作である DEK の再暗号化だけです。

## <a name="benefits-of-the-customer-managed-tde"></a>カスタマー マネージド TDE の利点

カスタマー マネージド TDE は、顧客に次の利点をもたらします。

- TDE 保護機能の使用と管理の完全かつ詳細な制御

- TDE 保護機能の使用の透過性

- 組織内でキーとデータの管理における職務の分離を実施することができる

- Key Vault 管理者は、暗号化されたデータベースにアクセスできないようにキーのアクセス許可を取り消すことができる

- AKV でのキーの一元管理

- AKV は Microsoft が暗号化キーを表示したり抽出したりできないように設計されているため、エンド カスタマーからの信頼が高まる

## <a name="how-customer-managed-tde-works"></a>カスタマー マネージド TDE のしくみ

![カスタマー マネージド TDE のセットアップと機能](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

サーバーで DEK の暗号化のために AKV に格納されている TDE 保護機能を使用できるようにするには、キー コンテナー管理者が一意の AAD ID を使用して、サーバーに次のアクセス権を付与する必要があります。

- **get** - Key Vault 内のキーの公開部分とプロパティを取得します

- **wrapKey** - DEK を保護 (暗号化) できるようにします

- **unwrapKey** - DEK を保護解除 (復号化) できるようにします

キー コンテナー管理者は、後で監査できるように、[キー コンテナーの監査イベントのログ記録を有効](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)にすることもできます。

AKV の TDE 保護機能を使用するようにサーバーを構成すると、そのサーバーから各 TDE 対応データベースの DEK が暗号化のためキー コンテナーに送信されます。 キー コンテナーから、暗号化された DEK が返され、その後、ユーザー データベースに格納されます。

必要に応じて、保護された DEK が復号化のためにサーバーからキー コンテナーに送信されます。

ログ記録が有効になっている場合、監査者は Azure Monitor を使用してキー コンテナーの AuditEvent ログを確認できます。


## <a name="requirements-for-configuring-customer-managed-tde"></a>カスタマー マネージド TDE を構成するための要件

### <a name="requirements-for-configuring-akv"></a>AKV を構成するための要件

- キー コンテナーと SQL Database/マネージド インスタンスは、同じ Azure Active Directory テナントに属している必要があります。 キー コンテナーとサーバーのテナント間の対話はサポートされていません。 後でリソースを移動するには、AKV を使用して TDE を再構成する必要があります。 リソースの移動の詳細については、[こちら](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)をご覧ください。

- キー (またはキー コンテナー) を誤って削除するデータ損失から保護するには、キー コンテナーで[論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)機能が有効になっている必要があります。 論理的に削除されたリソースは、顧客が復旧または削除しない限り、90 日間保持されます。 *recover* アクションと *purge* アクションには、キー コンテナーのアクセス ポリシーに独自のアクセス許可が関連付けられています。 論理的な削除機能は、既定ではオフになっており、[PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) または [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)を使用して有効にすることができます。 Azure portal で有効にすることはできません。  

- Azure Active Directory (Azure AD) ID を使用して、SQL Database サーバーまたはマネージド インスタンスにキー コンテナーへのアクセス権 (get、wrapKey、unwrapKey) を付与します。 Azure portal を使用すると、Azure AD ID が自動的に作成されます。 PowerShell または CLI を使用する場合は、Azure AD ID を明示的に作成し、完了を確認する必要があります。 PowerShell を使用するときの詳細な手順については、[BYOK 対応 TDE の構成](transparent-data-encryption-byok-azure-sql-configure.md)と [Managed Instance 用 BYOK 対応 TDE の構成](https://aka.ms/sqlmibyoktdepowershell)に関する記事を参照してください。

- AKV を使用してファイアウォールを使用する場合は、 *[Allow trusted Microsoft services to bypass the firewall]\(信頼された Microsoft サービスがファイアウォールをバイパスすることを許可する\)* オプションを有効にする必要があります。

### <a name="requirements-for-configuring-tde-protector"></a>TDE 保護機能を構成するための要件

- TDE 保護機能に使用できるのは、非対称キー、RSA 2048 キー、または RSA HSM 2048 キーだけです。

- キーがアクティブ化された日時 (設定する場合) は、過去の日付と時刻にする必要があります。 有効期限の日時 (設定する場合) は、将来の日付と時刻にする必要があります。

- キーは、"*有効*" 状態になっている必要があります。

- キー コンテナーに既存のキーをインポートする場合は、サポートされているファイル形式 (.pfx、.byok、または. backup) で提供する必要があります。

## <a name="recommendations-when-configuring-customer-managed-tde"></a>カスタマー マネージド TDE を構成する際の推奨事項

### <a name="recommendations-when-configuring-akv"></a>AKV を構成する際の推奨事項

- サーバーがキー コンテナー内の TDE 保護機能にアクセスする際の高可用性を確保するために、1 つのサブスクリプションで 1 つのキー コンテナーに最大 500 の General Purpose データベースまたは 200 の Business Critical データベースを関連付けます。 これらの数字は経験に基づいており、[キー コンテナー サービスの制限](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)に記載されています。 ここでの目的は、サーバーのフェールオーバー後の問題を回避することです。これは、フェールオーバーによってそのサーバー内のデータベースと同じ数のキー操作がコンテナーに対してトリガーされるためです。 

- キー コンテナーにリソース ロックを設定して、この重要なリソースを削除できるユーザーを制御し、誤削除や許可されていない削除を防ぎます。 リソース ロックの詳細については、[こちら](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)をご覧ください。

- すべての暗号化キーの監査およびレポートを有効にします。キー コンテナーで提供されるログは、他のセキュリティ情報およびイベント管理ツールに簡単に挿入できます。 Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) は、既に統合されているサービスの一例です。

- 暗号化されたデータベースの高可用性を確保するため、各サーバーを異なるリージョンに存在する 2 つのキー コンテナーとリンクします。 TDE 保護機能と同じリージョン内のキー コンテナーのキーのみをマークします。 システムが使用します

### <a name="recommendations-when-configuring-tde-protector"></a>TDE 保護機能を構成する際の推奨事項
- TDE 保護機能のコピーを安全な場所に保管するか、エスクロー サービスにエスクローします。 

- キーがキー コンテナー内に生成される場合は、初めて AKV でキーを使用する前に、キーのバックアップを作成します。 バックアップは Azure Key Vault にのみ復元できます。 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) コマンドの詳細については、こちらをご覧ください。

- キー (キー属性、タグ、ACL など) に変更を加えるたびに新しいバックアップを作成します。

- データベースの古いバックアップを復元できるように、キーを交換するときは、キー コンテナーにキーの**以前のバージョンを保持**します。 データベースの TDE 保護機能が変更されても、データベースの古いバックアップが、最新の TDE 保護機能を使用するように**更新されることはありません**。 復元時には、各バックアップに作成時に暗号化された TDE 保護機能が必要です。 キーの交換を実行するには、[PowerShell を使用した Transparent Data Encryption 保護機能の交換](transparent-data-encryption-byok-azure-sql-key-rotation.md)に関する記事の手順に従います。

- サービス マネージド キーに切り替えた後でも、以前に使用したすべてのキーを AKV で保持します。 これにより、AKV に格納された TDE 保護機能を使用してデータベースのバックアップを復元できます。  Azure Key Vault で作成された TDE 保護機能は、残りの保存されているすべてのバックアップがサービス マネージド キーを使用して作成されるまで保持される必要があります。 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) を使用して、これらのキーの回復可能なバックアップ コピーを作成します。

- セキュリティ インシデントの発生時に、データ損失のリスクを伴わずに、侵害された可能性のあるキーを削除するには、[侵害された可能性のあるキーの削除](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)の手順に従ってください。

## <a name="inaccessible-tde-protector"></a>アクセスできない TDE 保護機能

Transparent Data Encryption がカスタマー マネージド キーを使用するように構成されている場合、データベースをオンラインのままにするには、TDE 保護機能への継続的アクセスが必要です。 サーバーが AKV 内のカスタマー マネージド TDE 保護機能にアクセスできなくなると、10 分以内にデータベースで、対応するエラー メッセージが表示されてすべての接続が拒否されるようになり、状態が*アクセス不可*に変わります。 アクセス不可状態のデータベースで許可される唯一のアクションは、削除だけです。

> [!NOTE]
> 断続的なネットワークの停止が原因でデータベースにアクセスできない場合、必要な操作はなく、データベースは自動的にオンラインに戻ります。

キーへのアクセスが復元された後、データベースをオンラインに戻すには、追加の時間と手順が必要です。これは、キーにアクセスできなくなってからの経過時間とデータベース内のデータのサイズによって異なる場合があります。

- キーのアクセスが 8 時間以内に復元された場合、データベースは次の 1 時間以内に自動回復します。

- キーのアクセスが 8 時間を超えて復元された場合は、自動回復は不可能で、データベースのサイズによっては、データベースの復旧にかなりの時間がかかることがあり、サポート チケットを開く必要があります。 データベースがオンラインに戻ると、以前に構成されたサーバーレベルの設定 ([フェールオーバー グループ](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) 構成、ポイントインタイム リストア履歴、タグなど) が失われます。 そのため、8 時間以内にキー アクセスの潜在的な問題を特定して対処できるようにする通知システムを実装することをお勧めします。

### <a name="accidental-tde-protector-access-revocation"></a>TDE 保護機能アクセスの誤失効

キー コンテナーに対する十分なアクセス権を持つユーザーが、次のことを行うことで、キーへのサーバー アクセスを誤って無効にしてしまうことがあります。

- サーバーからキー コンテナーの *get*、*wrapKey*、*unwrapKey* アクセス許可を取り消す

- キーを削除する

- キー コンテナーを削除する

- キー コンテナーのファイアウォール規則を変更する

- Azure Active Directory でサーバーのマネージド ID を削除する

データベースにアクセスできなくなる一般的な原因については、[こちら](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)を参照してください。

## <a name="monitoring-of-the-customer-managed-tde"></a>カスタマー マネージド TDE の監視

データベースの状態を監視したり、TDE 保護機能にアクセスできなくなった場合のアラートを有効にしたりするには、Azure の次の機能を構成します。
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 TDE 保護機能へのアクセスが失われたアクセス不可能なデータベースには、データベースへの最初の接続が拒否された後、"使用できません" と表示されます。
- [アクティビティ ログ](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications)。ユーザーが管理するキー コンテナーの TDE 保護機能へのアクセスに失敗すると、アクティビティ ログにエントリが追加されます。  これらのイベントに対してアラートを作成すると、できるだけ早くアクセスを再開できるようになります。
- [アクション グループ](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)は、設定 (メール/SMS/プッシュ/音声、ロジック アプリ、Webhook、ITSM、Automation Runbook など) に基づいて通知やアラートを送信できます。

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>カスタマー マネージド TDE を使用したデータベースのバックアップと復元

Key Vault のキーを使用して TDE でデータベースを暗号化すると、新しく生成されるバックアップも同じ TDE 保護機能で暗号化されます。 TDE 保護機能が変更されても、データベースの古いバックアップが、最新の TDE 保護機能を使用するように**更新されることはありません**。

Key Vault の TDE 保護機能で暗号化されたバックアップを復元するには、キー マテリアルがターゲット サーバーで使用できることを確認します。 そのため、データベースのバックアップを復元できるように、TDE 保護機能の古いバージョンをすべてキー コンテナーに保持しておくことをお勧めします。 

> [!IMPORTANT]
> どのような時でも 1 台のサーバーに対して複数の TDE 保護機能を設定することはできません。 これは、Azure portal ブレードで [選択したキーを既定の TDE 保護機能にします] とマークされたキーです。 ただし、TDE 保護機能としてマークしなくても、複数の追加キーをサーバーにリンクすることができます。 これらのキーは DEK の保護には使用されませんが、バックアップ ファイルが対応する拇印を持つキーで暗号化されている場合は、バックアップからの復元中に使用できます。

ターゲット サーバーでバックアップの復元に必要なキーが使用できなくなった場合は、復元の試行時に次のエラー メッセージが返されます。"Target server `<Servername>` does not have access to all AKV URIs created between \<Timestamp #1> and \<Timestamp #2>. Please retry operation after restoring all AKV URIs." (ターゲット サーバー <Servername> は <Timestamp #1> から <Timestamp #2> までの間に作成されたどの AKV URI にもアクセスできません。 すべての AKV URI を復元してから操作をやり直してください\)

これを軽減するには、ターゲット SQL Database 論理サーバーに対して [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) コマンドレットを実行するか、ターゲット マネージド インスタンスに対して [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) を実行して、使用可能なキーの一覧を返し、不足しているキーを識別します。 すべてのバックアップを復元できるようにするには、復元のターゲット サーバーが必要なすべてのキーにアクセスできることを確認します。 これらのキーを TDE 保護機能としてマークする必要はありません。

SQL Database のバックアップの回復の詳細については、[Azure SQL データベースの復旧](sql-database-recovery-using-backups.md)に関する記事をご覧ください。 SQL Data Warehouse のバックアップの回復の詳細については、[Azure SQL Data Warehouse の復元](../sql-data-warehouse/backup-and-restore.md)に関する記事をご覧ください。 マネージド インスタンスを使用した SQL Server のネイティブ バックアップ/復元については、「[クイックスタート:データベースをマネージド インスタンスに復元する](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)」を参照してください。 

ログ ファイルに関するその他の考慮事項:TDE 保護機能が交換され、データベースが新しい TDE 保護機能を使用している場合でも、バックアップ ログ ファイルは元の TDE 保護機能で暗号化されたままになっています。  復元時に、データベースを復元するには両方のキーが必要になります。  サービス マネージド TDE を使用するようにデータベースが変更された場合でも、Azure Key Vault に格納されている TDE 保護機能がログ ファイルで使用されている場合は、復元時にこのキーが必要になります。

## <a name="high-availability-with-customer-managed-tde"></a>カスタマー マネージド TDE による高可用性

サーバーに geo 冗長が構成されていない場合でも、同じキー マテリアルで、2 つの異なるリージョンの 2 つの異なるキー コンテナーを使用するようにサーバーを構成することを強くお勧めします。 これを実現するには、サーバーと同じリージョンに併置されたプライマリ キー コンテナーを使用して TDE 保護機能を作成し、別の Azure リージョンのキー コンテナーにキーを複製します。これにより、データベースの稼働中にプライマリ キー コンテナーが停止した場合に、サーバーはもう 1 つのキー コンテナーにアクセスできます。 

Backup-AzKeyVaultKey コマンドレットを使用して、プライマリ キー コンテナーから暗号化された形式のキーを取得し、Restore-AzKeyVaultKey コマンドレットを使用して、キーを複製する 2 つ目のリージョンのキー コンテナーを指定します。 または、Azure portal を使用して、キーのバックアップと復元を行います。 他のリージョンのセカンダリ キー コンテナー内のキーは、TDE 保護機能としてマークすることはできず、許可もされません。

 プライマリ キー コンテナーに影響する障害が発生したときに初めて、システムにより、セカンダリ キー コンテナー内に同じ拇印を持つ他のリンクされたキーに自動的に切り替えられます (存在する場合)。 アクセス権の取り消しにより、あるいはキーまたはキー コンテナーが削除されたことにより TDE 保護機能にアクセスできない場合、切り替えは行われないことに注意してください。顧客がサーバーからキーへのアクセスを意図的に制限することを望む場合があるためです。

![単一サーバーの HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR とカスタマー マネージド TDE

[アクティブ geo レプリケーション](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) と [フェールオーバー グループ](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)のシナリオでは、どちらも関連する各サーバーに個別のキー コンテナーが必要です。これは、同じ Azure リージョンにサーバーと併置する必要があります。 リージョンの停止によってプライマリにアクセスできなくなり、フェールオーバーがトリガーされた場合に、geo セカンダリが同期されていて、ローカルのキー コンテナーから同じキーを使用して引き継ぐことができるように、顧客には、キー コンテナー間でキー マテリアルを一貫性のあるものに保つ責任があります。 最大 4 つのセカンダリを構成でき、チェーン (セカンダリのセカンダリ) はサポートされていません。

不完全なキー マテリアルが原因で geo レプリケーションの確立中または実行中に問題が発生するのを回避するには、カスタマー マネージド TDE を構成するときに、次の規則に従うことが重要です。

- 関連するすべてのキー コンテナーに、同じプロパティと、それぞれのサーバーに対する同じアクセス権がある必要があります。

- 関連するすべてのキー コンテナーに、同じキー マテリアルが含まれている必要があります。 これは現在の TDE 保護機能だけでなく、バックアップ ファイルで使用されている可能性がある以前のすべての TDE 保護機能にも適用されます。

- TDE 保護機能の初期セットアップとローテーションは、どちらも最初にセカンダリで実行してから、プライマリで実行する必要があります。

![フェールオーバー グループと Geo-DR](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

フェールオーバーをテストするには、[アクティブ geo レプリケーションの概要](sql-database-geo-replication-overview.md)の手順に従います。 これは、両方のキー コンテナーに対する SQL のアクセス許可が保持されていることを確認するために、定期的に行う必要があります。

## <a name="next-steps"></a>次の手順

また、カスタマー マネージド TDE を使用した一般的な操作については、次の PowerShell サンプル スクリプトでも確認できます。

- [PowerShell を使用して SQL Database の Transparent Data Encryption 保護機能をローテーションする](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [PowerShell を使用して SQL Database の Transparent Data Encryption (TDE) 保護機能を削除する](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [PowerShell を使用して、Managed Instance で独自のキーを使用して Transparent Data Encryption を管理する](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
