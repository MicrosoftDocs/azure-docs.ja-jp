---
title: "Azure Machine Learning に関してよく寄せられる質問 (FAQ) | Microsoft Docs"
description: "Azure Machine Learning の概要: 効率的な予測モデリングのためのクラウド サービスについて、その課金、機能、制限についての疑問点に答える FAQ です。"
keywords: "機械の概要、予測モデリング、機械学習とは"
services: machine-learning
documentationcenter: 
author: garyericson
manager: paulettm
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 21d2c20a3ac8dc4fee0680028d6c004c80a3ae38
ms.openlocfilehash: 2835e4e8be2a11b659118f836d53c84a2c021263
ms.lasthandoff: 03/02/2017


---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Azure Machine Learning に関してよく寄せられる質問: 課金、機能、制限、サポート
ここでは、Web サービスを使用して予測モデルの作成とソリューションの運用を可能にするクラウド サービスである Azure Machine Learning についてよく寄せられる質問とその回答をいくつか紹介します。 この FAQ では、課金モデル、機能、制限、サポートなど、サービスの使用方法に関する質問を取り上げています。

## <a name="general-questions"></a>一般的な質問
**Azure Machine Learning とは**

Azure Machine Learning は、クラウドで予測分析ソリューションを作成、テスト、操作、管理するための、十分に管理されたサービスです。 ブラウザーさえあれば、サインイン、データのアップロード、機械学習の実験をすぐに始めることができます。 ドラッグ アンド ドロップによる予測モデリング、モジュールの大きなパレット、開始用テンプレートのライブラリによって、一般的な機械学習のタスクがシンプルになり、作業時間が短縮されます。 詳細については、「 [Azure Machine Learning サービスの概要](https://azure.microsoft.com/services/machine-learning/)」に関するページをご覧ください。 機械学習の重要な用語と概念の説明については、[Azure Machine Learning の概要](machine-learning-what-is-machine-learning.md)に関するページをご覧ください。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Machine Learning Studio とは**

Machine Learning Studio は、Web ブラウザーを使用してアクセスするワークベンチ環境です。 Machine Learning Studio は、視覚的な構成インターフェイスにモジュールのパレットを備えており、実験の形式でエンド ツー エンドのデータサイエンス ワークフローを容易に構築できます。

Machine Learning Studio の詳細については、「 [Machine Learning Studio とは](machine-learning-what-is-ml-studio.md)

**Machine Learning API サービスとは**

Machine Learning API サービスを使用すると、Machine Learning Studio に組み込まれているものと同様の予測モデルを、スケーラブルでフォールト トレランスに優れた Web サービスとしてデプロイできます。 Machine Learning API サービスによって作成される Web サービスは、外部アプリケーションと予測分析モデルの間の通信用インターフェイスを提供する REST API として機能します。

詳細については、「 [Machine Learning Web サービスへの接続](machine-learning-connect-to-azure-machine-learning-web-service.md)」をご覧ください。

**クラシック Web サービスの一覧はどこで確認できますか?新しい Azure Resource Manager ベースの Web サービスの一覧はどこで確認できますか?**

クラシック デプロイメント モデルを使用して作成された Web サービスと新しい Azure Resource Manager デプロイメント モデルを使用して作成された Web サービスは、[Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/) ポータルに一覧表示されます。

クラシック Web サービスは、[Machine Learning Studio](http://studio.azureml.net) の **[Web サービス]** タブにも一覧表示されます。

## <a name="azure-machine-learning-questions"></a>Azure Machine Learning に関する質問
**Azure Machine Learning Web サービスとは何ですか?**

Machine Learning Web サービスは、アプリケーションと Machine Learning ワークフローのスコア付けモデルの間のインターフェイスを提供します。 外部のアプリケーションは Azure Machine Learning を使用して Machine Learning ワークフローのスコア付けモデルとリアルタイムで通信できます。 Machine Learning Web サービスを呼び出すと、予測結果が外部のアプリケーションに返されます。 Web サービスの呼び出しを実行するには、Web サービスのデプロイ時に作成された API キーを渡します。 Machine Learning Web サービスは、Web プログラミング プロジェクトで広く使われているアーキテクチャの REST に基づいています。

Azure Machine Learning には、2 種類の Web サービスがあります。

* 要求応答サービス (RRS) - 待ち時間が短くスケーラブルなサービス。Machine Learning Studio を使用して作成およびデプロイされたステートレスなモデルへのインターフェイスを提供します。
* バッチ実行サービス (BES) - データ レコードのバッチをスコア付けする非同期のサービス。

REST API を使用して Web サービスにアクセスするには、いくつかの方法があります。 たとえば、Web サービスをデプロイしたときに生成されたサンプル コードを使用して、C#、R、または Python でアプリケーションを記述できます。

サンプル コードは以下のページで入手できます。
- Azure Machine Learning Web サービス ポータルの Web サービスの使用に関するページ
- Machine Learning Studio の Web サービス ダッシュボードの API ヘルプ ページ

作成済みのサンプル Microsoft Excel ブックを使用することもできます。これも Machine Learning Studio の Web サービス ダッシュボードで入手できます。

**Azure Machine Learning の主な更新内容を教えてください。**

最新の更新内容については、「[Azure Machine Learning の新機能](machine-learning-whats-new.md)」をご覧ください。

## <a name="machine-learning-studio-questions"></a>Machine Learning Studio に関する質問
### <a name="import-and-export-data-for-machine-learning"></a>Machine Learning 用データのインポートとエクスポート
**Machine Learning はどのようなデータ ソースをサポートしていますか。**

Machine Learning Studio の実験にデータをダウンロードする方法は&3; つあります。

- ローカル ファイルをデータセットとしてアップロードする
- モジュールを使用して、クラウド データ サービスからデータをインポートする
- 別の実験から保存されたデータセットをインポートする

サポートされているファイル形式の詳細については、[Machine Learning Studio へのトレーニング データのインポート](machine-learning-data-science-import-data.md)に関する記事を参照してください。

#### <a id="ModuleLimit"></a>モジュールのデータ セットの大きさはどの程度まで許容されますか。
Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。 モジュールが 1 つ以上の入力を受け取る場合、10 GB という値はすべての入力サイズの合計です。 Hive または Azure SQL Database のクエリを使用して、さらに大きなデータセットをサンプリングしたり、インジェストの前に、前処理としてカウントによる学習を使用したりすることもできます。  

次の種類のデータは、特徴の正規化の際に、より大きなデータセットに展開できますが、10 GB 未満に制限されています。

* スパース
* カテゴリ
* 文字列
* バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

* 推奨モジュール
* Synthetic Minority Oversampling Technique (SMOTE) モジュール
* スクリプト モジュール: R、Python、SQL
* 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)
* イテレーションの数が非常に大きい場合のクロス検証、調整モデル ハイパーパラメーター、順序回帰、一対全多クラス

#### <a id="UploadLimit"></a>データのアップロードの制限は何ですか。
数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、Azure HDInsight を使用する必要があります。

**Amazon S3 からデータを読み取ることはできますか。**

少量のデータがあり、HTTP URL 経由でこれを公開する場合は、[データのインポート][import-data] モジュールを使用できます。 大量のデータの場合は、まず Azure Storage に転送してから、[データのインポート][import-data] モジュールを使用して実験に取り込みます。
<!--

<SEE CLOUD DS PROCESS>
-->

**組み込みイメージの入力機能はありますか。**

イメージの入力機能については、「[Import Images (イメージのインポート)][image-reader]」を参照してください。

### <a name="modules"></a>モジュール
**探しているアルゴリズム、データ ソース、データ形式、データ変換操作が Azure Machine Learning Studio にありません。どうすればいいでしょうか?**

[ユーザー フィードバック フォーラム](http://go.microsoft.com/fwlink/?LinkId=404231)にアクセスして、機能に関する追跡中の要望をご覧いただけます。 探している機能が既に要求されている場合は、その要求に投票を追加できます。 探している機能が存在しない場合は、新しい要求を作成してください。 作成した要求の状態もフォーラムで確認できます。 この一覧はこまめに追跡され、機能の提供状況は頻繁に更新されます。 また、R と Python に対する組み込みのサポートを使用すれば、必要に応じてカスタム変換を作成できます。

**既存のコードを Machine Learning Studio に取り込むことはできますか?**

はい。Machine Learning Studio に既存の R コードまたは Python コードを取り込み、それを Azure Machine Learning の学習ツールと同じ実験で実行し、Azure Machine Learning 経由で Web サービスとしてソリューションをデプロイすることができます。 詳細については、「[R を使用した実験の拡張](machine-learning-extend-your-experiment-with-r.md)」と「[Azure Machine Learning Studio での Python Machine Learning スクリプトの実行](machine-learning-execute-python-scripts.md)」を参照してください。

**[PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) などを使用してモデルを定義することはできますか。**

いいえ、PMML (Predictive Model Markup Language) はサポートされていません。 ただし、カスタムの R と Python コードを使用してモジュールを定義できます。

**実験でいくつのモジュールを並列実行できますか。**  

実験では最大&4; つのモジュールを並列実行できます。

### <a name="data-processing"></a>データ処理
**実験内で対話的にデータを視覚化する (R の視覚化以上) 機能はありますか。**

モジュールの出力をクリックすると、データを視覚化し、統計情報を取得できます。

**ブラウザーで結果やデータをプレビューする際に行数と列数が制限されています。なぜですか?**

大量のデータがブラウザーに送信される可能性があるため、Machine Learning Studio のパフォーマンスが低下しないように、データ サイズが制限されています。 すべてのデータ/結果を表示するには、データをダウンロードし、Excel などのツールを使用することをお勧めします。

### <a name="algorithms"></a>アルゴリズム
**Machine Learning Studio では既存のどの ML アルゴリズムがサポートされていますか。**

Machine Learning Studio は、拡張性の高い強化された意思決定ツリー、ベイズの推薦システム、ディープ ニューラル ネットワーク、Microsoft Research で開発された意思決定のジャングルなど、最先端のアルゴリズムを提供します。 Vowpal Wabbit のようなスケーラブルなオープン ソース機械学習パッケージも含まれます。 Machine Learning Studio は、複数クラスと二項分類、回帰、クラスタリングで、機械学習アルゴリズムをサポートします。 「[Machine Learning Modules (Machine Learning のモジュール)][machine-learning-modules]」の完全な一覧をご覧ください。

**データを使用するための正しい Machine Learning アルゴリズムを自動的に提案していますか。**

いいえ。ただし、Machine Learning Studio には、各アルゴリズムの結果を比較して問題に合ったアルゴリズムを決定できるさまざまな方法があります。

**提供されているアルゴリズムから&1; つのアルゴリズムを選ぶ際のガイドラインはありますか。**

[アルゴリズムの選択方法](machine-learning-algorithm-choice.md)」に関するページをご覧ください。

**提供されているアルゴリズムは、R または Python で記述されていますか。**

いいえ。これらのアルゴリズムは、多くの場合、より優れたパフォーマンスを提供するためにコンパイル済みの言語で記述されています。

**アルゴリズムの詳細は提供されていますか。**

ドキュメントでアルゴリズムに関する情報を提供しています。使用するアルゴリズムを最適化するために、チューニング用のパラメーターについて説明しています。  

**オンライン ラーニングの任意のサポートはありますか。**

いいえ。現在はプログラムによる再トレーニングのみがサポートされています。

**組み込みのモジュールを使用してニューラル ネットワーク モデルのレイヤーを視覚化できますか。**

いいえ。

**C# または他の言語で独自のモジュールを作成できますか。**

現在、新しいカスタム モジュールは R でのみ作成できます。

### <a name="r-module"></a>R モジュール
**Machine Learning Studio で使用できるのはどの R パッケージですか。**

Machine Learning Studio では現在、400 を超える CRAN R パッケージがサポートされています。対象となるすべてのパッケージが記載された最新の一覧については、[こちら](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)を参照してください。 また、この一覧を自身で取得する方法については、「[R を使用した実験の拡張](machine-learning-extend-your-experiment-with-r.md)」を参照してください。 必要なパッケージがこの一覧にない場合は、[ユーザー フィードバック フォーラム](http://go.microsoft.com/fwlink/?LinkId=404231)でパッケージの名前を指定してください。

**カスタム R モジュールを構築することはできますか。**

はい。詳細については、「[Azure Machine Learning でカスタム R モジュールを作成する](machine-learning-custom-r-modules.md)」をご覧ください。

**R 用の REPL 環境はありますか。**

いいえ。Studio には R 用の REPL (Read-Eval-Print-Loop) 環境はありません。

### <a name="python-module"></a>Python モジュール
**カスタム Python モジュールを構築することはできますか。**

現在はできません。ただし、1 つまたは複数の [Python スクリプトの実行][python]モジュールを使用して、同じ結果を得ることができます。

**Python 用の REPL 環境はありますか。**

Machine Learning Studio では Jupyter Notebooks を使用できます。 詳細については、 [Azure Machine Learning Studio への Jupyter Notebooks の導入](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)に関するブログ記事を参照してください。

## <a name="web-service"></a>Web サービス
### <a name="retrain"></a>再トレーニング
**プログラムで Machine Learning のモデルを再トレーニングするにはどうすればよいですか?**

再トレーニング API を使用してください。 詳細については、「 [プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」をご覧ください。 サンプル コードは、「 [Microsoft Azure Maching Learning Retraining Demo (Microsoft Azure Maching Learning 再トレーニング デモ)](https://azuremlretrain.codeplex.com/)」にもあります。

### <a name="create"></a>作成
**ローカルまたはインターネットに接続していないアプリケーションでモデルをデプロイすることはできますか?**

いいえ。

**すべての Web サービスで予測される基本的な遅延はありますか。**

[Azure サブスクリプションの制限](../azure-subscription-service-limits.md)に関するページをご覧ください。

### <a name="use"></a>最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、
**バッチ実行サービスと要求応答サービスとして予測モデルを実行するのはいつがいいですか。**

要求応答サービス (RRS) は、待ち時間が短く、拡張性の高い Web サービスで、実験環境で作成、デプロイされたステートレスなモデルへのインターフェイスを提供するために使用されます。 バッチ実行サービス (BES) は、データ レコードのバッチに対して非同期でスコアを付けるサービスです。 BES への入力は、RRS で使用されるデータ入力と似ています。 主な違いは、BES が、Azure Blob Storage、Azure Table Storage、Azure SQL Database、HDInsight (Hive Query)、HTTP ソースなど、さまざまなソースからレコードのブロックを読み取る点です。 詳細については、「 [How to consume Machine Learning web services (Machine Learning Web サービスを使用する方法)](machine-learning-consume-web-services.md)」をご覧ください。

**Web サービスにデプロイされたモデルはどのように更新できますか。**

既にデプロイされたサービスの予測モデルを更新するには、トレーニング済みのモデルの作成および保存に使用した実験を変更し、再実行します。 新しいバージョンのトレーニング済みモデルが使用できるようになると、Machine Learning Studio は Web サービスを更新するかどうかをたずねます。 デプロイされた Web サービスを更新する方法の詳細については、[Machine Learning Web サービスのデプロイ](machine-learning-publish-a-machine-learning-web-service.md)に関するページをご覧ください。

Retraining API を使用することもできます。
詳細については、「 [プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」をご覧ください。 サンプル コードは、「 [Microsoft Azure Maching Learning Retraining Demo (Microsoft Azure Maching Learning 再トレーニング デモ)](https://azuremlretrain.codeplex.com/)」にもあります。

**運用環境にデプロイされた Web サービスはどのように監視できますか?**

予測モデルは、デプロイ後、Azure クラシック ポータル (クラシック Web サービスのみ) または Azure Machine Learning Web サービス ポータルから監視できます。 デプロイされた各サービスには専用のダッシュボードがあり、ここでそのサービスについての監視情報を参照できます。 デプロイされた Web サービスの管理方法の詳細については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](machine-learning-manage-new-webservice.md)」および「[Azure Machine Learning ワークスペースの管理](machine-learning-manage-workspace.md)」を参照してください。

**RRS/BES の出力を確認できまる場所はありますか。**

RRS の場合は通常、Web サービスの応答に結果が表示されます。 また、Azure Blob Storage に書き込むこともできます。 BES の場合、既定では BLOB に出力が書き込まれます。 また、[データのエクスポート][export-data] モジュールを使用してデータベースやテーブルに出力を書き込むこともできます。

**Web サービスを作成できるのは Machine Learning Studio で作成されたモデルからのみですか?**

いいえ。Jupyter Notebooks と RStudio から直接 Web サービスを作成することもできます。

**エラー コードの詳細はどこで入手できますか?**

エラー コードと説明の一覧は、「 [Machine Learning Module Error Codes (Machine Learning モジュールのエラー コード)](https://msdn.microsoft.com/library/azure/dn905910.aspx) 」を参照してください。

## <a name="scalability"></a>拡張性
**Web サービスのスケーラビリティはどれくらいですか。**

現時点では、既定のエンドポイントは 1 つのエンドポイントにつき 20 件の同時 RRS 要求でプロビジョニングされます。 これは、[Web サービスのスケーリング](machine-learning-scaling-webservice.md)に関するページで説明されているように、エンドポイントあたりの同時要求を 200 件まで拡張でき、Web サービスごとのエンドポイント数は 10,000 個まで拡張できます。 BES の場合、各エンドポイントは一度に 40 件の要求を処理でき、40 件を超えた要求はキューに登録されます。 これらのキューに登録された要求は、キューから放出されると自動的に実行されます。

**R のジョブはノード間に分散されますか。**

いいえ。  

**トレーニングに使用できるデータの量はどれくらいですか?**

Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。 モジュールが 1 つ以上の入力を受け取る場合、10 GB はすべての入力の合計です。 インジェストの前に、Hive クエリ、Azure SQL Database クエリ、または[カウントを使用した学習][counts]モジュールによる前処理を使用して、さらに大きなデータセットをサンプリングすることもできます。  

次の種類のデータは、特徴の正規化の際に、より大きなデータセットに展開できますが、10 GB 未満に制限されています。

* スパース
* カテゴリ
* 文字列
* バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

* 推奨モジュール
* Synthetic Minority Oversampling Technique (SMOTE) モジュール
* スクリプト モジュール: R、Python、SQL
* 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)
* イテレーションの数が非常に大きい場合のクロス検証、調整モデル ハイパーパラメーター、順序回帰、一対全多クラス

数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用してください。

**ベクターのサイズに制限はありますか。**

行と列は、それぞれ .NET 制限の相互作用数の上限 2,147,483,647 に制限されています。

**Web サービスを実行する仮想マシンのサイズは調整できますか?**

いいえ。  

## <a name="security-and-availability"></a>セキュリティと可用性
**既定では Web サービスの http エンドポイントにだれがアクセスできますか?エンドポイントへのアクセスはどのようにして制限できますか?**

Web サービスがデプロイされた後に、そのサービスに対して既定のエンドポイントが作成されます。 既定のエンドポイントは、その API キーを使用して呼び出すことができます。 エンドポイントの追加は、Azure クラシック ポータルからそれぞれのキーを使用して行うか、Web Service Management API を使用してプログラム的に行うことができます。 Web サービスを呼び出すには、アクセス キーが必要です。 詳細については、「 [Machine Learning Web サービスへの接続](machine-learning-connect-to-azure-machine-learning-web-service.md)」をご覧ください。

**Azure ストレージ アカウントが見つからない場合はどうなりますか?**

Machine Learning Studio は、ワークフローの実行時に中間データを保存する際、ユーザーが指定した Azure ストレージ アカウントを使用します。 このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供されます。 ワークスペースの作成後にストレージ アカウントが削除され、見つけることができない場合は、ワークスペースが機能しなくなり、そのワークスペースのすべての実験が失敗します。

誤ってストレージ アカウントを削除した場合は、削除されたストレージ アカウンと同じリージョンに同じ名前のストレージ アカウントを再作成します。 その後、アクセス キーを再同期します。

**ストレージ アカウントのアクセス キーの同期が失われてしまった場合はどうなりますか?**

Machine Learning Studio は、ワークフローの実行時に中間データを格納する際、ユーザーが指定した Azure ストレージ アカウントを使用します。 このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供され、アクセス キーはそのワークスペースに関連付けられます。 ワークスペースの作成後にアクセス キーが変更されると、ワークスペースはストレージ アカウントにアクセスできなくなります。 その場合、機能が停止し、そのワークスペース内のすべての実験が失敗します。

ストレージ アカウントのアクセス キーを変更した場合は、Azure クラシック ポータルを使用してワークスペースのアクセス キーを再同期します。  

## <a name="support-and-training"></a>サポートとトレーニング
**どこで Azure Machine Learning のトレーニングを受講できますか?**

[Azure Machine Learning ドキュメント センター](https://azure.microsoft.com/services/machine-learning/)に、ハウツー ガイドやビデオ チュートリアルが用意されています。 順を追ったハウツー ガイドでは、サービスの概要に加え、データのインポートから、データのクリーニング、予測モデルの構築、Azure Machine Learning を使用した運用環境へのデプロイまで、データ技術のライフ サイクルが説明されています。

Machine Learning Center には継続的に新しい資料が追加されます。 Machine Learning Center に追加の学習用資料が必要な場合は、 [ユーザー フィードバック フォーラム](https://windowsazure.uservoice.com/forums/257792-machine-learning)でご要望をご投稿いただけます。

[Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)でトレーニングを検索することもできます。

**どのようにして Azure Machine Learning のサポートを受けることができますか。**

Azure Machine Learning のテクニカル サポートを受けるには、[Azure のサポート](/support/options/) ページにアクセスし、**[Machine Learning]**を選択します。

MSDN にも、Azure Machine Learning に関するコミュニティ フォーラムがあり、ここで、Azure Machine Learning に関連する質問をすることができます。 このフォーラムは Azure Machine Learning チームが監視しています。 Azure フォーラムには[こちら](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)からアクセスできます。

## <a name="billing-questions"></a>課金に関する質問
**Machine Learning ではどのように請求が行われますか。**

Azure Machine Learning には、Machine Learning Studio と Machine Learning Web サービスという&2; つのコンポーネントがあります。

Machine Learning Studio の評価中は、無料 (Free) の課金レベルをご利用いただけます。 この Free レベルでは、クラシック Web サービスを制限付きの容量でデプロイすることもできます。

Azure Machine Learning がニーズを満たすものであるとわかれば、Standard レベルにサインアップできます。 サインアップには、Microsoft Azure サブスクリプションが必要です。

Standard レベルでは、Machine Learning Studio で定義するワークスペースごとに月単位で請求が行われます。 スタジオで実験を実行する場合は、実験の実行時に使用するコンピューティング リソースに対して請求されます。 クラシック Web サービスをデプロイする場合は、トランザクションおよびコンピューティング時間に対して従量課金制ベースで請求されます。

新しい (Resource Manager ベースの) Web サービスでは、費用がより予測しやすい課金プランを導入しています。 レベル別の価格設定は、大容量を必要とするお客様向けの割引率を提供するためのものです。

プランを作成すると、含まれる API コンピューティング時間と API トランザクションの数量に対応する固定費用が確定します。 含まれる数量を増やす必要がある場合は、プランにインスタンスを追加することができます。 含まれる数量を大幅に増やす必要がある場合は、上位のレベルのプランを選択することで、含まれる数量が大幅に増加し、割引率も上がります。

既存のインスタンスに含まれる数量を使い切ると、追加の使用量に対し、課金プランのレベルに関連付けられた超過料金が課されます。

> [!NOTE]
含まれる数量は 30 日ごとに再割り当てが行われますが、未使用分は次の期間に繰り越されません。

課金と料金の詳細については、「 [Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)」を参照してください。

**Azure Machine Learning に無料試用版はありますか。**

 Azure Machine Learning には、無料サブスクリプションのオプションが用意されています。詳細については、「[Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)」を参照してください。 Machine Learning Studio には、簡易評価用に&8; 時間の試用版が用意されています。[Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2) にサインインして入手できます。

 さらに、Azure の無料試用版にサインアップすると、1 か月間すべての Azure サービスを試すことができます。 Azure 無料試用版の詳細については、「[Azure 無料アカウント FAQ](https://azure.microsoft.com/pricing/free-trial-faq/)」を参照してください。

**トランザクションとは何ですか?**

トランザクションとは、Azure Machine Learning が応答する API 呼び出しです。 要求応答サービス (RRS) 呼び出しとバッチ実行サービス (BES) 呼び出しからのトランザクションは、集計のうえ、課金プランに基づいて課金されます。

**プランに含まれるトランザクションの数量は、RRS と BES 両方のトランザクションに対して使用できますか?**

はい。RRS と BES からのトランザクションは、集計のうえ、課金プランに基づいて課金されます。

**API コンピューティング時間とは何ですか?**

API コンピューティング時間は、Machine Learning コンピューティング リソースを使用した API 呼び出しの実行にかかる時間の課金単位です。 実行したすべての呼び出しが、課金を目的に集計されます。

**通常、実稼働 API 呼び出しにはどれくらいの時間がかかりますか?**

実稼働 API 呼び出しにかかる時間は状況により大幅に異なりますが、一般的には数百ミリ秒から数秒までの範囲です。 データ処理と機械学習モデルの複雑さによっては、API 呼び出しに数分かかることもあります。 実稼働 API 呼び出しにかかる時間を推定するための最適な方法は、Machine Learning サービスでモデルのベンチマークを実行することです。

**スタジオ コンピューティング時間とは何ですか?**

スタジオ コンピューティング時間は、スタジオでコンピューティング リソースを実験に使用した時間の総計を表す課金単位です。

**新しい (Azure Resource Manager ベースの) Web サービスでの開発/テスト レベルとは何のためのものですか?**

Resource Manager ベースの Web サービスでは、課金プランのプロビジョニングに使用できる価格レベルが複数用意されています。 開発/テスト価格レベルでは、含まれる数量が制限付きで提供され、費用をかけることなく、実験を Web サービスとしてテストできます。 Web サービスを使用してみることで、動作方法の確認ができます。

**ストレージには別に課金されますか?**

Machine Learning の Free レベルでは、別個のストレージは必要ありませんし、使用することもできません。 Machine Learning の Standard レベルでは、ユーザーが Azure ストレージ アカウントを持っていることが必要です。 Azure Storage の料金は[別途請求されます](https://azure.microsoft.com/pricing/details/storage/)。

**Machine Learning では高可用性がサポートされますか?**

はい。 詳細については、「[Machine Learning の価格](https://azure.microsoft.com/en-us/pricing/details/machine-learning/)」のサービス レベル アグリーメント (SLA) に関する説明をご覧ください。

**実稼働 API 呼び出しの実行には、どのような種類のコンピューティング リソースが使用されますか?**

Machine Learning サービスはマルチテナント サービスです。 バックエンドで実際に使用されるコンピューティング リソースの種類はさまざまで、パフォーマンスと予測可能性を向上させるために最適化されます。

### <a name="management-of-new-resource-manager-based-web-services"></a>新しい (Resource Manager ベースの) Web サービスの管理
**プランを削除すると、どうなりますか?**

プランはサブスクリプションから削除され、使用量に対して按分計算で課金されます。

> [!NOTE]
Web サービスが使用中のプランを削除することはできません。 使用中のプランを削除するには、新しいプランを Web サービスに割り当てるか、Web サービスを削除する必要があります。

**プラン インスタンスとは何ですか?**

プラン インスタンスとは、課金プランに追加可能である含まれる数量の単位です。 課金プランの課金レベルを選択すると、1 つのインスタンスが提供されます。 含まれる数量を増やす必要がある場合は、選択した課金レベルのインスタンスをプランに追加することができます。

**プランスのインスタンスはいくつ追加できますか?**

開発/テスト価格レベルのインスタンスは、1 つのサブスクリプションにつき&1; つとなります。

Standard S1、Standard S2、Standard S3 レベルでは、必要なだけインスタンスを追加できます。

> [!NOTE]
予想される使用量によっては、現在のレベルにインスタンスを追加するよりも、含まれる数量の多いレベルにアップグレードした方が、コスト効果の向上につながる場合があります。

**プラン レベルを変更 (アップグレード/ダウングレード) すると、どうなりますか?**

古いプランが削除され、現在の使用量に対して按分計算で課金されます。 残りの期間用には、アップグレード/ダウングレード後のレベルに含まれる数量をすべて含む新しいプランが作成されます。

> [!NOTE]
含まれる数量は期間ごとに割り当てられ、未使用分は繰り越されません。

**プラン内のインスタンスの数を増やすと、どうなりますか?**

数量は按分計算で割り当てられ、有効になるまで 24 時間かかる場合があります。

**プランのインスタンスを削除すると、どうなりますか?**

インスタンスはサブスクリプションから削除され、使用量に対して按分計算で課金されます。

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>新しい (Resource Manager ベースの) Web サービス プランへのサインアップ
**プランにサインアップするにはどうすればよいですか?**

課金プランの作成方法は&2; とおりあります。

Resource Manager ベースの Web サービスを最初にデプロイするときには、既存のプランを選択することも、新しいプランを作成することもできます。

この方法で作成されたプランは既定のリージョンに含まれ、Web サービスはそのリージョンにデプロイされます。

既定のリージョン以外のリージョンにサービスをデプロイする場合、サービスをデプロイする前に課金プランを定義したいことがあります。

このような場合は、Azure Machine Learning Web サービス ポータルにサインインして、プランのページに移動します。 そのページから、プランの追加や削除、既存のプランの変更を行うことができます。

**最初はどのプランを選択すればよいですか?**

Standard S1 レベルから開始して、サービスの使用状況を監視することをお勧めします。 含まれる数量の消費が速いと感じたら、インスタンスを追加するか、上位のレベルに移行して割引率を上げることもできます。 請求サイクル全体にわたり、必要に応じて課金プランを調整することができます。

**新しいプランはどのリージョンで利用できますか?**

新しい課金プランは、新しい Web サービスがサポートされている&3; つの運用リージョンで利用できます。

* 米国中南部
* 西ヨーロッパ
* 東南アジア

**私は複数のリージョンに Web サービスを所有しています。すべてのリージョンにプランが必要ですか?**

はい。 プランの価格設定はリージョンによって異なります。 Web サービスを別のリージョンにデプロイするときは、そのリージョンに固有のプランを割り当てる必要があります。 詳細については、「[リージョン別の利用可能な製品]( https://azure.microsoft.com/regions/services/)」を参照してください。

### <a name="new-web-services-overages"></a>新しい Web サービス - 超過料金
**Web サービスの使用量が超過しているかどうかを確認するには、どうすればよいですか?**

Azure Machine Learning Web サービス ポータルの [プラン] ページで、すべてのプランの使用状況を確認することができます。 ポータルにサインインして、**[プラン]** メニュー オプションをクリックします。

表の **[トランザクション]** 列と **[コンピューティング]** 列に、プランに含まれる数量と使用率が表示されます。

**開発/テスト価格レベルに含まれる数量を使い切ると、どうなりますか?**

開発/テスト価格レベルが割り当てられているサービスは、次の期間まで、または有料レベルに移行するまで停止します。

**クラシック Web サービスと、新しい (Resource Manager ベースの) Web サービスの超過使用量に関して、要求応答サービス (RRS) とバッチ実行サービス (BES) のワークロードの料金はどのように計算されていますか?**

RRS ワークロードの場合、実行した API トランザクション呼び出しごとと、それらの要求に関連したコンピューティング時間に対して課金されます。 RRS 実稼働 API トランザクションの費用は、実行された API 呼び出しの総数を 1,000 トランザクション単位に除算し、それに対して 1,000 トランザクションあたりの料金を乗算して計算されます。 RRS 実稼働 API コンピューティング時間の費用は、実行された各 API 呼び出しで必要な時間の合計に、API トランザクション総数を乗算し、さらにそれに実稼働 API コンピューティング時間あたりの料金を乗算して計算されます。

例として、Standard S1 の超過料金の場合を考えてみましょう。1,000,000 API トランザクションの実行に 0.72 秒かかったとします。実稼働 API トランザクションの費用は 500 ドル (1,000,000 * 0.50 ドル/1K API トランザクション) で、実稼働 API コンピューティング時間の費用は 400 ドル (1,000,000 * 0.72 秒 * 2 ドル/時間) となり、合計 900 ドルになります。

BES ワークロードの場合も、同じ方法で課金されます。 ただし、API トランザクションの費用は送信したバッチ ジョブの数に基づいて算出され、コンピューティングの費用はそれらのバッチ ジョブに関連したコンピューティング時間を表します。 BES 実稼働 API トランザクションの費用は、送信したジョブの総数を 1,000 トランザクション単位に除算し、それに対して 1,000 トランザクションあたりの料金を乗算して計算されます。 BES API 実稼働 API コンピューティング時間の費用は、実行するジョブの各行で必要な時間の合計に、ジョブの総行数を乗算し、ジョブの総数を乗算して、さらにそれに実稼働 API コンピューティング時間あたりの料金を乗算して計算されます。 Machine Learning 料金計算ツールを使用する場合、トランザクション メーターは送信予定のジョブ数を表し、トランザクションあたりの時間フィールドは実行する各ジョブにあるすべての行で必要な合計時間を表します。

例として、Standard S1 の超過料金の場合を考えてみましょう。それぞれが 500 行で構成されるジョブを毎日 100 個送信し、各ジョブの実行に 0.72 秒かかったとします。 1 か月の超過分の費用は、実稼働 API トランザクションの費用が 1.55 ドル (100 ジョブ/日 = 3,100 ジョブ/月 * 0.50 ドル/1K API トランザクション) で、実稼働 API コンピューティング時間の費用は 620 ドル (500 行 * 0.72 秒 * 3,100 ジョブ * 2 ドル/時間) となり、合計は 621.55 ドルになります。

### <a name="azure-machine-learning-classic-web-services"></a>Azure Machine Learning のクラシック Web サービス
**従量課金制は現在も利用できますか?**

はい。クラシック Web サービスは、Azure Machine Learning でも引き続き利用できます。  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Azure Machine Learning の Free および Standard レベル
**Azure Machine Learning の Free レベルには何が含まれますか?**

Azure Machine Learning の Free レベルは、Azure Machine Learning Studio を深く体験していただくためのものです。 サインアップに必要なのは、Microsoft アカウントだけです。 Free レベルには、 [Microsoft アカウント](https://www.microsoft.com/account/default.aspx)1 つにつき&1; つの Azure Machine Learning Studio ワークスペースへの無料アクセスが含まれます。 このレベルでは、最大 10 GB のストレージを使用でき、モデルをステージング API として運用できます。 Free レベルのワークロードは SLA の対象外であり、開発や個人用の目的にのみ使用されます。 

Free レベルのワークスペースには次の制限事項があります。

* ワークロードは、SQL Server を実行するオンプレミスのサーバーに接続してデータにアクセスすることはできません。
* 新しい Resource Manager ベースの Web サービスをデプロイすることはできません。


**Azure Machine Learning の Standard レベルおよびプランには何が含まれますか?**

Azure Machine Learning の Standard レベルは、Azure Machine Learning Studio の有料製品版です。 Azure Machine Learning Studio の月額料金は、1 つのワークスペースに対して月単位で請求され、1 か月未満については日割計算されます。 Azure Machine Learning スタジオ実験時間は、アクティブな実験のコンピューティング時間ごとに請求されます。 1 時間未満の請求は按分計算されます。  

Azure Machine Learning API サービスの請求額は、クラシック Web サービスか新しい (Resource Manager ベースの) Web サービスかによって異なります。

以下の請求は、サブスクリプションのワークスペースごとに集計されます。

* Machine Learning ワークスペース サブスクリプション - Machine Learning ワークスペース サブスクリプションは、Machine Learning Studio ワークスペースへのアクセスに必要な月額料金です。 スタジオで実験を実行する場合と実稼働 API を使用する場合にも、このサブスクリプションが必要です。
* スタジオ実験時間 - Machine Learning Studio での実験の実行と、ステージング環境での実稼働 API 呼び出しの実行によって発生したすべてのコンピューティング料金が、このメーターに集計されます。
* トレーニングとスコア付けのためにモデル内で SQL Server を実行するオンプレミスのサーバーに接続することで発生するデータ アクセス。
* クラシック Web サービスの場合:
  * 実稼働 API のコンピューティング時間 - 運用環境で実行されている Web サービスにより発生するコンピューティング料金が、このメーターに含まれます。
  * 実稼働 API トランザクション (1,000 件) - 実働 Web サービスの呼び出しごとに発生する料金が、このメーターに含まれます。

上記の料金とは別に、Resource Manager ベースの Web サービスの場合は選択したプランに以下の各料金が集計されます。

* Standard S1/S2/S3 API プラン (ユニット数) - このメーターは、Resource Manager ベースの Web サービス用に選択したインスタンスの種類を表します。
* Standard S1/S2/S3 超過 API コンピューティング時間 - このメーターには、既存のインスタンスに含まれる数量を使い切った後、運用環境で実行されている Resource Manager ベースの Web サービスによって発生するコンピューティング料金が含まれます。 追加の使用量は、S1/S2/S3 プラン レベルに関連付けられている超過料金で請求されます。
* Standard S1/S2/S3 超過 API トランザクション (1,000 件) - このメーターには、既存のインスタンスに含まれる数量を使い切った後、Resource Manager ベースの実稼働 Web サービスの呼び出しごとに発生する料金が含まれます。 追加の使用量は、S1/S2/S3 プラン レベルに関連付けられている超過料金で請求されます。
* 含まれる API コンピューティング時間の数量 - Resource Manager ベースの Web サービスでは、このメーターは含まれる API コンピューティング時間の数量を表します。
* 含まれる API トランザクションの数量 (1,000 件) - Resource Manager ベースの Web サービスでは、このメーターは含まれる API トランザクションの数量を表します。

**Azure Machine Learning Free レベルにサインアップするには、どうすればよいですか?**

必要なのは、Microsoft アカウントだけです。 [Azure Machine Learning ホーム](https://azure.microsoft.com/services/machine-learning/)に移動し、**[Start Now (今すぐ開始)]** ボタンをクリックします。 ご自分の Microsoft アカウントでサインインすると、Free レベル用のワークスペースが作成されます。 すぐに Machine Learning の実験を作成して試すことができます。

**Azure Machine Learning Standard レベルにサインアップするには、どうすればよいですか?**

Standard Machine Learning ワークスペースを作成するには、まず Azure サブスクリプションに対するアクセスが必要です。 30 日間の無料試用版 Azure サブスクリプションにサインアップし、後で Azure 有料サブスクリプションにアップグレードすることも、最初から Azure 有料サブスクリプションを購入することもできます。 サブスクリプションへのアクセスを取得後、Microsoft Azure クラシック ポータルから Machine Learning ワークスペースを作成できます。 [こちら](https://azure.microsoft.com/trial/get-started-machine-learning-b/)から詳細な手順をご確認ください。

また、Standard Machine Learning ワークスペースの所有者からの招待によって、その所有者のワークスペースにアクセスするという方法もあります。

**既に所有している Azure Blob Storage アカウントを指定して、Free レベルで使用することはできますか?**

いいえ、できません。Standard レベルは、これらのレベルが導入される前から利用可能だった Machine Learning サービスのバージョンと同等のものです。

**Free レベルで自分の機械学習モデルを API としてデプロイすることはできますか?**

はい。機械学習モデルを、Free レベルに含まれるステージング API サービスとして運用することができます。 ステージング API サービスを運用環境に移行し、運用可能にするサービスの実稼働エンドポイントを取得するには、Standard レベルを使用する必要があります。

**Azure 無料試用版と Azure Machine Learning の Free レベルの違いは何ですか?**

[Microsoft Azure 無料試用版](https://azure.microsoft.com/free/)では、任意の Azure サービスに&1; か月間適用できるクレジットを提供しています。 Azure Machine Learning の Free レベルは、特に非運用環境のワークロード用に Azure Machine Learning への継続的なアクセスを提供します。

**Free レベルから Standard レベルへの実験の移行はどのように行いますか?**

Free レベルから Standard レベルに実験をコピーするには:

1. Azure Machine Learning Studio にサインインし、最上部のナビゲーション バーのワークスペース セレクターに Free ワークスペースと Standard ワークスペースの両方が表示されていることを確認します。
2. Standard ワークスペースが選択されている場合は、Free ワークスペースに切り替えます。
3. コピーする実験を実験リスト ビューで選択し、**[コピー]** コマンド ボタンをクリックします。
4. ポップアップ ダイアログ ボックスから Standard ワークスペースを選択し、**[コピー]** ボタンをクリックします。
   すべての関連するデータセット、学習済みモデルなどが、実験と共に Standard ワークスペースにコピーされます。
5. Standard ワークスペースで、実験を再実行し、Web サービスを再発行することが必要です。

### <a name="studio-workspace"></a>Studio ワークスペース
**ワークスペースが異なると、表示される請求内容も異なりますか?**

ワークスペースの料金は、1 件の請求で適用可能なメーターごとに別々に表示されます。

**実験の実行には、どのような種類のコンピューティング リソースが使用されますか?**

Machine Learning サービスはマルチテナント サービスです。 バックエンドで実際に使用されるコンピューティング リソースの種類はさまざまで、パフォーマンスと予測可能性を向上させるために最適化されます。

### <a name="guest-access"></a>ゲスト アクセス
**Azure Machine Learning Studio に対するゲスト アクセスとは何ですか?**

ゲスト アクセスとは、機能が制限された試用版です。 費用をかけず、認証もなしで、Azure Machine Learning Studio で実験を作成して実行できます。 ゲスト セッションは非永続的で保存ができず、利用できる時間は&8; 時間に制限されます。 他にも、R と Python がサポートされていない、ステージング API がない、データセットのサイズとストレージの容量が制限されている、などの制限事項があります。 一方 Microsoft アカウントでサインインしたユーザーの場合は、前述した Machine Learning Studio の Free レベルへのフル アクセスが提供され、永続ワークスペースやさらに包括的な機能を使用できます。 [https://studio.azureml.net](https://studio.azureml.net) の **Get started (開始)** をクリックして **Guest Access (ゲスト アクセス)** または Sign In with Microsoft account (Microsoft アカウントでサインイン) を選択し、無料の Machine Learning の機能をお試しください。

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

