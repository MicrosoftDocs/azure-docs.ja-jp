---
title: 開発者用の API とツール
description: Azure Batch サービスを使用したソリューションの開発に利用できる API とツールについて説明します。
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89146404"
---
# <a name="overview-of-batch-apis-and-tools"></a>Batch API とツールの概要

Azure Batch による並列ワークロードの処理は、通常、Batch API のいずれかを使用して、プログラムで実行されます。 クライアント アプリケーションまたはサービスは、Batch API を使用して Batch サービスと通信することができます。 Batch API を使用すると、コンピューティング ノード (仮想マシンまたはクラウド サービス) のプールを作成して管理できます。 その後、それらのノードで実行するジョブおよびタスクをスケジュールできます。

組織の大規模なワークロードを効率的に処理したり、ノードの数を問わず (1 つ、数百、数千など)、必要なときに、またはスケジュールに基づいてジョブやタスクを実行できるように、顧客にサービス フロントエンドを提供したりできます。 また、Azure Batch を大規模なワークフローの一部として使用し、[Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json) などのツールで管理できます。

> [!TIP]
> Azure Batch で使用される機能とワークフローの詳細については、「[Batch サービスのワークフローとリソース](batch-service-workflow-features.md)」を参照してください。

## <a name="azure-accounts-for-batch-development"></a>バッチの開発用の Azure アカウント

Batch ソリューションを開発するとき、ご自身の Azure サブスクリプションで次のアカウントを使用します。

- **Batch アカウント** - Azure Batch リソース (プール、コンピューティング ノード、ジョブ、タスクなど) は、Azure [Batch アカウント](accounts.md)に関連付けられています。 アプリケーションは、Batch サービスに対する要求を行う際に、Azure Batch アカウント名、アカウントの URL、およびアクセス キーまたは Azure Active Directory トークンを使用して要求を認証します。 Azure portal またはプログラムで [Batch アカウントを作成](batch-account-create-portal.md)できます。
- **ストレージ アカウント** - Batch には、[Azure Storage](../storage/index.yml) のファイルを操作するためのサポートが組み込まれています。 ほぼすべての Batch シナリオで、Azure Blob Storage が使用されます。タスクで実行されるプログラムや、プログラムで処理されるデータのステージングに使用されたり、プログラムで生成される出力データの格納に使用されたりします。 各 Batch アカウントは、通常、対応するストレージ アカウントに関連付けられています。

## <a name="service-level-and-management-level-apis"></a>サービスレベルおよび管理レベルの API

Azure Batch には、サービス レベル用と管理レベル用の 2 つの API のセットがあります。 名前はよく似ていますが、返される結果は異なります。

アクティビティ ログで追跡されるのは、管理 API からのアクションのみです。 サービス レベル API は、Azure Resource Management (management.azure.com) レイヤーをバイパスし、ログには記録されません。

たとえば、[プールを削除するための Batch サービス API](/rest/api/batchservice/pool/delete) は、Batch アカウントで直接ターゲットになります: `DELETE {batchUrl}/pools/{poolId}`

これに対し、[プールを削除するための Batch 管理 API](/rest/api/batchmanagement/pool/delete) は、management.azure.com レイヤーでターゲットになります: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>Batch サービス API

