---
title: Azure SQL Managed Instance エージェントを使用して SSIS パッケージを実行する
description: Azure SQL Managed Instance エージェントを使用して SSIS パッケージを実行する方法について説明します。
ms.service: data-factory
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 916d799ba08f46cb86ee2e22c4af7fc1b92b385f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386154"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>Azure SQL Managed Instance エージェントを使用して SSIS パッケージを実行する

この記事では、Azure SQL Managed Instance エージェントを使用して SQL Server Integration Services (SSIS) パッケージを実行する方法について説明します。 この機能では、オンプレミス環境内で SQL Server エージェントを使用して SSIS パッケージをスケジュールする場合と同様の動作が提供されます。

この機能を使用すると、SQL Managed Instance、Azure Files のようなファイル システム、または Azure-SSIS Integration Runtime パッケージ ストア内の SSISDB に格納されている SSIS パッケージを実行できます。

## <a name="prerequisites"></a>前提条件

この機能を使用するには、最新の SQL Server Management Studio (SSMS) を[ダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)してインストールします。 バージョンのサポートの詳細は、次のとおりです。

- SSISDB またはファイル システム内のパッケージを実行するには、SSMS バージョン 18.5 以降をインストールします。
- パッケージ ストア内のパッケージを実行するには、SSMS バージョン 18.6 以降をインストールします。

また、Azure Data Factory で [Azure SSIS 統合ランタイムをプロビジョニングする](./tutorial-deploy-ssis-packages-azure.md)必要があります。 SQL Managed Instance をエンドポイント サーバーとして使用します。

## <a name="run-an-ssis-package-in-ssisdb"></a>SSISDB で SSIS パッケージを実行する

この手順では、SQL Managed Instance エージェントを使用して、SSISDB に格納されている SSIS パッケージを呼び出します。

