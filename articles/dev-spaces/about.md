---
title: Azure Dev Spaces とは
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Azure Kubernetes Service クラスターにおける反復型の迅速な Kubernetes 開発エクスペリエンスをチームに提供する Azure Dev Spaces について説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, kubectl, k8s
manager: gwallace
ms.openlocfilehash: fd6f52c71c81f253f3f40f05408e45b6a6c0dbce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199477"
---
# <a name="what-is-azure-dev-spaces"></a>Azure Dev Spaces とは

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces は、Azure Kubernetes Service (AKS) クラスター内のチームに迅速で、反復的な Kubernetes 開発エクスペリエンスを提供します。 Azure Dev Spaces ではまた、依存関係をレプリケートしたりモックアップしたりすることなく、最小限の開発用コンピューターのセットアップで AKS 内のアプリケーションのすべてのコンポーネントをデバッグしてテストすることもできます。

![この図は、別個に開発されたアプリケーションの 2 つのバージョンを示しています。 その後、これらは Azure Dev Spaces 開発環境で 1 つに結合されます。](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev Spaces が省力化する Kubernetes 開発の実際

Azure Dev Spaces を利用すると、チームは AKS で実行されているアプリケーションやマイクロサービス アーキテクチャ全体を直接取り扱えるようになるため、マイクロサービス アプリケーションに関する開発と迅速な反復作業に集中できます。 また、Azure Dev Spaces では、マイクロサービス アーキテクチャの一部を切り離して、残りの AKS クラスターや他の開発者に影響を与えずに個別に更新することができます。 Azure Dev Spaces は、下位レベルの開発およびテスト環境で行う開発とテスト用に使用されるものであり、運用環境の AKS クラスター上での実行は想定されていません。

Azure Dev Spaces では、チームがアプリケーション全体を取り扱いながら AKS 上で直接共同作業ができるため、次のようなメリットがあります。

* ローカル マシンのセットアップを最小限に抑える
* チームの新しい開発者がセットアップにかける時間を削減する
* 反復作業を高速化することで、チームのベロシティを向上させる
* チーム メンバーがクラスターを共有できるので、冗長な開発環境と統合環境の数を削減できる
* 依存関係の複製とモックアップが不要になる
* 開発チーム全体に加えて、DevOps チームなどの関連チームも含めたコラボレーションを向上させる

Azure Dev Spaces は、Docker と Kubernetes のアセットをプロジェクト用に生成するツールを提供します。 このツールを使用することで、開発空間とその他の AKS クラスターの両方に、新規および既存のアプリケーションを簡単に追加できます。

Azure Dev Spaces のしくみについては、「[Azure Dev Spaces のしくみと構成方法][how-dev-spaces-works]」を参照してください。

## <a name="supported-regions-and-configurations"></a>サポートされているリージョンと構成

Azure Dev Spaces は、[一部のリージョン][supported-regions]の AKS クラスターでのみサポートされます。 Azure Dev Spaces では、AKS でアプリケーションをビルドして実行するために、Linux、macOS、または Windows 8 以上にインストールされた [Azure CLI](/cli/azure/install-azure-cli) または [Visual Studio Code](https://code.visualstudio.com/download) および [Azure Dev Spaces 拡張機能](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)の使用がサポートされます。 また、Azure 開発ワークロードがインストールされている Windows 上の [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) の使用もサポートされます。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
