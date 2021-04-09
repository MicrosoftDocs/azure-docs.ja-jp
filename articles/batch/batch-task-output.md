---
title: ジョブやタスクからの出力をデータ ストアに保存する
description: Batch タスクやジョブから出力データを保持するさまざまなオプションについて説明します。 データは、Azure Storage、または別のデータ ストアに保持することができます。
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965112"
---
# <a name="persist-job-and-task-output"></a>ジョブとタスク出力を保持する

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

一般的なタスク出力の例には次のものがあります。

- タスクによって入力データが処理されて作成されるファイル。
- タスクの実行に関連したログ ファイル。

この記事では、タスク出力を保持するためのさまざまなオプションについて説明します。

## <a name="options-for-persisting-output"></a>出力を保持する際のオプション

タスクの出力を保持するには、シナリオに応じて、いくつかの異なるアプローチがあります。

- [Batch サービス API を使用する](batch-task-output-files.md)。  
- [.NET 用の Batch ファイル規則ライブラリを使用する](batch-task-output-file-conventions.md)。  
- アプリケーション内に Batch ファイル規則の標準を実装する。
- カスタムのファイル移動ソリューションを実装する。

以下のセクションでは、各アプローチと、出力の保持に関する一般的な設計上の考慮事項について簡単に説明します。

### <a name="use-the-batch-service-api"></a>Batch サービス API を使用する

Batch サービスでは、[タスクをジョブに追加する](/rest/api/batchservice/add-a-task-to-a-job)時点、または[タスクのコレクションをジョブに追加する](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job)時点で、タスク データ用に Azure Storage 内に出力ファイルを指定することができます。

Batch サービス API を使用してタスク出力を保持することの詳細については、「[Persist task data to Azure Storage with the Batch service API](batch-task-output-files.md) (Batch サービス API を使用してタスクのデータを Azure Storage に保持する)」を参照してください。

### <a name="use-the-batch-file-conventions-library-for-net"></a>.NET 用の Batch ファイル規則ライブラリの使用

Batch では、Azure Storage 内でのタスク出力ファイルの名前付けについて、任意選択の規則セットが定義されています。 これらの規則については、[Batch ファイル規則の標準に関するページ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions)を参照してください。 ファイル規則の標準では、特定の出力ファイルについて Azure Storage 内での保存先コンテナーと BLOB パスの名前を決める際に、ジョブとタスクの名前を基にします。

出力データ ファイルの名前付けでファイル規則の標準を採用するかどうかは、開発者が自分で決めることができます。 また、保存先コンテナーや BLOB の名前を、自分で自由に付けることもできます。 出力ファイルの名前付けにファイル規則の標準を使用した場合は、[Azure Portal][portal] で出力ファイルを表示できます。

C# と .NET で Batch ソリューションを作成している開発者は、[.NET 用のファイル規則ライブラリ][nuget_package]を使用し、[Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions)に従って、タスクのデータを Azure Storage アカウントに保持することができます。 ファイル規則ライブラリでは、出力ファイルの Azure Storage への移動や、移動先のコンテナーと BLOB の名前付けが、よく知られた方法で処理されます。

.NET 用のファイル規則ライブラリを使用してタスク出力を保持することの詳細については、「[.NET 用の Batch ファイル規則ライブラリを使用した Azure Storage へのジョブおよびタスクのデータの保持](batch-task-output-file-conventions.md)」を参照してください。

### <a name="implement-the-batch-file-conventions-standard"></a>Batch ファイル規則の標準を実装する

.NET 以外の言語を使用している場合は、開発者が自分のアプリケーション内で [Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions)を実装することができます。

実証済みの名前付けスキームを使用する必要がある場合、または Azure Portal でタスク出力を表示する必要がある場合は、ファイル規則の名前付け標準を自分で実装することもできます。

### <a name="implement-a-custom-file-movement-solution"></a>カスタムのファイル移動ソリューションを実装する

ファイルの移動ソリューション全体を、開発者が自分で実装することもできます。 このアプローチは、次のような場合に使用します。

