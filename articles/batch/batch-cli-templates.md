---
title: "コードを記述せずに Azure Batch ジョブをエンド ツー エンドで実行する (プレビュー) | Microsoft Docs"
description: "Azure CLI で Batch のプール、ジョブ、タスクを作成するためのテンプレート ファイルを作成します。"
services: batch
author: mscurrell
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: markscu
ms.openlocfilehash: 6b91466da46d1f4ca9f25bf1718be783603efc58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Azure Batch CLI のテンプレートとファイル転送の使用 (プレビュー)

Azure CLI を使用して、コードを記述せずに Batch ジョブを実行できます。

Batch のプール、ジョブ、タスクを作成できるようにするためのテンプレート ファイルを作成し、Azure CLI で使用できます。 Batch アカウントに関連付けられたストレージ アカウントにジョブ入力ファイルを簡単にアップロードし、ジョブ出力ファイルを簡単にダウンロードできます。

## <a name="overview"></a>概要

Azure CLI の拡張機能を使用すると、開発者以外のユーザーが Batch をエンド ツー エンドで使用できます。 コードを必要とせずに、CLI を直接使用するか、スクリプトに統合して、プールの作成、入力データのアップロード、ジョブと関連するタスクの作成、結果の出力データのダウンロードを行うことができます。

[Azure CLI での既存の Batch サポート](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation)に基づく Batch テンプレートでは、プール、ジョブ、タスクなどのアイテムを作成するためのプロパティ値を JSON ファイルで指定できます。 Batch テンプレートにより、JSON ファイルでは実現できない次の機能が追加されます。

-   パラメーターを定義できます。 テンプレートを使用するときに、アイテムを作成するためのパラメーター値だけを指定します。アイテムの他のプロパティ値は、テンプレートの本文で指定されています。 Batch と Batch によって実行されるアプリケーションの知識があるユーザーは、プール、ジョブ、タスクのプロパティ値を指定してテンプレートを作成できます。 Batch やアプリケーションの知識が乏しいユーザーは、定義済みのパラメーターの値を指定するだけで済みます。

-   ジョブ タスク ファクトリは、ジョブに関連する 1 つまたは複数のタスクを作成し、多くのタスクの定義を作成する必要をなくし、ジョブの送 信を大幅に簡略化します。


ジョブの入力データ ファイルを指定する必要があり、多くの場合、出力データ ファイルが生成されます。 各 Batch アカウントにストレージ アカウントが既定で関連付けられます。コーディング、ストレージ資格情報を必要とせずに、CLI を使用してこのストレージ アカウントとの間でファイルを簡単に転送できます。

