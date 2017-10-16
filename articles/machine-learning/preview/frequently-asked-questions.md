---
title: "Azure Machine Learning 2017 のプレビューに関する FAQ | Microsoft Docs"
description: "この記事では、Azure Machine Learning のプレビュー機能についてよく寄せられる質問とその回答を示します"
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 4b3720019ba396d90be5586fa8fff4fa7e7d6e46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Azure Machine Learning に関してよく寄せられる質問

## <a name="general-product-questions"></a>製品に関する一般的な質問

**Azure Machine Learning とは**

Azure Machine Learning は機械学習モデルおよび AI モデルの作成、テスト、管理、デプロイを行うことができる、完全管理型の Azure サービスです。 Microsoft の新サービスとダウンロード可能なアプリケーションでは、クラウドからオンプレミス、エッジまでを活用して強力で高速かつ柔軟なモデルのトレーニング、デプロイ、管理、監視を実現するコードファースト方式を採用しています。 これと異なり、Azure Machine Learning Studio には、ドラッグ アンド ドロップでモデルを作成できる視覚的なブラウザーベース環境が用意されており、コードを作成する必要はありません。 

**プレビューの使用を開始するにはどうすればよいですか?**

使用を開始する最も簡単な方法は、Azure で実験サービスにサインアップすることです。 ポータルでは、無料のワークベンチ アプリケーションのダウンロードや、モデル管理アカウントへのサインアップも可能です。 さらに、ドキュメント サイトにアクセスしてハウツー記事やビデオ、デモ コードなどを確認することもできます。 

**Azure サブスクリプションを所有していません。サブスクリプションがなくてもこの新サービスを試すことはできますか?**

この新サービスは Microsoft Azure のポートフォリオの一部であるため、ご利用には Azure サブスクリプションが必要です。 また、Resource Groups や Virtual Machines などのアセットを作成するための適切な権限も必要になります。 

**この新サービスはどのリージョンで利用できますか?**

