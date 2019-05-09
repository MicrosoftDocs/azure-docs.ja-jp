---
title: Azure Dev Spaces ツールのアップグレード方法
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: conceptual
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー
ms.openlocfilehash: 4e0a3c5aa849799872371ef1c5ac0867babffebb
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791904"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Azure Dev Spaces ツールのアップグレード方法

新しいリリースがあり、既に Azure Dev Spaces を使用している場合、Azure Dev Spaces クライアント ツールのアップグレードが必要になることがあります。

## <a name="update-the-azure-cli"></a>Azure CLI の更新

最新の Azure CLI を更新すると、最新バージョンの Dev Spaces CLI 拡張機能も取得されます。

以前のバージョンをアンインストールする必要はなく、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) で適切なダウンロードを見つけるだけで構いません。


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Dev Spaces CLI 拡張機能とコマンド ライン ツールの更新

次のコマンドを実行します。

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>VS Code 拡張機能の更新

インストールが完了すると、拡張機能は自動的に更新されます。 新機能を使用するために、拡張機能の再読み込みが必要になる場合があります。 VS Code で **[拡張機能]** ウィンドウを開き、**[Azure Dev Spaces]** 拡張機能を開いて **[Reload] (再読み込み)** を選択します。

## <a name="update-the-visual-studio-extension"></a>Visual Studio 拡張機能の更新

他の拡張機能や更新プログラムと同じように、Azure Dev Spaces を含む Visual Studio Tools for Kubernetes で更新プログラムが使用可能になると、Visual Studio から通知されます。 画面の右上の旗のアイコンを探してください。

Visual Studio のツールを更新するには、**[Tools] (ツール) > [Extensions and Updates] (拡張機能と更新プログラム)** メニュー項目を選択し、左側の **[Updates] (更新プログラム)** を選択します。 **[Visual Studio Tools for Kubernetes]** を探し、**[Update] (更新)** ボタンを選択します。

## <a name="next-steps"></a>次の手順

新しいクラスターを作成して、新しいツールをテストします。 [Azure Dev Spaces](/azure/dev-spaces) でクイック スタートおよびチュートリアルをお試しください。

> [!WARNING]
> 既存クラスター上の Azure Dev Spaces には、パッチは即座に適用されません。そのため、すべての Azure デプロイで確実に最新バージョンが使用されるように、ツールをアップグレードした後、新しいクラスターを作成します。
