---
title: API と開発者向けツール - Azure Batch | Microsoft Docs
description: Azure Batch サービスを使用したソリューションの開発に利用できる API とツールについて説明します。
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 912e3342b4e8031b4404dffb56f1add2cc705f8e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772587"
---
# <a name="overview-of-batch-apis-and-tools"></a>Batch API とツールの概要

Azure Batch による並列ワークロードの処理は、通常、Batch API のいずれかを使用して、プログラムで実行されます。 クライアント アプリケーションまたはサービスは、Batch API を使用して Batch サービスと通信することができます。 Batch API を使用すると、コンピューティング ノード (仮想マシンまたはクラウド サービス) のプールを作成して管理できます。 その後、それらのノードで実行するジョブおよびタスクをスケジュールできます。 

組織の大規模なワークロードを効率的に処理したり、ノードの数を問わず (1 つ、数百、数千など)、必要なときに、またはスケジュールに基づいてジョブやタスクを実行できるように、顧客にサービス フロントエンドを提供したりできます。 また、Azure Batch を大規模なワークフローの一部として使用し、[Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json) などのツールで管理できます。

> [!TIP]
> Batch API の機能をもっと詳しく理解するには、「 [開発者向け Batch 機能の概要](batch-api-basics.md)」をご覧ください。
> 
> 

## <a name="azure-accounts-for-batch-development"></a>バッチの開発用の Azure アカウント
Batch ソリューションを開発するとき、ご自身の Azure サブスクリプションで次のアカウントを使用します。

* **Batch アカウント** - Azure Batch リソース (プール、コンピューティング ノード、ジョブ、タスクなど) は、Azure [Batch アカウント](batch-api-basics.md#account)に関連付けられています。 アプリケーションは、Batch サービスに対する要求を行う際に、Azure Batch アカウント名、アカウントの URL、およびアクセス キーまたは Azure Active Directory トークンを使用して要求を認証します。 Azure Portal またはプログラムで [Batch アカウントを作成](batch-account-create-portal.md)できます。
* **ストレージ アカウント** - Batch には、[Azure Storage][azure_storage] のファイルを操作するためのサポートが組み込まれています。 ほぼすべての Batch シナリオで、Azure Blob Storage が使用されます。タスクで実行されるプログラムや、プログラムで処理されるデータのステージングに使用されたり、プログラムで生成される出力データの格納に使用されたりします。 Batch のストレージ アカウント オプションについては、[Batch 機能の概要](batch-api-basics.md#azure-storage-account)に関するページをご覧ください。

## <a name="batch-service-apis"></a>Batch サービス API

アプリケーションとサービスは、直接 REST APIを呼び出したり、以下の 1 つ以上のクライアント ライブラリを使用したりして、Azure Batch ワークロードを実行および管理することができます。

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル | 詳細情報 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |該当なし |- |- | [サポートされているバージョン](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[チュートリアル](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [リリース ノート](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[チュートリアル](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Readme](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[チュートリアル](batch-nodejs-get-started.md) |- | [Readme](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Readme][api_sample_java] | [Readme](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Management API

Batch 用の Azure Resource Manager API には、Batch アカウントにプログラムでアクセスする機能が用意されています。 これらの API を使用すると、プログラムから Microsoft.Batch プロバイダーを介して、Batch アカウント、クォータ、アプリケーション パッケージなどのリソースを管理できます。  

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[docs.microsoft.com][api_rest_mgmt] |該当なし |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [チュートリアル](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Batch Management Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Batch Management Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Batch Management Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Batch コマンド ライン ツール

これらのコマンド ライン ツールには、Batch サービスや Batch Management API と同じ機能が備わっています。 

* [Batch PowerShell コマンドレット][batch_ps]: [Azure PowerShell](/powershell/azure/overview) モジュールの Azure Batch コマンドレットを使用すると、PowerShell で Batch リソースを管理できます。
* [Azure CLI](/cli/azure):Azure CLI は、Batch サービスや Batch Management サービスなどの多くの Azure サービスを操作するためのシェル コマンドを提供するクロスプラットフォーム ツールセットです。 Batch での Azure CLI の使用について詳しくは、「[Azure CLI で Batch リソースを管理する](batch-cli-get-started.md)」を参照してください。

## <a name="other-tools-for-application-development"></a>その他のアプリケーション開発用ツール

ここでは、Batch アプリケーションや Batch サービスの構築とデバッグに役立つと思われるその他のツールをいくつか紹介します。

* [Azure portal][portal]: Azure portal では、Batch プール、ジョブ、タスクを作成、監視、削除できます。 ジョブの実行時にこれらをはじめとする各種リソースの状態情報を確認できるほか、プールのコンピューティング ノードからファイルをダウンロードすることもできます。 たとえばトラブルシューティングの際に、失敗したタスクの `stderr.txt` をダウンロードすることができます。 コンピューティング ノードへのログインに使用できるリモート デスクトップ (RDP) ファイルをダウンロードすることもできます。
* [Azure Batch Explorer][batch_labs]: Batch Explorer (旧称: BatchLabs) は、Azure Batch アプリケーションの作成、デバッグ、および監視を支援する、豊富な機能を備えた無料のスタンドアロン クライアント ツールです。 Mac、Linux、または Windows 用の[インストール パッケージ](https://azure.github.io/BatchExplorer/)をダウンロードしてください。
* [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard): Batch Shipyard は、コンテナーベースのバッチ処理と HPC ワークロードを Azure Batch に対してプロビジョニング、実行、監視できるようにするツールです。
* [Azure Storage Explorer][storage_explorer]: 厳密には Azure Batch ツールではありませんが、ストレージ エクスプローラーは Batch ソリューションの開発とデバッグで役に立つツールです。

## <a name="additional-resources"></a>その他のリソース

- Batch アプリケーションからのイベントのログ記録について詳しくは、「[Batch ソリューションの診断の評価と監視のログ イベント](batch-diagnostics.md)」を参照してください。 Batch サービスで発生するイベントのリファレンスについては、「[一括分析](batch-analytics.md)」を参照してください。
- 計算ノードの環境変数については、「[Azure Batch コンピューティング ノードの環境変数](batch-compute-node-environment-variables.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* Batch を使用するための準備を担当する方は、「 [開発者向け Batch 機能の概要](batch-api-basics.md)」で重要な情報をご確認ください。 この記事には、Batch アプリケーションを構築するときに使用できる多数の API 機能、プール、ノード、ジョブ、タスクなど、Batch サービスのリソースに関する詳しい情報が記載されています。
* [.NET 向け Azure Batch ライブラリの概要](tutorial-parallel-dotnet.md) 」では、C# と Batch .NET ライブラリを利用し、一般的な Batch ワークフローを使用して簡単なワークロードを実行する方法を学習できます。 [Python バージョン](tutorial-parallel-python.md)と [Node.js のチュートリアル](batch-nodejs-get-started.md)も用意されています。
* [GitHub のサンプル コード][github_samples]をダウンロードし、C# と Python の両方について、Batch とやり取りしてサンプル ワークロードのスケジュール設定と処理を実行する方法を確認してください。

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
