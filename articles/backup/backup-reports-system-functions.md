---
title: Azure Monitor ログに対するシステム関数
description: システム関数を使用して Azure Monitor ログに対するカスタム クエリを記述する
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 1d26adfd2bd1a3fc1506a334b4b661b66172192d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510431"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Azure Monitor ログに対するシステム関数

Azure Backup には、システム関数またはソリューション関数と呼ばれる一連の関数が用意されています。既定では、これらをご利用の Log Analytics (LA) ワークスペース内で使用できます。
 
これらの関数を使用すると、LA の[未加工の Azure Backup テーブル](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)のデータを操作し、書式を設定したデータを返すことができます。これにより、シンプルなクエリを使用して、バックアップ関連のすべてのエンティティの情報を容易に取得できます。 ユーザーはこれらの関数にパラメーターを渡すことで、これらの関数から返されるデータをフィルター処理することができます。 

LA ワークスペース内のバックアップ データに対してクエリを実行してカスタム レポートを作成する場合は、システム関数を使用することをお勧めします。以下のセクションに詳述するように、それらにはさまざまな利点があるためです。

## <a name="benefits-of-using-system-functions"></a>システム関数を使用することの利点

* **よりシンプルなクエリ**: 関数を使用すると、クエリで必要とされる結合の数を減らすことができます。 既定で、これらの関数から返されるものは、クエリ対象のエンティティ (バックアップ インスタンス、ジョブ、コンテナーなど) に関するすべての情報を含む "フラット化された" スキーマです。 たとえば、バックアップ項目名とそれに関連付けられているコンテナーごとに、成功したバックアップ ジョブの一覧を取得する必要がある場合は、 **_AzureBackup_getJobs()** 関数を呼び出すだけで、ジョブごとに該当する情報をすべて取得できます。 一方、未加工のテーブルに対してクエリを直接実行する場合は、[AddonAzureBackupJobs](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#addonazurebackupjobs) と [CoreAzureBackup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#coreazurebackup) テーブル間で複数の結合を実行する必要があります。

* **レガシ診断イベントからのより円滑な移行**: システム関数を使用すれば、[レガシ診断イベント](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event) (AzureDiagnostics mode モードでの AzureBackupReport) から [リソース固有のイベント](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)に円滑に移行することができます。 Azure Backup に用意されているすべてのシステム関数でパラメーターを指定できます。それにより、関数では、リソース固有のテーブルからのデータに対してのみクエリを実行するか、(レコードの重複除去を使用して) レガシ テーブルとリソース固有のテーブルの両方からのデータに対してクエリを実行するかを選択することができます。
    * リソース固有のテーブルへの移行が正常に完了した場合は、関数によるクエリの対象からレガシ テーブルを除外することを選択できます。
    * 現在、移行の過程にあって、分析に必要とする一部のデータがレガシ テーブル内にある場合は、レガシ テーブルを含めることを選択できます。 移行が完了し、レガシ テーブルからのデータが不要になった場合は、ご利用のクエリ内で関数に渡すパラメーターの値を更新するだけで、レガシ テーブルを除外することができます。
    * レガシ テーブルのみを引き続き使用する場合、同じパラメーターを使用してレガシ テーブルを含めるように選択すれば、関数は今までどおりに機能します。 ただし、できるだけ早い段階で、[リソース固有のテーブルに切り替える](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace)ことをお勧めします。

* **カスタム クエリが中断する可能性が少なくなる**: 将来のレポート シナリオに対応するために、基になる LA テーブルのスキーマの改善点が Azure Backup に導入された場合は、そのスキーマの変更を考慮するように関数の定義も更新されます。 したがって、システム関数を使用してカスタム クエリを作成すれば、テーブルの基になるスキーマに変更が加えられたとしても、ご利用のクエリが中断されることはありません。

> [!NOTE]
> システム関数は Microsoft によって管理されていて、ユーザーがその定義を編集することはできません。 編集可能な関数が必要な場合は、LA に[保存される関数](https://docs.microsoft.com/azure/azure-monitor/logs/functions) を作成できます。

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Azure Backup に用意されているシステム関数の種類

* **コア関数**: これらは、バックアップ インスタンス、コンテナー、ポリシー、ジョブ、課金エンティティなど、主な Azure Backup エンティティに対してクエリを実行する場合に役立つ関数です。 たとえば、 **_AzureBackup_getBackupInstances** 関数を呼び出すと、最新の完了日 (UTC) の時点でご利用の環境内に存在するすべてのバックアップ インスタンスの一覧が返されます。 この記事では、これらの各コア関数のパラメーターと返されるスキーマを以下にまとめてあります。

* **傾向関数**: これらは、バックアップ関連のエンティティ (バックアップ インスタンスや課金グループなど) の履歴レコードを返す関数です。これを使用することで、該当するエンティティに関連する主要なメトリック (カウント、使用されたストレージなど) について日次、週次、月次の傾向情報を取得することができます。 この記事では、これらの各傾向関数のパラメーターと返されるスキーマを以下にまとめてあります。

> [!NOTE]
> 現在、システム関数からは、最後の完了日 (UTC) までのデータが返されます。 まだ終わっていない今日のデータは返されません。 そのため、今日のレコードを取得することを試みる場合は、未加工の LA テーブルを使用する必要があります。


## <a name="list-of-system-functions"></a>システム関数の一覧

### <a name="core-functions"></a>コア関数

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults()

この関数を呼び出すと、LA ワークスペースに関連付けられている、ご利用の Azure 環境内のすべての Recovery Services コンテナーの一覧が返されます。

**パラメーター**

| **パラメーター名** | **説明** | **必須** | **値の例** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart から RangeEnd までの期間のコンテナー関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを RangeEnd パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって取得できるのは各コンテナーの最新のレコードのみとなります。 | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart から RangeEnd までの期間のコンテナー関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを RangeStart パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって取得できるのは各コンテナーの最新のレコードのみとなります。 | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のサブスクリプション セットにフィルター処理されます。 サブスクリプション ID のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したサブスクリプション内にあるコンテナーのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのサブスクリプションにわたるレコード検索がこの関数によって行われます。 | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のリージョン セットにフィルター処理されます。 リージョンのコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したリージョン内にあるコンテナーのみを取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのリージョンにわたるレコード検索がこの関数で行われます。 | N | "eastus,westus"|
| VaultList    |このパラメーターを使用すると、関数の出力は、特定のコンテナー セットにフィルター処理されます。 コンテナー名のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したコンテナーのみに関連するレコードを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのコンテナーにわたるレコード検索がこの関数によって行われます。 | N |"vault1,vault2,vault3"|
| VaultTypeList     | このパラメーターを使用すると、関数の出力は特定のコンテナーの種類に関係するレコードにフィルター処理されます。 現在サポートされているコンテナーの種類は、"Microsoft.RecoveryServices/vaults" のみであり、このパラメーターの既定値となります。 | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | このパラメーターを使用すると、レガシ AzureDiagnostics テーブル内のデータに対してクエリを実行するかどうかを選択できます。 このパラメーターの値を false にした場合は、この関数によって、AzureDiagnostics テーブルとリソース固有のテーブルの両方からのデータに対するクエリが実行されます。 このパラメーターの値を true にした場合は、この関数によって、リソース固有のテーブルのみからのデータに対するクエリが実行されます。 既定値は true です。 | N | true |

**返されるフィールド**

| **フィールド名** | **説明** |
| -------------- | --------------- |
| UniqueId | コンテナーの一意の ID を示す主キー |
| Id | コンテナーの Azure Resource Manager (ARM) ID |
| 名前 | コンテナーの名前 |
| SubscriptionId | コンテナーが存在するサブスクリプションの ID |
| 場所 | コンテナーが存在する場所 |
| VaultStore_StorageReplicationType | コンテナーに関連付けられたストレージ レプリケーションの種類 |
| タグ | コンテナーのタグ |
| TimeGenerated | レコードのタイムスタンプ |
| 種類 |  コンテナーの種類 ("Microsoft.RecoveryServices/vaults")|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies()

この関数を呼び出すと、ご利用の Azure 環境で使用されているバックアップ ポリシーの一覧と共に、データソースの種類やストレージ レプリケーションの種類など、各ポリシーに関する詳細な情報が返されます。

**パラメーター**

| **パラメーター名** | **説明** | **必須** | **値の例** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart から RangeEnd までの期間のポリシー関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを RangeStart パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって取得できるのは各ポリシーの最新のレコードのみとなります。 | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart から RangeEnd までの期間のポリシー関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを RangeStart パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって取得できるのは各ポリシーの最新のレコードのみとなります。 | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のサブスクリプション セットにフィルター処理されます。 サブスクリプション ID のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したサブスクリプション内にあるポリシーのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのサブスクリプションにわたるレコード検索がこの関数によって行われます。 | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のリージョン セットにフィルター処理されます。 リージョンのコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したリージョン内にあるポリシーのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのリージョンにわたるレコード検索がこの関数で行われます。 | N | "eastus,westus"|
| VaultList    |このパラメーターを使用すると、関数の出力は、特定のコンテナー セットにフィルター処理されます。 コンテナー名のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したコンテナーのみに関連するポリシーのレコードを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのコンテナーにわたってポリシーのレコードの検索がこの関数によって行われます。 | N |"vault1,vault2,vault3"|
| VaultTypeList     | このパラメーターを使用すると、関数の出力は特定のコンテナーの種類に関係するレコードにフィルター処理されます。 現在サポートされているコンテナーの種類は、"Microsoft.RecoveryServices/vaults" のみであり、このパラメーターの既定値となります。 | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | このパラメーターを使用すると、レガシ AzureDiagnostics テーブル内のデータに対してクエリを実行するかどうかを選択できます。 このパラメーターの値を false にした場合は、この関数によって、AzureDiagnostics テーブルとリソース固有のテーブルの両方からのデータに対するクエリが実行されます。 このパラメーターの値を true にした場合は、この関数によって、リソース固有のテーブルのみからのデータに対するクエリが実行されます。 既定値は true です。 | N | true |
| BackupSolutionList | このパラメーターを使用すると、関数の出力は、ご利用の Azure 環境で使用されている特定のバックアップ ソリューション セットにフィルター処理されます。 たとえば、このパラメーターの値として「Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM」を指定した場合、Azure 仮想マシンのバックアップ、Azure VM 内の SQL のバックアップ、または DPM から Azure へのバックアップを使用してバックアップされた項目に関連するレコードのみがこの関数から返されます。 既定では、このパラメーターの値は ' * ' です。この場合、この関数からは、バックアップ レポートでサポートされているすべてのバックアップ ソリューションに関連するレコードが返されます (サポートされている値は、"Azure Virtual Machine Backup"、"SQL in Azure VM Backup"、"SAP HANA in Azure VM Backup"、"Azure Storage (Azure Files) Backup"、"Azure Backup Agent"、"DPM"、"Azure Backup Server"、またはコンマ区切りのこれらの値の任意の組み合わせです)。 | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |

**返されるフィールド**

| **フィールド名** | **説明** |
| -------------- | --------------- |
| UniqueId | ポリシーの一意の ID を示す主キー |
| Id | ポリシーの Azure Resource Manager (ARM) ID |
| 名前 | ポリシーの名前 |
| バックアップ ソリューション | ポリシーが関連付けられたバックアップ ソリューション。 Azure VM のバックアップ、Azure VM 内の SQL のバックアップなどがあります。 |
| TimeGenerated | レコードのタイムスタンプ |
| VaultUniqueId | ポリシーに関連付けられたコンテナーを参照する外部キー |
| VaultResourceId | ポリシーに関連付けられたコンテナーの Azure Resource Manager (ARM) ID |
| VaultName | ポリシーに関連付けられたコンテナーの名前 |
| VaultTags | ポリシーに関連付けられたコンテナーのタグ |
| VaultLocation | ポリシーに関連付けられたコンテナーの場所 |
| VaultSubscriptionId | ポリシーに関連付けられたコンテナーのサブスクリプション ID |
| VaultStore_StorageReplicationType | ポリシーに関連付けられたコンテナーのストレージ レプリケーションの種類 |
| VaultType | コンテナーの種類 ("Microsoft.RecoveryServices/vaults") |
| ExtendedProperties | ポリシーの追加のプロパティ |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs()

この関数を呼び出すと、指定した時間範囲内にトリガーされたすべてのバックアップおよび復元に関連するジョブの一覧と共に、ジョブの状態、ジョブの期間、転送されたデータなど、各ジョブの詳細情報が返されます。

**パラメーター**

| **パラメーター名** | **説明** | **必須** | **値の例** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | このパラメーターを RangeEnd パラメーターと共に使用すると、RangeStart から RangeEnd までの期間内に開始されたすべてのジョブの一覧を取得できます。 | Y | "2021-03-03 00:00:00" |
| RangeEnd     | このパラメーターを RangeStart パラメーターと共に使用すると、RangeStart から RangeEnd までの期間内に開始されたすべてのジョブの一覧を取得できます。 | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のサブスクリプション セットにフィルター処理されます。 サブスクリプション ID のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したサブスクリプション内にあるコンテナーに関連付けられたジョブのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのサブスクリプションにわたるレコード検索がこの関数によって行われます。 | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のリージョン セットにフィルター処理されます。 リージョンのコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したリージョン内にあるコンテナーに関連付けられたジョブのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのリージョンにわたるレコード検索がこの関数で行われます。 | N | "eastus,westus"|
| VaultList    | このパラメーターを使用すると、関数の出力は、特定のコンテナー セットにフィルター処理されます。 コンテナー名のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したコンテナーのみに関連するジョブを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのコンテナーにわたるジョブ検索がこの関数によって行われます。 | N |"vault1,vault2,vault3"|
| VaultTypeList     | このパラメーターを使用すると、関数の出力は特定のコンテナーの種類に関係するレコードにフィルター処理されます。 現在サポートされているコンテナーの種類は、"Microsoft.RecoveryServices/vaults" のみであり、このパラメーターの既定値となります。 | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | このパラメーターを使用すると、レガシ AzureDiagnostics テーブル内のデータに対してクエリを実行するかどうかを選択できます。 このパラメーターの値を false にした場合は、この関数によって、AzureDiagnostics テーブルとリソース固有のテーブルの両方からのデータに対するクエリが実行されます。 このパラメーターの値を true にした場合は、この関数によって、リソース固有のテーブルのみからのデータに対するクエリが実行されます。 既定値は true です。 | N | true |
| BackupSolutionList | このパラメーターを使用すると、関数の出力は、ご利用の Azure 環境で使用されている特定のバックアップ ソリューション セットにフィルター処理されます。 たとえば、このパラメーターの値として「Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM」を指定した場合、Azure 仮想マシンのバックアップ、Azure VM 内の SQL のバックアップ、または DPM から Azure へのバックアップを使用してバックアップされた項目に関連するレコードのみがこの関数から返されます。 既定では、このパラメーターの値は ' * ' です。この場合、この関数からは、バックアップ レポートでサポートされているすべてのバックアップ ソリューションに関連するレコードが返されます (サポートされている値は、"Azure Virtual Machine Backup"、"SQL in Azure VM Backup"、"SAP HANA in Azure VM Backup"、"Azure Storage (Azure Files) Backup"、"Azure Backup Agent"、"DPM"、"Azure Backup Server"、またはコンマ区切りのこれらの値の任意の組み合わせです)。 | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| JobOperationList | このパラメーターを使用すると、関数の出力は特定の種類のジョブにフィルター処理されます。 たとえば、バックアップや復元などです。 既定では、このパラメーターの値は "*" であるため、バックアップおよび復元の両方のジョブの検索が、この関数によって行われます。 | N | "Backup" | 
| JobStatusList | このパラメーターを使用すると、関数の出力はジョブの特定の状態にフィルター処理されます。 たとえば、完了や失敗などです。 既定では、このパラメーターの値は "*" であるため、状態に関係なくすべてのジョブがこの関数によって検索されます。 | N | "Failed,CompletedWithWarnings" |
| JobFailureCodeList | このパラメーターを使用すると、関数の出力は特定のエラー コードにフィルター処理されます。 既定では、このパラメーターの値は "*" であるため、エラー コードに関係なくすべてのジョブがこの関数によって検索されます。 | N | "Success"
| DatasourceSetName | このパラメーターを使用すると、関数の出力は特定の親リソースにフィルター処理されます。 たとえば、仮想マシン "testvm" に属する、Azure VM 内の SQL のバックアップ インスタンスを返すには、このパラメーターの値として _testvm_ を指定します。 既定では、この値は "*" であるため、すべてのバックアップ インスタンスにわたるレコード検索がこの関数によって行われます。 | N | "testvm" |
| BackupInstanceName | このパラメーターを使用すると、特定のバックアップ インスタンスに対するジョブを名前で検索できます。 既定では、この値は "*" であるため、すべてのバックアップ インスタンスにわたるレコード検索がこの関数によって行われます。 | N | "testvm" |
| ExcludeLog | このパラメーターを使用すると、ログ ジョブが関数から返されなくなります (クエリのパフォーマンス向上に役立ちます)。 既定では、このパラメーターの値は true であるため、ログ ジョブはこの関数によって除外されます。 | N | true


**返されるフィールド**

| **フィールド名** | **説明** |
| -------------- | --------------- |
| UniqueId | ジョブの一意の ID を示す主キー |
| OperationCategory | 実行中の操作のカテゴリ。 Backup、Restore などがあります |
| Operation | 実行中の操作の詳細。 Log (ログ バックアップの場合) などです|
| 状態 | ジョブの状態。 Completed、Failed、CompletedWithWarnings などがあります |
| ErrorTitle | ジョブのエラー コード |
| StartTime | ジョブが開始された日付と時刻 |
| DurationInSecs | ジョブの期間 (秒) |
| DataTransferredInMBs | ジョブによって転送されたデータ (MB) |
| RestoreJobRPDateTime | 回復する復旧ポイントが作成された日付と時刻 |
| RestoreJobRPLocation | 回復する復旧ポイントが作成された場所 |
| BackupInstanceUniqueId | ジョブに関連付けられたバックアップ インスタンスを参照する外部キー |
| BackupInstanceId | ジョブに関連付けられたバックアップ インスタンスの Azure Resource Manager (ARM) ID |
| BackupInstanceFriendlyName | ジョブに関連付けられたバックアップ インスタンスの名前 |
| DatasourceResourceId | ジョブに関連付けられた基になるデータソースの Azure Resource Manager (ARM) ID。 VM の Azure Resource Manager (ARM) ID などがあります |
| DatasourceFriendlyName | ジョブに関連付けられた基になるデータソースのフレンドリ名 |
| DatasourceType | ジョブに関連付けられたデータソースの種類。 "Microsoft.Compute/virtualMachines" などがあります |
| BackupSolution | ジョブが関連付けられたバックアップ ソリューション。 Azure VM のバックアップ、Azure VM 内の SQL のバックアップなどがあります。 |
| DatasourceSetResourceId | データソースの親リソースの Azure Resource Manager (ARM) ID (該当する場合)。 たとえば、Azure VM 内の SQL のデータソースの場合、このフィールドには、SQL Database が存在する VM の Azure Resource Manager (ARM) ID が格納されます。 |
| DatasourceSetType | データソースの親リソースの種類 (該当する場合)。 たとえば、Azure VM 内の SAP HANA のデータソースの場合、親リソースが Azure VM であるため、このフィールドには Microsoft.Compute/virtualMachines が格納されます。 |
| VaultResourceId | ジョブに関連付けられたコンテナーの Azure Resource Manager (ARM) ID |
| VaultUniqueId | ジョブに関連付けられたコンテナーを参照する外部キー |
| VaultName | ジョブに関連付けられたコンテナーの名前 |
| VaultTags | ジョブに関連付けられたコンテナーのタグ |
| VaultSubscriptionId | ジョブに関連付けられたコンテナーのサブスクリプション ID |
| VaultLocation | ジョブに関連付けられたコンテナーの場所 |
| VaultStore_StorageReplicationType | ジョブに関連付けられたコンテナーのストレージ レプリケーションの種類 |
| VaultType | コンテナーの種類 ("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | レコードのタイムスタンプ |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances()

この関数を呼び出すと、ご利用の Recovery Services コンテナーに関連付けられたバックアップ インスタンスの一覧と共に、クラウド ストレージの使用量や関連付けられたポリシーなど、各バックアップ インスタンスに関する詳細情報が返されます。

**パラメーター**

| **パラメーター名** | **説明** | **必須** | **値の例** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart から RangeEnd までの期間のバックアップ インスタンス関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを RangeEnd パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって返されるのは各バックアップ インスタンスの最新のレコードのみとなります。 | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart から RangeEnd までの期間のバックアップ インスタンス関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを RangeStart パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって返されるのは各バックアップ インスタンスの最新のレコードのみとなります。 | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のサブスクリプション セットにフィルター処理されます。 サブスクリプション ID のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したサブスクリプション内にあるバックアップ インスタンスのみを取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのサブスクリプションにわたるレコード検索がこの関数によって行われます。 | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のリージョン セットにフィルター処理されます。 リージョンのコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したリージョン内にあるバックアップ インスタンスのみを取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのリージョンにわたるレコード検索がこの関数で行われます。 | N | "eastus,westus"|
| VaultList    |このパラメーターを使用すると、関数の出力は、特定のコンテナー セットにフィルター処理されます。 コンテナー名のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したコンテナーのみに関連するバックアップ インスタンスのレコードを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのコンテナーにわたってバックアップ インスタンスのレコードの検索がこの関数によって行われます。 | N |"vault1,vault2,vault3"|
| VaultTypeList     | このパラメーターを使用すると、関数の出力は特定のコンテナーの種類に関係するレコードにフィルター処理されます。 現在サポートされているコンテナーの種類は、"Microsoft.RecoveryServices/vaults" のみであり、このパラメーターの既定値となります。 | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | このパラメーターを使用すると、レガシ AzureDiagnostics テーブル内のデータに対してクエリを実行するかどうかを選択できます。 このパラメーターの値を false にした場合は、この関数によって、AzureDiagnostics テーブルとリソース固有のテーブルの両方からのデータに対するクエリが実行されます。 このパラメーターの値を true にした場合は、この関数によって、リソース固有のテーブルのみからのデータに対するクエリが実行されます。 既定値は true です。 | N | true |
| BackupSolutionList | このパラメーターを使用すると、関数の出力は、ご利用の Azure 環境で使用されている特定のバックアップ ソリューション セットにフィルター処理されます。 たとえば、このパラメーターの値として「Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM」を指定した場合、Azure 仮想マシンのバックアップ、Azure VM 内の SQL のバックアップ、または DPM から Azure へのバックアップを使用してバックアップされた項目に関連するレコードのみがこの関数から返されます。 既定では、このパラメーターの値は ' * ' です。この場合、この関数からは、バックアップ レポートでサポートされているすべてのバックアップ ソリューションに関連するレコードが返されます (サポートされている値は、"Azure Virtual Machine Backup"、"SQL in Azure VM Backup"、"SAP HANA in Azure VM Backup"、"Azure Storage (Azure Files) Backup"、"Azure Backup Agent"、"DPM"、"Azure Backup Server"、またはコンマ区切りのこれらの値の任意の組み合わせです)。 | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| ProtectionInfoList | このパラメーターを使用すると、アクティブに保護されているバックアップ インスタンスのみを含めるか、保護が停止されているインスタンスと初回バックアップが保留にされているインスタンスも含めるかを選択できます。 サポートされている値は、"Protected"、"ProtectionStopped"、"InitialBackupPending"、またはコンマ区切りのこれらの値の任意の組み合わせです。 既定では、この値は "*" であるため、保護の詳細に関係なく、すべてのバックアップ インスタンスがこの関数によって検索されます。 | N | "Protected" |
| DatasourceSetName | このパラメーターを使用すると、関数の出力は特定の親リソースにフィルター処理されます。 たとえば、仮想マシン "testvm" に属する、Azure VM 内の SQL のバックアップ インスタンスを返すには、このパラメーターの値として _testvm_ を指定します。 既定では、この値は "*" であるため、すべてのバックアップ インスタンスにわたるレコード検索がこの関数によって行われます。 | N | "testvm" |
| BackupInstanceName | このパラメーターを使用すると、特定のバックアップ インスタンスを名前で検索できます。 既定では、この値は "*" であるため、すべてのバックアップ インスタンスがこの関数によって検索されます。 | N | "testvm" |
| DisplayAllFields | このパラメーターを使用すると、関数によって返されるフィールドのサブセットのみを取得するかどうかを選択できます。 このパラメーターの値を false にすると、ストレージおよび保有ポイントに関連する情報が関数の出力から除外されます。 これが役に立つのは、大規模なクエリの中間手順としてこの関数を使用していて、分析に必要のない列を削除することでクエリのパフォーマンスを最適化する必要がある場合です。 既定では、このパラメーターの値は true であるため、バックアップ インスタンスに関連するすべてのフィールドがこの関数から返されます。 | N | true |

**返されるフィールド**

| **フィールド名** | **説明** |
| -------------- | --------------- |
| UniqueId | バックアップ インスタンスの一意の ID を示す主キー |
| Id | バックアップ インスタンスの Azure Resource Manager (ARM) ID |
| FriendlyName | バックアップ インスタンスのフレンドリ名 |
| ProtectionInfo | バックアップ インスタンスの保護設定に関する情報。 保護が構成されている、保護が停止されている、初回バックアップが保留中である、などがあります |
| LatestRecoveryPoint | バックアップ インスタンスに関連付けられた最新の回復ポイントの日付と時刻 |
| OldestRecoveryPoint | バックアップ インスタンスに関連付けられた最も古い回復ポイントの日付と時刻 |
| SourceSizeInMBs | バックアップ インスタンスのフロントエンドのサイズ (MB) |
| VaultStore_StorageConsumptionInMBs | Vault-Standard 層内のバックアップ インスタンスによって使用されているクラウド ストレージの合計 |
| DataSourceFriendlyName | バックアップ インスタンスに対応するデータソースのフレンドリ名 |
| BackupSolution | バックアップ インスタンスが関連付けられているバックアップ ソリューション。 Azure VM のバックアップ、Azure VM 内の SQL のバックアップなどがあります。 |
| DatasourceType | バックアップ インスタンスに対応するデータソースの種類。 "Microsoft.Compute/virtualMachines" などがあります |
| DatasourceResourceId | バックアップ インスタンスに対応する基になるデータソースの Azure Resource Manager (ARM) ID。 VM の Azure Resource Manager (ARM) ID などがあります |
| DatasourceSetFriendlyName | データソースの親リソースのフレンドリ名 (該当する場合)。 たとえば、Azure VM 内の SQL のデータソースの場合、このフィールドには、SQL Database が存在する VM の名前が格納されます。 |
| DatasourceSetResourceId | データソースの親リソースの Azure Resource Manager (ARM) ID (該当する場合)。 たとえば、Azure VM 内の SQL のデータソースの場合、このフィールドには、SQL Database が存在する VM の Azure Resource Manager (ARM) ID が格納されます。 |
| DatasourceSetType | データソースの親リソースの種類 (該当する場合)。 たとえば、Azure VM 内の SAP HANA のデータソースの場合、親リソースが Azure VM であるため、このフィールドには Microsoft.Compute/virtualMachines が格納されます。 |
| PolicyName | バックアップ インスタンスに関連付けられたポリシーの名前 |
| PolicyUniqueId | バックアップ インスタンスに関連付けられたポリシーを参照する外部キー  |
| PolicyId | バックアップ インスタンスに関連付けられたポリシーの Azure Resource Manager (ARM) ID |
| VaultResourceId | バックアップ インスタンスに関連付けられたコンテナーの Azure Resource Manager (ARM) ID |
| VaultUniqueId | バックアップ インスタンスに関連付けられたコンテナーを参照する外部キー |
| VaultName | バックアップ インスタンスに関連付けられたコンテナーの名前 |
| VaultTags | バックアップ インスタンスに関連付けられたコンテナーのタグ |
| VaultSubscriptionId | バックアップ インスタンスに関連付けられたコンテナーのサブスクリプション ID |
| VaultLocation | バックアップ インスタンスに関連付けられたコンテナーの場所 |
| VaultStore_StorageReplicationType | バックアップ インスタンスに関連付けられたコンテナーのストレージ レプリケーションの種類 |
| VaultType | コンテナーの種類 ("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | レコードのタイムスタンプ |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups()

この関数を呼び出すと、バックアップに関連するすべての課金エンティティ (課金グループ) の一覧と共に、フロントエンドのサイズやクラウド ストレージの合計など、主要な課金コンポーネントに関する情報が返されます。

**パラメーター**

| **パラメーター名** | **説明** | **必須** | **値の例** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart から RangeEnd までの期間の課金グループ関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを RangeEnd パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって取得できるのは各課金グループの最新のレコードのみとなります。 | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart から RangeEnd までの期間の課金グループ関連のすべてのレコードを取り込む必要がある場合にのみ、このパラメーターを　RangeStart パラメーターと一緒に使用します。 既定では、RangeStart と RangeEnd の値は null であるため、この関数によって取得できるのは各課金グループの最新のレコードのみとなります。 | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のサブスクリプション セットにフィルター処理されます。 サブスクリプション ID のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したサブスクリプション内にある課金グループのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのサブスクリプションにわたるレコード検索がこの関数によって行われます。 | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のリージョン セットにフィルター処理されます。 リージョンのコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したリージョン内にある課金グループのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのリージョンにわたるレコード検索がこの関数で行われます。 | N | "eastus,westus"|
| VaultList    |このパラメーターを使用すると、関数の出力は、特定のコンテナー セットにフィルター処理されます。 コンテナー名のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したコンテナーのみに関連するバックアップ インスタンスのレコードを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのコンテナーにわたってポリシーの課金グループのレコードの検索がこの関数によって行われます。 | N |"vault1,vault2,vault3"|
| VaultTypeList     | このパラメーターを使用すると、関数の出力は特定のコンテナーの種類に関係するレコードにフィルター処理されます。 現在サポートされているコンテナーの種類は、"Microsoft.RecoveryServices/vaults" のみであり、このパラメーターの既定値となります。 | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | このパラメーターを使用すると、レガシ AzureDiagnostics テーブル内のデータに対してクエリを実行するかどうかを選択できます。 このパラメーターの値を false にした場合は、この関数によって、AzureDiagnostics テーブルとリソース固有のテーブルの両方からのデータに対するクエリが実行されます。 このパラメーターの値を true にした場合は、この関数によって、リソース固有のテーブルのみからのデータに対するクエリが実行されます。 既定値は true です。 | N | true |
| BackupSolutionList | このパラメーターを使用すると、関数の出力は、ご利用の Azure 環境で使用されている特定のバックアップ ソリューション セットにフィルター処理されます。 たとえば、このパラメーターの値として「Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM」を指定した場合、Azure 仮想マシンのバックアップ、Azure VM 内の SQL のバックアップ、または DPM から Azure へのバックアップを使用してバックアップされた項目に関連するレコードのみがこの関数から返されます。 既定では、このパラメーターの値は ' * ' です。この場合、この関数からは、バックアップ レポートでサポートされているすべてのバックアップ ソリューションに関連するレコードが返されます (サポートされている値は、"Azure Virtual Machine Backup"、"SQL in Azure VM Backup"、"SAP HANA in Azure VM Backup"、"Azure Storage (Azure Files) Backup"、"Azure Backup Agent"、"DPM"、"Azure Backup Server"、またはコンマ区切りのこれらの値の任意の組み合わせです)。 | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| BillingGroupName | このパラメーターを使用すると、特定の課金グループを名前で検索できます。 既定では、この値は "*" であるため、すべての課金グループがこの関数によって検索されます。 | N | "testvm" |

**返されるフィールド**

| **フィールド名** | **説明** |
| -------------- | --------------- |
| UniqueId | 課金グループの一意の ID を示す主キー |
| FriendlyName | 課金グループのフレンドリ名 |
| 名前 | 課金グループの名前 |
| 種類 | 課金グループの種類。 ProtectedContainer や BackupItem などがあります |
| SourceSizeInMBs | 課金グループのフロントエンドのサイズ (MB) |
| VaultStore_StorageConsumptionInMBs | Vault-Standard 層内の課金グループによって使用されているクラウド ストレージの合計 |
| BackupSolution | 課金グループが関連付けられているバックアップ ソリューション。 Azure VM のバックアップ、Azure VM 内の SQL のバックアップなどがあります。 |
| VaultResourceId | 課金グループに関連付けられたコンテナーの Azure Resource Manager (ARM) ID |
| VaultUniqueId | 課金グループに関連付けられたコンテナーを参照する外部キー |
| VaultName | 課金グループに関連付けられたコンテナーの名前 |
| VaultTags | 課金グループに関連付けられたコンテナーのタグ |
| VaultSubscriptionId | 課金グループに関連付けられたコンテナーのサブスクリプション ID |
| VaultLocation | 課金グループに関連付けられたコンテナーの場所 |
| VaultStore_StorageReplicationType | 課金グループに関連付けられたコンテナーのストレージ レプリケーションの種類 |
| VaultType | コンテナーの種類 ("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | レコードのタイムスタンプ |
| ExtendedProperties | 課金グループの追加のプロパティ |

### <a name="trend-functions"></a>傾向関数

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends()

この関数を呼び出すと、各バックアップ インスタンスの履歴レコードが返されるので、バックアップ インスタンスの数とストレージの使用量に関する日次、週次、月次の傾向を、複数の細分性レベルで表示できます。

**パラメーター**

| **パラメーター名** | **説明** | **必須** | **値の例** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | このパラメーターを RangeEnd パラメーターと共に使用すると、RangeStart から RangeEnd までの期間内のバックアップ インスタンス関連のすべてのレコードを取得できます。 | Y | "2021-03-03 00:00:00" |
| RangeEnd     | このパラメーターを RangeStart パラメーターと共に使用すると、RangeStart から RangeEnd までの期間内のバックアップ インスタンス関連のすべてのレコードの一覧を取得できます。 | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のサブスクリプション セットにフィルター処理されます。 サブスクリプション ID のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したサブスクリプション内にあるバックアップ インスタンスのみを取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのサブスクリプションにわたるレコード検索がこの関数によって行われます。 | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のリージョン セットにフィルター処理されます。 リージョンのコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したリージョン内にあるバックアップ インスタンスのみを取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのリージョンにわたるレコード検索がこの関数で行われます。 | N | "eastus,westus"|
| VaultList    |このパラメーターを使用すると、関数の出力は、特定のコンテナー セットにフィルター処理されます。 コンテナー名のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したコンテナーのみに関連するバックアップ インスタンスのレコードを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのコンテナーにわたってバックアップ インスタンスのレコードの検索がこの関数によって行われます。 | N |"vault1,vault2,vault3"|
| VaultTypeList     | このパラメーターを使用すると、関数の出力は特定のコンテナーの種類に関係するレコードにフィルター処理されます。 現在サポートされているコンテナーの種類は、"Microsoft.RecoveryServices/vaults" のみであり、このパラメーターの既定値となります。 | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | このパラメーターを使用すると、レガシ AzureDiagnostics テーブル内のデータに対してクエリを実行するかどうかを選択できます。 このパラメーターの値を false にした場合は、この関数によって、AzureDiagnostics テーブルとリソース固有のテーブルの両方からのデータに対するクエリが実行されます。 このパラメーターの値を true にした場合は、この関数によって、リソース固有のテーブルのみからのデータに対するクエリが実行されます。 既定値は true です。 | N | true |
| BackupSolutionList | このパラメーターを使用すると、関数の出力は、ご利用の Azure 環境で使用されている特定のバックアップ ソリューション セットにフィルター処理されます。 たとえば、このパラメーターの値として「Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM」を指定した場合、Azure 仮想マシンのバックアップ、Azure VM 内の SQL のバックアップ、または DPM から Azure へのバックアップを使用してバックアップされた項目に関連するレコードのみがこの関数から返されます。 既定では、このパラメーターの値は ' * ' です。この場合、この関数からは、バックアップ レポートでサポートされているすべてのバックアップ ソリューションに関連するレコードが返されます (サポートされている値は、"Azure Virtual Machine Backup"、"SQL in Azure VM Backup"、"SAP HANA in Azure VM Backup"、"Azure Storage (Azure Files) Backup"、"Azure Backup Agent"、"DPM"、"Azure Backup Server"、またはコンマ区切りのこれらの値の任意の組み合わせです)。 | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| ProtectionInfoList | このパラメーターを使用すると、アクティブに保護されているバックアップ インスタンスのみを含めるか、保護が停止されているインスタンスと初回バックアップが保留にされているインスタンスも含めるかを選択できます。 サポートされている値は、"Protected"、"ProtectionStopped"、"InitialBackupPending"、またはコンマ区切りのこれらの値の任意の組み合わせです。 既定では、この値は "*" であるため、保護の詳細に関係なく、すべてのバックアップ インスタンスがこの関数によって検索されます。 | N | "Protected" |
| DatasourceSetName | このパラメーターを使用すると、関数の出力は特定の親リソースにフィルター処理されます。 たとえば、仮想マシン "testvm" に属する、Azure VM 内の SQL のバックアップ インスタンスを返すには、このパラメーターの値として _testvm_ を指定します。 既定では、この値は "*" であるため、すべてのバックアップ インスタンスにわたるレコード検索がこの関数によって行われます。 | N | "testvm" |
| BackupInstanceName | このパラメーターを使用すると、特定のバックアップ インスタンスを名前で検索できます。 既定では、この値は "*" であるため、すべてのバックアップ インスタンスがこの関数によって検索されます。 | N | "testvm" |
| DisplayAllFields | このパラメーターを使用すると、関数によって返されるフィールドのサブセットのみを取得するかどうかを選択できます。 このパラメーターの値を false にすると、ストレージおよび保有ポイントに関連する情報が関数の出力から除外されます。 これが役に立つのは、大規模なクエリの中間手順としてこの関数を使用していて、分析に必要のない列を削除することでクエリのパフォーマンスを最適化する必要がある場合です。 既定では、このパラメーターの値は true であるため、バックアップ インスタンスに関連するすべてのフィールドがこの関数から返されます。 | N | true |
| AggregationType | このパラメーターを使用すると、データを取得する時間の細分性を指定できます。 このパラメーターの値を "Daily" にした場合、この関数からは 1 日あたりのバックアップ インスタンスごとのレコードが返されます。これにより、ストレージの使用量とバックアップ インスタンスの数について日次の傾向を分析できます。 このパラメーターの値を "Weekly" にした場合、この関数からは週あたりのバックアップ インスタンスごとのレコードが返されます。これにより、週次の傾向を分析できます。 同様に、"Monthly" を指定すれば、月次の傾向を分析できます。 既定値は "Daily" です。 より広い時間範囲のデータを表示する場合は、クエリのパフォーマンスを向上させ、傾向分析を容易にするために、"Weekly" または "Monthly" を使用することをお勧めします。 | N | "Weekly" |

**返されるフィールド**

| **フィールド名** | **説明** |
| -------------- | --------------- |
| UniqueId | バックアップ インスタンスの一意の ID を示す主キー |
| Id | バックアップ インスタンスの Azure Resource Manager (ARM) ID |
| FriendlyName | バックアップ インスタンスのフレンドリ名 |
| ProtectionInfo | バックアップ インスタンスの保護設定に関する情報。 保護が構成されている、保護が停止されている、初回バックアップが保留中である、などがあります |
| LatestRecoveryPoint | バックアップ インスタンスに関連付けられた最新の回復ポイントの日付と時刻 |
| OldestRecoveryPoint | バックアップ インスタンスに関連付けられた最も古い回復ポイントの日付と時刻 |
| SourceSizeInMBs | バックアップ インスタンスのフロントエンドのサイズ (MB) |
| VaultStore_StorageConsumptionInMBs | Vault-Standard 層内のバックアップ インスタンスによって使用されているクラウド ストレージの合計 |
| DataSourceFriendlyName | バックアップ インスタンスに対応するデータソースのフレンドリ名 |
| BackupSolution | バックアップ インスタンスが関連付けられているバックアップ ソリューション。 Azure VM のバックアップ、Azure VM 内の SQL のバックアップなどがあります。 |
| DatasourceType | バックアップ インスタンスに対応するデータソースの種類。 "Microsoft.Compute/virtualMachines" などがあります |
| DatasourceResourceId | バックアップ インスタンスに対応する基になるデータソースの Azure Resource Manager (ARM) ID。 VM の Azure Resource Manager (ARM) ID などがあります |
| DatasourceSetFriendlyName | データソースの親リソースのフレンドリ名 (該当する場合)。 たとえば、Azure VM 内の SQL のデータソースの場合、このフィールドには、SQL Database が存在する VM の名前が格納されます。 |
| DatasourceSetResourceId | データソースの親リソースの Azure Resource Manager (ARM) ID (該当する場合)。 たとえば、Azure VM 内の SQL のデータソースの場合、このフィールドには、SQL Database が存在する VM の Azure Resource Manager (ARM) ID が格納されます。 |
| DatasourceSetType | データソースの親リソースの種類 (該当する場合)。 たとえば、Azure VM 内の SAP HANA のデータソースの場合、親リソースが Azure VM であるため、このフィールドには Microsoft.Compute/virtualMachines が格納されます。 |
| PolicyName | バックアップ インスタンスに関連付けられたポリシーの名前 |
| PolicyUniqueId | バックアップ インスタンスに関連付けられたポリシーを参照する外部キー  |
| PolicyId | バックアップ インスタンスに関連付けられたポリシーの Azure Resource Manager (ARM) ID |
| VaultResourceId | バックアップ インスタンスに関連付けられたコンテナーの Azure Resource Manager (ARM) ID |
| VaultUniqueId | バックアップ インスタンスに関連付けられたコンテナーを参照する外部キー |
| VaultName | バックアップ インスタンスに関連付けられたコンテナーの名前 |
| VaultTags | バックアップ インスタンスに関連付けられたコンテナーのタグ |
| VaultSubscriptionId | バックアップ インスタンスに関連付けられたコンテナーのサブスクリプション ID |
| VaultLocation | バックアップ インスタンスに関連付けられたコンテナーの場所 |
| VaultStore_StorageReplicationType | バックアップ インスタンスに関連付けられたコンテナーのストレージ レプリケーションの種類 |
| VaultType | コンテナーの種類 ("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | レコードのタイムスタンプ |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends()

この関数を呼び出すと、各課金エンティティの履歴レコードが返されます。これにより、複数の細分性レベルで、フロントエンドのサイズとストレージの使用量に関連する日次、週次、および月次の主要な傾向を表示できます。

**パラメーター**

| **パラメーター名** | **説明** | **必須** | **値の例** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | このパラメーターを RangeEnd パラメーターと共に使用すると、RangeStart から RangeEnd までの期間内の課金グループ関連のすべてのレコードを取得できます。 | Y | "2021-03-03 00:00:00" |
| RangeEnd     | このパラメーターを RangeStart パラメーターと共に使用すると、RangeStart から RangeEnd までの期間内の課金グループ関連のすべてのレコードを取得できます。 | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のサブスクリプション セットにフィルター処理されます。 サブスクリプション ID のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したサブスクリプション内にある課金グループのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのサブスクリプションにわたるレコード検索がこの関数によって行われます。 | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | このパラメーターを使用すると、関数の出力は、バックアップ データが存在する特定のリージョン セットにフィルター処理されます。 リージョンのコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したリージョン内にある課金グループのみを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのリージョンにわたるレコード検索がこの関数で行われます。 | N | "eastus,westus"|
| VaultList    |このパラメーターを使用すると、関数の出力は、特定のコンテナー セットにフィルター処理されます。 コンテナー名のコンマ区切りリストをこの関数のパラメーターとして指定すると、指定したコンテナーのみに関連するバックアップ インスタンスのレコードを容易に取得できます。 既定では、このパラメーターの値は '*' であるため、すべてのコンテナーにわたってポリシーの課金グループのレコードの検索がこの関数によって行われます。 | N |"vault1,vault2,vault3"|
| VaultTypeList     | このパラメーターを使用すると、関数の出力は特定のコンテナーの種類に関係するレコードにフィルター処理されます。 現在サポートされているコンテナーの種類は、"Microsoft.RecoveryServices/vaults" のみであり、このパラメーターの既定値となります。 | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | このパラメーターを使用すると、レガシ AzureDiagnostics テーブル内のデータに対してクエリを実行するかどうかを選択できます。 このパラメーターの値を false にした場合は、この関数によって、AzureDiagnostics テーブルとリソース固有のテーブルの両方からのデータに対するクエリが実行されます。 このパラメーターの値を true にした場合は、この関数によって、リソース固有のテーブルのみからのデータに対するクエリが実行されます。 既定値は true です。 | N | true |
| BackupSolutionList | このパラメーターを使用すると、関数の出力は、ご利用の Azure 環境で使用されている特定のバックアップ ソリューション セットにフィルター処理されます。 たとえば、このパラメーターの値として「Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM」を指定した場合、Azure 仮想マシンのバックアップ、Azure VM 内の SQL のバックアップ、または DPM から Azure へのバックアップを使用してバックアップされた項目に関連するレコードのみがこの関数から返されます。 既定では、このパラメーターの値は ' * ' です。この場合、この関数からは、バックアップ レポートでサポートされているすべてのバックアップ ソリューションに関連するレコードが返されます (サポートされている値は、"Azure Virtual Machine Backup"、"SQL in Azure VM Backup"、"SAP HANA in Azure VM Backup"、"Azure Storage (Azure Files) Backup"、"Azure Backup Agent"、"DPM"、"Azure Backup Server"、またはコンマ区切りのこれらの値の任意の組み合わせです)。 | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| BillingGroupName | このパラメーターを使用すると、特定の課金グループを名前で検索できます。 既定では、この値は "*" であるため、すべての課金グループがこの関数によって検索されます。 | N | "testvm" |
| AggregationType | このパラメーターを使用すると、データを取得する時間の細分性を指定できます。 このパラメーターの値を "Daily" にした場合、この関数からは 1 日あたりの課金グループごとのレコードが返されます。これにより、ストレージの使用量とフロントエンドのサイズについて日次の傾向を分析できます。 このパラメーターの値を "Weekly" にした場合、この関数からは週あたりのバックアップ インスタンスごとのレコードが返されます。これにより、週次の傾向を分析できます。 同様に、"Monthly" を指定すれば、月次の傾向を分析できます。 既定値は "Daily" です。 より広い時間範囲のデータを表示する場合は、クエリのパフォーマンスを向上させ、傾向分析を容易にするために、"Weekly" または "Monthly" を使用することをお勧めします。 | N | "Weekly" |

**返されるフィールド**

| **フィールド名** | **説明** |
| -------------- | --------------- |
| UniqueId | 課金グループの一意の ID を示す主キー |
| FriendlyName | 課金グループのフレンドリ名 |
| 名前 | 課金グループの名前 |
| 種類 | 課金グループの種類。 ProtectedContainer や BackupItem などがあります |
| SourceSizeInMBs | 課金グループのフロントエンドのサイズ (MB) |
| VaultStore_StorageConsumptionInMBs | Vault-Standard 層内の課金グループによって使用されているクラウド ストレージの合計 |
| BackupSolution | 課金グループが関連付けられているバックアップ ソリューション。 Azure VM のバックアップ、Azure VM 内の SQL のバックアップなどがあります。 |
| VaultResourceId | 課金グループに関連付けられたコンテナーの Azure Resource Manager (ARM) ID |
| VaultUniqueId | 課金グループに関連付けられたコンテナーを参照する外部キー |
| VaultName | 課金グループに関連付けられたコンテナーの名前 |
| VaultTags | 課金グループに関連付けられたコンテナーのタグ |
| VaultSubscriptionId | 課金グループに関連付けられたコンテナーのサブスクリプション ID |
| VaultLocation | 課金グループに関連付けられたコンテナーの場所 |
| VaultStore_StorageReplicationType | 課金グループに関連付けられたコンテナーのストレージ レプリケーションの種類 |
| VaultType | コンテナーの種類 ("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | レコードのタイムスタンプ |
| ExtendedProperties | 課金グループの追加のプロパティ |

## <a name="sample-queries"></a>サンプル クエリ

システム関数の使用を開始する際に役立つサンプル クエリを以下にいくつか示します。

- 特定の時間範囲内で失敗したすべての Azure VM バックアップ ジョブ

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- 特定の時間範囲内でのすべての SQL ログ バックアップ ジョブ

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- VM "testvm" でのバックアップ ストレージの週次の使用傾向

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>次のステップ
[バックアップ レポートに関する詳細を確認する](https://docs.microsoft.com/azure/backup/configure-reports)
