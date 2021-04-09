---
title: DevOps for LUIS アプリのテスト
description: DevOps 環境で Language Understanding (LUIS) アプリをテストする方法。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788453"
---
# <a name="testing-for-luis-devops"></a>LUIS DevOps のテスト

Language Understanding (LUIS) アプリを開発するソフトウェア エンジニアは、次のガイドラインに従って、[ソース管理](luis-concept-devops-sourcecontrol.md)、[自動ビルド](luis-concept-devops-automation.md)、[テスト](luis-concept-devops-testing.md)、[リリース管理](luis-concept-devops-automation.md#release-management)に DevOps プラクティスを適用することができます。

アジャイル ソフトウェア開発手法では、テストが、高品質のソフトウェアを構築するうえで不可欠な役割を果たします。 LUIS アプリに対する重要な変更には、開発者がアプリに組み込もうとしている新機能を調べるためのテストが必ず伴います。 これらのテストは、LUIS アプリの `.lu` ソースと共にソース コード リポジトリにチェックインされます。 テストの要件をアプリが満たしたときに変更の実装が完了します。

テストは、[CI/CD ワークフロー](luis-concept-devops-automation.md)の重要な要素です。 LUIS アプリに対する変更が pull request (PR) で提案されたときや、変更がメイン ブランチにマージされた後は、その更新によって回帰が発生していないことを、CI ワークフローでテストして検証する必要があります。

## <a name="how-to-do-unit-testing-and-batch-testing"></a>単体テストとバッチ テストの実施方法

LUIS アプリには、継続的インテグレーション ワークフローで実施すべきテストが 2 種類あります。

- **単体テスト** - LUIS アプリの主要な機能を検証する比較的単純なテスト。 特定のテスト発話に関して期待される意図とエンティティが返されたときに、単体テストは成功となります。 テストの実行が正常に完了するためには、すべての単体テストが成功する必要があります。  
この種のテストは、[LUIS ポータル](https://www.luis.ai/)から実行できる[対話型テスト](./luis-concept-test.md)に似ています。

- **バッチ テスト** - バッチ テストは、現在のトレーニング済みモデルのパフォーマンスを測定するために、そのモデルに関して実行する包括的なテストです。 単体テストとは異なり、バッチ テストは、成功か失敗かを判定するものではありません。 バッチ テストで期待されるのは、期待される意図とエンティティがすべてのテストで返されることではありません。 バッチ テストは、それぞれの意図とエンティティの正確性をアプリで確認したり、時間の経過に沿って改善を比較したりするための助けとなります。  
この種のテストは、LUIS ポータルから対話形式で実行できる[バッチ テスト](./luis-how-to-batch-test.md)と同じです。

単体テストは、プロジェクトの開始時から使用することができます。 バッチ テストは、LUIS アプリのスキーマの開発が完了した後、その正確性の向上に取り組む段階ではじめて真価が発揮されます。

単体テストであれバッチ テストであれ、テスト発話とトレーニング発話は必ず分けてください。 トレーニング時と同じデータでテストを実行した場合、テスト データに対してオーバーフィットしているだけであるにもかかわらず、アプリが正常に動作しているかのように見えてしまいます。 モデルが適切に一般化されていることを確かめるためには、モデルにとってテストが未知のものである必要があります。

### <a name="writing-tests"></a>テストを作成する

一連のテストを作成する際は、各テストについて、次の情報を定義する必要があります。

* テスト発話
* 期待される意図
* 期待されるエンティティ

JSON 形式のファイルでテストのグループを定義するには、LUIS [バッチ ファイル構文](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities)を使用します。 次に例を示します。

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

[NLU.DevOps](https://github.com/microsoft/NLU.DevOps) など、一部のテスト ツールでは、LUDown 形式のテスト ファイルもサポートされます。

#### <a name="designing-unit-tests"></a>単体テストを設計する

単体テストは、LUIS アプリの主要な機能をテストするように設計されている必要があります。 アプリ開発の各イテレーション (スプリント) において、そこで実装しようとしている主要な機能が正しく動作していることを確認するうえで十分な数のテストを作成する必要があります。

単体テストではそれぞれ、特定のテスト発話に関して次のことを実行できます。

* 正しい意図が返されるかどうかをテストする。
* "主要" エンティティ (ソリューションにとって不可欠なエンティティ) が返されるかどうかをテストする。
* 意図とエンティティの[予測スコア](./luis-concept-prediction-score.md)が、定義したしきい値を超えているかどうかをテストする。 たとえば、テストが成功したと見なすのは、意図と主要エンティティの予測スコアが 0.75 を上回る場合のみ、とすることができます。

単体テストでは、予測応答で主要エンティティが返されるかどうかをテストしますが、擬陽性は無視することをお勧めします。 "*擬陽性*" とは、予測応答で検出されたエンティティのうち、テストに期待される結果としては定義されていないものをいいます。 擬陽性を無視することで、単体テストの作成に伴う負担を減らしつつ、ソリューションにとって重要なデータが予測応答で返されるかどうかのテストに専念することができます。

> [!TIP]
> [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) ツールは、LUIS テストのあらゆるニーズに対応します。 `compare` コマンドを[単体テスト モード](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)で使用した場合、すべてのテストが成功するようアサートされ、期待される結果としてラベル付けされていないエンティティに対する擬陽性の結果は無視されます。

#### <a name="designing-batch-tests"></a>バッチ テストを設計する

バッチ テスト セットには、LUIS アプリに含まれるすべての意図とすべてのエンティティをテストするように設計された多数のテスト ケースが含まれている必要があります。 バッチ テスト セットの定義については、[LUIS ポータルでのバッチ テスト](./luis-how-to-batch-test.md)に関するページを参照してください。

### <a name="running-tests"></a>テストを実行する

LUIS ポータルには、対話型テストを支援する機能が用意されています。

* [**対話型テスト**](./luis-concept-test.md)では、サンプル発話を送信して、LUIS によって認識された意図とエンティティの応答を受け取ることができます。 テストの成功は、目視で確認します。

* [**バッチ テスト**](./luis-how-to-batch-test.md)では、バッチ テスト ファイルを入力に使用して、アクティブなトレーニング済みバージョンを検証し、その予測の正確性を測定できます。 バッチ テストは、アクティブなバージョンに含まれる各意図やエンティティの精度を表示するのに役立ち、結果がグラフで表示されます。

#### <a name="running-tests-in-an-automated-build-workflow"></a>自動ビルド ワークフローでテストを実行する

LUIS ポータルの対話型テスト機能は便利ですが、DevOps に関して CI/CD ワークフローで実行される自動テストには特定の要件があります。

* テスト ツールは、ビルド サーバー上のワークフロー ステップで実行する必要があります。 つまり、コマンド ラインからツールを実行できる必要があります。
* テスト ツールは、特定のエンドポイントに対して一連のテストを実行し、実際の結果と照らして、期待される結果を自動的に検証できる必要があります。
* テストに失敗した場合、テスト ツールは、状態コードを返してワークフローを停止させ、"ビルドを失敗" させる必要があります。

これらの機能を備えたコマンドライン ツールや高水準の API は、LUIS には用意されていません。 CI/CD ワークフロー内の自動テスト時とコマンド ラインの両方で、[NLU.DevOps](https://github.com/microsoft/NLU.DevOps) ツールを使用してテストを実行し、結果を検証するようお勧めします。

LUIS ポータルから利用できるテスト機能は、LUIS 作成機能の一部であり、公開されたエンドポイントは必要ありません。 自動ビルド ワークフローにテストを導入する場合は、NLU.DevOps などのテスト ツールがテストの過程で予測要求を送信できるよう、テスト対象の LUIS アプリ バージョンをエンドポイントに発行する必要があります。

> [!TIP]
> * 独自のテスト ソリューションを実装し、テスト発話をエンドポイントに送信するコードをご自身で作成する場合、LUIS オーサリング キーの使用時は、許可されるトランザクション レートは 5 TPS に制限されることに注意してください。 送信速度をスロットルするか、予測キーを使用してください。
> * テスト クエリをエンドポイントに送信する際は、予測要求のクエリ文字列には必ず `log=false` を使用してください。 テスト発話が LUIS により記録され、LUIS の[アクティブ ラーニング](./luis-concept-review-endpoint-utterances.md)機能によって提示されるエンドポイント発話レビュー リストに登録されて、その結果、意図せずアプリのトレーニング発話に追加されるのを防ぐことができます。

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>コマンド ラインと CI/CD ワークフローで単体テストを実行する

[NLU.DevOps](https://github.com/microsoft/NLU.DevOps) パッケージを使用すると、コマンド ラインからテストを実行することができます。

* テスト ファイルからエンドポイントにテストを送信し、実際の予測結果をファイルにキャプチャするには、NLU.DevOps の [test コマンド](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)を使用します。
* 入力テスト ファイルに定義されている期待される結果に対して実際の結果を比較するには、NLU.DevOps の [compare コマンド](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)を使用します。 `compare` コマンドは、NUnit テスト出力を生成します。`--unit-test` フラグを使用して[単体テスト モード](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)で使用した場合、すべてのテストが成功するようアサートされます。

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>コマンド ラインと CI/CD ワークフローでバッチ テストを実行する

NLU.DevOps パッケージを使用して、コマンド ラインからバッチ テストを実行することもできます。

* 単体テストと同様、テスト ファイルからエンドポイントにテストを送信し、実際の予測結果をファイルにキャプチャするには、NLU.DevOps の [test コマンド](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)を使用します。
* アプリのパフォーマンスを測定するには、NLU.DevOps の [compare コマンド](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)を[パフォーマンス テスト モード](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode)で使用します。ベースライン パフォーマンス ベンチマーク (メイン リリースまたは現行リリースに対する直近のコミットから得た結果など) に対してアプリのパフォーマンスを比較することもできます。 パフォーマンス テスト モードの `compare` コマンドからは、NUnit テスト出力と[バッチ テスト結果](./luis-glossary.md#batch-test)が JSON 形式で生成されます。

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS の非決定論的トレーニングとテストへの影響

LUIS は、モデル (意図など) をトレーニングするとき、陽性のデータ (モデルのアプリをトレーニングする目的で与えた、ラベル付けされたトレーニング発話) と陰性のデータ (そのモデルの使用例として有効 "*ではない*" データ) の両方を必要とします。 トレーニング中 LUIS は、モデルの陰性データを、他のモデル用に与えられたすべての陽性データから構築しますが、場合によっては、そこからデータの不均衡が生じることがあります。 その不均衡を回避するために、LUIS は、非決定論的な方法で陰性データのサブセットをサンプリングし、トレーニング セットの不均衡の改善、モデルのパフォーマンス向上、トレーニング時間の短縮に向けた最適化を行います。

この非決定論的トレーニングの結果、一般に[予測スコア](./luis-concept-prediction-score.md)が高くない意図またはエンティティに関して、[異なるトレーニング セッション間でわずかに異なる予測応答](./luis-concept-prediction-score.md)が得られる場合があります。

テスト目的で作成している LUIS アプリ バージョンについて、非決定論的トレーニングを無効にしたい場合は、`UseAllTrainingData` を `true` に設定して、[バージョン設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) を使用してください。

## <a name="next-steps"></a>次のステップ

* [CI/CD ワークフローの導入](luis-concept-devops-automation.md)について学習します。
* [GitHub で DevOps for LUIS を実装する](luis-how-to-devops-with-github.md)方法を学習します。