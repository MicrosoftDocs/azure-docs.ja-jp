---
title: タスク ランタイム環境変数
description: タスク実行の環境変数に関するガイダンスと Azure Batch 解析のリファレンスです。
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 6b8ade312146802ede6e12181a082a8fcd3842fe
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960913"
---
# <a name="azure-batch-runtime-environment-variables"></a>Azure Batch ランタイム環境変数

[Azure Batch サービス](https://azure.microsoft.com/services/batch/)は、コンピューティング ノードで以下の環境変数を設定します。 これらの環境変数は、タスク コマンドラインと、コマンド ラインにより実行されるプログラムとスクリプトで参照できます。

Batch での環境変数の使用に関する詳細については、「[タスクの環境設定](./jobs-and-tasks.md#environment-settings-for-tasks)」を参照してください。

## <a name="environment-variable-visibility"></a>環境変数の可視性

これらの環境変数は、**タスク ユーザー**のコンテキスト、つまりタスクが実行されるノードのユーザー アカウントだけで表示されます。 リモート デスクトップ プロトコル (RDP) や Secure Shell (SSH) を介してコンピューティング ノードに*リモート接続*して環境変数を一覧表示しようとしても、それらは "[表示されません](./error-handling.md#connect-to-compute-nodes)"。 これは、リモート接続に使用されるユーザー アカウントが、タスクで使用されるアカウントと異なることが原因です。

環境変数の現在の値を取得するには、Windows コンピューティング ノード上で`cmd.exe`を、またはLinux ノード上で`/bin/sh`を起動します。

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>環境変数のコマンドライン拡張

コンピューティング ノード上のタスクによって実行されるコマンドラインは、シェルの下では実行されません。 そのため、これらのコマンド ラインは、環境変数の展開 (これには `PATH` が含まれる) など、シェルの機能をネイティブに利用することはできません。 このような機能を利用するためには、コマンド ラインで**シェルを呼び出す**必要があります。 たとえば、`cmd.exe` を Windows コンピューティング ノードで起動するか、または `/bin/sh` をLinux ノードで起動します。

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>環境変数

| 変数名                     | 説明                                                              | 可用性 | 例 |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | タスクが属する Batch アカウントの名前。                  | すべてのタスク。   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Batch アカウントの URL。 | すべてのタスク。 | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | すべてのアプリ パッケージ環境変数のプレフィックス。 たとえば、アプリケーション "FOO" のバージョン "1" がプールにインストールされる場合、環境変数は AZ_BATCH_APP_PACKAGE_FOO_1 (Linux の場合) または AZ_BATCH_APP_PACKAGE_FOO#1 (Windows の場合) です。 AZ_BATCH_APP_PACKAGE_FOO_1 は、パッケージがダウンロードされた場所 (フォルダー) を示します。 アプリ パッケージの既定のバージョンを使用する場合は、バージョン番号を指定せずに AZ_BATCH_APP_PACKAGE 環境変数を使用します。 Linux の場合、アプリケーション パッケージ名が "Agent-Linux-x64" で、バージョンが "1.1.46.0" だとすると、環境名は実際には次のようになります。AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0 (アンダースコアと小文字を使用)。 詳細については、[こちら](./batch-application-packages.md#execute-the-installed-applications)を参照してください。 | 関連付けられたアプリ パッケージがある任意のタスク。 ノード自体にアプリケーション パッケージがある場合は、すべてのタスクに対しても使用できます。 | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) または AZ_BATCH_APP_PACKAGE_FOO # 1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Batch サービス操作の制限されたセットへのアクセスを許可する認証トークン。 この環境変数は、[タスクが追加される](/rest/api/batchservice/task/add#request-body)ときに、[authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) が設定された場合のみに存在します。 このトークン値は、[BatchClient.Open() .NET API](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_) などの Batch API 内で、Batch クライアントを作成するための資格署名として使用されます。 | すべてのタスク。 | OAuth2 アクセス トークン |
| AZ_BATCH_CERTIFICATES_DIR       | [ タスク作業ディレクトリ内のディレクトリ][files_dirs]、この中に Linux コンピューティング ノードの証明書が格納される。 この環境変数は Windows コンピューティング ノードに適用されません。                                                  | すべてのタスク。   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | [マルチ インスタンス タスク][multi_instance]に割り当てられているノードのリストを形式 `nodeIP,nodeIP` で示します。 | マルチ インスタンスのプライマリおよびサブタスク。 | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | 現在のノードが[マルチインスタンス タスク][multi_instance]のマスター ノードかどうかを指定します。 設定可能な値は `true` および `false` です。| マルチ インスタンスのプライマリおよびサブタスク。 | `true` |
| AZ_BATCH_JOB_ID                 | タスクが属するジョブの ID。 | 開始タスクを除くすべてのタスク。 | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | ノード上のジョブ準備[タスク ディレクトリ][files_dirs]の完全パス。 | 開始タスクおよびジョブ準備タスクを除くすべてのタスク。 ジョブがジョブ準備タスクで構成されている場合にのみ使用できます。 | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | ノード上のジョブ準備[タスク作業ディレクトリ][files_dirs]の完全パス。 | 開始タスクおよびジョブ準備タスクを除くすべてのタスク。 ジョブがジョブ準備タスクで構成されている場合にのみ使用できます。 | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | [マルチ インスタンス タスク][multi_instance]のプライマリ タスクを実行するコンピューティング ノードの IP アドレスとポート。 ここで指定したポートは MPI や NCCL 通信に使用しないでください。このポートは Azure Batch サービス用に予約されています。 代わりに、変数 MASTER_PORT を使用してください。この変数には、コマンド ライン引数で渡された値を設定するか (ポート 6105 は適切な既定の選択肢です)、AML が設定した値を使用します (該当する場合)。 | マルチ インスタンスのプライマリおよびサブタスク。 | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | タスクが割り当てられているノードの ID。 | すべてのタスク。 | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | `true` の場合、現在のノードが専用ノードです。 `false` の場合、これは[優先順位の低いノード](batch-low-pri-vms.md)です。 | すべてのタスク。 | `true` |
| AZ_BATCH_NODE_LIST              | [マルチ インスタンス タスク][multi_instance]に割り当てられているノードのリストを形式 `nodeIP;nodeIP` で示します。 | マルチ インスタンスのプライマリおよびサブタスク。 | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | すべてのマウント ディレクトリが存在するノード レベルの[ファイル システム マウント](virtual-file-mount.md)場所の完全パス。 Windows ファイル共有はドライブ文字を使用するので、Windows の場合、マウント ドライブはデバイスとドライブの一部になります。  |  マウントされたディレクトリのマウント アクセス許可をユーザーが認識している場合、開始タスクを含むすべてのタスクがユーザーにアクセスできます。 | たとえば、Ubuntu での場所は `/mnt/batch/tasks/fsmounts` です。 |
| AZ_BATCH_NODE_ROOT_DIR          | ノード上のすべての [Batch ディレクトリ][files_dirs]のルートの完全パス。 | すべてのタスク。 | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | ノード上の[共有ディレクトリ][files_dirs]の完全パス。 ノードで実行されるすべてのタスクに、このディレクトリに対する読み取り/書き込みアクセス権があります。 他のノードで実行されるタスクにはこのディレクトリに対するリモート アクセス権がありません (「共有」ネットワーク ディレクトリではありません)。 | すべてのタスク。 | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | ノード上の[開始タスク ディレクトリ][files_dirs]の完全パス。 | すべてのタスク。 | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | タスクが実行されているプールの ID。 | すべてのタスク。 | batchpool001 |
| AZ_BATCH_TASK_DIR               | ノード上の[タスク ディレクトリ][files_dirs]の完全パス。 このディレクトリには、タスクの `stdout.txt` と `stderr.txt`、および AZ_BATCH_TASK_WORKING_DIR が含まれます。 | すべてのタスク。 | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | 現在のタスクの ID。 | 開始タスクを除くすべてのタスク。 | task001 |
| AZ_BATCH_TASK_SHARED_DIR | プライマリ タスクと、[マルチインスタンス タスク][multi_instance]のすべてのサブタスクで同一なディレクトリ パス。 パスは、マルチインスタンス タスクが実行されるすべてのノードで存在し、そのノードで実行されるタスク コマンド ([調整コマンド][coord_cmd]と[アプリケーション コマンド][app_cmd]の両方) に対して読み取りおよび書き込みアクセス可能です。 他のノードで実行されるサブタスクやプライマリ タスクにはこのディレクトリに対するリモート アクセス権がありません (「共有」ネットワーク ディレクトリではありません)。 | マルチ インスタンスのプライマリおよびサブタスク。 | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | ノード上の[タスク作業ディレクトリ][files_dirs]の完全パス。 現在実行中のタスクにはこのディレクトリに対する読み取り/書き込みアクセス権があります。 | すべてのタスク。 | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | ノードのリストと、[マルチインスタンス タスク][multi_instance]に割り当てられているノードあたりのコア数。 ノードとコアが `numNodes<space>node1IP<space>node1Cores<space>` の形式で一覧表示されます<br/>`node2IP<space>node2Cores<space> ...`、ノードの番号の後に 1 つまたは複数のノード IP アドレスと、それぞれのコア数が続きます。 |  マルチ インスタンスのプライマリおよびサブタスク。 |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: ./files-and-directories.md
[multi_instance]: ./batch-mpi.md
[coord_cmd]: ./batch-mpi.md#coordination-command
[app_cmd]: ./batch-mpi.md#application-command
