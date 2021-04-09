---
title: Azure Automation データ セキュリティ
description: この記事では、Azure Automation でお客様のプライバシーとデータを保護するしくみについて説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 2bdf25ef24f1fbf4aaf4dec154ea6af3421b915a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050819"
---
# <a name="management-of-azure-automation-data"></a>Azure Automation データの管理

この記事には、Azure Automation 環境でデータを保護し、セキュリティを確保するしくみについて説明するいくつかのトピックが含まれています。

## <a name="tls-12-enforcement-for-azure-automation"></a>Azure Automation に対する TLS 1.2 の強制

Azure Automation に転送中のデータのセキュリティを確保するには、トランスポート層セキュリティ (TLS) 1.2 の使用を構成することを強くお勧めします。 次に示すのは、HTTPS で Automation サービスと通信するメソッドまたはクライアントの一覧です。

* Webhook 呼び出し

* Hybrid Runbook Worker。Update Management、Change Tracking、Inventory によって管理されるコンピューターが含まれます。

* DSC ノード

以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。 エージェントで TLS 1.2 のみを使用するように明示的に設定することは、絶対に必要な場合を除いてお勧めしません。なぜなら、そうすることで、TLS 1.3 などのより新しいよくより安全なプロトコルを自動的に検出して利用できるようにするプラットフォーム レベルのセキュリティ機能が無効になる可能性があるためです。

Windows および Linux 用の Log Analytics エージェントでの TLS 1.2 サポート (これは Hybrid Runbook Worker ロールの依存関係です) については、[Log Analytics エージェントの概要の TLS 1.2 に関する記述](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol)を参照してください。

### <a name="platform-specific-guidance"></a>プラットフォーム固有のガイダンス

