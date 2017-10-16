---
title: "Azure Machine Learning データ準備で使用可能なサポートされているデータ変換先/出力先 | Microsoft Docs"
description: "このドキュメントでは、Azure ML データ準備で使用可能なサポートされている変換先/出力先の完全な一覧を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-exports-for-this-preview"></a>このプレビューでサポートしているデータ エクスポート 
いくつかの異なる形式にエクスポートすることができます。 これらの形式を使用して、Machine Learning ワークフローの残りの部分に統合する前にデータ準備の中間結果を保持できます。

## <a name="types"></a>型 
### <a name="csv-file"></a>CSV ファイル 
コンマ区切り値ファイルを記憶域に書き込みます

#### <a name="options"></a>オプション
- 行の終わり
- null 値置換データ
- エラー置換データ 
- 区切り記号


### <a name="parquet"></a>Parquet ###
Parquet として記憶域にデータセットを書き込みます。

形式としての Parquet は、記憶域でさまざまなフォームを取ることができます。 小さなデータセットでは多くの場合、単一の ".parquet" ファイルが使用され、単一の ".parquet" ファイルの読み取り/書き込みは、さまざまな python ライブラリでサポートされています。 現時点では、AMLWB は、ローカルで "対話式" に使用している間は、Parquet の書き出しに PyArrow python ライブラリに依存します。 つまり、単一ファイルの parquet が現在、ローカルで対話式に使用しているときにサポートされる唯一の Parquet 出力形式になります。

AMLWB は、(Spark 上での) スケールアウト実行中、Spark の Parquet 読み取り/書き込み機能に依存します。 Spark の既定の Parquet 用出力形式 (現在、サポートされている唯一の形式) は、構造体が HIVE データセットに似ています。 これは、それぞれが大きなデータセットの小さなパーティションである ".parquet" ファイルを多数含んだフォルダーを意味します。 

#### <a name="caveats"></a>注意事項 ####
形式としての Parquet は、比較的歴史が浅く、さまざまなライブラリ間での実装の不整合が複数発生しています。 たとえば、Spark では、Parquet に書き込むときに列名に有効な文字を制限しますが、PyArrow では制限しません。 " ,;{}()\\n\\t=" のどの文字も列名では使用できません。

>[!NOTE]
>Spark との互換性を確保するため、Parquet にデータが書き込まれるときはいつでも、これらの文字は列名に出現するごとに "_" (アンダー スコア) に置換されます。**

>[!NOTE]
>ローカルおよびスケールアウトでの一貫性を得るため、アプリ、Python、または Spark を介して Parquet に書き込まれたすべてのデータは、その列名の不要な部分が削除されて Spark との互換性を確保します。 Parquet に書き込むときに予想どおりの列名を得るには、書き出す前に、Sparks で無効になる文字セットの文字を列から削除してください。



## <a name="locations"></a>場所 
### <a name="local"></a>ローカル 
ローカルのハード ドライブまたはマッピングされたネットワーク上の保存場所

### <a name="azure-blob"></a>Azure BLOB 
Azure Storage (BLOB)、Azure サブスクリプションが必要

