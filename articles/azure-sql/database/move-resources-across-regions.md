---
title: 新しいリージョンへのリソースの移動
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: データベースまたはマネージド インスタンスを別のリージョンに移動する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594166"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>新しいリージョンへのリソースの移動 - Azure SQL Database および Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、データベースまたはマネージド インスタンスを新しいリージョンに移動する方法に関する一般的なワークフローについて説明します。

## <a name="overview"></a>概要

既存のデータベースまたはマネージド インスタンスをリージョン間で移動することが必要になるさまざまなシナリオがあります。 たとえば、ビジネスを新しいリージョンに拡張しており、新しい顧客ベース用にそれを最適化したい場合があります。 コンプライアンス上の理由により、操作を別のリージョンに移動する必要がある場合や、 Azure が、近接性を向上させ、カスタマー エクスペリエンスを向上させる、新しいリージョンをリリースした場合もあります。  

この記事では、リソースを別のリージョンに移動するための一般的なワークフローについて説明します。 このワークフローは次のステップで構成されます。

1. 移動の前提条件を確認します。
1. スコープ内のリソースの移動を準備します。
1. 準備プロセスを監視します。
1. 移動プロセスをテストします。
1. 実際の移動を開始します。
1. ソース リージョンからリソースを削除します。

> [!NOTE]
> この記事は、Azure パブリック クラウド内または同じソブリン クラウド内での移行に適用されます。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>データベースの移動

### <a name="verify-prerequisites"></a>前提条件を確認する

1. 各ソース サーバーにターゲット サーバーを作成します。
1. [PowerShell](scripts/create-and-configure-database-powershell.md) を使用して、適切な例外でファイアウォールを構成します。  
1. 正しいログインを使用してサーバーを構成します。 サブスクリプション管理者または SQL サーバー管理者でない場合は、管理者に協力を求め、必要なアクセス許可を割り当てます。 詳細については、[ディザスター リカバリーの後に Azure SQL Database セキュリティを管理する方法](active-geo-replication-security-configure.md)に関する記事を参照してください。
1. データベースが透過的なデータ暗号化で暗号化されていて、Azure Key Vault で独自の暗号化キーを使用している場合は、ターゲット リージョンで適切な暗号化マテリアルがプロビジョニングされていることを確認してください。 詳細については、[Azure Key Vault のカスタマー マネージド キーを使用した Azure SQL Transparent Data Encryption](transparent-data-encryption-byok-overview.md) に関する記事を参照してください。
1. データベース レベルの監査が有効になっている場合は、無効にし、代わりにサーバー レベルの監査を有効にします。 フェールオーバー後、データベース レベルの監査では、リージョン間のトラフィックが必要になります。これは、移動後には不要となり、不可能になります。
1. サーバー レベルの監査では、次のことを確認します。
   - 既存の監査ログを含むストレージ コンテナー、Log Analytics、またはイベント ハブが、ターゲット リージョンに移動されていること。
   - ターゲット サーバーで監査が構成されていること。 詳細については、「[SQL Database 監査の使用](../../azure-sql/database/auditing-overview.md)」を参照してください。
1. インスタンスに長期リテンション ポリシー (LTR) がある場合、既存の LTR バックアップは現在のサーバーに関連付けられたままになります。 ターゲット サーバーが異なるため、たとえサーバーが削除されても、ソース サーバーを使用してソース リージョンのそれ以前の LTR バックアップにアクセスできるようになります。

      > [!NOTE]
      > これは、ソブリン クラウドとパブリック リージョン間の移動には不十分です。 このような移行では、現在サポートされていない LTR バックアップをターゲット サーバーに移動する必要があります。

### <a name="prepare-resources"></a>リソースを準備する