|プラットフォーム/言語 | サポート | 詳細情報 |
| --- | --- | --- |
|Linux | Linux ディストリビューションでは、TLS 1.2 のサポートに関して [OpenSSL](https://www.openssl.org) に依存する傾向があります。  | [OpenSSL の Changelog](https://www.openssl.org/news/changelog.html) を参照して、使用している OpenSSL のバージョンがサポートされていることを確認してください。|
| Windows 8.0 - 10 | サポートされています。既定で有効になっています。 | [既定の設定](/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには。  |
| Windows Server 2012 - 2016 | サポートされています。既定で有効になっています。 | [既定の設定](/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには |
| Windows 7 SP1 および Windows Server 2008 R2 SP1 | サポートされていますが、既定では有効になっていません。 | 有効にする方法の詳細については、「[トランスポート層セキュリティ (TLS) のレジストリ設定](/windows-server/security/tls/tls-registry-settings)」を参照してください。  |

## <a name="data-retention"></a>データの保持

Azure Automation でリソースを削除すると、完全に削除される前に、監査目的に応じた日数だけ保持されます。 この期間にリソースを表示または使用することはできません。 このポリシーは、削除された Automation アカウントに属するリソースにも適用されます。 保持ポリシーはすべてのユーザーに適用され、現在カスタマイズすることはできません。 ただし、さらに長くデータを保持する必要がある場合は、[Azure Automation ジョブのデータを Azure Monitor ログに転送する](automation-manage-send-joblogs-log-analytics.md)ことができます。

次の表は、さまざまなリソースの保持ポリシーをまとめたものです。

| Data | ポリシー |
|:--- |:--- |
| アカウント |アカウントは、ユーザーによって削除された日から 30 日後に完全に消去されます。 |
| アセット |アセットは、ユーザーによって削除された日から 30 日後、またはアセットを保持するアカウントがユーザーによって削除された日から 30 日後に、完全に消去されます。 アセットには、変数、スケジュール、資格情報、証明書、Python 2 パッケージ、および接続が含まれます。 |
| DSC ノード |DSC ノード は、Azure portal または Windows PowerShell の [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) コマンドレットを使用して、Automation アカウントから登録解除された日から 30 日後に、完全に消去されます。 また、ノードは、ノードを保持するアカウントがユーザーによって削除されてから 30 日後に、完全に消去されます。 |
| ジョブ |ジョブは、変更 (ジョブの完了など) が停止または中断された日から 30 日後に、削除されて完全に消去されます。 |
| モジュール |モジュールは、ユーザーによって削除された日から 30 日後、またはモジュールを保持するアカウントがユーザーによって削除された日から 30 日後に、完全に消去されます。 |
| ノード構成/MOF ファイル |古いノード構成は、新しいノード構成が生成された日から 30 日後に、完全に消去されます。 |
| ノード レポート |ノード レポートは、そのノードの新しいレポートが生成されてから 90 日後に、完全に消去されます。 |
| Runbooks |Runbook は、ユーザーによってリソースが削除された日から 30 日後、またはリソースを保持するアカウントがユーザーによって削除された日から 30 日後に、完全に消去されます<sup>1</sup>。 |

<sup>1</sup>Runbook は、Microsoft Azure サポートに Azure サポート インシデントを提出することで、30 日以内に復旧できます。 [Azure サポート サイト](/support/options)にアクセスし、 **[サポート リクエストの送信]** を選択してください。

## <a name="data-backup"></a>[データ バックアップ]

Azure で Automation アカウントを削除すると、そのアカウント内のすべてのオブジェクトが削除されます。 オブジェクトには、Runbook、モジュール、構成、設定、ジョブ、アセットが含まれます。 アカウントを削除した後で、それらを復旧することはできません。 削除する前に、以下の情報を使用して Automation アカウントの内容をバックアップできます。

### <a name="runbooks"></a>Runbooks

Azure Portal または Windows PowerShell の [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) コマンドレットを使用して、Runbook をスクリプト ファイルにエクスポートできます。 「[Azure Automation で Runbook を管理する](manage-runbooks.md)」で説明されているようにして、これらのスクリプト ファイルを別の Automation アカウントにインポートすることができます。

### <a name="integration-modules"></a>統合モジュール

Azure Automation から統合モジュールをエクスポートすることはできません。 Automation アカウントの外部で使用できるようにする必要があります。

### <a name="assets"></a>アセット

Azure Automation のアセット (証明書、接続、資格情報、スケジュール、変数) をエクスポートすることはできません。 代わりに、Azure portal と Azure コマンドレットを使用して、これらのアセットの詳細を記録できます。 その後、これらの詳細を使用して、Runbook で使用されるアセットを作成し、別の Automation アカウントにインポートします。

暗号化されている変数または資格情報のパスワード フィールドの値を、コマンドレットを使用して取得することはできません。 これらの値がわからない場合は、Runbook で取得できます。 変数の値を取得する方法については、「[Azure Automation での変数アセット](shared-resources/variables.md)」を参照してください。 資格情報の値の取得の詳細については、「[Azure Automation での資格情報資産](shared-resources/credentials.md)」を参照してください。

### <a name="dsc-configurations"></a>DSC の構成

Azure portal または Windows PowerShell の [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) コマンドレットを使用して、DSC の構成をスクリプト ファイルにエクスポートできます。 これらの構成を別の Automation アカウントにインポートして使用できます。

## <a name="geo-replication-in-azure-automation"></a>Azure Automation での geo レプリケーション

Azure Automation アカウントでは geo レプリケーションが標準です。 アカウントを設定するときに、プライマリ リージョンを選択します。 内部の Automation geo レプリケーション サービスによって、セカンダリ リージョンが自動的にアカウントに割り当てられます。 その後、サービスによって、プライマリ リージョンからセカンダリ リージョンにアカウント データが継続的にバックアップされます。 プライマリ リージョンとセカンダリ リージョンの完全な一覧については、「[ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)」をご覧ください。

Automation の geo レプリケーション サービスによって作成されるバックアップは、Automation のアセットや構成などの完全なコピーです。 プライマリ リージョンがダウンしてデータが失われた場合、このバックアップを使用できます。 万一、プライマリ リージョンのデータが失われた場合、Microsoft によってその復旧が試みられます。 プライマリ データを復旧できない場合は、自動フェールオーバーが使用され、Azure サブスクリプションを通じて状況が通知されます。

リージョンで障害が発生した場合、外部のお客様が Automation の geo レプリケーション サービスに直接アクセスすることはできません。 リージョンで障害が発生したときに Automation の構成と Runbook を維持した場合は、次のようにします。

1. プライマリ Automation アカウントの地理的リージョンとペアにするセカンダリ リージョンを選択します。

2. セカンダリ リージョンに Automation アカウントを作成します。

3. プライマリ アカウントで、Runbook をスクリプト ファイルとしてエクスポートします。

4. セカンダリ リージョンの Automation アカウントに Runbook をインポートします。

## <a name="next-steps"></a>次のステップ

* Azure Automation でのセキュリティ保護されたアセットの詳細については、「[Azure Automation でセキュリティで保護された資産を暗号化する](automation-secure-asset-encryption.md)」を参照してください。

* geo レプリケーションの詳細については、「[アクティブ geo レプリケーションの作成と使用](../azure-sql/database/active-geo-replication-overview.md)」を参照してください。
