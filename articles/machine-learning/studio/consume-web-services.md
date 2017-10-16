---
title: "Azure Machine Learning Web サービスを使用する方法 | Microsoft Docs"
description: "機械学習サービスがデプロイされると、利用可能になっている RESTFul Web サービスを、リアルタイムの要求応答サービスまたはバッチ実行サービスのいずれかとして使用できます。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.author: garye
ms.openlocfilehash: fc1152f1431474b6625f389a1290a121e86fbdac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Azure Machine Learning Web サービスを使用する方法

Azure Machine Learning の予測モデルを Web サービスとしてデプロイすると、REST API を使用してデータを送信し、予測を取得することができます。 リアルタイムまたはバッチ モードでデータを送信できます。

Machine Learning Studio を使用して Machine Learning Web サービスを作成してデプロイする方法の詳細については、次を参照してください。

* Machine Learning Studio で実験を作成する方法については、 [初めての実験を作成する方法のチュートリアル](create-experiment.md)をご覧ください。
* Web サービスをデプロイする方法の詳細については、「 [Azure Machine Learning Web サービスをデプロイする](publish-a-machine-learning-web-service.md)」をご覧ください。
* Machine Learning 全般の詳細については、 [Machine Learning ドキュメント センター](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>概要
Azure Machine Learning Web サービスを使用して、外部のアプリケーションが Machine Learning のワークフローのスコア付けモデルとリアルタイムで通信します。 Machine Learning Web サービスの呼び出しは、予測結果を外部のアプリケーションに返します。 Machine Learning Web サービスの呼び出しを実行するために、予測のデプロイ時に作成される API キーを渡します。 Machine Learning Web サービスは、Web プログラミング プロジェクトでよく選択されるアーキテクチャの REST に基づいています。

Azure Machine Learning には、2 種類のサービスがあります。

* 要求応答サービス (RRS) – 待ち時間が短く拡張性の高い、Machine Learning Studio から作成およびデプロイされるステートレスなモデルへのインターフェイスを提供するサービス。
* バッチ実行サービス (BES) – データ レコードのバッチをスコア付けする非同期のサービス。

Machine Learning Web サービスの詳細については、「[Azure Machine Learning Web サービスをデプロイする](publish-a-machine-learning-web-service.md)」をご覧ください。

## <a name="get-an-azure-machine-learning-authorization-key"></a>Azure Machine Learning の承認キーを取得する
実験をデプロイすると、Web サービスの API キーが生成されます。 複数の場所からキーを取得できます。

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Microsoft Azure Machine Learning Web サービス ポータルを使用する
[Microsoft Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにサインインする

新しい Machine Learning Web サービスの API キーを取得するには:

1. Azure Machine Learning Web サービス ポータルで、上部メニューの **[Web サービス]** をクリックします。
2. キーを取得する Web サービスをクリックします。
3. 上部のメニューで **[Consume(使用)]**をクリックします。
4. **主キー**をコピーして保存します。

従来の Machine Learning Web サービスの API キーを取得するには:

1. Azure Machine Learning Web サービス ポータルで、上部メニューの **[Classic Web Services (クラシック Web サービス)]** をクリックします。
2. 使用する Web サービスをクリックします。
3. キーを取得するエンドポイントをクリックします。
4. 上部のメニューで **[Consume(使用)]**をクリックします。
5. **主キー**をコピーして保存します。

### <a name="classic-web-service"></a>従来の Web サービス
 Machine Learning Studio または Azure クラシック ポータルからクラシック Web サービスのキーを取得することもできます。

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. Machine Learning Studio で、左側の **[Web サービス]** をクリックします。
2. Web サービスをクリックします。 **[ダッシュボード]** タブに **[API キー]** があります。

#### <a name="azure-classic-portal"></a>Azure クラシック ポータル
1. 左側の **[Machine Learning]** をクリックします。
2. Web サービスがあるワークスペースをクリックします。
3. **[Web サービス]**をクリックします。
4. Web サービスをクリックします。
5. エンドポイントをクリックします。 [API キー] が右下にあります。

## <a id="connect"></a>Machine Learning Web サービスに接続する
HTTP 要求と応答をサポートする任意のプログラミング言語を使用して、Machine Learning Web サービスに接続することができます。 Machine Learning Web サービス ヘルプ ページから、C#、Python、および R の例を表示できます。

**Machine Learning API ヘルプ** Machine Learning API ヘルプは、Web サービスをデプロイするときに作成されます。 「 [Azure Machine Learning チュートリアル - Web サービスをデプロイする](walkthrough-5-publish-web-service.md)」を参照してください。
Machine Learning API ヘルプには、予測 Web サービスの詳細が含まれます。

1. 使用する Web サービスをクリックします。
2. API のヘルプ ページを表示するエンドポイントをクリックします。
3. 上部のメニューで **[Consume(使用)]**をクリックします。
4. [要求/応答] または [バッチの実行] の **[API ヘルプ ページ]** をクリックします。

**新しい Web サービスの Machine Learning API ヘルプを表示するには**

Azure Machine Learning Web Services ポータルで:

1. 上部のメニューで **[Web サービス]** をクリックします。
2. キーを取得する Web サービスをクリックします。

**[Consume (使用)]** をクリックして、[要求/応答] および [バッチの実行] サービスの URI と、C#、R、および Python のサンプル コードを取得します。

**Swagger API** をクリックして、指定した URI から呼び出される API の Swagger ベースのドキュメントを取得します。

### <a name="c-sample"></a>C# のサンプル
Machine Learning Web サービスに接続するには、ScoreData を渡す **HttpClient** を使用します。 ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。 Machine Learning サービスの認証には、API キーを使用します。

Machine Learning Web サービスに接続するには、**Microsoft.AspNet.WebApi.Client** NuGet パッケージをインストールする必要があります。

**Microsoft.AspNet.WebApi.Client NuGet in Visual Studio をインストールする**

1. 「UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」Web サービスを発行します。
2. **[ツール]** > **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順にクリックします。
3. **[Install-Package Microsoft.AspNet.WebApi.Client]**を選択します。

**サンプル コードを実行するには**

1. Machine Learning サンプル コレクションに含まれる「サンプル 1: UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」実験を発行します。
2. Web サービスからのキーを持つ apiKey を割り当てます。 前述の「 **Azure Machine Learning の承認キーを取得する** 」をご覧ください。
3. 要求の URI を含む serviceUri を割り当てます。

### <a name="python-sample"></a>Python サンプル
Machine Learning Web サービスに接続するには、ScoreData を渡す **urllib2** ライブラリを使用します。 ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。 Machine Learning サービスの認証には、API キーを使用します。

**サンプル コードを実行するには**

1. Machine Learning サンプル コレクションに含まれる「サンプル 1: UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」実験をデプロイします。
2. Web サービスからのキーを持つ apiKey を割り当てます。 この記事の冒頭の「**Azure Machine Learning の承認キーを取得する**」セクションをご覧ください。
3. 要求の URI を含む serviceUri を割り当てます。

