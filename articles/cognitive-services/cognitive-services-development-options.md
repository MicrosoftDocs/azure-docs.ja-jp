---
title: Azure Cognitive Services の開発オプション
description: クライアント ライブラリ、REST API、Logic Apps、Power Automate、Azure Functions、Azure App Service、Azure Databricks など、開発とデプロイのさまざまなオプションで Azure Cognitive Services を使用する方法について説明します。
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 975f7eec31839aefcb1782f573d0210df29a4d00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98873571"
---
# <a name="cognitive-services-development-options"></a>Cognitive Services の開発オプション

このドキュメントでは、Azure Cognitive Services の使用を始めるときに役立つ、開発とデプロイのオプションの概要について説明します。

Azure Cognitive Services はクラウド ベースの AI サービスであり、開発者は機械学習に関する深い知識がなくても、アプリケーションと製品にインテリジェンスを組み込むことができます。 Cognitive Services でアクセスできる AI 機能やモデルは、Microsoft によって構築、トレーニング、更新されており、アプリケーションですぐに使用できます。 多くの場合、ビジネス ニーズに合わせてモデルをカスタマイズすることもできます。 

Cognitive Services は、次の 4 つのカテゴリに分類されます: 決定、言語、音声、視覚。 通常は、これらのサービスにアクセスするには、Microsoft によって提供される REST API、クライアント ライブラリ、カスタム ツール (コマンド ライン インターフェイスなど) を使用します。 しかし、これは成功するための 1 つのパスにすぎません。 Azure を使用すると、次のようないくつかの開発オプションにアクセスすることもできます。

* Logic Apps や Power Automate のような自動化および統合ツール。
* Azure Functions や App Service のようなデプロイ オプション。 
* セキュリティで保護されたアクセス用の Cognitive Services Docker コンテナー。
* ビッグ データ シナリオ用の Apache Spark、Azure Databricks、Azure Synapse Analytics、Azure Kubernetes Service などのツール。 

始める前に、Cognitive Services が主に 2 つの異なるタスクに使用されることを理解しておくことが重要です。 実行するタスクに基づいて、開発とデプロイのさまざまなオプションを選択できます。 

