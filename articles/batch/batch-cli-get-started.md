---
title: "Batch 用の Azure CLI の概要 | Microsoft Docs"
description: "Azure Batch サービスのリソースを管理するために使用できる Azure CLI の Batch コマンドの概要を簡単に説明します。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 01/23/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 698c481e2eff5e0a3b893a0377d9f4cd2f052eb4
ms.lasthandoff: 03/21/2017


---
# <a name="manage-batch-resources-with-azure-cli"></a>Azure CLI で Batch リソースを管理する

クロスプラットフォームの Azure コマンド ライン インターフェイス (Azure CLI) を使用すると、Linux、Mac、Windows のコマンド シェルで Batch アカウントや各種リソース (プール、ジョブ、タスクなど) を管理できます。 Batch API、Azure Portal、Batch PowerShell コマンドレットを使用して実行するタスクの多くは、Azure CLI で実行したりスクリプト化したりできます。

この記事は、Azure CLI バージョン 0.10.5 に基づいています。

## <a name="prerequisites"></a>前提条件
* [Azure CLI のインストール](../cli-install-nodejs.md)
* [Azure サブスクリプションへの Azure CLI の接続](../xplat-cli-connect.md)
* **Resource Manager モード**への切り替え: `azure config mode arm`

> [!TIP]
> 最新のサービスや機能強化を活かすためにも、Azure CLI のインストールを定期的に更新することをお勧めします。
> 
> 

## <a name="command-help"></a>コマンドのヘルプ
Azure CLI では、すべてのコマンドでヘルプ テキストを表示できます。ヘルプを表示するには、コマンドの後ろに `-h` オプションのみを追加します。 次に例を示します。

* `azure` コマンドのヘルプを表示するには、「`azure -h`」と入力します。
* CLI のすべての Batch コマンドの一覧を取得するには、「`azure batch -h`」と入力します。
* Batch アカウントの作成に関するヘルプを表示するには、「 `azure batch account create -h`

確信が持てない場合は、コマンド ライン オプション `-h` を使用して、任意の Azure CLI コマンドのヘルプを表示してください。

## <a name="create-a-batch-account"></a>Batch アカウントを作成する
用途:

    azure batch account create [options] <name>

例:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

指定したパラメーターを使用して、新しい Batch アカウントを作成します。 少なくとも、場所、リソース グループ、アカウント名を指定する必要があります。 リソース グループがない場合は、`azure group create` を実行してリソース グループを作成します。`--location` オプションには Azure リージョンのいずれか ("West US" など) を指定します。 次に例を示します。

    azure group create --name "resgroup001" --location "West US"

> [!NOTE]
> Batch アカウント名は、アカウントが作成される Azure リージョン内で一意である必要があります。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。 Batch アカウント名に `-` や `_` のような特殊文字は使用できません。
> 
> 

### <a name="linked-storage-account-autostorage"></a>リンクされたストレージ アカウント (autostorage)
Batch アカウントの作成時に、(必要に応じて) **汎用** のストレージ アカウントを Batch アカウントにリンクすることができます。 Batch の[アプリケーション パッケージ](batch-application-packages.md)機能では、[Batch File Conventions .NET](batch-task-output.md) ライブラリの場合と同様に、リンクされた汎用の Storage アカウント内の BLOB ストレージを使用します。 これらのオプション機能は、Batch タスクで実行するアプリケーションのデプロイや、そのアプリケーションによって生成されるデータの保持に役立ちます。

Batch アカウントの作成時に、既存の Azure ストレージ アカウントを新しい Batch アカウントにリンクするには、 `--autostorage-account-id` オプションを指定します。 このオプションでは、ストレージ アカウントの完全修飾リソース ID が必要です。

初めに、ストレージ アカウントの詳細を表示します。

    azure storage account show --resource-group "resgroup001" "storageaccount001"

次に、`--autostorage-account-id` オプションの **Url** 値を使用します。 Url 値は "/subscriptions/" で始まり、サブスクリプション ID とストレージ アカウントのリソース パスが含まれています。

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Batch アカウントを削除する
用途:

    azure batch account delete [options] <name>

例:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

指定した Batch アカウントを削除します。 プロンプトが表示されたら、アカウントの削除を確定します (アカウントの削除は完了するまでに時間がかかる場合があります)。

