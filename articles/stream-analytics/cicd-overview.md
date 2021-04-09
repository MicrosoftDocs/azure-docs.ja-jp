---
title: Azure Stream Analytics の継続的インテグレーションと継続的デプロイ
description: この記事では、Azure Stream Analytics の CI/CD (継続的インテグレーションと継続的デプロイ) パイプラインについて概説します。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 940813f12d542715db47781731144a75e854a98e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019569"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Azure Stream Analytics の継続的インテグレーションと継続的デプロイ (CI/CD)

ソース管理の統合を使用して、Azure Stream Analytics ジョブを継続的にデプロイすることができます。 ソース管理の統合を使用すると、コードの更新によって Azure へのリソースのデプロイがトリガーされるワークフローが可能になります。 この記事では、CI/CD (継続的インテグレーションと継続的デプロイ) パイプラインを作成する基本的な手順を概説します。

Azure Stream Analytics を初めてお使いの方は、最初に [Azure Stream Analytics のクイックスタート](stream-analytics-quick-create-portal.md)をご覧ください。

## <a name="create-a-cicd-pipeline"></a>CI/CD パイプラインの作成

Stream Analytics の CI/CD パイプラインは、このガイドの手順に従って作成します。

1. Azure Stream Analytics クエリを作成します。

   [Visual Studio Code](./quick-create-visual-studio-code.md) 用または [Visual Studio](stream-analytics-quick-create-vs.md) 用の Azure Stream Analytics ツールを使用して、[クエリをローカルで作成、テスト](develop-locally.md)します。 ローカル プロジェクトに[既存のジョブをエクスポート](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project)することもできます。

2. Azure Stream Analytics プロジェクトを Git リポジトリなどのソース管理システムにコミットします。

3. [Azure Stream Analytics CI/CD ツール](cicd-tools.md)を使用してプロジェクトを作成し、デプロイ用の Azure リソース管理テンプレートを生成します。

4. 品質の回帰を確認するための[自動スクリプト テスト](cicd-tools.md#automated-test)を実行します。

5. Azure に自動的に[ジョブをデプロイ](cicd-tools.md#deploy-to-azure)します。

## <a name="auto-build-test-and-deploy"></a>ビルド、テスト、デプロイを自動的に実行する

コマンド ラインと [Azure Stream Analytics CI/CD ツール](cicd-tools.md)を使用してビルド、テスト、デプロイを自動化することができます。 [Azure Pipelines](set-up-cicd-pipeline.md) で CI/CD パイプラインを設定することもできます。 パイプライン管理、視覚化、トリガーなど、より高度な機能を実現するための Azure Pipelines。

## <a name="next-steps"></a>次のステップ

* [CI/CD ツールを使用して Azure Stream Analytics ジョブのビルド、テスト、デプロイを自動化する](cicd-tools.md)
* [Azure Pipelines を使用して Stream Analytics ジョブの CI/CD パイプラインを設定する](set-up-cicd-pipeline.md)