1. ソースのサーバーとターゲットのサーバーとの間で[フェールオーバー グループ](failover-group-add-single-database-tutorial.md#2---create-the-failover-group)を作成します。  
1. 移動したいデータベースをフェールオーバー グループに追加します。
  
    追加されたすべてのデータベースのレプリケーションが自動的に開始されます。 詳細については、「[単一データベースとエラスティック プールでフェールオーバー グループを使用する場合のベスト プラクティス](auto-failover-group-overview.md#best-practices-for-sql-database)」を参照してください。

### <a name="monitor-the-preparation-process"></a>準備プロセスを監視する

[Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) を定期的に呼び出して、ソースからターゲットへのデータベースのレプリケーションを監視することができます。 `Get-AzSqlDatabaseFailoverGroup` の出力オブジェクトには、**ReplicationState** のプロパティが含まれます。

- **ReplicationState = 2** (CATCH_UP) は、データベースが同期されており、安全にフェールオーバーできることを示します。
- **ReplicationState = 0** (SEEDING) は、データベースがまだシード処理されておらず、フェールオーバーの試行が失敗することを示します。

### <a name="test-synchronization"></a>同期をテストする

**ReplicationState** が `2` になったら、セカンダリ エンドポイント `<fog-name>.secondary.database.windows.net` を使用して各データベースまたはデータベースのサブセットに接続し、データベースに対して任意のクエリを実行して、接続性、適切なセキュリティ構成、データ レプリケーションを保証します。

### <a name="initiate-the-move"></a>移動を開始する

1. セカンダリ エンドポイント `<fog-name>.secondary.database.windows.net` を使用してターゲット サーバーに接続します。
1. [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) を使用して、セカンダリ マネージド インスタンスを完全に同期したプライマリに切り替えます。 この操作は成功するか、そうでなければロールバックします。
1. `nslook up <fog-name>.secondary.database.windows.net` を使用してコマンドが正常に完了したことを検証して、DNS CNAME エントリがターゲット リージョンの IP アドレスを指していることを確認します。 switch コマンドが失敗した場合、CNAME は更新されません。

### <a name="remove-the-source-databases"></a>ソース データベースを削除する

移動が完了したら、不要な料金が発生しないように、ソース リージョンのリソースを削除します。

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) を使用して、フェールオーバー グループを削除します。
1. ソース サーバー上の各データベースに対して、[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) を使用して各ソース データベースを削除します。 これにより、geo レプリケーション リンクが自動的に終了します。
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver) 使用してソース サーバーを削除します。
1. キー コンテナー、監査ストレージ コンテナー、イベント ハブ、Azure Active Directory (Azure AD) インスタンス、その他の依存リソースを削除して、課金を停止します。

## <a name="move-elastic-pools"></a>エラスティック プールの移動

### <a name="verify-prerequisites"></a>前提条件を確認する

1. 各ソース サーバーにターゲット サーバーを作成します。
1. [PowerShell](scripts/create-and-configure-database-powershell.md) を使用して、適切な例外でファイアウォールを構成します。
1. 正しいログインを使用してサーバーを構成します。 サブスクリプション管理者またはサーバー管理者でない場合は、管理者に協力を求め、必要なアクセス許可を割り当てます。 詳細については、[ディザスター リカバリーの後に Azure SQL Database セキュリティを管理する方法](active-geo-replication-security-configure.md)に関する記事を参照してください。
1. データベースが透過的なデータ暗号化で暗号化されていて、Azure Key Vault で独自の暗号化キーを使用している場合は、ターゲット リージョンで適切な暗号化マテリアルがプロビジョニングされていることを確認してください。
1. ソース エラスティック プールごとにターゲット エラスティック プールを作成して、同じ名前と同じサイズのプールが同じサービス レベルで作成されるようにします。
1. データベース レベルの監査が有効になっている場合は、無効にし、代わりにサーバー レベルの監査を有効にします。 フェールオーバー後、データベース レベルの監査では、リージョン間のトラフィックが必要になります。これは、移動後には不要または不可能になります。
1. サーバー レベルの監査では、次のことを確認します。
    - 既存の監査ログを含むストレージ コンテナー、Log Analytics、またはイベント ハブが、ターゲット リージョンに移動されていること。
    - 監査構成は、ターゲット サーバーで構成されます。 詳細については、[SQL Database の監査](../../azure-sql/database/auditing-overview.md)に関する記事を参照してください。