## <a name="manage-account-access-keys"></a>アカウントのアクセス キーを管理する
Batch アカウントで [リソースを作成および変更する](#create-and-modify-batch-resources) には、アクセス キーが必要です。

### <a name="list-access-keys"></a>アクセス キーを一覧表示する
用途:

    azure batch account keys list [options] <name>

例:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

指定された Batch アカウントのアカウント キーを一覧表示します。

### <a name="generate-a-new-access-key"></a>新しいアクセス キーを生成する
用途:

    azure batch account keys renew [options] --<primary|secondary> <name>

例:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

指定された Batch アカウントの指定されたアカウント キーを再生成します。

## <a name="create-and-modify-batch-resources"></a>Batch リソースを作成および変更する
Azure CLI を使用して、プール、計算ノード、ジョブ、タスクなどの Batch リソースを作成、読み取り、更新、削除 (CRUD) することができます。 これらの CRUD 操作には、Batch アカウント名、アクセス キー、エンドポイントが必要です。 これらを指定するには、`-a`、`-k`、`-u` の各オプションを使用するか、(設定されている場合に) CLI が自動的に使用する[環境変数](#credential-environment-variables)を設定します。

### <a name="credential-environment-variables"></a>資格情報の環境変数
実行する各コマンドのコマンド ラインで `-a`、`-k`、`-u` オプションを指定する代わりに、環境変数 `AZURE_BATCH_ACCOUNT`、`AZURE_BATCH_ACCESS_KEY`、`AZURE_BATCH_ENDPOINT` を設定できます。 Batch CLI はこれらの変数 (設定されている場合) を使用するため、`-a`、`-k`、`-u` オプションを省略できます。 以降の説明では、これらの環境変数の使用を想定しています。

> [!TIP]
> `azure batch account keys list` でキーの一覧を表示し、`azure batch account show` でアカウントのエンドポイントを表示します。
> 
> 

### <a name="json-files"></a>JSON ファイル
プールやジョブなどの Batch リソースを作成する際、コマンド ライン オプションとしてパラメーターを渡す代わりに、新しいリソースの構成を含む JSON ファイルを指定できます。 For example:

`azure batch pool create my_batch_pool.json`

多くのリソース作成操作をコマンド ライン オプションのみを使用して実行できますが、一部の機能では、リソースの詳細を含む JSON 形式のファイルが必要です。 たとえば、開始タスクのリソース ファイルを指定する場合は、JSON ファイルを使用する必要があります。

リソースの作成に必要な JSON を確認するには、MSDN の [Batch の REST API リファレンス][rest_api]のドキュメントを参照してください。 「" *リソースの種類*" の追加」の各トピックには、リソース作成用の JSON の例が含まれていて、JSON ファイルのテンプレートとして使用できます。 たとえば、プール作成用の JSON は、「[アカウントへのプールの追加][rest_add_pool]」に含まれています。

> [!NOTE]
> リソースの作成時に JSON ファイルを指定すると、そのリソースに対してコマンド ラインで指定したその他すべてのパラメーターが無視されます。
> 
> 

## <a name="create-a-pool"></a>プールを作成する
用途:

    azure batch pool create [options] [json-file]

例 (仮想マシンの構成):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

例 (Cloud Services の構成):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Batch サービスに計算ノードのプールを作成します。

[Batch 機能の概要](batch-api-basics.md#pool)のページで説明したように、プール内のノードに使用するオペレーティング システムには、**仮想マシンの構成**と **Cloud Services の構成**という&2; つのオプションがあります。 仮想マシンの構成のプールを作成するには `--image-*` オプション、Cloud Services の構成のプールを作成するには `--os-family` オプションを使用します。 `--os-family` オプションと `--image-*` オプションの両方を指定することはできません。

プールの[アプリケーション パッケージ](batch-application-packages.md)と、[開始タスク](batch-api-basics.md#start-task)のコマンド ラインを指定できます。 ただし、開始タスクのリソース ファイルを指定するには、代わりに [JSON ファイル](#json-files)を使用する必要があります。

次のコマンドを使用してプールを削除します。

    azure batch pool delete [pool-id]

> [!TIP]
> [仮想マシン イメージの一覧](batch-linux-nodes.md#list-of-virtual-machine-images)で、`--image-*` オプションの適切な値を確認してください。
> 
> 

## <a name="create-a-job"></a>ジョブを作成する
用途:

    azure batch job create [options] [json-file]

例:

    azure batch job create --id "job001" --pool-id "pool001"

Batch アカウントにジョブを追加して、そのタスクを実行するプールを指定します。

次のコマンドを使用してジョブを削除します。

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>プール、ジョブ、タスク、その他のリソースを一覧表示する
各バッチ リソースの種類で `list` コマンドがサポートされています。このコマンドは Batch アカウントを照会し、その種類のリソースを一覧表示します。 たとえば、アカウント内のプールやジョブ内のタスクを一覧表示することができます。

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>リソースを効率的に一覧表示する
`list` 操作に **select**、**filter**、**expand** の句オプションを指定して、クエリを高速化できます。 これらのオプションを使用すると、Batch サービスが返すデータの量が制限されます。 フィルター処理はすべてサーバー側で行われるので、送信されるのは関心のあるデータのみになります。 これらの句を使用して、一覧表示操作の実行時に帯域幅を節約できます (それに伴って時間も節約できます)。

たとえば、次のコマンドは ID が "renderTask" で始まるプールのみを返します。

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Batch CLI は、Batch サービスがサポートしている&3; つの句すべてをサポートします。

* `--select-clause [select-clause]` 各エンティティのプロパティのサブセットを返します
* `--filter-clause [filter-clause]` 指定した OData 式に一致するエンティティのみを返します
* `--expand-clause [expand-clause]` 基になる&1; つの REST 呼び出しのエンティティ情報を取得します。 現時点では、expand 句は `stats` プロパティのみをサポートしています。

3 つの句とこれらを使用したリスト クエリの詳細については、「 [効率的な Azure Batch サービスのクエリ](batch-efficient-list-queries.md)」を参照してください。

## <a name="application-package-management"></a>アプリケーション パッケージの管理
アプリケーション パッケージを利用すると、プール内の計算ノードにアプリケーションを簡単にデプロイできます。 Azure CLI を使用して、アプリケーション パッケージのアップロード、パッケージのバージョンの管理、パッケージの削除を実行できます。

新しいアプリケーションを作成してパッケージのバージョンを追加する手順は、以下のとおりです。

**作成** する:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**追加** する:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**アクティブ化** する:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

アプリケーションの**既定のバージョン**を設定する:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>アプリケーション パッケージをデプロイする
新しいプールの作成時に、デプロイ用の&1; つ以上のアプリケーション パッケージを指定できます。 プールの作成時にパッケージを指定すると、ノードがプールに参加する際にパッケージが各ノードにデプロイされます。 ノードが再起動または再イメージ化されるときにも、パッケージがデプロイされます。

プールのノードがそこに参加するタイミングでアプリケーション パッケージをデプロイするには、`--app-package-ref` オプションを指定してプールを作成します。 `--app-package-ref` オプションには、コンピューティング ノードにデプロイする一連のアプリケーションの ID をセミコロン区切りで指定できます。

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

現時点では、コマンド ライン オプションを使ってプールを作成するとき、コンピューティング ノードにデプロイするアプリケーション パッケージの "*バージョン*" を指定することができません ("1.10-beta3" など)。 そのため先にアプリケーションの既定のバージョンを `azure batch application set [options] --default-version <version-id>` で指定してから、プールを作成するようにしてください (前出のセクションを参照)。 ただし、プールの作成時にコマンド ライン オプションではなく [JSON ファイル](#json-files)を使用している場合は、プールに使用するパッケージのバージョンを指定することができます。

アプリケーション パッケージの詳細については、「[Azure Batch アプリケーション パッケージを使用したアプリケーションのデプロイ](batch-application-packages.md)」をご覧ください。

> [!IMPORTANT]
> アプリケーション パッケージを使用するには、お使いの Batch アカウントに [Azure ストレージ アカウントをリンクする](#linked-storage-account-autostorage) 必要があります。
> 
> 

### <a name="update-a-pools-application-packages"></a>プールに含まれるアプリケーション パッケージの更新
既存のプールに割り当てられたアプリケーションを更新するには、`--app-package-ref` オプションを指定して `azure batch pool set` コマンドを実行します。

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

既存のプールに元から存在するコンピューティング ノードに対して新しいアプリケーション パッケージをデプロイするには、それらのノードを再起動するか、再イメージ化する必要があります。

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

> [!TIP]
> `azure batch node list` を使用すると、プール内の一連のノードとそのノード ID を取得できます。
> 
> 

デプロイする前に、あらかじめアプリケーションの既定のバージョンを設定しておくことを忘れないでください (`azure batch application set [options] --default-version <version-id>`)。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント
このセクションは、Azure CLI の問題のトラブルシューティングに使用できるリソースを提供することを目的としています。 すべての問題を解決できるとは限りませんが、原因を絞り込み、ヘルプ リソースを示すうえで役立ちます。

* `-h` を使用して、任意の CLI コマンドの **ヘルプ テキスト** を取得します。
* `-v` と `-vv` を使用して、コマンドの**詳細な**出力を表示します。`-vv` は "特に" 詳細で、実際の REST 要求と応答を表示します。 これらのスイッチは、完全なエラー出力を表示する場合に便利です。
* `--json` オプションを使用すると、**コマンド出力を JSON として**表示できます。 たとえば、 `azure batch pool show "pool001" --json` を実行すると、pool001 のプロパティが JSON 形式で表示されます。 この出力をコピーして変更し、 `--json-file` で使用できます (この記事の前半で説明した [JSON ファイル](#json-files) の項目を参照してください)。
* [MSDN の Batch フォーラム][batch_forum]は、非常に役立つヘルプ リソースであり、Batch チームのメンバーが入念にチェックしています。 問題が発生した場合や、特定の操作についてアドバイスが欲しい場合は、質問を投稿してください。
* 現在、すべての Batch リソース操作が Azure CLI でサポートされているわけではありません。 たとえば、現時点ではプールのアプリケーション パッケージで指定できるのはパッケージ ID のみで、パッケージの " *バージョン* " は指定できません。 このような場合は、コマンド ライン オプションを使用する代わりに、コマンドに `--json-file` を指定する必要があります。 CLI のバージョンを常に最新の状態に保ち、今後の拡張機能を必ず取り入れてください。

## <a name="next-steps"></a>次のステップ
* Batch 計算ノードで実行するアプリケーションをこの機能を使用して管理およびデプロイする方法については、「 [Azure Batch アプリケーション パッケージを使用したアプリケーションのデプロイ](batch-application-packages.md) 」を参照してください。
* Batch に対してクエリから返される情報の項目数と種類を制限する方法の詳細については、 [効率的な Batch サービスのクエリ](batch-efficient-list-queries.md) に関する記事を参照してください。

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx

