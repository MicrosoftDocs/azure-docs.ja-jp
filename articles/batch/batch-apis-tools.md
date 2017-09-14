---
title: "Azure Batch API とツールを使用して大規模な並列処理ソリューションを開発する | Microsoft Docs"
description: "Azure Batch サービスを使用したソリューションの開発に利用できる API とツールについて説明します。"
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c8c76944f4a95d3c8181454a7103ea0a3022189a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Batch API とツールの概要

Azure Batch による並列ワークロードの処理は、通常、[Batch API](#batch-development-apis) のいずれかを使用して、プログラムで実行されます。 クライアント アプリケーションまたはサービスは、Batch API を使用して Batch サービスと通信することができます。 Batch API を使用すると、コンピューティング ノード (仮想マシンまたはクラウド サービス) のプールを作成して管理できます。 その後、それらのノードで実行するジョブおよびタスクをスケジュールできます。 

組織の大規模なワークロードを効率的に処理したり、ノードの数を問わず (1 つ、数百、数千など)、必要なときに、またはスケジュールに基づいてジョブやタスクを実行できるように、顧客にサービス フロントエンドを提供したりできます。 また、Azure Batch を大規模なワークフローの一部として使用し、[Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json) などのツールで管理できます。

> [!TIP]
> Batch API の機能をもっと詳しく理解するには、「 [開発者向け Batch 機能の概要](batch-api-basics.md)」をご覧ください。
> 
> 

## <a name="azure-accounts-for-batch-development"></a>バッチの開発用の Azure アカウント
Batch ソリューションを開発するとき、Microsoft Azure で次のアカウントが必要になります。

* **Azure アカウントとサブスクリプション** - Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典][msdn_benefits]を有効にするか、[無料 Azure アカウント][free_account]にサインアップしてください。 アカウントを作成すると、既定のサブスクリプションが自動的に作成されます。
* **Batch アカウント** - Azure Batch リソース (プール、コンピューティング ノード、ジョブ、タスクなど) は、Azure Batch アカウントに関連付けられています。 アプリケーションは、Batch サービスに対する要求を行う際に、Azure Batch アカウント名、アカウントの URL、およびアクセス キーを使用して要求を認証します。 Azure Portal で [Batch アカウントを作成](batch-account-create-portal.md) できます。
* **ストレージ アカウント** - Batch には、[Azure Storage][azure_storage] のファイルを操作するためのサポートが組み込まれています。 ほぼすべての Batch シナリオで、Azure Blob Storage が使用されます。タスクで実行されるプログラムや、プログラムで処理されるデータのステージングに使用されたり、プログラムで生成される出力データの格納に使用されたりします。 ストレージ アカウントの作成方法については、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」を参照してください。

## <a name="batch-service-apis"></a>Batch サービス API

