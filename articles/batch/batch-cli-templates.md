---
title: テンプレートを使用してジョブをエンドツーエンドで実行する - Azure Batch | Microsoft Docs
description: テンプレート ファイルと Azure CLI を使用して、Batch のプール、ジョブ、タスクを作成します。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 5e5ed8a69d5140814899c24e96eded6dc61e5908
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323669"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Azure Batch CLI テンプレートとファイル転送を使用する

Azure CLI に対して Azure Batch 拡張機能を使用すると、コードを記述せずに Batch ジョブを実行できます。

Azure CLI で JSON テンプレート ファイルを作成および使用して、Batch のプール、ジョブ、タスクを作成します。 CLI 拡張機能コマンドを使用して、Batch アカウントに関連付けられたストレージ アカウントにジョブ入力ファイルを簡単にアップロードし、ジョブ出力ファイルを簡単にダウンロードします。

## <a name="overview"></a>概要

Azure CLI の拡張機能を使用すると、開発者以外のユーザーが Batch をエンド ツー エンドで使用できます。 CLI コマンドのみで、プールの作成、入力データのアップロード、ジョブと関連するタスクの作成、結果の出力データのダウンロードを行うことができます。 コードを追加する必要はありません。 CLI コマンドを直接実行するか、スクリプトに統合します。

