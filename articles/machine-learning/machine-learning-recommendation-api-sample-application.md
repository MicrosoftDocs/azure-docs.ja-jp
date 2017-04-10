---
title: "Machine Learning の Recommendations API の一般的な操作 | Microsoft Docs"
description: "Azure ML Recommendation サンプル アプリケーション"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 0220bc17-3315-47d7-84a3-ef490263a343
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: luisca
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-datamarket-deprecation
translationtype: Human Translation
ms.sourcegitcommit: 29c718d0c34d1e2f9d17b285a7270541a9ff15cf
ms.openlocfilehash: 9afbae9b7c24c72cbb2ef64e693f7a317dc81a46
ms.lasthandoff: 02/24/2017


---
# <a name="recommendations-api-sample-application-walkthrough"></a>サンプル アプリケーションを使用した Recommendations API のチュートリアル
> [!NOTE]
> このバージョンの代わりに Recommendations API Cognitive Service の使用を開始することをお勧めします。 このサービスは Recommendations Cognitive Service に置き換えられ、新機能はすべて Cognitive Service で開発されるようになります。 Cognitive Service には、バッチ処理のサポート、API エクスプローラーの改善、API サーフェスの簡素化、より一貫性のあるサインアップおよび課金方法などの新機能が含まれています。
> 詳細については、「 [Migrating to the new Cognitive Service](http://aka.ms/recomigrate)
> 
> 

## <a name="purpose"></a>目的
このドキュメントでは、 [サンプル アプリケーション](https://code.msdn.microsoft.com/Recommendations-144df403)を使用して、Machine Learning の Recommendations API を使用する方法を示しています。

このアプリケーションは、すべての機能や API の使用を意図したものではありません。 Machine Learning の Recommendation サービスを初めて使用する際に実行する一般的な操作の一部を示します。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="introduction-to-machine-learning-recommendation-service"></a>Machine Learning Recommendation サービスの概要
Machine Learning Recommendation サービスを使用した推奨機能は、次のデータに基づいて推奨モデルを構築する際に有効です。

* 推奨する項目のリポジトリ (カタログ)
* ユーザーまたはセッションごとの項目の使用状況を表すデータ (これは、サンプル アプリケーションの一部ではなく、データ取得を使用して時間をかけて取得できます)

推奨モデルが構築されたら、それを使用して、ユーザーが選択する一連の項目 (または&1; つの項目) に従って、ユーザーが関心のある項目を予測できます。

前のシナリオを使用するには、Azure Machine Learning の Recommendation サービスで次の操作を実行します。

* モデルの作成: これは、データ (カタログと使用状況) と予測モデルを保持する論理コンテナーです。 各モデルのコンテナーは、作成時に割り当てられた一意の ID を使用して識別されます。 この ID はモデル ID と呼ばれ、API のほとんどで使用されます。 
* カタログへのアップロード: モデルのコンテナーが作成されると、それをカタログに関連付けることができます。

**注**: モデルの作成 およびカタログへのアップロードは通常、モデルのライフ サイクルで&1; 回実行されます。

* 使用状況のアップロード: 使用状況データをモデル コンテナーに追加します。
* 推奨モデルの構築: 必要なデータが取得されたら、推奨モデルが構築できます。 この操作では、最高の Machine Learning アルゴリズムを使用して、推奨モデルを作成します。 各ビルドは一意の ID に関連付けられています。 一部の API の機能に必要になるため、この ID の記録を保持する必要があります。
* 構築プロセスの監視: 推奨モデルのビルドは非同期操作で、データ (カタログおよび使用状況) の量およびビルド パラメーターに応じて数分から数時間かかります。 そのため、ビルドを監視する必要があります。 推奨モデルは、関連付けられているビルドが正常に終了した場合の作成されます。
* (省略可能) アクティブな推奨モデル ビルドを選択します。この手順は、モデルのコンテナー内に複数の推奨モデル ビルドがある場合のみ必須です。 アクティブな推奨モデルを指定しないで推奨を取得するよう要求すると、システムによって既定のアクティブなビルドに自動的にリダイレクトされます。 

**注**: アクティブな推奨モデルは運用環境で使用でき、運用ワークロード用に構築されます。 逆に非アクティブな推奨設定のモデルはテスト環境 (ステージングとも呼ばれます) に留まります。

* 推奨の取得: 推奨モデルを作成すると、選択した&1; つまたは一連の項目に対する推奨をトリガーすることができます。 

通常は、一定時間の間、推奨の取得を呼び出します。 その間、使用状況データを Machine Learning の推奨システムにリダイレクトすることができます。システムでは、このデータを指定されたコンテナーのモデルに追加します。 十分な使用状況データが取得されたら、新しい推奨モデルを構築してより多くの実際のデータを使用できます。 

## <a name="prerequisites"></a>前提条件
* Visual Studio 2013 以降
* インターネットへのアクセス 
* Recommendations API (https://datamarket.azure.com/dataset/amla/recommendations) のサブスクリプション。

## <a name="azure-machine-learning-sample-app-solution"></a>Azure Machine Learning サンプル アプリケーション ソリューション
このソリューションには、ソース コード、サンプルの使用状況とカタログのファイル、およびコンパイルに必要なパッケージをダウンロードするディレクティブが含まれています。

## <a name="the-apis-used"></a>使用される API
アプリケーションは、使用可能な API のサブセットを通じて、Machine Learning Recommendation 機能を使用します。 次の API がアプリケーションに示されています。

* モデルの作成: 論理のコンテナーを作成し、データと推奨モデルを保持します。 モデルは名前によって識別されます。同じ名前のモデルを&2; 回作成することはできません。
* カタログ ファイルのアップロード: カタログ データのアップロードに使用します。
* 使用状況ファイルのアップロード： 使用状況データのアップロードに使用します。
* ビルドのトリガー: 推奨モデルの作成に使用します。
* ビルド実行の監視: 推奨モデル ビルドの状態の監視に使用します。
* 推奨のビルド モデルの選択: 特定のモデル コンテナーに対して既定で使用する推奨モデルを示します。 この手順は、複数の推奨モデルが存在し、非アクティブなビルドをアクティブな推奨モデルとして使用する場合にのみ必要です。
* 推奨の取得: 与えられた&1; つまたは一連の項目に応じて、推奨項目を取得するために使用します。 

API の詳細については、Microsoft Azure Marketplace のドキュメントをご覧ください。 

**注**: モデルには (同時にではなく) 時間の経過と共に複数のビルドが含まれます。 各ビルドは、同じまたは更新されたカタログ、追加の使用状況データで作成されています。

## <a name="common-pitfalls"></a>よくある落とし穴
* サンプル アプリケーションを実行するためには、ユーザー名と Microsoft Azure Marketplace のプライマリ アカウント キーを提供する必要があります。
* サンプル アプリケーションを連続して実行すると失敗します。 アプリケーション フローには、作成、アップロード、ビルドの監視、事前定義されたモデルからの推奨の取得が含まれ、呼び出しの間でモデル名を変更しないと、連続した実行は失敗します。
* 推奨はデータなしで終了する可能性があります。 サンプル アプリケーションは、ごく小さいカタログと使用状況ファイルを使用します。 よってカタログの項目の一部には推奨項目が返されません。

## <a name="disclaimer"></a>免責事項
サンプル アプリケーションは、運用環境での実行を意図していません。 カタログに提供されたデータはごく小さく、意味のある推奨モデルとはなりませんが、 デモの目的でのみ提供されています。 