1. インスタンスに長期リテンション ポリシー (LTR) がある場合、既存の LTR バックアップは現在のサーバーに関連付けられたままになります。 ターゲット サーバーが異なるため、たとえサーバーが削除されても、ソース サーバーを使用してソース リージョンのそれ以前の LTR バックアップにアクセスできるようになります。

      > [!NOTE]
      > これは、ソブリン クラウドとパブリック リージョン間の移動には不十分です。 このような移行では、現在サポートされていない LTR バックアップをターゲット サーバーに移動する必要があります。

### <a name="prepare-to-move"></a>移動の準備をする

1. ソース サーバー上の各エラスティック プールとターゲット サーバー上の対応するエラスティック プールとの間に、個別の[フェールオーバー グループ](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group)を作成します。
1. プール内のすべてのデータベースをフェールオーバー グループに追加します。

    追加されたデータベースのレプリケーションが自動的に開始されます。 詳細については、[エラスティック プールを使用したフェールオーバー グループのベスト プラクティス](auto-failover-group-overview.md#best-practices-for-sql-database)に関するセクションを参照してください。

      > [!NOTE]
      > 複数のエラスティック プールを含むフェールオーバー グループを作成することはできますが、プールごとに個別のフェールオーバー グループを作成することを強くお勧めします。 移動する必要のある複数のエラスティック プールにわたって多数のデータベースがある場合は、準備手順を並行して実行してから、移動手順を並行して開始することができます。 このプロセスは、同じフェールオーバー グループ内に複数のエラスティック プールがある場合と比べて、拡張性が高く、所要時間は短くなります。

### <a name="monitor-the-preparation-process"></a>準備プロセスを監視する

[Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) を定期的に呼び出して、ソースからターゲットへのデータベースのレプリケーションを監視することができます。 `Get-AzSqlDatabaseFailoverGroup` の出力オブジェクトには、**ReplicationState** のプロパティが含まれます。

- **ReplicationState = 2** (CATCH_UP) は、データベースが同期されており、安全にフェールオーバーできることを示します。
- **ReplicationState = 0** (SEEDING) は、データベースがまだシード処理されておらず、フェールオーバーの試行が失敗することを示します。

### <a name="test-synchronization"></a>同期をテストする

**ReplicationState** が `2` になったら、セカンダリ エンドポイント `<fog-name>.secondary.database.windows.net` を使用して各データベースまたはデータベースのサブセットに接続し、データベースに対して任意のクエリを実行して、接続性、適切なセキュリティ構成、データ レプリケーションを保証します。

### <a name="initiate-the-move"></a>移動を開始する

1. セカンダリ エンドポイント `<fog-name>.secondary.database.windows.net` を使用してターゲット サーバーに接続します。
1. [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) を使用して、セカンダリ マネージド インスタンスを完全に同期したプライマリに切り替えます。 この操作は成功するか、成功しなかった場合はロールバックされます。
1. `nslook up <fog-name>.secondary.database.windows.net` を使用してコマンドが正常に完了したことを検証して、DNS CNAME エントリがターゲット リージョンの IP アドレスを指していることを確認します。 switch コマンドが失敗した場合、CNAME は更新されません。

### <a name="remove-the-source-elastic-pools"></a>ソース エラスティック プールを削除する

移動が完了したら、不要な料金が発生しないように、ソース リージョンのリソースを削除します。

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) を使用して、フェールオーバー グループを削除します。
1. [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool) を使用して、ソース サーバー上の各ソース エラスティック プールを削除します。
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver) 使用してソース サーバーを削除します。
1. キー コンテナー、監査ストレージ コンテナー、イベント ハブ、Azure AD インスタンス、その他の依存リソースを削除して、課金を停止します。

## <a name="move-a-managed-instance"></a>マネージド インスタンスの移動

### <a name="verify-prerequisites"></a>前提条件を確認する