たとえば、[ffmpeg](http://ffmpeg.org/) は、オーディオ/ビデオ ファイルを処理する一般的なアプリケーションです。 Azure Batch CLI を使用して ffmpeg を呼び出し、ソース ビデオ ファイルをさまざまな解像度にトランスコードできます。

-   プール テンプレートを作成します。 テンプレートを作成するユーザーには、ffmpeg アプリケーションを呼び出す方法とその要件に関する知識があります。作成者は、適切な OS、VM サイズ、ffmpeg のインストール方法 (アプリケーション パッケージの使用、パッケージ マネージャーの使用など)、プールの他のプロパティ値を指定します。 テンプレートを使用するときにパラメーターが作成されます。指定する必要があるのは、プール ID と VM の数だけです。

-   ジョブ テンプレートを作成します。 テンプレートを作成するユーザーは、ffmpeg を呼び出してソース ビデオを別の解像度にトランスコードする必要があることをわかっており、タスク コマンド ラインを指定します。作成者は、ソース ビデオ ファイルを格納するフォルダーがあり、入力ファイルごとにタスクが必要であることもわかっています。

-   トランスコードする一連のビデオ ファイルを持つエンド ユーザーは、まずプール テンプレートを使用してプールを作成し、プール ID と必要な VM の数のみを指定します。 これで、トランスコードするソース ファイルをアップロードできるようになります。 次に、ジョブ テンプレートを使用し、プール ID とアップロードするソース ファイルの場所だけを指定してジョブを送信できます。 生成される入力ファイルごとに 1 つのタスクを含む Batch ジョブが作成されます。 最後に、トランスコードされた出力ファイルをダウンロードできます。

## <a name="installation"></a>インストール

テンプレートとファイル転送の機能を使用するには、拡張機能をインストールする必要があります。

Azure CLI をインストールする方法については、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」をご覧ください。

Azure CLI をインストールしたら、次の CLI コマンドを使って Batch 拡張機能をインストールできます。

```azurecli
az component update --add batch-extensions --allow-third-party
```

Batch 拡張機能の詳細については、[Windows、Mac、Linux 向けの Microsoft Azure Batch CLI 拡張機能](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux)に関するページをご覧ください。

## <a name="templates"></a>テンプレート

Azure Batch CLI では、プロパティの名前と値が含まれた JSON ファイルを指定することで、プール、ジョブ、タスクなどのアイテムを作成できます。 For example:

```azurecli
az batch pool create –-json-file AppPool.json
```

Azure Batch テンプレートは、機能と構文が Azure Resource Manager テンプレートと似ています。 これらのテンプレートは、アイテムのプロパティの名前と値が含まれた JSON ファイルですが、次の主要概念が追加されます。

-   **パラメーター**

    -   本文セクションでプロパティ値を指定し、テンプレートを使用するときに指定する必要があるパラメーター値だけを含めることができます。 たとえば、プールの完全な定義を本文に配置し、プール ID の 1 つのパラメーターだけを定義します。これにより、プールを作成するために指定する必要があるのはプール ID 文字列だけになります。
        
    -   テンプレートの本文は、Batch と Batch によって実行されるアプリケーションの知識があるユーザーが作成できます。テンプレートを使用するときは、作成者が定義したパラメーターの値だけを指定する必要があります。 そのため、Batch やアプリケーションの深い知識のないユーザーがテンプレートを使用できます。

-   **変数**

    -   単純または複雑なパラメーター値を 1 つの場所で指定し、テンプレートの本文内の 1 つ以上の場所で使用できます。 変数により、テンプレートを簡素化し、サイズを小さくすることができるだけでなく、値が変わる可能性のあるプロパティを 1 か所で変更できるので、テンプレートの保守が容易になります。

-   **高度なコンストラクト**

    -   Batch API ではまだ使用できない一部の高度なコンストラクトをテンプレートで使用できます。 たとえば、タスク ファクトリは、共通のタスク定義を使ってジョブの複数のタスクを作成するジョブ テンプレートで定義できます。 これらのコンストラクトにより、複数の JSON ファイル (タスクごとに 1 ファイルなど) を動的に作成するコードを記述したり、パッケージ マネージャーを使用してアプリケーションをインストールするためのスクリプト ファイルを作成したりする必要がなくなります。

    -   ある時点で、これらのコンストラクトを Batch サービスに追加し、Batch API、UI などで使用できます (該当する場合)。

### <a name="pool-templates"></a>プール テンプレート

パラメーターと変数という標準テンプレート機能に加え、プール テンプレートでは次のより高度なコンストラクトがサポートされています。

-   **パッケージ参照**

    -   必要に応じて、パッケージ マネージャーを使用して、ソフトウェアをプール ノードにコピーできます。 パッケージ マネージャーとパッケージ ID が指定されます。 1 つ以上のパッケージを宣言できるので、必要なパッケージを取得するスクリプトを作成し、そのスクリプトをインストールして、各プール ノードで実行する必要がなくなります。

ffmpeg がインストールされた Linux VM のプールを作成し、プール ID 文字列と VM の数だけを指定する必要があるテンプレートの例を次に示します。

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
                "description": "The pool id "
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
                    "sku": "16.04.0-LTS",
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

このテンプレート ファイルに "_pool-ffmpeg.json_" という名前を付けた場合、テンプレートを次のように呼び出します。

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>ジョブ テンプレート

パラメーターと変数という標準テンプレート機能に加え、ジョブ テンプレートでは次のより高度なコンストラクトがサポートされています。

-   **タスク ファクトリ**

    -   1 つのタスク定義からジョブの複数のタスクを作成します。 パラメーター スイープ、ファイルごとのタスク、タスク コレクションの 3 種類のタスク ファクトリがサポートされています。

ソース ビデオ ファイルごとに 1 つのタスクを作成し、ffmpeg を使用して MP4 ビデオ ファイルを 2 つの低解像度のいずかにトランスコードするジョブを作成するテンプレートの例を次に示します。

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

このテンプレート ファイルに "_job-ffmpeg.json_" という名前を付けた場合、テンプレートを次のように呼び出します。

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>ファイル グループとファイル転送

ほとんどのジョブとタスクは、入力ファイルを必要し、出力ファイルを生成します。 通常、入力ファイルと出力ファイルは両方とも、クライアントからノードまたはノードからクライアントに転送する必要があります。 Azure Batch CLI 拡張機能では、ファイル転送を排除し、Batch アカウントごとに既定で作成されるストレージ アカウントを利用します。

ファイル グループは Azure ストレージ アカウントに作成されるコンテナーに相当します。 ファイル グループにはサブフォルダーを含めることができます。

Batch CLI 拡張機能には、クライアントから指定したファイル グループにファイルをアップロードするコマンドと、指定したファイル グループからクライアントにファイルをダウンロードするコマンドが用意されています。

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

プール テンプレートとジョブ テンプレートでは、プール ノードにコピーしたり、プール ノードからファイル グループにコピーしたりするために、ファイル グループに保存されたファイルを指定できます。 たとえば、前に指定したジョブ テンプレートでは、コード変換するためにノードにコピーされるソース ビデオ ファイルの場所として、タスク ファクトリにファイル グループ "ffmpeg-input" が指定されています。ファイル グループ "ffmpeg-output" は、各タスクを実行するノードとの間で、コード変換された出力ファイルをコピーする場所として使用されます。

## <a name="summary"></a>概要

現在、テンプレートとファイル転送のサポートは、Azure CLI にのみ追加されています。 目標は、Batch を使用できる対象ユーザーを、Batch API 使用してコードを開発する必要のないユーザー (研究者、IT ユーザーなど) に拡大することです。 Azure、Batch、Batch によって実行されるアプリケーションの知識があるユーザーは、コードを記述せずにプールやジョブを作成するためのテンプレートを作成できます。 テンプレートのパラメーターにより、Batch やアプリケーションの詳細な知識のないユーザーがテンプレートを使用できるようになります。

Azure CLI の Batch 拡張機能を試し、フィードバックまたは提案をお寄せください。その際には、この記事に対するコメントまたは [Azure Batch フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)をご利用ください。

## <a name="next-steps"></a>次のステップ

- Batch テンプレートのブログ投稿: [Azure CLI を利用した Azure Batch ジョブの実行 (コード記述不要)](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/) をご覧ください。
- インストールと使用方法に関する詳細なドキュメント、サンプル、ソース コードは、[Azure GitHub リポジトリ](https://github.com/Azure/azure-batch-cli-extensions)で入手できます。