JSON ファイル用の [Azure CLI での既存の Batch サポート](batch-cli-get-started.md#json-files-for-resource-creation)に基づく Batch テンプレートで、プール、ジョブ、タスクなどのアイテムを作成するときのプロパティ値を指定します。 Batch テンプレートは、次の機能を追加します。

-   パラメーターを定義できます。 テンプレートを使用するときに、アイテムを作成するためのパラメーター値だけを指定します。アイテムの他のプロパティ値は、テンプレートの本文で指定されます。 Batch と Batch によって実行されるアプリケーションの知識があるユーザーは、プール、ジョブ、タスクのプロパティ値を指定してテンプレートを作成できます。 Batch やアプリケーションの知識が乏しいユーザーは、定義済みのパラメーターの値を指定するだけで済みます。

-   ジョブ タスク ファクトリは、ジョブに関連する 1 つまたは複数のタスクを作成し、多くのタスクの定義を作成する必要をなくし、ジョブの送 信を大幅に簡略化します。


通常、ジョブは入力データ ファイルを使用して出力データ ファイルを生成します。 既定では、ストレージ アカウントは各 Batch アカウントに関連付けられています。 コーディングやストレージの資格情報を使用せずに、CLI を使用してこのストレージ アカウントとの間でファイルを転送します。

たとえば、[ffmpeg](https://ffmpeg.org/) は、オーディオ/ビデオ ファイルを処理する一般的なアプリケーションです。 Azure Batch CLI を使用して ffmpeg を呼び出し、ソース ビデオ ファイルをさまざまな解像度にトランスコードする手順は次のとおりです。

-   プール テンプレートを作成します。 テンプレートを作成するユーザーには、ffmpeg アプリケーションを呼び出す方法とその要件に関する知識があります。作成者は、適切な OS、VM サイズ、ffmpeg のインストール方法 (アプリケーション パッケージの使用、パッケージ マネージャーの使用など)、プールの他のプロパティ値を指定します。 テンプレートを使用するときにパラメーターが作成されます。指定する必要があるのは、プール ID と VM の数だけです。

-   ジョブ テンプレートを作成します。 テンプレートを作成するユーザーは、ffmpeg を呼び出してソース ビデオを別の解像度にトランスコードする必要があることをわかっており、タスク コマンド ラインを指定します。作成者は、ソース ビデオ ファイルを格納するフォルダーがあり、入力ファイルごとにタスクが必要であることもわかっています。

-   トランスコードする一連のビデオ ファイルを持つエンド ユーザーは、まずプール テンプレートを使用してプールを作成し、プール ID と必要な VM の数のみを指定します。 これで、トランスコードするソース ファイルをアップロードできるようになります。 次に、ジョブ テンプレートを使用し、プール ID とアップロードするソース ファイルの場所だけを指定してジョブを送信できます。 生成される入力ファイルごとに 1 つのタスクを含む Batch ジョブが作成されます。 最後に、トランスコードされた出力ファイルをダウンロードできます。

## <a name="installation"></a>インストール

Azure Batch CLI 拡張機能をインストールするには、まず [Azure CLI 2.0 をインストール](/cli/azure/install-azure-cli)するか、Azure CLI を [Azure Cloud Shell](../cloud-shell/overview.md) で実行します。

次の Azure CLI コマンドを使って、最新バージョンの Batch 拡張機能をインストールします。

```azurecli
az extension add --name azure-batch-cli-extensions
```

Batch CLI 拡張機能と追加のインストール オプションの詳細については、[GitHub リポジトリ](https://github.com/Azure/azure-batch-cli-extensions)を参照してください。


CLI 拡張機能を使用するには、Azure Batch アカウントが必要です。ストレージにファイルを転送するコマンドとストレージからファイルを転送するコマンドでは、リンクされたストレージ アカウントが必要です。

Azure CLI を使用して Batch アカウントにログインするには、[Azure CLI を使用した Batch リソースの管理](batch-cli-get-started.md)の関する記事を参照してください。

## <a name="templates"></a>テンプレート

Azure Batch テンプレートは、機能と構文が Azure Resource Manager テンプレートと似ています。 これらのテンプレートは、アイテムのプロパティの名前と値が含まれた JSON ファイルですが、次の主要概念が追加されます。

-   **パラメーター**

    -   本文セクションでプロパティ値を指定し、テンプレートを使用するときに指定する必要があるパラメーター値だけを含めることができます。 たとえば、プールの完全な定義を本文に配置し、プール ID の 1 つのパラメーターだけを定義します。これにより、プールを作成するために指定する必要があるのはプール ID 文字列だけになります。
        
    -   テンプレートの本文は、Batch と Batch によって実行されるアプリケーションの知識があるユーザーが作成できます。テンプレートを使用するときは、作成者が定義したパラメーターの値だけを指定する必要があります。 そのため、Batch やアプリケーションの深い知識のないユーザーがテンプレートを使用できます。

-   **変数**

    -   単純または複雑なパラメーター値を 1 つの場所で指定し、テンプレートの本文内の 1 つ以上の場所で使用できます。 変数により、テンプレートを簡素化し、サイズを小さくすることができるだけでなく、プロパティを 1 か所で変更できるので、テンプレートの保守が容易になります。

-   **高度なコンストラクト**

    -   Batch API ではまだ使用できない一部の高度なコンストラクトをテンプレートで使用できます。 たとえば、タスク ファクトリは、共通のタスク定義を使って、ジョブの複数のタスクを作成するジョブ テンプレート内に定義できます。 これらのコンストラクトにより、複数の JSON ファイル (タスクごとに 1 ファイルなど) を動的に作成するコードを記述したり、パッケージ マネージャーを使用してアプリケーションをインストールするためのスクリプト ファイルを作成したりする必要がなくなります。

    -   ある時点で、これらのコンストラクトを Batch サービスに追加し、Batch API、UI などで使用できます。

### <a name="pool-templates"></a>プール テンプレート

プール テンプレートは、パラメーターと変数の標準的なテンプレートの機能をサポートします。 次の高度なコンストラクトもサポートします。

-   **パッケージ参照**

    -   必要に応じて、パッケージ マネージャーを使用して、ソフトウェアをプール ノードにコピーできます。 パッケージ マネージャーとパッケージ ID が指定されます。 1 つ以上のパッケージを宣言することで、必要なパッケージを取得するスクリプトを作成し、そのスクリプトをインストールして、各プール ノードで実行する必要がなくなります。

ffmpeg がインストールされた Linux VM のプールを作成するテンプレートの例を次に示します。 これを使用するには、プールの ID 文字列と、プール内の VM の数のみを指定します。

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

このテンプレート ファイルに _pool-ffmpeg.json_ という名前を付けた場合、テンプレートを次のように呼び出します。

```azurecli
az batch pool create --template pool-ffmpeg.json
```

CLI は、`poolId` パラメーターと `nodeCount` パラメーターの値を指定することを要求します。 JSON ファイル内にこれらのパラメーターを指定することもできます。 例:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

パラメーターの JSON ファイルに *pool-parameters.json* という名前を付けた場合は、テンプレートを次のように呼び出します。

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>ジョブ テンプレート

ジョブ テンプレートは、パラメーターと変数の標準的なテンプレートの機能をサポートします。 次の高度なコンストラクトもサポートします。

-   **タスク ファクトリ**

    -   1 つのタスク定義からジョブの複数のタスクを作成します。 パラメーター スイープ、ファイルごとのタスク、タスク コレクションの 3 種類のタスク ファクトリがサポートされています。

ffmpeg を使用して MP4 ビデオ ファイルを 2 つの低解像度のいずれかにトランスコードするジョブを作成するテンプレートの例を次に示します。 ソース ビデオ ファイルごとに 1 つのタスクを作成します。 ジョブの入力と出力用のファイル グループの詳細については、「[ファイル グループと ファイル転送](#file-groups-and-file-transfer)」を参照してください。

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

このテンプレート ファイルに _job-ffmpeg.json_ という名前を付けた場合、テンプレートを次のように呼び出します。

```azurecli
az batch job create --template job-ffmpeg.json
```

前と同じように、CLI は、パラメーターの値を指定することを要求します。 JSON ファイル内にこれらのパラメーターを指定することもできます。

### <a name="use-templates-in-batch-explorer"></a>Batch Explorer でテンプレートを使用する

Batch CLI テンプレートを [Batch Explorer](https://github.com/Azure/BatchExplorer) デスクトップ アプリケーション (旧称 BatchLabs) にアップロードして、Batch プールまたはジョブを作成できます。 Batch Explorer ギャラリーに定義済みのプールとジョブ テンプレートから選択することもできます。

テンプレートをアップロードするには:

1. Batch Explorer で、 **[ギャラリー]**  >  **[ローカル テンプレート]** を選択します。

2. ローカル プールまたはジョブ テンプレートを選択するか、ドラッグ アンド ドロップします。

3. **[このテンプレートを使用する]** を選択し、画面のプロンプトに従います。

## <a name="file-groups-and-file-transfer"></a>ファイル グループとファイル転送

ほとんどのジョブとタスクは、入力ファイルを必要し、出力ファイルを生成します。 通常、入力ファイルと出力ファイルは、クライアントからノードまたはノードからクライアントに転送します。 Azure Batch CLI 拡張機能では、ファイル転送を排除し、各 Batch アカウントに関連付けることができるストレージ アカウントを利用します。

ファイル グループは Azure ストレージ アカウントに作成されるコンテナーに相当します。 ファイル グループにはサブフォルダーを含めることができます。

Batch CLI 拡張機能には、クライアントから指定したファイル グループにファイルをアップロードするコマンドと、指定したファイル グループからクライアントにファイルをダウンロードするコマンドが用意されています。

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

プール テンプレートとジョブ テンプレートでは、プール ノードにコピーしたり、プール ノードからファイル グループにコピーしたりするために、ファイル グループに保存されたファイルを指定できます。 たとえば、前に指定したジョブ テンプレートでは、コード変換するためのノードにコピーされるソース ビデオ ファイルの場所として、ファイル グループ *ffmpeg-input* がタスク ファクトリに指定されています。 ファイル グループ *ffmpeg-output* は、コード変換された出力ファイルが各タスクを実行するノードからコピーされる場所です。

## <a name="summary"></a>まとめ

現在、テンプレートとファイル転送のサポートは、Azure CLI にのみ追加されています。 目標は、Batch を使用できる対象ユーザーを、Batch API を使用してコードを開発する必要のないユーザー (研究者、IT ユーザーなど) に拡大することです。 Azure、Batch、Batch によって実行されるアプリケーションの知識があるユーザーは、コードを記述せずにプールやジョブを作成するためのテンプレートを作成できます。 テンプレートのパラメーターにより、Batch やアプリケーションの詳細な知識のないユーザーがテンプレートを使用できるようになります。

Azure CLI の Batch 拡張機能を試し、フィードバックまたは提案をお寄せください。その際には、この記事に対するコメントまたは [Batch コミュニティ リポジトリ](https://github.com/Azure/Batch)をご利用ください。

## <a name="next-steps"></a>次の手順

- インストールと使用方法に関する詳細なドキュメント、サンプル、ソース コードは、[Azure GitHub リポジトリ](https://github.com/Azure/azure-batch-cli-extensions)で入手できます。

- [Batch Explorer](https://github.com/Azure/BatchExplorer) を使用した Batch リソースの作成と管理方法を確認します。
