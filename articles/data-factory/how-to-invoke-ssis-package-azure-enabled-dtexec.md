---
title: Azure 対応の dtexec ユーティリティを使用して SQL Server Integration Services (SSIS) パッケージを実行する
description: Azure 対応の dtexec ユーティリティを使用して SQL Server Integration Services (SSIS) パッケージを実行する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: a5540eea91937319a6ac947b50698ccaa8b25847
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931697"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Azure 対応の dtexec ユーティリティを使用して SQL Server Integration Services パッケージを実行する
この記事では、Azure 対応の dtexec (AzureDTExec) コマンド プロンプト ユーティリティについて説明します。 これは Azure Data Factory の Azure-SSIS Integration Runtime (IR) で SQL Server Integration Services (SSIS) パッケージを実行するために使用されます。

従来の dtexec ユーティリティには、SQL Server が付属しています。 詳細については、「[dtexec ユーティリティ](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)」を参照してください。 多くの場合、オンプレミスで SSIS パッケージを実行するために、ActiveBatch や Control-M などのサードパーティ製のオーケストレーターやスケジューラによって呼び出されます。 

最新の AzureDTExec ユーティリティは、SQL Server Management Studio (SSMS) ツールに付属しています。 また、Azure で SSIS パッケージを実行するために、サードパーティ製のオーケストレーターまたはスケジューラで呼び出すこともできます。 これにより、SSIS パッケージのクラウドへのリフト アンド シフトまたは移行が促進されます。 移行後、日常業務でサードパーティ製のオーケストレーターまたはスケジューラを使い続けたい場合は、dtexec ではなく AzureDTExec を呼び出します。

AzureDTExec は、Data Factory パイプラインで SSIS パッケージの実行アクティビティとしてパッケージを実行します。 詳細については、[Azure Data Factory アクティビティとしての SSIS パッケージの実行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。 

AzureDTExec は、データ ファクトリのパイプラインを生成する Azure Active Directory (Azure AD) アプリケーションを使用するように、SSMS で構成できます。 また、パッケージが格納されているファイル システム、ファイル共有、または Azure Files にアクセスするように構成することもできます。 AzureDTExec では、呼び出しオプションで指定した値に基づいて、SSIS パッケージの実行アクティビティが含まれる固有の Data Factory パイプラインが生成されて実行されます。 オプションに同じ値を指定して AzureDTExec を呼び出すと、既存のパイプラインが再実行されます。

## <a name="prerequisites"></a>前提条件
AzureDTExec を使用するには、最新バージョンの SSMS (バージョン 18.3 以降) をダウンロードしてインストールします。 これは、[こちらの Web サイト](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)からダウンロードできます。

## <a name="configure-the-azuredtexec-utility"></a>AzureDTExec ユーティリティを構成する
ローカル コンピューターに SSMS をインストールすると、AzureDTExec もインストールされます。 設定を構成するには、 **[管理者として実行]** オプションを使用して SSMS を起動します。 次に、 **[ツール]**  >  **[Azure への移行]**  >  **[Azure 対応の DTExec を構成する]** の順に選択します。

![[Configure Azure-enabled DTExec]\(Azure 対応の DTExec の構成\) メニュー](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

この操作により、 **[AzureDTExecConfig]** ウィンドウが開きますす。このウィンドウは、*AzureDTExec.settings* ファイルに書き込むために、管理者特権で開く必要があります。 SSMS を管理者として実行していない場合は、[ユーザーアカウント制御 (UAC)] ウィンドウが開きます。 管理者パスワードを入力して、特権を昇格させてください。

![[Configure Azure-enabled DTExec]\(Azure 対応の DTExec の構成\) の設定](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

**[AzureDTExecConfig]** ウィンドウでは、次のように構成設定を入力します。

- **ApplicationId**: データ ファクトリにパイプラインを生成するための適切なアクセス許可を使用して作成した Azure AD アプリの一意の識別子を入力します。 詳細については、[Azure portal での Azure AD アプリとサービス プリンシパルの作成](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)に関する記事を参照してください。
- **AuthenticationKey**: Azure AD アプリの認証キーを入力します。
- **TenantId**: Azure AD アプリがその下に作成される Azure AD テナントの一意の識別子を入力します。
- **SubscriptionId**: データ ファクトリがその下に作成された Azure サブスクリプションの一意の識別子を入力します。
- **ResourceGroup**:データ ファクトリが作成された Azure リソース グループの名前を入力します。
- **DataFactory**: データ ファクトリの名前を入力します。その中に、AzureDTExec を呼び出すときに指定するオプションの値に基づいて、SSIS パッケージの実行アクティビティを含む固有のパイプラインが生成されます。
- **IRName**: データ ファクトリ内の Azure-SSIS IR の名前を入力します。AzureDTExec を呼び出すときに、汎用名前付け規則 (UNC) パスで指定するパッケージがここで実行されます。
- **PackageAccessDomain**: AzureDTExec を呼び出すときに指定する UNC パス内のパッケージにアクセスするためのドメイン資格情報を入力します。
- **PackageAccessUserName**: AzureDTExec を呼び出すときに指定する UNC パス内のパッケージにアクセスするためのユーザー名資格情報を入力します。
- **PackageAccessPassword**: AzureDTExec を呼び出すときに指定する UNC パス内のパッケージにアクセスするためのパスワード資格情報を入力します。
- **LogPath**: ログ フォルダーの UNC パスを入力します。その中に、Azure-SSIS IR でのパッケージ実行からのログ ファイルが書き込まれます。
- **LogLevel**: Azure-SSIS IR でのパッケージ実行に対するログ記録のスコープを入力します。事前定義されたオプション **null**、**Basic**、**Verbose**、または **Performance** から選択します。
- **LogAccessDomain**: ログ ファイルを書き込むときに、UNC パス内のログ フォルダーにアクセスするためのドメイン資格情報を入力します。これは、**LogPath** が指定されていて、**LogLevel** が **null** ではないときは必須です。
- **LogAccessUserName**: ログ ファイルを書き込むときに、UNC パス内のログ フォルダーにアクセスするためのユーザー名資格情報を入力します。これは、**LogPath** が指定されていて、**LogLevel** が **null** ではないときは必須です。
- **LogAccessPassword**: ログ ファイルを書き込むときに、UNC パス内のログ フォルダーにアクセスするためのパスワード資格情報を入力します。これは、**LogPath** が指定されていて、**LogLevel** が **null** ではないときは必須です。
- **PipelineNameHashStrLen**: AzureDTExec を呼び出すときに指定するオプションの値から生成されるハッシュ文字列の長さを入力します。 この文字列は、Azure-SSIS IR でパッケージを実行する Data Factory パイプラインの一意な名前を形成するために使用されます。 通常、長さは 32 文字で十分です。

パッケージとログ ファイルをオンプレミスのファイル システムまたはファイル共有に保存するために、オンプレミスのネットワークに接続されている仮想ネットワークに Azure-SSIS IR を結合し、パッケージをフェッチしてログ ファイルを書き込むことができるようにします。 詳細については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)」を参照してください。

*AzureDTExec.settings* ファイルに書き込まれた機密の値がプレーン テキストで表示されないようにするために、それらは Base64 エンコードの文字列にエンコードされます。 AzureDTExec を呼び出すと、Base64 でエンコードされたすべての文字列が元の値にデコードされます。 *AzureDTExec.settings* ファイルにアクセスできるアカウントを制限することで、セキュリティを強化できます。

## <a name="invoke-the-azuredtexec-utility"></a>AzureDTExec ユーティリティを呼び出す
コマンド ライン プロンプトで AzureDTExec を呼び出し、ユース ケース シナリオの特定のオプションに関連する値を指定できます。

ユーティリティが `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` にインストールされていることを確認します。 パスを 'PATH' 環境変数に追加すると、どこからでも呼び出すことができます。

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

AzureDTExec の呼び出しでは、dtexec の呼び出しと同様のオプションが提供されます。 詳細については、「[dtexec ユーティリティ](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)」を参照してください。 現在サポートされているオプションは次のとおりです。

- **/F[ile]** : ファイル システム、ファイル共有、または Azure Files に格納されているパッケージを読み込みます。 このオプションの値としては、ファイル システム、ファイル共有、または Azure Files 内のパッケージ ファイルの UNC パスに .dtsx 拡張子を付けたものを指定できます。 指定する UNC パスにスペースが含まれている場合は、パス全体を引用符で囲みます。
- **/Conf[igFile]** : 値を抽出する構成ファイルを指定します。 このオプションを使用すると、設計時に指定したものと異なるパッケージの実行時構成を設定できます。 XML 構成ファイルに異なる設定を格納し、パッケージの実行前に読み込むことができます。 詳しくは、[SSIS パッケージの構成](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)に関する記事をご覧ください。 このオプションの値を指定するには、ファイル システム、ファイル共有、または Azure Files 内の構成ファイルの UNC パスに dtsConfig 拡張子を付けたものを使用します。 指定する UNC パスにスペースが含まれている場合は、パス全体を引用符で囲みます。
- **/Conn[ection]** : パッケージ内の既存の接続マネージャーに対する接続文字列を指定します。 このオプションを使用すると、パッケージ内の既存の接続マネージャーに対して、設計時に指定したものとは異なる実行時の接続文字列を設定できます。 このオプションの値は、次のように指定します: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`。
- **/Set**: パッケージ内のパラメーター、変数、プロパティ、コンテナー、ログ プロバイダー、Foreach 列挙子、または接続の構成を上書きします。 このオプションは、複数回指定できます。 このオプションの値は、次のように指定します: `property_path;value`。 たとえば、`\package.variables[counter].Value;1` は `counter` 変数の値を 1 としてオーバーライドします。 **パッケージ構成**ウィザードを使用すると、値を上書きするパッケージ内の項目の `property_path` の値を検索、コピー、貼り付けすることができます。 詳しくは、[パッケージ構成ウィザード](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)に関する記事をご覧ください。
- **/De[crypt]** : **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** 保護レベルで構成されているパッケージの暗号化解除パスワードを設定します。

> [!NOTE]
> オプションに新しい値を指定して AzureDTExec を呼び出すと、新しいパイプラインが生成されます (オプション **/De[cript]** を除く)。

## <a name="next-steps"></a>次の手順

AzureDTExec を呼び出した後に、SSIS パッケージの実行アクティビティを含む一意のパイプラインが生成され、実行されたら、Data Factory ポータルで監視できます。 詳細については、[Data Factory アクティビティとしての SSIS パッケージの実行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。

> [!WARNING]
> 生成されたパイプラインは、AzureDTExec によってのみ使用されることが想定されています。 そのプロパティまたはパラメーターは将来変更される可能性があるため、変更したり他の目的で再利用したりしないでください。 変更によって AzureDTExec が壊れる可能性があります。 そのような場合は、パイプラインを削除してください。 AzureDTExec により、次回の呼び出し時に新しいパイプラインが生成されます。