アプリケーションとサービスは、直接 REST APIを呼び出したり、以下の 1 つ以上のクライアント ライブラリを使用したりして、Azure Batch ワークロードを実行および管理することができます。

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル | 詳細情報 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[Azure REST API - ドキュメント](/rest/api/batchservice/) |該当なし |- |- | [サポートされているバージョン](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[Azure SDK for .NET - ドキュメント](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[チュートリアル](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [リリース ノート](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Azure SDK for Python - ドキュメント](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[チュートリアル](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Readme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[Azure SDK for JavaScript - ドキュメント](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[チュートリアル](batch-nodejs-get-started.md) |- | [Readme](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Azure SDK for Java - ドキュメント](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Readme](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Management API

Batch 用の Azure Resource Manager API には、Batch アカウントにプログラムでアクセスする機能が用意されています。 これらの API を使用すると、プログラムから Microsoft.Batch プロバイダーを介して、Batch アカウント、クォータ、アプリケーション パッケージなどのリソースを管理できます。  

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[Azure REST API - ドキュメント](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[Azure SDK for .NET - ドキュメント](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [チュートリアル](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Batch Management Python** |[Azure SDK for Python - ドキュメント](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Batch Management Node.js** |[Azure SDK for JavaScript - ドキュメント](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Batch Management Java** |[Azure SDK for Java - ドキュメント](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Batch コマンド ライン ツール

これらのコマンド ライン ツールには、Batch サービスや Batch Management API と同じ機能が備わっています。 

- [Batch PowerShell コマンドレット](/powershell/module/az.batch/): [Azure PowerShell](/powershell/azure/) モジュールの Azure Batch コマンドレットを使用すると、PowerShell で Batch リソースを管理できます。
- [Azure CLI](/cli/azure):Azure CLI は、Batch サービスや Batch Management サービスなどの多くの Azure サービスを操作するためのシェル コマンドを提供するクロスプラットフォーム ツールセットです。 Batch での Azure CLI の使用について詳しくは、「[Azure CLI で Batch リソースを管理する](batch-cli-get-started.md)」を参照してください。

## <a name="other-tools-for-application-development"></a>その他のアプリケーション開発用ツール

これらの追加ツールは、Batch アプリケーションや Batch サービスの構築とデバッグに役立つ場合があります。

- [Azure ポータル](https://portal.azure.com/):Azure portal では、Batch プール、ジョブ、タスクを作成、監視、削除できます。 ジョブの実行時にこれらをはじめとする各種リソースの状態情報を確認できるほか、プールのコンピューティング ノードからファイルをダウンロードすることもできます。 たとえばトラブルシューティングの際に、失敗したタスクの `stderr.txt` をダウンロードすることができます。 コンピューティング ノードへのログインに使用できるリモート デスクトップ (RDP) ファイルをダウンロードすることもできます。
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): Batch Explorer (旧称: BatchLabs) は、Azure Batch アプリケーションの作成、デバッグ、および監視を支援する、豊富な機能を備えた無料のスタンドアロン クライアント ツールです。 Mac、Linux、または Windows 用の[インストール パッケージ](https://azure.github.io/BatchExplorer/)をダウンロードしてください。
- [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard): Batch Shipyard は、コンテナーベースのバッチ処理と HPC ワークロードを Azure Batch に対してプロビジョニング、実行、監視できるようにするツールです。
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/): 厳密には Azure Batch ツールではありませんが、ストレージ エクスプローラーは Batch ソリューションの開発とデバッグで役に立つツールです。

## <a name="additional-resources"></a>その他のリソース

- Batch アプリケーションからのイベントのログ記録について詳しくは、「[Batch の診断の評価と監視用のメトリック、アラート、およびログ](batch-diagnostics.md)」を参照してください。
- Batch サービスで発生するイベントのリファレンス情報については、「[一括分析](batch-analytics.md)」を参照してください。
- 計算ノードの環境変数については、「[Azure Batch ランタイム環境変数](batch-compute-node-environment-variables.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- [.NET 向け Azure Batch ライブラリの概要](tutorial-parallel-dotnet.md) 」では、C# と Batch .NET ライブラリを利用し、一般的な Batch ワークフローを使用して簡単なワークロードを実行する方法を学習できます。 [Python バージョン](tutorial-parallel-python.md)と [Node.js のチュートリアル](batch-nodejs-get-started.md)も用意されています。
- [GitHub のサンプル コード](https://github.com/Azure-Samples/azure-batch-samples)をダウンロードし、C# と Python の両方について、Batch とやり取りしてサンプル ワークロードのスケジュール設定と処理を実行する方法を確認してください。
