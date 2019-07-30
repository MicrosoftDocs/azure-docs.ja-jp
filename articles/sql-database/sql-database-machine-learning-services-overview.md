---
title: Azure SQL Database Machine Learning Services と R (プレビュー) の概要
description: この記事では、Azure SQL Database の Machine Learning Services と R およびそのしくみについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427937"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services と R (プレビュー)

Machine Learning Services は、データベース内の R スクリプトを実行するために使用される、Azure SQL Database の機能です。 その機能には、高パフォーマンスの予測分析と機械学習のための Microsoft R パッケージが含まれています。 ストアド プロシージャ、R ステートメントを含む T-SQL、または T-SQL を含む R コードにより、R スクリプトでリレーショナル データを使用できます。

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services と R は、現在パブリック プレビュー期間です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> パブリック プレビューは、**General Purpose** および **Business Critical** サービス レベルでの仮想コアベースの購入モデルを使用した、単一データベースとエラスティック プールを対象に提供されます。 この初回パブリック プレビューでは、**ハイパースケール** サービス レベルと**マネージド インスタンス** デプロイ オプションは、サポートされていません。 現在、サポートされている言語は R だけです。 現時点では、Python はサポートされていません。
>
> プレビューは現在、次のリージョンでご利用いただけます:西ヨーロッパ、北ヨーロッパ、米国西部 2、米国東部、米国中南部、米国中北部、カナダ中部、東南アジア、インド南部、およびオーストラリア南東部。
>
> 後で示す方法で[プレビューにサインアップしてください](#signup)。

## <a name="what-you-can-do-with-r"></a>R でできること

R 言語の機能を使用して、データベース内で高度な分析と機械学習を提供します。 この機能を使用すると、データが存在する場所で計算と処理を行うことができ、ネットワーク経由でデータをプルする必要がありません。 また、エンタープライズ R パッケージの機能を利用して、大規模で高度な分析を提供できます。

Machine Learning Services には、R の基本ディストリビューションに、Microsoft 提供のエンタープライズ R パッケージがオーバーレイされたものが含まれます。 Microsoft の R 関数とアルゴリズムはスケールと実用性の両方を考慮して設計されており、予測分析、統計モデリング、データの視覚化、および最先端の機械学習アルゴリズムが提供されます。

### <a name="r-packages"></a>R パッケージ

最も一般的なオープン ソースの R パッケージが、Machine Learning Services にプレインストールされています。 Microsoft が提供する次の R パッケージも含まれます。

| R パッケージ | 説明|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open は、Microsoft が提供する R の強化されたディストリビューションです。 統計分析とデータ サイエンスのための完全なオープン ソース プラットフォームです。 R が基になっていて、R との間に 100% の互換性があり、パフォーマンスと再現性が向上する追加機能が含まれています。 |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR は、スケーラブルな R のためのプライマリ ライブラリです。このライブラリの関数は最も広く使用されています。 データの変換と操作、統計の要約、視覚化、およびモデリングと分析の多くのフォームが、これらのライブラリに収められています。 さらに、これらのライブラリの関数は、並列処理のためにワークロードを使用可能なコア間に自動的に分散させ、計算エンジンによって調整および管理されるデータのチャンクを処理する機能を備えています。 |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML では、テキスト分析、画像分析、およびセンチメント分析用のカスタム モデルを作成するための機械学習アルゴリズムが追加されます。 |

プレインストールされたパッケージだけでなく、[追加パッケージをインストールする](sql-database-machine-learning-services-add-r-packages.md)ことができます。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>プレビューのサインアップ

パブリック プレビューにサインアップするには、以下の手順のようにします。

1. Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/)してください。

2. パブリック プレビューへのサインアップを希望される場合は、Microsoft ([sqldbml@microsoft.com](mailto:sqldbml@microsoft.com)) にメールをお送りください。 SQL Database における Machine Learning Services のパブリック プレビューと R は、既定では無効になっています。

プログラムへの登録が完了すると、Microsoft がパブリック プレビューへのお客様のオンボードを行い、既存または新規のデータベースに R を使用できるようになります。

パブリック プレビューの期間中は、運用環境のワークロードに Machine Learning Services と R を使用することはお勧めしません。

## <a name="next-steps"></a>次の手順

- [SQL Server Machine Learning Services との重要な違い](sql-database-machine-learning-services-differences.md)に関する記事を参照してください。
- Azure SQL Database Machine Learning Services (プレビュー) を R で照会する方法について、「[クイック スタート ガイド](sql-database-connect-query-r.md)」を参照してください。
- シンプルな R スクリプトから始めるには、「[Azure SQL Database Machine Learning Services (プレビュー) で簡単な R スクリプトを作成して実行する](sql-database-quickstart-r-create-script.md)」を参照してください。
