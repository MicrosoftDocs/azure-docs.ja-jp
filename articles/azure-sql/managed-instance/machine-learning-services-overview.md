---
title: Azure SQL Managed Instance の Machine Learning Services (プレビュー)
description: この記事では、Azure SQL Managed Instance の概要または Machine Learning Services について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: ad25a9a62757e1e031ce8b93a44e6f2ada4d9964
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689509"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Azure SQL Managed Instance の Machine Learning Services (プレビュー)

Machine Learning Services は Azure Python Managed Instance (プレビュー) の機能であり、データベース内の機械学習を提供し、Python スクリプトと R スクリプトの両方をサポートしています。 その機能には、高パフォーマンスの予測分析と機械学習のための Microsoft Python および R のパッケージが含まれています。 ストアド プロシージャ、Python または R ステートメントを含む T-SQL、または T-SQL を含む Python または R コードにより、スクリプトでリレーショナル データを使用できます。

> [!IMPORTANT]
> Machine Learning Services は、現在パブリック プレビュー段階にある Azure SQL Managed Instance の機能です。
> このプレビュー機能は、初期段階では米国、アジア、ヨーロッパ、オーストラリアの限られた数のリージョンでのみ利用可能であり、今後、リージョンが追加される予定です。
>
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> 後で示す方法で[プレビューにサインアップしてください](#signup)。

## <a name="what-is-machine-learning-services"></a>Machine Learning Services とは

Azure SQL Managed Instance の Machine Learning Services を使用すると、Python および R スクリプトをデータベース内で実行できます。 この機能を使用して、データの準備とクリーンアップ、特徴エンジニアリング、およびデータベース内での機械学習モデルのトレーニング、評価、およびデプロイを行うことができます。 この機能により、データが存在する場所でスクリプトが実行され、ネットワークを介して別のサーバーにデータが転送されなくなります。

Azure SQL Managed Instance で R および Python をサポートする Machine Learning Services を使用すると、次のことを行うことができます。

- **R および Python スクリプトを実行してデータ準備と汎用データ処理を行う** - 自作の R および Python スクリプトをデータが存在する Azure SQL Managed Instance に取り込むことができるようになりました。R および Python スクリプトを実行するためにデータを他のサーバーに移動する必要はありません。 データ移動の必要性と、遅延、セキュリティ、コンプライアンスに関連する関連問題を排除できます。

- **データベースで機械学習モデルをトレーニングする** - 任意のオープン ソース アルゴリズムを使用してモデルをトレーニングできます。 データベースから引き出されたサンプル データセットに依存するのではなく、トレーニングをデータセット全体に簡単にスケーリングできます。

- **モデルとスクリプトをストアド プロシージャの運用環境にデプロイする** - スクリプトとトレーニング済みモデルは、T-SQL ストアド プロシージャに埋め込むだけで運用できます。 Azure SQL Managed Instance に接続するアプリでは、ストアド プロシージャを呼び出すだけで、このようなモデルの予測とインテリジェンスを活用できます。 また、ネイティブの T-SQL PREDICT 関数を使用して、モデルを操作可能にして、高度な並行リアルタイム スコアリング シナリオで高速スコアリングを行うこともできます。

Python と R のベース ディストリビューションは Machine Learning Services に含まれています。 Python 用の Microsoft パッケージ [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) および [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) と、R 用の [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler)、[MicrosoftML](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml)、[olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr)、および [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) に加え、PyTorch、TensorFlow、scikit-learn などのオープンソースのパッケージとフレームワークをインストールおよび使用できます。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>プレビューのサインアップ

この限定パブリック プレビューには、[Azure プレビューの使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)が適用されます。 

プレビュー プログラムへの参加に関心をお持ちで、その使用条件に同意する場合は、[ **https://azure.microsoft.com/support/create-ticket/** ](https://azure.microsoft.com/support/create-ticket/) で Azure サポート チケットを作成して登録を依頼できます。 

1. 次のオプションを選択します。
   - 問題の種類 - **技術**
   - サブスクリプション - *ご使用のサブスクリプションを選択します*
   - サービス - **SQL Database Managed Instance**
   - 概要 - *要求の簡単な説明を入力します*
   - 問題の種類 - **SQL Managed Instance 向けの Machine Learning Services (プレビュー)**
   - 問題のサブタイプ - **その他の問題または "操作方法" に関する質問**

1. **次へ: 「解決方法」** を参照してください。

1. プレビューに関する情報を読み、次に **[詳細]** をクリックします。

1. **[説明]** には、プレビューで登録する、論理的サーバー名、リージョン、サブスクリプション ID など、要求の詳細を入力します。 その他の詳細を適宜入力します。

1. 完了したら、 **[次へ: 確認および作成]** をクリックし、 **[作成]** をクリックします。

プログラムへの登録が完了すると、Microsoft がパブリック プレビューへのお客様のオンボードを行い、既存または新規のデータベースに Machine Learning Services を使用できるようになります。

パブリック プレビュー段階では、運用環境のワークロードに SQL Managed Instance の Machine Learning Services は推奨されません。

## <a name="next-steps"></a>次のステップ

- [SQL Server Machine Learning Services との重要な違い](machine-learning-services-differences.md)に関する記事を参照してください。
- Machine Learning Services で Python を使用する方法については、[Python スクリプトの実行](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)に関するページを参照してください。
- Machine Learning Services で R を使用する方法については、[R スクリプトの実行](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)に関するページを参照してください。
- 他の SQL プラットフォームの機械学習に関する詳細については、[SQL 機械学習のドキュメント](https://docs.microsoft.com/sql/machine-learning/)を参照してください。