アプリケーションとサービスは、直接 REST APIを呼び出したり、以下の 1 つ以上のクライアント ライブラリを使用したりして、Azure Batch ワークロードを実行および管理することができます。

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル | 詳細情報 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |該当なし |- |- | [サポートされているバージョン](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[チュートリアル](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [リリース ノート](http://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[チュートリアル](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Readme](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [Readme](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[github.io][api_java] |[Maven][api_java_jar] |- |[Readme][api_sample_java] | [Readme](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Management API

Batch 用の Azure Resource Manager API には、Batch アカウントにプログラムでアクセスする機能が用意されています。 これらの API を使用すると、Batch アカウント、クォータ、およびアプリケーション パッケージをプログラムで管理できます。  

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |該当なし |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [チュートリアル](batch-management-dotnet.md) |[GitHub][api_sample_net] |

## <a name="batch-command-line-tools"></a>Batch コマンド ライン ツール

これらのコマンド ライン ツールには、Batch サービスや Batch Management API と同じ機能が備わっています。 

* [Batch PowerShell コマンドレット][batch_ps]: [Azure PowerShell](/powershell/azure/overview) モジュールの Azure Batch コマンドレットを利用すると、PowerShell で Batch リソースを管理できます。
* [Azure CLI](/cli/azure/overview): Azure コマンド ライン インターフェイス (Azure CLI) は、Batch サービスや Batch Management サービスなどの多くの Azure サービスを操作するためのシェル コマンドを提供するクロスプラットフォーム ツールセットです。 Batch での Azure CLI の使用について詳しくは、「[Azure CLI で Batch リソースを管理する](batch-cli-get-started.md)」を参照してください。

## <a name="other-tools-for-application-development"></a>その他のアプリケーション開発用ツール

ここでは、Batch アプリケーションや Batch サービスの構築とデバッグに役立つと思われるその他のツールをいくつか紹介します。

* [Azure Portal][portal]: Azure Portal の Batch ブレードでは、Batch プール、ジョブ、タスクを作成、監視、削除できます。 ジョブの実行時にこれらをはじめとする各種リソースの状態情報を確認できるほか、プールのコンピューティング ノードからファイルをダウンロードすることもできます。 たとえばトラブルシューティングの際に、失敗したタスクの `stderr.txt` をダウンロードすることができます。 コンピューティング ノードへのログインに使用できるリモート デスクトップ (RDP) ファイルをダウンロードすることもできます。
* [Azure Batch Explorer][batch_explorer]: Batch Explorer には Azure Portal と同様の Batch リソース管理機能がありますが、これはスタンドアロンの Windows Presentation Foundation (WPF) クライアント アプリケーションの形で提供されます。 [GitHub][github_samples] で提供されている Batch .NET サンプル アプリケーションの 1 つであり、Visual Studio 2015 以上でビルドできます。これを使うと、Batch ソリューションの開発とデバッグの際に、Batch アカウントのリソースを参照、管理できます。 ジョブ、プール、タスクの詳細を表示したり、コンピューティング ノードからファイルをダウンロードしたりできます。また、Batch Explorer を使ってダウンロードできるリモート デスクトップ (RDP) ファイルを利用してノードにリモート接続することもできます。
* [Microsoft Azure ストレージ エクスプローラー][storage_explorer]: 厳密には Azure Batch ツールではありませんが、ストレージ エクスプローラーは Batch ソリューションの開発とデバッグで役に立つツールです。

## <a name="additional-resources"></a>その他のリソース

- Batch アプリケーションからのイベントのログ記録について詳しくは、「[Batch ソリューションの診断の評価と監視のログ イベント](batch-diagnostics.md)」を参照してください。 Batch サービスで発生するイベントのリファレンスについては、「[一括分析](batch-analytics.md)」を参照してください。
- 計算ノードの環境変数については、「[Azure Batch コンピューティング ノードの環境変数](batch-compute-node-environment-variables.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Batch を使用するための準備を担当する方は、「 [開発者向け Batch 機能の概要](batch-api-basics.md)」で重要な情報をご確認ください。 この記事には、Batch アプリケーションを構築するときに使用できる多数の API 機能、プール、ノード、ジョブ、タスクなど、Batch サービスのリソースに関する詳しい情報が記載されています。
* [.NET 向け Azure Batch ライブラリの概要](batch-dotnet-get-started.md) 」では、C# と Batch .NET ライブラリを利用し、一般的な Batch ワークフローを使用して簡単なワークロードを実行する方法を学習できます。 この記事は、Batch サービスの使用方法を学習するときに最初に参考していただきたい記事の 1 つです。 また、 [Python 向け](batch-python-tutorial.md) のチュートリアルも用意されています。
* [GitHub のサンプル コード][github_samples]をダウンロードし、C# と Python の両方について、Batch とやり取りしてサンプル ワークロードのスケジュール設定と処理を実行する方法を確認してください。
* [Batch ラーニング パス][learning_path]で、Batch の使用方法を学習する際に利用できるリソースを確認してください。


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

