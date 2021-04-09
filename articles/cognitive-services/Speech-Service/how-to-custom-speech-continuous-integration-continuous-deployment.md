---
title: Custom Speech の CI/CD - 音声サービス
titleSuffix: Azure Cognitive Services
description: Custom Speech および CI/CD ワークフローを使用して DevOps を適用します。 独自のプロジェクトに既存の DevOps ソリューションを実装します。
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f7f11ef097d3abee2b4c18c32a1cb215d9fe8ce8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939892"
---
# <a name="cicd-for-custom-speech"></a>Custom Speech の CI/CD

トレーニングおよびテスト データに更新を適用するときに、Custom Speech モデルの継続的な改善が可能になるように、自動トレーニング、テスト、リリース管理を実装します。 CI/CD ワークフローを効果的に実装することで、確実に最高のパフォーマンスの Custom Speech モデルのエンドポイントを常に使用できるようになります。

[継続的インテグレーション](/azure/devops/learn/what-is-continuous-integration) (CI) は、共有リポジトリで更新を頻繁にコミットし、それに対して自動ビルドを実行するエンジニアリング手法です。 Custom Speech の CI ワークフローでは、データ ソースから新しいモデルがトレーニングされ、新しいモデルに対して自動テストが実行されて、確実に以前のモデルよりも優れたパフォーマンスを発揮するようにします。

[継続的デリバリー](/azure/devops/learn/what-is-continuous-delivery) (CD) では、CI プロセスからモデルが受け取られ、改善された Custom Speech モデルごとにエンドポイントが作成されます。 CD を使用すると、エンドポイントをソリューションに簡単に統合できます。

カスタム CI/CD ソリューションも使用できますが、堅牢で事前に構築されたソリューションの場合は、GitHub Actions を使用して CI/CD ワークフローを実行する [Speech DevOps テンプレート リポジトリ](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)をお使いください。

## <a name="cicd-workflows-for-custom-speech"></a>Custom Speech の CI/CD ワークフロー

これらのワークフローの目的は、各 Custom Speech モデルの認識の正確性を確実に以前のビルドよりも向上させることです。 テスト データやトレーニング データの更新によって正確性が向上する場合、これらのワークフローによって新しい Custom Speech エンドポイントが作成されます。

GitHub や Azure DevOps などの Git サーバーを使用すると、マージや pull request などの特定の Git イベントが発生したときに、自動ワークフローを実行できます。 たとえば、テスト データの更新が "*メイン*" ブランチにプッシュされたときに、CI ワークフローをトリガーできます。 Git サーバーによってツールも異なりますが、ビルド サーバー上で実行されるように、スクリプト コマンドライン インターフェイス (CLI) コマンドを作成することができます。

その過程で、ワークフローでは、元のコミットまたはバージョンまでさかのぼって追跡できるように、データ、テスト、テスト ファイル、モデル、エンドポイントに名前を付けて保存するようにします。 また、このような資産に名前を付けると、テスト データとトレーニング データを更新した後に作成された資産を簡単に確認できるようになります。

### <a name="ci-workflow-for-testing-data-updates"></a>データの更新をテストするための CI ワークフロー

CI/CD ワークフローの主な目的は、トレーニング データを使用して新しいモデルを構築することと、テスト データを使用してそのモデルをテストし、[ワード エラー率](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) がそれまでの最高のパフォーマンスのモデル ("ベンチマーク モデル") と比較して改善されたかどうかを確認することです。 新しいモデルのパフォーマンスが向上すると、新しいモデルが以降のモデルと比較される新しいベンチマーク モデルになります。

データの更新をテストするための CI ワークフローでは、更新されたテスト データを使用して現在のベンチマーク モデルを再テストし、変更された WER を計算する必要があります。 こうすることで、新しいモデルの WER とベンチマークの WER を比較するときに、両方のモデルが同じテスト データに対してテストされ、同じように比較されていることが保証されます。