- Azure Storage 以外のデータ ストアにタスクのデータを保持する必要がある。 Azure SQL や Azure DataLake のようなデータ ストアにファイルをアップロードするには、その場所にアップロードするためのカスタム スクリプトまたは実行可能ファイルを作成します。 作成したファイルは、主要な実行可能ファイルを実行した後に、コマンド ラインで呼び出すことができます。 たとえば、Windows ノードで、これら 2 つのコマンドを次のように呼び出すことができます: `doMyWork.exe && uploadMyFilesToSql.exe`
- チェックポイント処理または初期結果の早期アップロードを実行する必要がある。
- エラー処理を詳細に制御する必要がある。 たとえば、特定のタスク終了コードに基づいて特定のアップロード アクションを実行するなど、タスクと依存関係のあるアクションが必要な場合は、独自のソリューションを実装することができます。 タスクと依存関係のあるアクションの詳細については、「[Create task dependencies to run tasks that depend on other tasks](batch-task-dependencies.md) (タスクの依存関係を作成して他のタスクに依存するタスクを実行する)」を参照してください。

## <a name="design-considerations-for-persisting-output"></a>出力の保持を設計する際の考慮事項

Batch ソリューションを設計する際は、ジョブとタスクの出力に関連する次の事項を考慮してください。

- **コンピューティング ノードの有効期間**:コンピューティング ノードは多くの場合、一時的なものです (特に自動スケールが有効なプールの場合)。 ノード上で実行されているタスクの出力は、そのノードが存在している間のみ、かつ開発者がタスクに設定したファイルのリテンション期間内にのみ使用可能です。 タスクの生成する出力がタスクの完了後に必要になる可能性がある場合、タスクは、Azure Storage などの持続性のあるストアに出力ファイルをアップロードする必要があります。

- **出力の格納**:タスク出力のデータ ストアとして Azure Storage をお勧めしますが、持続性のある記憶域であれば他のものでも使用できます。 Azure Storage へのタスクの出力の書き込みは、Batch サービス API に統合されています。 別の形式の持続ストレージを使用する場合は、タスク出力を保持するためのアプリケーション ロジックを開発者が自分で作成する必要があります。

- **出力の取得**:タスク出力は、プール内のコンピューティング ノードから直接取得するか、タスク出力を保持した場合は Azure Storage や別のデータ ストアから取得することができます。 タスクの出力をコンピューティング ノードから直接取得するには、ファイル名とノード上の出力場所が必要です。 タスク出力を Azure Storage に保持する場合は、Azure Storage SDK を使用して出力ファイルをダウンロードするために、Azure Storage 内のファイルへの完全なパスが必要になります。

- **出力の表示**:Azure portal で Batch のタスクに移動し、 **[ノード上のファイル]** を選択すると、目的の出力ファイルだけでなく、タスクに関連するすべてのファイルが表示されます。 繰り返しになりますが、コンピューティング ノード上のファイルは、そのノードが存在し、タスクに設定したファイルのリテンション期間内である場合のみ使用可能です。 Azure Storage に保持したタスク出力を表示するには、Azure portal を使用するか、[Azure Storage Explorer][storage_explorer] などの Azure Storage クライアント アプリケーションを使用することができます。 Azure Storage 内の出力データを Portal または別のツールで表示するには、ファイルの場所を知っていて、その場所に直接移動できる必要があります。

## <a name="next-steps"></a>次のステップ

- Batch サービス API の新機能を使用してタスクのデータを保持する方法について、「[Batch サービス API を使用してタスクのデータを Azure Storage に保持する](batch-task-output-files.md)」を読む。
- .NET 用の Batch ファイル規則ライブラリを使用してタスク出力を保持することについて、「[.NET 用の Batch ファイル規則ライブラリを使用した Azure Storage へのジョブおよびタスクのデータの保持](batch-task-output-file-conventions.md)」を読む。
- .NET 用の Batch クライアント ライブラリと .NET 用のファイル規則ライブラリの両方を使用して持続性のあるストレージにタスク出力を保持する方法について、GitHub にある [PersistOutputs][github_persistoutputs] サンプル プロジェクトを参照する。

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