* [予測と分析に関する開発のオプション](#development-options-for-prediction-and-analysis)
* [モデルをカスタマイズおよび構成するためのツール](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>予測と分析に関する開発のオプション 

モデルのカスタマイズと構成に使用するツールは、Cognitive Services の呼び出しに使用するツールとは異なります。 ほとんどの Cognitive Services は、何もカスタマイズすることなく、そのままの状態で、データを送信し、分析情報を受信することができます。 次に例を示します。 

* Computer Vision サービスに画像を送信して、単語や語句を検出したり、フレーム内の人の数を数えたりすることができます
* Speech サービスにオーディオ ファイルを送信して、文字起こしを行い、同時に音声をテキストに変換することができます
* PDF を Form Recognizer サービスに送信し、テーブル、セル、セル内のテキストを検出して、座標と詳細を含む JSON 出力を取得することができます

Azure にはさまざまな種類のユーザー向けに設計された幅広いツールが用意されており、その多くは Cognitive Services で使用できます。 デザイナー駆動型ツールは最も使いやすく、簡単に設定して自動化できますが、カスタマイズに関しては制限がある場合があります。 REST API とクライアント ライブラリを使用すると、ユーザーが制御できる範囲や柔軟性は向上しますが、ソリューションを構築するにはより多くの労力、時間、専門知識が必要になります。 REST API とクライアント ライブラリを使用する場合は、C#、Java、Python、JavaScript、その他の一般的なプログラミング言語など、最新のプログラミング言語を使用すると作業が快適になると思われます。 

Cognitive Services を使用できるさまざまな方法を見ていきます。

### <a name="client-libraries-and-rest-apis"></a>クライアント ライブラリと REST API

Cognitive Services のクライアント ライブラリと REST API を使用すると、サービスに直接アクセスできます。 これらのツールを使用すると、Cognitive Services とそのベースライン モデルにプログラムでアクセスでき、多くの場合、モデルとソリューションをプログラムでカスタマイズできます。 

* **対象ユーザー**: 開発者とデータ科学者
* **利点**:任意の言語と環境からのサービスの呼び出しに関して、最大限の柔軟性が提供されます。 
* **UI**: なし - コードのみ
* **サブスクリプション**: Azure アカウント + Cognitive Services リソース

使用可能なクライアント ライブラリと REST API の詳細については、[Cognitive Services の概要](index.yml)に関するページでサービスを選択し、視覚、決定、言語、音声に関するクイックスタートのいずれかを参照してください。

### <a name="cognitive-services-for-big-data"></a>ビッグ データの Cognitive Services

ビッグ データ向けの Cognitive Services を使用すると、継続的に改善されるインテリジェントなモデルを Apache Spark&trade; と SQL の計算に直接埋め込むことができます。 これらのツールにより、開発者は低レベルの細かいネットワーク設定から解放され、スマートな分散アプリケーションの作成に集中できます。 ビッグ データ用の Cognitive Services の場合、次のプラットフォームとコネクタがサポートされています: Azure Databricks、Azure Synapse、Azure Kubernetes Service、データ コネクタ。

* **対象ユーザー**: データ科学者、データ エンジニア
* **利点**:ビッグ データ向けの Azure Cognitive Services を使用すると、ユーザーは Apache Spark&trade; を使用して、テラバイト単位のデータを Cognitive Services に渡すことができます。 データストアを使用して大規模なインテリジェント アプリケーションを簡単に作成できます。
* **UI**: なし - コードのみ
* **サブスクリプション**: Azure アカウント + Cognitive Services リソース

Cognitive Services のビッグ データの詳細については、[概要](./big-data/cognitive-services-for-big-data.md)に関するページを参照してください。 構築を始める準備ができたら、[Python](./big-data/samples-python.md) または [Scala](./big-data/samples-scala.md) のサンプルを試してみてください。

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions と Azure Service Web ジョブ

[Azure Functions](../azure-functions/index.yml) と [Azure App Service Web ジョブ](../app-service/index.yml)はどちらも、開発者向けに設計されたコード第一の統合サービスを提供するものであり、[Azure App Service](../app-service/index.yml) 上に構築されています。 これらの製品により、コードを記述するためのサーバーレス インフラストラクチャが提供されます。 そのコード内では、クライアント ライブラリと REST API を使用して、サービスを呼び出すことができます。 

* **対象ユーザー**: 開発者とデータ科学者
* **利点**:イベントによってトリガーされるコードを実行できるサーバーレス コンピューティング サービス。 
* **UI**: はい
* **サブスクリプション**: Azure アカウント + Cognitive Services リソース + Azure Functions サブスクリプション

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) を使用すると、Power Automate と同じワークフロー デザイナーとコネクタが共有されますが、Visual Studio や DevOps との統合など、より高度な制御が提供されます。 Power Automate を使用すると、API にプロキシまたはラッパーを提供するサービス固有のコネクタを通じて、ご自分のコグニティブ サービス リソースと簡単に統合できます。 これらは、Power Automate で使用できるものと同じコネクタです。 

* **対象ユーザー**: 開発者、インテグレーター、IT プロフェッショナル、DevOps
* **利点**:コーディングの少ないソリューションで高度なオプションと統合を提供するデザイナー第一 (宣言型) の開発モデル。
* **UI**: はい
* **サブスクリプション**: Azure アカウント + Cognitive Services リソース + Logic Apps デプロイ

### <a name="power-automate"></a>Power Automate 

Power Automate は [Power Platform](/power-platform/) のサービスであり、コードを記述せずにアプリとサービスの間の自動化されたワークフローを作成するのに役立ちます。 Power Automate ソリューションで Cognitive Services リソースと簡単にやりとりできるように、いくつかのコネクタが用意されています。 Power Automate は Logic Apps の上に構築されています。 

* **対象ユーザー**: ビジネス ユーザー (アナリスト)、SharePoint 管理者
* **利点**:デスクトップからマウス クリック、キーストローク、コピーと貼り付けの手順を記録するだけで、反復的な手動タスクを自動化できます。
* **UI ツール**: はい - UI のみ
* **サブスクリプション**: Azure アカウント + Cognitive Services リソース + Power Automate サブスクリプション + Office 365 サブスクリプション

### <a name="ai-builder"></a>AI Builder 

Microsoft Power Platform の機能である [AI Builder](/ai-builder/overview) を使用すると、プロセスを自動化して結果を予測することにより、ビジネスのパフォーマンスを向上させることができます。 AI Builder を使用すると、ポイント アンド クリック エクスペリエンスによりソリューションに AI の機能を組み込むことができます。 Form Recognizer、Text Analytics、Computer Vision などの多くの Cognitive Services がここに直接統合されており、独自のコグニティブ サービスを作成する必要はありません。 

* **対象ユーザー**: ビジネス ユーザー (アナリスト)、SharePoint 管理者
* **利点**:ポイント アンド クリック エクスペリエンスにより AI の機能を組み込むターンキー ソリューション。 コーディングやデータ サイエンスのスキルは必要ありません。
* **UI ツール**: はい - UI のみ
* **サブスクリプション**: AI Builder

### <a name="continuous-integration-and-deployment"></a>継続的インテグレーションと継続的配置

Azure DevOps と GitHub Actions を使用して、デプロイを管理できます。 [次のセクション](#continuous-integration-and-delivery-with-devops-and-github-actions)には、Speech および Language Understanding (LUIS) サービス用のカスタム モデルをトレーニングしてデプロイするための CI/CD 統合の 2 つの例があります。 

* **対象ユーザー**: 開発者、データ科学者、データ エンジニア
* **利点**:プログラムによってアプリケーションやモデルを継続的に調整、更新、デプロイできます。 データを使用して音声、視覚、言語、決定のモデルを定期的に改善および更新する場合、大きな利点があります。 
* **UI ツール**: なし - コードのみ 
* **サブスクリプション**: Azure アカウント + Cognitive Services リソース + GitHub アカウント

## <a name="tools-to-customize-and-configure-models"></a>モデルをカスタマイズおよび構成するためのツール

Cognitive Services を使用してアプリケーションまたはワークフローを構築する作業を進める過程で、目的のパフォーマンスを実現するためにモデルのカスタマイズが必要であることがわかる場合があります。 多くのサービスの場合、前に構築したモデルを基にして、特定のビジネス ニーズを満たすことができます。 カスタマイズ可能なすべてのサービスで、プロセスの手順に沿った UI 駆動型エクスペリエンスと、コード駆動型トレーニング用の API の両方が提供されています。 次に例を示します。

* Custom Speech モデルをトレーニングして、単語エラー率 (WER) 3% 未満で医療用語を正しく認識します
* 針葉樹と広葉樹の違いを示すことができる画像分類器を、Custom Vision を使用して作成します
* 自動カスタマー エクスペリエンス向上のため、個人の音声データを使用してカスタム ニューラル音声を作成します

モデルのトレーニングと構成に使用するツールは、Cognitive Services の呼び出しに使用するツールとは異なります。 多くの場合、カスタマイズをサポートする Cognitive Services には、モデルのトレーニング、評価、デプロイを支援するように設計されたポータルと UI ツールが用意されています。 いくつかのオプションを簡単に見てみましょう。<br><br>

| 重要な要素 | サービス | カスタマイズ UI | クイック スタート |
|--------|---------|------------------|------------|
| 視覚 | Custom Vision | https://www.customvision.ai/ | [クイックスタート](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| 視覚 | Form Recognizer | [サンプル ラベル付けツール](https://fott-preview.azurewebsites.net/) | [クイックスタート](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| 決定 | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [クイックスタート](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| 決定 | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [クイックスタート](./metrics-advisor/quickstarts/web-portal.md) |
| 決定 | Personalizer | UI は、Azure portal の Personalizer リソースで使用できます。 | [クイックスタート](./personalizer/quickstart-personalizer-sdk.md) |
| Language | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Language | QnA Maker | https://www.qnamaker.ai/ | [クイックスタート](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Language | Translator、カスタム翻訳ツール | https://portal.customtranslator.azure.ai/ | [クイックスタート](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Speech | カスタム コマンド | https://speech.microsoft.com/ | [クイックスタート](./speech-service/custom-commands.md) |
| Speech | Custom Speech | https://speech.microsoft.com/ | [クイックスタート](./speech-service/custom-speech-overview.md) |
| Speech | Custom Voice | https://speech.microsoft.com/ | [クイックスタート](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>DevOps と GitHub Actions を使用した継続的インテグレーションと継続的デリバリー

Language Understanding と Speech サービスには、Azure DevOps と GitHub Actions を利用した継続的インテグレーション ソリューションと継続的配置ソリューションが用意されています。 これらのツールは、カスタム モデルのトレーニング、テスト、リリース管理を自動化するために使用されます。 

* [Custom Speech の CI/CD](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [LUIS 用の CI/CD](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>オンプレミス コンテナー 

Cognitive Services の多くは、オンプレミスでのアクセスと使用のためにコンテナーにデプロイできます。 これらのコンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由でデータに対して Cognitive Services を使用することが柔軟に可能になります。 Cognitive Services のコンテナーの完全な一覧については、[Cognitive Services 用のオンプレミス コンテナー](./cognitive-services-container-support.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
<!--
* Learn more about low code development options for Cognitive Services -->
* [Cognitive Services リソースを作成して構築を始める](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