1. ソース マネージド インスタンスごとに、ターゲット リージョン内に同じサイズである SQL Managed Instance のターゲット インスタンスを作成します。  
1. マネージド インスタンスのネットワークを構成します。 詳細については、「[ネットワーク構成](../managed-instance/how-to-content-reference-guide.md#network-configuration)」を参照してください。
1. 適切なログインを使用して、ターゲット マスター データベースを構成します。 サブスクリプションまたは SQL Managed Instance の管理者でない場合は、管理者に協力を求め、必要なアクセス許可を割り当てます。
1. データベースが透過的なデータ暗号化で暗号化されており、Azure Key Vault で独自の暗号化キーを使用している場合は、同じ暗号化キーを持つ Azure Key Vault がソースおよびターゲットの両方のリージョンに存在していることを確認してください。 詳細については、[Azure Key Vault のカスタマー マネージド キーを使用した Transparent Data Encryption](transparent-data-encryption-byok-overview.md) に関する記事を参照してください。
1. マネージド インスタンスに対して監査が有効になっている場合は、次のことを確認します。
    - 既存のログを含むストレージ コンテナーまたはイベント ハブが、ターゲット リージョンに移動されていること。
    - 監査がターゲット インスタンスで構成されていること。 詳細については、「[SQL Managed Instance での監査](../managed-instance/auditing-configure.md)に関する記事を参照してください。
1. インスタンスに長期リテンション ポリシー (LTR) がある場合、既存の LTR バックアップは現在のインスタンスに関連付けられたままになります。 ターゲット インスタンスが異なるため、たとえインスタンスが削除されても、ソース インスタンスを使用してソース リージョンのそれ以前の LTR バックアップにアクセスできるようになります。

  > [!NOTE]
  > これは、ソブリン クラウドとパブリック リージョン間の移動には不十分です。 このような移行では、現在サポートされていない LTR バックアップをターゲット インスタンスに移動する必要があります。

### <a name="prepare-resources"></a>リソースを準備する

各ソース マネージド インスタンスと対応する SQL Managed Instance のターゲット インスタンスの間に、フェールオーバー グループを作成します。

各インスタンスのすべてのデータベースのレプリケーションが自動的に開始されます。 詳細については、[自動フェールオーバー グループ](auto-failover-group-overview.md)に関する記事を参照してください。

### <a name="monitor-the-preparation-process"></a>準備プロセスを監視する

[Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) を定期的に呼び出して、ソースからターゲットへのデータベースのレプリケーションを監視することができます。 `Get-AzSqlDatabaseFailoverGroup` の出力オブジェクトには、**ReplicationState** のプロパティが含まれます。

- **ReplicationState = 2** (CATCH_UP) は、データベースが同期されており、安全にフェールオーバーできることを示します。
- **ReplicationState = 0** (SEEDING) は、データベースがまだシード処理されておらず、フェールオーバーの試行が失敗することを示します。

### <a name="test-synchronization"></a>同期をテストする

**ReplicationState** が `2` になったら、セカンダリ エンドポイント `<fog-name>.secondary.database.windows.net` を使用して各データベースまたはデータベースのサブセットに接続し、データベースに対して任意のクエリを実行して、接続性、適切なセキュリティ構成、データ レプリケーションを保証します。

### <a name="initiate-the-move"></a>移動を開始する

1. セカンダリ エンドポイント `<fog-name>.secondary.database.windows.net` を使用して、ターゲット マネージド インスタンスに接続します。
1. [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) を使用して、セカンダリ マネージド インスタンスを完全に同期したプライマリに切り替えます。 この操作は成功するか、そうでなければロールバックします。
1. `nslook up <fog-name>.secondary.database.windows.net` を使用してコマンドが正常に完了したことを検証して、DNS CNAME エントリがターゲット リージョンの IP アドレスを指していることを確認します。 switch コマンドが失敗した場合、CNAME は更新されません。

### <a name="remove-the-source-managed-instances"></a>ソース マネージド インスタンスを削除する

移動が完了したら、不要な料金が発生しないように、ソース リージョンのリソースを削除します。

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) を使用して、フェールオーバー グループを削除します。 これにより、フェールオーバー グループの構成が削除され、2 つのインスタンス間の geo レプリケーション リンクが終了します。
1. [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance) を使用して、ソース マネージド インスタンスを削除します。
1. リソース グループ内のその他のリソース (仮想クラスター、仮想ネットワーク、セキュリティ グループなど) をすべて削除します。

## <a name="next-steps"></a>次のステップ

移行後のデータベースを[管理します](manage-data-after-migrating-to-database.md)。
