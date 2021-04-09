---
title: Batch サービスのワークフローとリソース
description: 開発の観点から、Batch サービスの機能とその高レベルのワークフローについて説明します。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965214"
---
# <a name="batch-service-workflow-and-resources"></a>Batch サービスのワークフローとリソース

ここでは、Azure Batch サービスの主要コンポーネントについて簡単に紹介し、Batch を使って大規模な並列コンピューティング ソリューションを開発する際に利用できる高レベルのワークフロー、および使用される主なサービスとリソースについて取り上げます。

[REST API](/rest/api/batchservice/) を直接呼び出す分散コンピューティング アプリケーションまたはサービスを開発する場合も、いずれかの [Batch SDK](batch-apis-tools.md#batch-service-apis) を使う場合も、ここで紹介するさまざまなリソースや機能を活用できます。

> [!TIP]
> Batch サービスの基本については、「 [Azure Batch の基礎](batch-technical-overview.md)」を参照してください。 また、最新の [Batch サービスの更新](https://azure.microsoft.com/updates/?product=batch)も参照してください。

## <a name="basic-workflow"></a>基本的なワークフロー

以下に示したのは、Batch サービスを使って並列ワークロードを処理するアプリケーションやサービスでほぼ例外なく使用する基本的なワークフローです。

1. 処理対象の **データ ファイル** を [Azure Storage](../storage/index.yml) アカウントにアップロードします。 Batch には、Azure Blob Storage へのアクセスのサポートが組み込まれており、これらのファイルは、タスクの実行時に [コンピューティング ノード](nodes-and-pools.md#nodes) にダウンロードすることができます。
2. タスクで実行する **アプリケーション ファイル** をアップロードします。 たとえばジョブのタスクによって実行されるバイナリやスクリプト、さらには、その依存関係をアップロードできます。 これらのファイルは、タスクによって Storage アカウントからダウンロードできます。また、Batch の[アプリケーション パッケージ](nodes-and-pools.md#application-packages)機能をアプリケーションの管理とデプロイに利用することもできます。
3. コンピューティング ノードの [プール](nodes-and-pools.md#pools) を作成します。 プールを作成するときに、プールのコンピューティング ノードの数とサイズ、オペレーティング システムを指定します。 ジョブに含まれる各タスクは、その実行時に、プール内のいずれかのノードで実行されるように割り当てられます。
4. [ジョブ](jobs-and-tasks.md#jobs)を作成します。 ジョブはタスクのコレクションを管理するものです。 各ジョブは、ジョブのタスクの実行場所とする特定のプールに関連付けて使用します。
5. ジョブに [タスク](jobs-and-tasks.md#tasks) を追加します。 各タスクは、アップロードされたアプリケーション (またはスクリプト) を実行し、ストレージ アカウントからダウンロードしたデータ ファイルを処理します。 完了した各タスクは、その出力を Azure Storage にアップロードすることができます。
6. ジョブの進行状況を監視し、タスクの出力を Azure Storage から取得します。

> [!NOTE]
> Batch サービスを利用するには、[Batch アカウント](accounts.md)が必要です。 また、ほとんどの Batch ソリューションでは、関連付けられている [Azure Storage](../storage/index.yml) アカウントがファイルの格納と取得に使用されています。

## <a name="batch-service-resources"></a>Batch サービスのリソース

次のトピックでは、分散コンピューティングのシナリオを実現する Batch のリソースについて説明します。

- [Batch アカウントおよびストレージ アカウント](accounts.md)
- [ノードとプール](nodes-and-pools.md)
- [ジョブとタスク](jobs-and-tasks.md)
- [ファイルとディレクトリ](files-and-directories.md)

## <a name="next-steps"></a>次のステップ

- Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
- [Batch .NET クライアント ライブラリ](quick-run-dotnet.md)または [Python](quick-run-python.md) を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 これらのクイック スタートでは、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行するサンプル アプリケーションの開発手順を説明しています。また、Azure Storage を使用してワークロード ファイルのステージングと取得を行う方法についても取り上げています。
- Batch ソリューションを開発するときに使用する [Batch Explorer](https://azure.github.io/BatchExplorer/) をダウンロードしてインストールします。 Batch Explorer は、Azure Batch アプリケーションの作成、デバッグ、および監視に役立ちます。
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch)、[Batch Community リポジトリ](https://github.com/Azure/Batch)、[Azure Batch フォーラム](/answers/topics/azure-batch.html)などのコミュニティ リソースを参照してください。
