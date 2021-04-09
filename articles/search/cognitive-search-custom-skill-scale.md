---
title: カスタム スキルの拡張と管理
titleSuffix: Azure Cognitive Search
description: カスタム スキルを効率的にスケールアウトしてスループットを最大限に高めるためのツールと手法について説明します。 カスタム スキルでは、Azure Cognitive Search で AI によって強化されたインデックス作成パイプラインに追加できるカスタム AI モデルまたはロジックが呼び出されます。
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 22e48239631850d82cbb3e3208748416087da87c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103422118"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>カスタム スキルを効率的にスケールアウトする

カスタム スキルは、特定のインターフェイスを実装する Web API です。 カスタム スキルは、パブリックにアドレス指定可能なリソースに実装できます。 カスタム スキルの最も一般的な実装は次のとおりです。
* カスタム ロジック スキルのための Azure Functions
* コンテナー化されたシンプルな AI スキルのための Azure Web アプリ
* より複雑で大きなスキルのための Azure Kubernetes サービス。

## <a name="prerequisites"></a>前提条件

+ カスタム スキルで実装する必要がある入出力インターフェイスの概要については、[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)に関する記事を参照してください。

+ 環境を設定します。 初めに、[このチュートリアルを最初から最後まで](/python/tutorial-vs-code-serverless-python-01)実行することで、Visual Studio Code と Python の拡張機能を使用してサーバーレス Azure Functions を設定できます。

## <a name="skillset-configuration"></a>スキルセットの構成

インデックス作成プロセスのスループットを最大限に高めるためのカスタム スキルを構成するには、スキル、インデクサーの構成、およびスキルと各ドキュメントの関係について理解しておく必要があります。 たとえば、ドキュメントごとにスキルが呼び出される回数や、呼び出しごとの予想時間などです。

### <a name="skill-settings"></a>スキルの設定

[カスタム スキル](cognitive-search-custom-skill-web-api.md)で、次のパラメーターを設定します。

1. カスタム スキルの `batchSize` を設定して、スキルの 1 回の呼び出しでスキルに送信されるレコードの数を構成します。

2. `degreeOfParallelism` を設定して、インデクサーがスキルに対して実行する同時要求の数を調整します。

3. `timeout` に、スキルが有効な応答で応えるのに十分な値を設定します。

4. `indexer` の定義で、[`batchSize`](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) に、データ ソースから同時に読み取って強化する必要があるドキュメントの数を設定します。

### <a name="considerations"></a>考慮事項

これらの変数を設定してインデクサーのパフォーマンスを最適化する場合、小さな同時要求を多数実行するのと、大きな同時要求を少数実行するのとでは、どちらがスキルの機能をより活かせるかを決定する必要があります。 考慮すべきいくつかの点を次に示します。

* スキル呼び出しのカーディナリティ。 ドキュメントにつきスキルが 1 回の実行されるか (ドキュメント分類スキルなど)。それとも、ドキュメントにつきスキルが複数回実行されるか (段落の分類スキルなど)。

* スキル バッチ サイズに基づいてスキル要求を入力する際に、データソースから平均いくつのドキュメントが読み取られるか。 インデクサーのバッチ サイズよりも小さくするのが理想的です。 バッチサイズが 1 より大きい場合、スキルでは複数のソース ドキュメントからレコードを受け取ることができます。 たとえば、インデクサーのバッチ数が 5、およびスキルのバッチ数が 50 である一方で、各ドキュメントでは 5 つのレコードしか生成されない場合、インデクサーでは複数のインデクサー バッチにまたがる 1 つのカスタム スキル要求を入力する必要があります。

* インデクサー バッチによって生成可能な要求の平均数は、並列処理の次数の最適な設定を示しています。 スキルをホストしているインフラストラクチャで、そのレベルの同時実行がサポートされない場合は、並列処理の次数を下げることを検討してください。 ベスト プラクティスとしては、いくつかのドキュメントを使用して構成をテストし、パラメーターの選択内容を検証します。

