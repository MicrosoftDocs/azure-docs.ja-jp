---
title: LUIS アプリの継続的ワークフロー
description: Language Understanding (LUIS) 用に DevOps の CI/CD ワークフローを実装する方法。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932530"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>LUIS DevOps の継続的インテグレーションと継続的デリバリーのワークフロー

Language Understanding (LUIS) アプリを開発するソフトウェア エンジニアは、[ソース管理](luis-concept-devops-sourcecontrol.md)、[自動ビルド](luis-concept-devops-automation.md)、[テスト](luis-concept-devops-testing.md)、[リリース管理](luis-concept-devops-automation.md#release-management)に DevOps プラクティスを適用できます。 この記事では、LUIS で自動ビルドを実装するための概念について説明します。

## <a name="build-automation-workflows-for-luis"></a>LUIS のビルド自動化ワークフロー

![CI ワークフロー](./media/luis-concept-devops-automation/luis-automation.png)

ソース コード管理 (SCM) システムで、次のイベント時に実行されるように自動ビルド パイプラインを構成します。

1. [Pull request](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) が発生したときにトリガーされる **PR ワークフロー**。 このワークフローでは、更新がメイン ブランチにマージされる *前に* PR の内容を検証します。
1. メイン ブランチに更新がプッシュされたとき (たとえば、PR からの変更をマージしたとき) にトリガーされる **CI/CD ワークフロー**。 このワークフローでは、メイン ブランチに対するすべての更新の品質を保証します。

**CI/CD ワークフロー** では、2 つの補完的な開発プロセスを組み合わせます。

* [継続的インテグレーション](/azure/devops/learn/what-is-continuous-integration) (CI) は、共有リポジトリでコードを頻繁にコミットし、それに対して自動ビルドを実行するエンジニアリング手法です。 自動[テスト](luis-concept-devops-testing.md)のアプローチと継続的インテグレーションを組み合わせると、更新のたびに LUDown ソースがまだ有効であり LUIS アプリにインポートできることに加えて、ソリューションに必要な意図とエンティティがトレーニング済みアプリで認識できることを検証する一連のテストにそれが合格することを検証できます。

* [継続的デリバリー](/azure/devops/learn/what-is-continuous-delivery) (CD) では、より詳細なテストを実行できる環境にアプリケーションを自動的にデプロイするために、継続的インテグレーションの概念をさらに取り入れます。 CD を使用すれば、変更によって起きる予期しない問題について、できるだけ早く知ることができ、テスト カバレッジのギャップについても知ることができます。

継続的インテグレーションと継続的デリバリーの目標は、"メインはいつでもリリース可能な状態である" ことの保証です。 LUIS アプリの場合、これは、必要であればメイン ブランチの LUIS アプリから任意のバージョンを取得して運用環境にリリースできるという意味です。

### <a name="tools-for-building-automation-workflows-for-luis"></a>LUIS の自動化ワークフローを構築するためのツール

ビルド自動化ワークフローの作成に使用できる、さまざまなビルド自動化テクノロジがあります。 これらすべてのテクノロジで必要なのは、コマンド ライン インターフェイス (CLI) または REST 呼び出しを使用してステップをスクリプト化し、ビルド サーバーで実行できることです。

LUIS のビルド自動化ワークフローには、次のツールを使用します。

* [Bot Framework Tools LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) では、LUIS アプリとバージョンを操作し、それらを LUIS サービス内でトレーニング、テスト、発行します。

* [Azure CLI](/cli/azure/) では、Azure サブスクリプションのクエリ、LUIS オーサリング キーと予測キーの取得、自動化認証に使用される Azure [サービス プリンシパル](/cli/azure/ad/sp)の作成を行います。

* [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) ツールでは、[LUIS アプリをテスト](luis-concept-devops-testing.md)し、テスト結果を分析します。

### <a name="the-pr-workflow"></a>PR ワークフロー

既に説明したように、機能ブランチからメイン ブランチにマージする変更を提案するために開発者が PR を提起すると実行されるように、このワークフローを構成します。 その目的は、メイン ブランチにマージされる前に、PR での変更の品質を検証することです。

このワークフローは次のようになります。

* PR で `.lu` ソースをインポートして、一時的な LUIS アプリを作成します。
* LUIS アプリ バージョンをトレーニングし、発行します。
* それに対してすべての[単体テスト](luis-concept-devops-testing.md)を実行します。
* すべてのテストに合格した場合はワークフローを合格とし、それ以外の場合は不合格とします。
* 一時的なアプリをクリーンアップして削除します。

SCM でサポートされている場合、このワークフローが正常に完了しない限り PR を完了できないというブランチ保護ルールを構成します。

### <a name="the-main-branch-cicd-workflow"></a>メイン ブランチの CI/CD ワークフロー

PR での更新がメイン ブランチにマージされた後に実行されるように、このワークフローを構成します。 その目的は、更新をテストすることにより、メイン ブランチの高い品質基準を維持することです。 更新が品質基準を満たしている場合、このワークフローにより、新しい LUIS アプリ バージョンが、より詳細なテストを実行できる環境にデプロイされます。

このワークフローは次のようになります。

* 更新されたソース コードを使用して、プライマリ LUIS アプリ (メイン ブランチ用に保守するアプリ) で新しいバージョンをビルドします。

* LUIS アプリ バージョンをトレーニングし、発行します。

  > [!NOTE]
  > [自動ビルド ワークフローでのテストの実行](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow)に関するページで説明しているように、テスト中の LUIS アプリ バージョンを発行して、NLU.DevOps などのツールがアクセスできるようにする必要があります。 LUIS で LUIS アプリ用にサポートされている名前付き発行スロットは *staging* と *production* の 2 つだけですが、[バージョンを直接発行](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)して [バージョンによるクエリ](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name)を実行することもできます。 名前付き発行スロットを使用するという制限を回避するには、自動化ワークフローで直接バージョン発行を使用します。

* すべての[単体テスト](luis-concept-devops-testing.md)を実行します。

* 必要に応じて[バッチ テスト](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing)を実行して、LUIS アプリ バージョンの品質と精度を測定したり、LUIS アプリ バージョンを何らかのベースラインと比較したりします。

* テストが正常に完了した場合:
  * リポジトリでソースにタグを付けます。
  * 継続的デリバリー (CD) ジョブを実行して、さらなるテストのための環境に LUIS アプリ バージョンをデプロイします。

### <a name="continuous-delivery-cd"></a>継続的デリバリー (CD)

CI/CD ワークフローの CD ジョブは、ビルドと自動ユニット テストの成功時に条件付きで実行されます。 そのジョブは、さらなるテストを実行できる環境に LUIS アプリケーションを自動的にデプロイするためのものです。

いかに最適に LUIS アプリをデプロイするかに関する 1 つの推奨ソリューションは存在せず、プロジェクトに適したプロセスを実装する必要があります。 [LUIS DevOps テンプレート](https://github.com/Azure-Samples/LUIS-DevOps-Template) リポジトリでは、このための簡易なソリューションとして、*production* 発行スロットに [新しい LUIS アプリ バージョンを発行する](./luis-how-to-publish-app.md)という動作を実装しています。 簡易なセットアップでは、これで問題ありません。 ただし、*開発*、*ステージング*、*UAT* など、さまざまな運用環境を同時にサポートする必要がある場合、アプリごとに 2 つの名前付き発行スロットという制限は不十分さを露呈します。

アプリ バージョンをデプロイするためのその他のオプションには、以下のものがあります。

* アプリ バージョンを直接バージョン エンドポイントに発行したままにし、下流の運用環境に直接バージョン エンドポイントを必要に応じて構成するプロセスを実装します。
* 運用環境ごとに異なる LUIS アプリを保守します。また、LUIS アプリをトレーニングして発行するために、デプロイ先の運用環境の LUIS アプリで `.lu` を新しいバージョンにインポートするための自動化ステップを記述します。
* テストした LUIS アプリ バージョンを [LUIS Docker コンテナー](./luis-container-howto.md?tabs=v3)にエクスポートし、LUIS コンテナーを Azure [Container instances](../../container-instances/index.yml) にデプロイします。

## <a name="release-management"></a>リリース管理

一般に、継続的デリバリーは、開発やステージングなど、運用以外の環境に対してのみ実行することを推奨します。 ほとんどのチームでは、運用環境へのデプロイに手動のレビューと承認プロセスが必要です。 運用環境へのデプロイの場合、必ず開発チームの主要担当者がサポートできるとき、またはトラフィックの少ない時間帯に実行することをお勧めします。

## <a name="next-steps"></a>次のステップ

* [GitHub で DevOps for LUIS を実装する](luis-how-to-devops-with-github.md)方法を学ぶ
* [NLU.DevOps を使用した GitHub Actions ワークフロー](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)を記述する方法を学ぶ
