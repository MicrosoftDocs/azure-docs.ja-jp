---
title: Azure Data Lake Analytics の概要
description: Data Lake Analytics は、あらゆる規模のクラウド データで得た分析情報を使ってビジネスを強力に推し進める手立てとなります。
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: f2916b45c04aac3e36e8dfb82a6bb9b332f55286
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449154"
---
# <a name="what-is-azure-data-lake-analytics"></a>Azure Data Lake Analytics とは

Azure Data Lake Analytics は、ビッグ データを簡略化するオンデマンド分析ジョブ サービスです。 ハードウェアのデプロイ、構成、チューニングを行う代わりに、クエリを作成してデータを変換し、価値ある洞察を抽出します。 この分析サービスでは、必要な性能をダイヤルで設定して、どのような規模のジョブでも即座に処理できます。 ジョブの実行中にのみ課金されるコスト効率の良いサービスです。 

## <a name="azure-data-lake-analytics-recent-update-information"></a>Azure Data Lake Analytics の最近の更新情報

Azure Data Lake Analytics サービスは、特定の目的のために随時更新されます。 コンポーネントの更新プログラム、コンポーネントのベータ プレビューなどにより、このサービスのサポートを引き続き提供します。 

- 最新の更新プログラムの一般情報については、「[Data Lake Analytics の新機能](data-lake-analytics-whats-new.md)」を参照してください。
- 各更新プログラムの詳細については、[Azure Data Lake Analytics のリリース ノート](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes)に関するページを参照してください。

## <a name="dynamic-scaling"></a>動的スケーリング
  
Data Lake Analytics は、リソースが動的にプロビジョニングされるので、テラバイトからエクサバイト規模のデータも分析できます。 課金の対象となるのは、使用した処理能力の分だけです。 保存するデータのサイズや使用するコンピューティング リソースの量を増やしたり減らしたりする際に、コードを書き直す必要はありません。 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>使い慣れたツールで、開発を迅速に、デバッグと最適化をスマートに
  
Data Lake Analytics は、Visual Studio と密接に統合されています。 使い慣れたツールでコードを実行、デバッグ、チューニングすることができます。 U-SQL ジョブが視覚化されるので、コードがどのように大規模に実行されるのかを見ることができます。そのため、パフォーマンスのボトルネックを簡単に特定し、コストを最適化できます。

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: シンプルで使いやすく、強力で拡張が可能
  
Data Lake Analytics には U-SQL が含まれています。これは、SQL のシンプルで使いやすい宣言的な特性を C# の表現力で拡張するクエリ言語です。 Microsoft 社内のエクサバイト規模の Data Lake で活用されている分散ランタイムが、U-SQL 言語にも使用されています。 SQL や .NET の開発者が、既に身に付けているスキルでデータを処理して分析できるようになります。

## <a name="integrates-seamlessly-with-your-it-investments"></a>既存の IT 投資とシームレスに統合
  
Data Lake Analytics では、ID、管理、セキュリティに、IT への既存の投資が活かされます。 この方法により、データ ガバナンスがシンプルになり、現在のデータ アプリケーションも容易に拡張できます。 Data Lake Analytics はユーザー管理とアクセス許可のための Active Directory と統合されており、監視と監査も組み込まれています。

## <a name="affordable-and-cost-effective"></a>リーズナブルな料金と高いコスト効率

Data Lake Analytics は、ビッグ データ ワークロードを実行するためのコスト効率の良いソリューションです。 データが処理されたときに、ジョブ ベースで料金が発生します。 ハードウェア、ライセンス、サービス固有のサポート契約は必要ありません。 ジョブの開始や完了に応じて、システムのスケールアップとスケールダウンが自動的に行われるため、必要以上に課金されることはありません。 詳細については、[コストの管理と節約](https://aka.ms/adlasavemoney)に関するページを参照してください。

## <a name="works-with-all-your-azure-data"></a>すべての Azure データに対応
  
Data Lake Analytics は、最高レベルのパフォーマンス、スループット、および並列化のために Azure Data Lake Storage と連携し、Azure Storage Blob、Azure SQL Database、Azure Synapse Analytics とも連動します。

## <a name="in-region-data-residency"></a>リージョンのデータ所在地
  
Data Lake Analytics によって、顧客データがデプロイされているリージョン外に移動されたり格納されたりすることはありません。


## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Analytics の新機能](data-lake-analytics-whats-new.md)に関するページで、Azure Data Lake Analytics の最新の更新プログラムを確認する
* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md) で Data Lake Analytics の使用を開始する
* [Azure portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md) でAzure Data Lake Analytics を管理する
* [Data Lake Analytics に関するコストを管理して節約する方法](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
