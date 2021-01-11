---
title: GitHub で DevOps を適用する方法 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) と GitHub を使用して DevOps を適用します。
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 6df65040277ac61cca5fb4bf7fce5b5a7b2f3afe
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783662"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>GitHub Actions を使用して LUIS アプリ開発に DevOps を適用する

LUIS の DevOps およびソフトウェア エンジニアリングのベスト プラクティスを実装する完全なソリューションについては、[LUIS DevOps テンプレート リポジトリ](https://github.com/Azure-Samples/LUIS-DevOps-Template)にアクセスしてください。 このテンプレート リポジトリを使用すると、CI/CD ワークフローとプラクティスの組み込みサポートを備えた独自のリポジトリを作成できます。これにより、独自のプロジェクトで LUIS を使用した[ソース管理](luis-concept-devops-sourcecontrol.md)、[自動ビルド](luis-concept-devops-automation.md)、[テスト](luis-concept-devops-testing.md)、および[リリース管理](luis-concept-devops-automation.md#release-management)が可能になります。

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps テンプレート リポジトリ

[LUIS DevOps テンプレート リポジトリ](https://github.com/Azure-Samples/LUIS-DevOps-Template)では、次を行う方法を説明しています。

* **テンプレート リポジトリの複製** - テンプレートを独自の GitHub リポジトリにコピーします。
* **LUIS リソースの構成** - 継続的インテグレーション ワークフローによって使用される [LUIS 作成と予測リソースを Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-resources-in-azure-cli) で作成します。
* **CI/CD ワークフローの構成** - CI/CD ワークフローのパラメーターを構成し、[GitHub シークレット](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)に保存します。
* **["開発者の内部ループ"](https://mitchdenny.com/the-inner-loop/) の手順の確認** - 開発者は、開発ブランチで作業している間にサンプル LUIS アプリのアップデートを行い、アップデートをテストした後、変更を提案してレビューの承認を求めるための pull request を出します。
* **CI/CD ワークフローの実行** - [継続的インテグレーション ワークフローを実行し、GitHub アクションを使用して LUIS アプリをビルドしてテスト](luis-concept-devops-automation.md)します。
* **自動テストの実行** - アプリの品質を評価するために、[LUIS アプリの自動バッチ テスト](luis-concept-devops-testing.md)を実行します。
* **LUIS アプリの展開** - [継続的デリバリー (CD) ジョブ](luis-concept-devops-automation.md#continuous-delivery-cd)を実行して、LUIS アプリを公開します。
* **独自のプロジェクトでリポジトリを使用する** - 独自の LUIS アプリケーションでリポジトリを使用する方法について説明します。

## <a name="next-steps"></a>次のステップ

* 独自のプロジェクトに DevOps を適用するには、[LUIS DevOps テンプレート リポジトリ](https://github.com/Azure-Samples/LUIS-DevOps-Template)を使用します。
* [LUIS のソース管理およびブランチ戦略](luis-concept-devops-sourcecontrol.md)
* [LUIS DevOps のテスト](luis-concept-devops-testing.md)
* [LUIS DevOps の自動化ワークフロー](luis-concept-devops-automation.md)