Azure Machine Learning の実験およびモデル管理サービスがサポートされている Auzre リージョンについては、[リージョン別の Azure 製品](https://azure.microsoft.com/regions/services/)に関するページをご覧ください。

本製品の発展に合わせてより多くのリージョンを追加する予定です。  [Azure Machine Learning のフィードバック](https://feedback.azure.com/forums/257792-machine-learning)に関するサイトで、デプロイ先リージョンの優先順位付けにご協力ください。 

**他にどのような Azure サービスが必要になりますか?**

Azure Machine Learning のすべての機能を完全に利用するには、Visual Studio VSTS アカウント、Azure Blob Storage、Azure Container Registry、データ サイエンス VM または HDInsight コンピューティング、および Azure Container Service が必要です。

**Azure Machine Learning と SQL Server 2017 の Microsoft Machine Learning サービスの関係性はどのようなものですか?**   

SQL Server 2017 の Machine Learning サービスは、機械学習のタスクをデータベース ワークフローに統合するためのスケーラブルで拡張可能なプラットフォームです。 このサービスは、データの移動が高額であるか困難な場合など、オンプレミス ソリューションが必要となるシナリオに最適です。 反対に、クラウドまたはハイブリッド ワークフローではこの新しい Azure サービスが大いに役立ちます。 

**Python と R はともにサポートされますか?C++ などの他のプログラミング言語についても教えてください。**

現時点でサポートされているのは Python のみです。 現在 R との統合を進めており、まもなく公開できる見込みです。 

**Azure Machine Learning と Microsoft Machine Learning for Spark の関係性はどのようなものですか?**

回答: MMLSpark では、生産性、実験の容易さ、最新のアルゴリズムに重点を置いて Apache Spark 向けにディープ ラーニング ツールとデータ サイエンス ツールを提供しています。 MMLSpark を使用すると、Spark Machine Learning パイプラインと Microsoft Cognitive Toolkit および OpenCV を統合できます。 画像データおよびテキスト データに対する強力で拡張性に優れた予測モデルと分析モデルを作成可能です。 MMLSpark はオープンソース ライセンスで提供されており、AML Workbench に使用可能なモデルとアルゴリズムのセットとして含まれています。 MMLSpark の詳細については、製品ドキュメントをご覧ください。 

**この新しいツールおよびサービスではどのバージョンの Spark がサポートされていますか?一番上のセクション**

現在 Workbench に含まれサポートされているのは MMLSpark バージョン 0.8 であり、このバージョンは Apache Spark 2.1 と互換性があります。 また、Linux 仮想マシンでは MMLSpark 0.8 の GPU 対応 Docker イメージを使用することもできます。

## <a name="experimentation-service"></a>実験サービス

**Azure Machine Learning 実験サービスとはどのようなものですか?**

実験サービスは、機械学習の実験を次のレベルへと進める管理型の Azure サービスです。 実験はローカルでもクラウドでも構築できます。 デスクトップで迅速にプロトタイプを作成し、その後仮想マシンや Spark クラスターに拡張できます。 最新の GPU テクノロジを備えた Azure VM により、ディープ ラーニングを素早く効率的に始められます。 また、Git と密接に統合されているため、コード追跡、構成、コラボレーション用の既存のワークフローを簡単に利用できます。 

**実験サービスの課金はどのように行われますか?**

お客様の Azure Machine Learning 実験サービスに関連付けられたユーザーのうち、最初の 2 名については無料となります。 以降のユーザーについては、パブリック プレビュー料金として毎月 50 ドルが課金されます。 価格と課金の詳細については、価格ページをご覧ください。

**課金は実験の実行回数に応じて行われますか?**

いいえ。実験サービスでは実験を必要なだけ実行することができ、課金はユーザーの数のみに応じて行われます。 実験のコンピューティング リソースについては別途課金されます。 複数の実験を行い、お客様のソリューションに一番合ったモデルをお探しください。   

**どのようなコンピューティング リソースとストレージ リソースを利用できますか?**

実験サービスでは、ローカル マシン (直接または Docker ベース)、[Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)、および [HDInsight](https://azure.microsoft.com/services/hdinsight/) で実験を行えます。 このサービスでは、実行出力の保管のために [Azure Storage](https://azure.microsoft.com/services/storage/) アカウントにもアクセスします。また、[Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) アカウントを利用してバージョン管理および Git への保管も行うことができます。 消費したコンピューティング リソースとストレージ リソースについては、それぞれの価格に基づいて個別に課金されることに注意してください。  


## <a name="model-management"></a>モデル管理

**Azure Machine Learning モデル管理サービスとはどのようなものですか?**

Azure Machine Learning モデル管理は、データ サイエンティストや dev-ops チームがさまざまな環境に予測モデルを確実にデプロイできるようにする管理型の Azure サービスです。 Git リポジトリと Docker コンテナーにより、追跡管理性と再現性が実現されています。 モデルはクラウド、オンプレミス、またはエッジに確実に配置できます。 運用開始後はモデルのパフォーマンスを管理し、パフォーマンスの低下を先読みして再トレーニングを行うことができます。 モデルはローカル マシン、[Azure VM](https://azure.microsoft.com/services/virtual-machines/)、[HDInsight](https://azure.microsoft.com/services/hdinsight/) 上の Spark、または Kubernetes により調整された [Azure Container Service](https://azure.microsoft.com/services/container-service/) クラスターへデプロイ可能です。  

**"モデル" とは何ですか?**

モデルとは、モデルの管理用に起用された実験実行の出力です。 再トレーニングまたはバージョン イテレーションにより更新されたモデルを含め、ホスティング アカウントに登録されたモデルがお客様のプランに対してカウントされます。

**"管理モデル" とは何ですか?**

モデルはトレーニング プロセスの出力であり、機械学習アルゴリズムをトレーニング データに適用したものです。 モデル管理では、Web サービスとしてモデルをデプロイし、さまざまなバージョンのモデルを管理し、パフォーマンスとメトリックを監視することができます。 "管理" モデルは、Azure Machine Learning モデル管理アカウントに登録されます。 たとえば、売上を予測するシナリオを考えます。 実験の段階では、さまざまなデータ セットやアルゴリズムを使用してたくさんのモデルを作成します。 精度が異なる 4 つのモデルを作成しても、登録するのは最も精度が高いモデルのみです。 このモデルを登録すると、最初の管理モデルになります。
 
**"デプロイ" とは何ですか?**

モデル管理では、パッケージ化された Web サービス コンテナーとして Azure にモデルをデプロイできます。 これらの Web サービスは、REST API を使用して呼び出すことができます。 各 Web サービスは単一のデプロイとしてカウントされ、アクティブなデプロイの総数がお客様のプランにカウントされます。 売上予測の例では、最もパフォーマンスの良いモデルをデプロイすると、プランが 1 デプロイ分増加します。 別のバージョンを再トレーニングしてデプロイすると、デプロイは 2 つになります。 新しいモデルの方が良いとわかり元のモデルを削除した場合、デプロイのカウントは 1 つ減少します。  

**デプロイで利用できるコンピューティング リソースにはどのようなものがありますか?** 

モデル管理では、[Azure Container Service](https://azure.microsoft.com/services/container-service/) に登録された Docker コンテナー、または [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) として、もしくはローカル マシンでデプロイを実行できます。 追加のデプロイ ターゲットはすぐに追加されます。 消費したコンピューティング リソースについては、それぞれの価格に基づいて個別に課金されることに注意してください。     

**Azure Machine Learning モデル管理では、実験サービス以外のツールを使用して構築したモデルをデプロイすることはできますか?**

実験サービスを使用して作成したモデルをデプロイした場合、最も優れたエクスペリエンスが得られます。 ただし、他のフレームワークやツールを使用して構築したモデルをデプロイすることも可能です。 MMLSpark、TensorFlow、Microsoft Cognitive Toolkit、scikit-learn、Keras など、さまざまなモデルがサポートされます。 

**自分の Azure リソースを使用できますか?**

はい。実験サービスとモデル管理は、複数の Azure データ ストアやコンピューティング ワークロード、その他のサービスと連携して動作します。 必要な Azure サービスの詳細については技術ドキュメントをご覧ください。

**デプロイ シナリオについて、オンプレミスとクラウドの両方がサポートされますか?**

はい。 Docker コンテナーを経由したローカルおよびオンプレミスでのデプロイ シナリオがサポートされます。 ローカル実行のターゲットとしては、単一ノードの Docker デプロイ、[ML サービスを搭載した Microsoft SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)、Hadoop、Spark などがあります。 また、Azure Container Service と Kubernetes、HDInsight、または Spark クラスターによるクラスター化デプロイなど、Docker 経由でのクラウド デプロイもサポートされています。 エッジ シナリオは、Docker コンテナーおよび Azure IoT Edge によりサポートされます。 

**Azure Machine Learning CLI を使用して作成した Docker イメージを別のホスト上で実行できますか?**

はい。 Docker ホストに Docker イメージをホストするのに十分なコンピューティング リソースがある場合、あらゆるホストでイメージを Web サービスとして使用できます。

**デプロイしたモデルを再トレーニングすることはできますか?**

はい。同じモデルについて複数のバージョンをデプロイできます。 モデル管理では、更新されたモデルおよびイメージすべてに対するサービス更新がサポートされています。

## <a name="workbench"></a>ワークベンチ

**Azure Machine Learning Workbench とは何ですか?**

Azure Machine Learning Workbench は、専門のデータ サイエンティスト向けに開発されたコンパニオン アプリケーションです。 Windows 版と Mac 版が用意されており、複数の機械学習ソリューションの概要を把握し、管理およびコントロールすることができます。 Machine Learning ワークベンチでは、Microsoft およびオープンソース コミュニティの両方から提供される最先端の AI フレームワークにアクセスすることもできます。 TensorFlow、Microsoft Cognitive Toolkit、Spark ML、scikit-learn など、最も人気のあるデータ サイエンス ツールキットが搭載されています。 また、Jupyter Notebook、PyCharm、Visual Studio Code などの一般的なデータ サイエンス IDE との統合にも対応しています。 Machine Learning ワークベンチには、構造化されているかどうかにかかわらずデータを高速にサンプリングし、把握して準備できるデータ準備機能が組み込まれています。 [PROSE](https://microsoft.github.io/prose/) というこの新しいデータ準備ツールは、Microsoft Reseach の最先端テクノロジに基づいて開発されています。  

**ワークベンチは IDE ですか?**

いいえ。 Machine Learning ワークベンチは Jupyter Notebook、Visual Studio Code、PyCharm などの一般的な IDE の付属品として設計されており、すべての機能が搭載された IDE ではありません。 Machine Learning ワークベンチには基本的なテキスト編集機能はいくつか用意されていますが、IntelliSense などの一般に使用される IDE 機能はサポートされません。 コードの開発、編集、デバッグにはお好みの IDE を使用することをお勧めします。 

**Azure Machine Learning Workbench の使用に料金はかかりますか?**

いいえ。 Azure Machine Learning Workbench は無料のアプリケーションです。 マシンとユーザーの数に応じて必要なだけダウンロード可能です。 Azure Machine Learning Workbench を使用するには、実験アカウントが必要になります。 が必要です。  

**コマンドライン機能はサポートされますか?**

回答: はい。Azure Machine Learning では完全な CLI インターフェイスを提供しています。 Machine Learning CLI は、既定では Azure Machine Learning Workbench とともにインストールされます。 この CLI は、Azure 上の Linux Data Science 仮想マシンの一部としても提供されており、[Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) への統合が予定されています。


**ワークベンチで Jupyter Notebook を使用できますか?**

はい。 Jupyter Notebook は、ブラウザーをクライアントとする場合と同じように、ワークベンチをクライアント ホスト アプリケーションとしてワークベンチで実行できます。 

**どの Jupyter Notebook カーネルがサポートされますか?**

回答: ワークベンチに含まれる現在のバージョンの Jupyter では Python 3 カーネルと、お客様の aml_config フォルダーの各 "runconfig" に対応した追加のカーネルが起動されます。 サポートされる構成は次のとおりです。
- ローカルの Python
- ローカルまたはリモートの Docker 内の Python

## <a name="data-formats-and-capabilities"></a>データ形式および機能

**ワークベンチでのデータ取り込みが現在サポートされているファイル形式はどのようなものですか?**

現在、ワークベンチのデータ準備ツールでは以下の形式からの取り込みがサポートされています。 
- 区切りファイル (CSV や TSV など)  
- 固定幅ファイル
- プレーン テキスト ファイル (.txt)
- Excel (.xls/xlsx)
- JSON ファイル
- Parquet ファイル 
- カスタム ファイル (スクリプト) (ソリューションで別のソースからデータを取り込む必要がある場合、Python コードを使用できます) 

**どのようなデータ保存場所が現在サポートされていますか?**

パブリック プレビューでは、ワークベンチは以下からのデータ取り込みをサポートしています。 
- ローカルのハード ドライブまたはマッピングされたネットワーク上の保存場所
- Azure Blob Storage または Azure Storage (Azure サブスクリプションが必要)
- Azure Data Lake または Azure Data Lake ストレージ (Azure サブスクリプションが必要)
- Azure SQL Server
- の接続文字列


**どのような種類のデータ ラングリング、データ準備、およびデータ変換を利用できますか?**

パブリック プレビューでは、ワークベンチは “サンプルに基づく列の導出”、“サンプルに基づく列の分割”、“テキスト クラスタリング”、“欠損値の処理” などをサポートしています。  また、データ型変換、データ集計 (カウント、平均値、分散など)、複雑なデータ結合もサポートされています。 サポートされる機能の一覧については製品ドキュメントをご覧ください。 

**Azure Machine Learning Workbench、実験、モデル管理で適用されるデータ サイズ制限はありますか?**

回答: いいえ。この新サービスでは、データ制限は課されません。 ただし、お客様がデータ準備、モデルのトレーニング、実験、またはデプロイを行う環境により制限が生じます。 たとえば、ローカル環境をトレーニングのターゲットとする場合、ハード ドライブの使用可能な容量による制限を受けることになります。 また、HDInsight をターゲットとする場合、関連するサイズまたはコンピューティングの制約により制限されます。 

## <a name="algorithms-and-libraries"></a>アルゴリズムとライブラリ

**Azure Machine Learning Workbench ではどのようなアルゴリズムがサポートされますか?**

プレビュー版の製品およびサービスには、オープン ソース コミュニティの最も良い部分が含まれています。 TensorFlow、scikit-learn、Apache Spark、Microsoft Cognitive Toolkit など多様なアルゴリズムとライブラリがサポートされます。 Azure Machine Learning Workbench には、[Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) パッケージもパッケージ化されています。

**Azure Machine Learning と Microsoft Cognitive Toolkit の関係性はどのようなものですか?**

[Microsoft Cognitive Toolkit](https://www.microsoft.com/cognitive-toolkit/) は、Microsoft の新しいツールとサービスでサポートされている多くのフレームワークの 1 つです。 Cognitive Toolkit は統合型のディープ ラーニング キットであり、フィードフォワード ディープ ニューラル ネットワーク、畳み込みネット、シーケンス変換、再帰型ネットワークなどの一般的な機械学習モデルを使用および結合することができます。 Microsoft Cognitive Toolkit の詳細については、[製品ドキュメント](https://docs.microsoft.com/cognitive-toolkit/)に関するページをご覧ください。 

## <a name="pricing-and-billing"></a>価格と課金

**Azure Machine Learning のプレビュー期間中に課金は行われますか?** 

回答: Azure サブスクライバーであれば、Azure Machine Learning Workbench アプリケーションは無料で使用できます。 実験サービスとモデル管理には無料レベルが用意されており、有料レベルについてはパブリック プレビュー期間中は割引料金でご利用いただけます。

**課金は実験の実行回数に応じて行われますか?**

いいえ。実験サービスでは実験を必要なだけ実行することができ、課金はユーザーの数のみに応じて行われます。 実験のコンピューティング リソースについては別途課金されます。  複数の実験を行い、お客様のソリューションに最適なモデルをお探しください。 

**課金は Web サービスの呼び出し回数に応じて行われますか?**

いいえ。 Web サービスは必要な回数だけ呼び出すことが可能であり、モデル管理の課金に影響は及びません。 アプリケーションのニーズに合わせて、デプロイのスケールを完全に自由に調整できます。

**Azure Machine Learning モデル管理で購入済みのユニット数を増減させるにはどうすればよいですか?**

ユニット数の増減は、Azure Portal または CLI のいずれかから行うことができます。 

**請求書はどのようなものですか?**

請求書は毎日作成されます。 課金額の計算における 1 日は、UTC の午前 0 時に開始します。 請求書は月単位で生成されます。 Azure Machine Learning との組み合わせで使用された Azure サービスがある場合は、別途料金が発生します。 こうした料金には次のものが含まれますが、これらに限定されるものではありません。 
- コンピューティング料金
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Azure Event Hub
- Azure Stream Analytics 詳細および請求書サンプルについては、価格ページをご覧ください。 

## <a name="support-and-training"></a>サポートとトレーニング

**どこで Azure Machine Learning のトレーニングを受講できますか?**

[Azure Machine Learning ドキュメント センター](./overview-what-is-azure-ml.md)に、ハウツー ガイドやビデオ チュートリアルが用意されています。 これらのステップバイステップ形式のガイドでは、サービスを紹介するとともに、データ サイエンスのライフ サイクルについて説明しています。 Machine Learning Center には継続的に新しい資料が追加されます。 追加の学習資料が必要な場合は、ユーザー フィードバック フォーラムからご要望をお寄せください。

**どのようにして Azure Machine Learning のサポートを受けることができますか。**

テクニカル サポートを受けるには、Azure のサポート ページにアクセスし、[Machine Learning] を選択します。 Azure Machine Learning では、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-machine-learning) に "Azure-Machine-Learning" タグが付いた活発なコミュニティも用意されており、チームが巡回しています。  [Azure Machine Learning のフィードバック](https://feedback.azure.com/forums/257792-machine-learning) サイトからご提案や機能に対するご要望をお寄せください。 
