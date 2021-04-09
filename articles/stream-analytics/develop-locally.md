---
title: Azure Stream Analytics ジョブをローカル環境で開発してデバッグする
description: Azure Stream Analytics ジョブを、Azure portal で実行する前に、ローカル コンピューター上で開発してテストする方法について説明します。
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016475"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Azure Stream Analytics ジョブをローカル環境で開発してデバッグする

Azure Stream Analytics ジョブの作成とテストは、Azure portal で行うこともできますが、多くの開発者は、ローカル開発エクスペリエンスを好みます。 Stream Analytics では、使い慣れたコード エディターと開発ツールを使用してジョブを作成し、完全に機能する単一ノードのローカル ランタイムを使用して Azure Event Hub、IoT Hub、Blob Storage からのライブ イベント ストリームでジョブをテストすることができます。 また、ローカル開発環境から Azure にジョブを直接送信することもできます。

## <a name="local-development-environments"></a>ローカル開発環境

ローカル コンピューターで Stream Analytics ジョブを作成する方法は、ツールの設定と機能の使用可能性によって異なります。 各開発環境でサポートされている機能については、「[Azure Stream Analytics の機能の比較](feature-comparison.md)」を参照してください。

ローカル開発をサポートする環境を次の表に示します。

|環境                              |説明    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Visual Studio Code 用の [Azure Stream Analytics Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa)を使用すると、ローカル環境とクラウドの両方で、豊富な IntelliSense とネイティブ ソース コントロールを使用して、Stream Analytics ジョブを作成、管理、テストできます。 Linux、MacOS、Windows 上での開発がサポートされています。 詳しくは、「[Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)」をご覧ください。 この拡張機能では、クラウドでホストされる開発環境である [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) もサポートされています。|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics Tools は、Visual Studio の Azure の開発ワークロードおよびデータの保存と処理ワークロードの一部です。 Visual Studio を使用して、C# でカスタム ユーザー定義関数と逆シリアライザーを記述できます。 詳しくは、「[Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)」をご覧ください。|
|[コマンド プロンプトまたはターミナル](stream-analytics-tools-for-visual-studio-cicd.md)|Azure Stream Analytics CI/CD NuGet パッケージでは、任意のコンピューターでの Visual Studio プロジェクトのビルドとローカル テストのためのツールが提供されています。 Azure Stream Analytics CI/CD npm パッケージでは、任意のコンピューターでの Visual Studio Code プロジェクトのビルドのためのツールが提供されています (Azure Resource Manager テンプレートが生成されます)。|

## <a name="next-steps"></a>次のステップ

* [Visual Studio Code を使用してサンプル データで Stream Analytics クエリをローカルでテストする](visual-studio-code-local-run.md)
* [Visual Studio Code を使用してライブ ストリーム入力に対して Stream Analytics クエリをローカルでテストする](visual-studio-code-local-run-live-input.md)
* [Visual Studio で Stream Analytics クエリをローカルでテストする](stream-analytics-vs-tools-local-run.md)
* [Visual Studio 用の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする](stream-analytics-live-data-local-testing.md)
