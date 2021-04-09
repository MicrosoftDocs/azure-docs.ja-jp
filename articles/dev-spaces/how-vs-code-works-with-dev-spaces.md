---
title: Visual Studio Code と Azure Dev Spaces が連動するしくみ
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code と Azure Dev Spaces による、Kubernetes アプリケーションのデバッグと迅速な反復処理の支援について説明します。
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: edfcb8107280bb86144b798da2d5b1c16371528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960747"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code と Azure Dev Spaces が連動するしくみ

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Visual Studio Code と [Azure Dev Spaces 拡張機能][azds-extension]を使用し、Azure Dev Spaces を利用するサービスを準備し、実行し、デバッグできます。 Visual Studio Code と Azure Dev Spaces 拡張機能でできること:

* AKS でサービスを実行し、デバッグするためのアセットを生成する
* Java、Node.js、.NET Core サービスを開発スペースで実行する
* 開発スペースで実行している Java、Node.js、.NET Core サービスを直接デバッグする

## <a name="generate-assets"></a>アセットを生成する

Visual Studio Code と Azure Dev Spaces 拡張機能で生成されるプロジェクト用のアセット:

* Maven を使用する Java アプリケーション、Node.js アプリケーション、.NET Core アプリケーション用の Dockerfile
* ほとんどすべての言語の Helm グラフと Dockerfile
* プロジェクトの [Azure Dev Spaces 構成ファイル][azds-yaml]である `azds.yaml` ファイル
* Maven を使用する Java アプリケーション、Node.js アプリケーション、.NET Core アプリケーション用のプロジェクトの Visual Studio Code 起動構成が含まれる `.vscode` フォルダー

Dockerfile、Helm グラフ、`azds.yaml` ファイルは `azds prep` の実行時に生成されるものと同じアセットです。 これらのファイルを Visual Studio Code の外で利用し、AKS で実行することもできます。たとえば、`azds up` を実行します。 `.vscode` フォルダーは、Visual Studio Code から AKS のプロジェクトを実行する目的でのみ、Visual Studio Code によって使用されます。

## <a name="run-your-service-in-aks"></a>AKS でサービスを実行する

プロジェクト用のアセットを生成したら、Visual Studio Code から、既存の開発スペースで Java、Node.js、.NET Core サービスを実行できます。 Visual Studio Code の *[デバッグ]* ページで、`.vscode` ディレクトリから起動構成を呼び出し、プロジェクトを実行できます。

AKS クラスターを作成し、Visual Studio Code の外でクラスターの Azure Dev Spaces を有効にする必要があります。 `azds prep` を実行することで生成されたアセットなど、Visual Studio Code の外で作成された既存の Dockerfile、Helm グラフ、`azds.yaml` ファイルを再利用できます。 Visual Studio Code の外で生成されたアセットを再利用する場合も、`.vscode` ディレクトリを用意する必要があります。 この `.vscode` ディレクトリは Visual Studio Code と Azure Dev Spaces 拡張機能で再生成できます。既存のアセットを上書きすることはありません。

.NET Core プロジェクトの場合、Visual Studio Code から .NET サービスを実行するには、[C# 拡張機能][csharp-extension]をインストールする必要があります。 Maven を使用する Java プロジェクトの場合も、Visual Studio Code から Java サービスを実行するには、[Java Debugger for Azure Dev Spaces 拡張機能][java-extension]をインストールし、[Maven をインストールして構成する][maven]必要があります。

## <a name="debug-your-service-in-aks"></a>AKS でサービスをデバッグする

プロジェクトを起動したら、Visual Studio Code から直接、開発スペースで実行している Java、Node.js、.NET Core サービスをデバッグできます。 `.vscode` ディレクトリの起動構成からは、開発スペースでデバッグが有効になっているサービスの実行に関して、付加的なデバッグ情報が与えられます。 Visual Studio Code は、開発スペースで実行中のコンテナーのデバッグ プロセスにも付随し、ブレーク ポイントの設定、変数の調査、その他のデバッグ操作を可能にします。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