* 少量のサンプル ドキュメントを使用したテストでは、ドキュメントの処理にかかった全時間に対し、スキルの実行時間を評価します。 インデクサーでは、バッチを作成する時間とスキルからの応答を待つ時間では、どちらのほうが長いか。 

* 並列処理の上流への影響を考慮してください。 カスタム スキルへの入力が前のスキルからの出力である場合、スキルセット内のすべてのスキルは、待機時間を最小限に抑えるために効率的にスケールアウトされるか。

## <a name="error-handling-in-the-custom-skill"></a>カスタム スキルでのエラー処理

スキルが正常に完了した場合、カスタム スキルでは成功の状態コード HTTP 200 を返す必要があります。 バッチ内の 1 つまたは複数のレコードでエラーが発生した場合は、マルチステータス コード 207 を返すことを検討してください。 レコードのエラーまたは警告の一覧には、適切なメッセージが含まれている必要があります。

バッチ内の項目でエラーが発生すると、対応するドキュメントは失敗します。 ドキュメントを成功させる必要がある場合は、警告を返します。

299 を超える状態コードはエラーとして評価され、すべてのエンリッチメントが失敗し、ドキュメントが失敗します。 

### <a name="common-error-messages"></a>一般的なエラー メッセージ

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.`: 実行時間が長くなるように、スキルに対して timeout パラメーターを設定します。

* `Could not execute skill because Web Api skill response is invalid.`: カスタム スキルの応答形式でメッセージを返さないスキルがあることを示しています。 これは、スキルでキャッチされない例外が原因である可能性があります。

* `Could not execute skill because the Web Api request failed.`: 認証エラーまたは例外が原因である可能性が最も高いです。

* `Could not execute skill.`: 通常は、ドキュメント階層内の既存のプロパティにマップされるスキル応答が原因です。

## <a name="testing-custom-skills"></a>カスタム スキルのテスト

初めに、REST API クライアントを使用してカスタム スキルをテストし、次の点について検証します。

* スキルによって、要求と応答のカスタム スキル インターフェイスが実装されること

* スキルによって MIME の種類 `application/JSON` の有効な JSON が返されること

* 有効な HTTP 状態 コードが返されること

スキルをスキル セットに追加する[デバッグ セッション](cognitive-search-debug-session.md)を作成し、有効なエンリッチメントが実現されることを確認します。 デバッグ セッションでは、スキルのパフォーマンスを調整することはできませんが、スキルが有効な値で構成されていること、および想定の強化されたオブジェクトが返されることは確認できます。

## <a name="best-practices"></a>ベスト プラクティス

* スキルでは、より大きなペイロードを受け入れて返すことができますが、JSON を返す場合は、応答を 150 MB 以下に制限することを検討してください。

* インデクサーとスキルでバッチ サイズを設定して、各データ ソースのバッチでスキルの完全なペイロードが生成されるようにすることを検討してください。

* 長時間実行されるタスクについては、ドキュメントを同時に処理するときにインデクサーでエラーが発生しないように、タイムアウトを高い値に設定します。

* インデクサーのバッチ サイズ、スキルのバッチ サイズ、および並列処理のスキル レベルを最適化して、スキルで想定されるロード パターンを生成したり、大きな要求の数を減らしたり、小さな要求の数を増やしたりします。

* データの可変性が原因で特定の要求が一貫して失敗するというシナリオが発生する可能性があるため、詳細なエラー ログでカスタム スキルを監視します。


## <a name="next-steps"></a>次のステップ
お疲れさまでした。 これで、カスタム スキルが正しく拡張され、インデクサーのスループットを最大限に高めることができました。 

+ [Power Skills: カスタム スキルのリポジトリ](https://github.com/Azure-Samples/azure-search-power-skills)
+ [AI エンリッチメント パイプラインにカスタム スキルを追加する方法](cognitive-search-custom-skill-interface.md)
+ [Azure Machine Learning スキルを追加する](https://docs.microsoft.com/azure/search/cognitive-search-aml-skill)
+ [デバッグ セッションを使用して変更をテストする](https://docs.microsoft.com/azure/search/cognitive-search-debug-session)