このワークフローは、テスト データの更新時にトリガーされます。

- 更新されたテスト データに対してベンチマーク モデルをテストします。
- 更新されたデータを使用して、ベンチマーク モデルの WER を含むテスト出力を保存します。
- これらのテストの WER は、今後のモデルが超える必要のある新しいベンチマーク WER になります。
- テスト データの更新では、CD ワークフローは実行されません。

### <a name="ci-workflow-for-training-data-updates"></a>トレーニング データの更新の CI ワークフロー

トレーニング データの更新とは、カスタム モデルの更新を意味します。

このワークフローは、トレーニング データの更新時にトリガーされます。

- 更新されたトレーニング データを使用して新しいモデルをトレーニングします。
- テスト データに対して新しいモデルをテストします。
- WER を含むテスト出力を保存します。
- 新しいモデルの WER とベンチマーク モデルの WER を比較します。
- WER が改善されない場合は、ワークフローを停止します。
- WER が改善された場合は、CD ワークフローを実行して Custom Speech エンドポイントを作成します。

### <a name="cd-workflow"></a>CD ワークフロー

トレーニング データを更新してモデルの認識が向上した後は、CD ワークフローが自動的に実行され、そのモデルの新しいエンドポイントが作成され、そのエンドポイントが使用可能になり、ソリューションで使用できるようになります。

#### <a name="release-management"></a>リリース管理

ほとんどのチームでは、運用環境へのデプロイに手動のレビューと承認プロセスが必要です。 運用環境へのデプロイの場合、必ず開発チームの主要担当者がサポートできるとき、またはトラフィックの少ない時間帯に実行することをお勧めします。

### <a name="tools-for-custom-speech-workflows"></a>Custom Speech ワークフロー用のツール

Custom Speech の CI/CD 自動化ワークフローには、次のツールを使用します。

- [Azure CLI](/cli/azure/)。Azure サービス プリンシパルの認証を作成し、Azure サブスクリプションのクエリを実行し、Azure BLOB にテスト結果を格納するために使用します。
- [Azure Speech CLI](spx-overview.md)。コマンド ラインまたは自動ワークフローから音声サービスを操作するために使用します。

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>GitHub Actions を使用する Custom Speech 用の DevOps ソリューション

Custom Speech 用に既に実装されている DevOps ソリューションについては、[Speech DevOps テンプレート リポジトリ](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)を参照してください。 テンプレートのコピーを作成し、GitHub Actions を使用したテスト、トレーニング、バージョン管理を含む堅牢な DevOps システムでカスタム モデルの開発を開始します。 このリポジトリには、設定を支援し、ワークフローを説明するためのサンプル テストとトレーニング データが用意されています。 初期セットアップ後、サンプル データをご自分のプロジェクト データに置き換えます。

[Speech DevOps テンプレート リポジトリ](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)には、以下のインフラストラクチャと詳細なガイダンスが用意されています。

- テンプレート リポジトリを GitHub アカウントにコピーし、GitHub Actions CI/CD ワークフロー用の Azure リソースと[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成します。
- "[開発者の内部ループ](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)" の手順を確認します。 機能ブランチのトレーニングおよびテスト データを更新し、一時的な開発モデルを使用して変更をテストし、pull request を発生させて変更を提案および確認します。
- トレーニング データが pull request で "*メイン*" に更新されたら、GitHub Actions CI ワークフローを使用してモデルをトレーニングします。
- 自動正確性テストを実行し、モデルの[ワード エラー率](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) を確立します。 テスト結果を Azure BLOB に格納します。
- WER が改善されたら、CD ワークフローを実行してエンドポイントを作成します。

## <a name="next-steps"></a>次のステップ

Speech を使用した DevOps の詳細を確認します。

- [Speech DevOps テンプレート リポジトリ](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)を使用し、GitHub Actions を使用して Custom Speech 用の DevOps を実装します。