1. 最新バージョンの SSMS で、SQL Managed Instance に接続します。
1. 新しいエージェント ジョブと新しいジョブ ステップを作成します。 **[SQL Server エージェント]** 下で、 **[ジョブ]** フォルダーを右クリックして **[新しいジョブ]** を選択します。

   ![新しいエージェント ジョブを作成するための選択](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. **[新しいジョブ ステップ]** ページで、種類として **[SQL Server Integration Services パッケージ]** を選択します。

   ![新しい SSIS ジョブ ステップを作成するための選択](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. **[パッケージ]** タブで、パッケージの場所として **[SSIS カタログ]** を選択します。
1. SSISDB は同じ SQL Managed Instance 内にあるため、認証を指定する必要はありません。
1. SSISDB から SSIS パッケージを指定します。

   ![パッケージ ソースの種類を選択した状態の [パッケージ] タブ](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. **[構成]** タブで、次の操作を実行できます。
  
   - **[パラメータ―]** でパラメーター値を指定します。
   - **[接続マネージャー]** で値を上書きします。
   - プロパティをオーバーライドして、 **[詳細]** でログ記録レベルを選択します。

   ![パッケージ ソースの種類を選択した状態の [構成] タブ](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. **[OK]** を選択して、エージェント ジョブ構成を保存します。
1. SSIS パッケージを実行するエージェント ジョブを開始します。

## <a name="run-an-ssis-package-in-the-file-system"></a>ファイル システム内の SSIS パッケージを実行する

この手順では、SQL Managed Instance エージェントを使用して、ファイル システムに格納されている SSIS パッケージを実行します。

1. 最新バージョンの SSMS で、SQL Managed Instance に接続します。
1. 新しいエージェント ジョブと新しいジョブ ステップを作成します。 **[SQL Server エージェント]** 下で、 **[ジョブ]** フォルダーを右クリックして **[新しいジョブ]** を選択します。

   ![新しいエージェント ジョブを作成するための選択](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. **[新しいジョブ ステップ]** ページで、種類として **[SQL Server Integration Services パッケージ]** を選択します。

   ![新しい SSIS ジョブ ステップを作成するための選択](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. **[パッケージ]** タブで、次の操作を実行します。

   1. **[パッケージの場所]** に **[ファイル システム]** を選択します。

   1. **[ファイル ソースの種類]** に、次のように設定を行います。

      - パッケージが Azure Files にアップロードされている場合は、 **[Azure ファイル共有]** を選択します。

        ![[ファイル ソースの種類] のオプション](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        パッケージのパスは、 **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** です。

        **[Package file access credential]\(パッケージ ファイル アクセス資格情報\)** に Azure ファイル アカウント名とアカウント キーを入力して、Azure ファイルにアクセスします。 ドメインは **Azure** として設定されます。

      - パッケージがネットワーク共有にアップロードされている場合は、 **[ネットワーク共有]** を選択します。

        パッケージ パスは、パッケージ ファイルの UNC パスであり、.dtsx 拡張子が付いています。

        ネットワーク共有パッケージ ファイルにアクセスするには、対応するドメイン、ユーザー名、およびパスワードを入力します。
   1. パッケージ ファイルがパスワードによって暗号化されている場合は、 **[暗号化用パスワード]** を選択して、パスワードを入力します。
1. SSIS パッケージを実行するための構成ファイルが必要な場合は、 **[構成]** タブ上で、構成ファイルのパスを入力します。
   構成を Azure Files に保存する場合、その構成のパスは **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** になります。
1. **[実行オプション]** タブで、SSIS パッケージを実行するために **Windows 認証** または **32 ビット ランタイム** のどちらを使用するかを選択できます。
1. **[ログ]** タブでは、ログ ファイルを格納するためのログ パスおよび対応するログ アクセス資格情報を選択できます。 
   既定では、ログ パスはパッケージ フォルダー パスと同じであり、ログ アクセス資格情報はパッケージ アクセス資格情報と同じになります。
   ログを Azure Files に保存する場合、ログ記録のパスは **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** になります。
1. **[値の設定]** タブで、プロパティのパスおよび値を入力して、パッケージのプロパティをオーバーライドできます。

   たとえば、ユーザー変数の値をオーバーライドするには、 **`\Package.Variables[User::<variable name>].Value`** の形式でパスを入力します。
1. **[OK]** を選択して、エージェント ジョブ構成を保存します。
1. SSIS パッケージを実行するエージェント ジョブを開始します。

## <a name="run-an-ssis-package-in-the-package-store"></a>パッケージ ストア内の SSIS パッケージを実行する

この手順では、SQL Managed Instance エージェントを使用して、Azure-SSIS IR パッケージ ストアに格納されている SSIS パッケージを実行します。

1. 最新バージョンの SSMS で、SQL Managed Instance に接続します。
1. 新しいエージェント ジョブと新しいジョブ ステップを作成します。 **[SQL Server エージェント]** 下で、 **[ジョブ]** フォルダーを右クリックして **[新しいジョブ]** を選択します。

   ![新しいエージェント ジョブを作成するための選択](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. **[新しいジョブ ステップ]** ページで、種類として **[SQL Server Integration Services パッケージ]** を選択します。

   ![新しい SSIS ジョブ ステップを作成するための選択](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. **[パッケージ]** タブで、次の操作を実行します。

   1. **[パッケージの場所]** に **[パッケージ ストア]** を選択します。

   1. **[パッケージのパス]** :

      パッケージのパスは、 **`<package store name>\<folder name>\<package name>`** です。

      ![パッケージ ストアの種類のオプション](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. パッケージ ファイルがパスワードによって暗号化されている場合は、 **[暗号化用パスワード]** を選択して、パスワードを入力します。
1. SSIS パッケージを実行するための構成ファイルが必要な場合は、 **[構成]** タブ上で、構成ファイルのパスを入力します。
   構成を Azure Files に保存する場合、その構成のパスは **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** になります。
1. **[実行オプション]** タブで、SSIS パッケージを実行するために **Windows 認証** または **32 ビット ランタイム** のどちらを使用するかを選択できます。
1. **[ログ]** タブでは、ログ ファイルを格納するためのログ パスおよび対応するログ アクセス資格情報を選択できます。
   既定では、ログ パスはパッケージ フォルダー パスと同じであり、ログ アクセス資格情報はパッケージ アクセス資格情報と同じになります。
   ログを Azure Files に保存する場合、ログ記録のパスは **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** になります。
1. **[値の設定]** タブで、プロパティのパスおよび値を入力して、パッケージのプロパティをオーバーライドできます。

   たとえば、ユーザー変数の値をオーバーライドするには、 **`\Package.Variables[User::<variable name>].Value`** の形式でパスを入力します。
1. **[OK]** を選択して、エージェント ジョブ構成を保存します。
1. SSIS パッケージを実行するエージェント ジョブを開始します。

## <a name="cancel-ssis-package-execution"></a>SSIS パッケージ実行の取り消し

SQL Managed Instance エージェント ジョブからパッケージの実行を取り消すには、エージェント ジョブを直接停止するのではなく、以下の手順を実行します。

1. **msdb.dbo.sysjobs** から SQL エージェント **jobId** を探します。
1. 次のクエリを使用して、ジョブ ID に基づいて対応する SSIS **executionId** を探します。
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   SSIS パッケージが SSISDB に含まれている場合は、ジョブ実行のテーブルとして **ssisdb.internal.execution_parameter_values** を使用します。 SSIS パッケージがファイル システムに含まれている場合は、**ssisdb.internal.execution_parameter_values_noncatalog** を使用します。
1. SSISDB カタログを右クリックして、 **[アクティブな操作]** を選択します。

   ![SSISDB カタログのショートカット メニューにある [アクティブな操作]](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. **executionId** に基づいて、対応する操作を停止します。

## <a name="next-steps"></a>次のステップ
Azure Data Factory を使用して、SSIS パッケージのスケジュールを設定することもできます。 詳細な手順については、[Azure Data Factory イベント トリガー](how-to-create-event-trigger.md)に関する記事を参照してください。