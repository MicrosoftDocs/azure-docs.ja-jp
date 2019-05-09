---
title: Azure Dev Spaces と共に kubectl を使用する方法
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773267"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Azure Dev Space で kubectl を使用する

Azure Dev Space の内部で Kubernetes クラスターにアクセスし、`kubectl` などの既存の Kubernetes ツールを使用することができます。

`az aks use-dev-spaces` コマンドを実行すると、`kubectl` 構成コンテキストが自動的に追加されるので、kubectl は Azure Dev Spaces の Kubernetes クラスターに既に接続されています。 次に例を示します。
- 現在のコンテキストを確認します: `kubectl config current-context`
- すべての使用可能なコンテキストを一覧表示します: `kubectl config get-contexts`。 
- コンテキストを変更します: `kubectl config use-context <context-name>`
- Kubernetes ダッシュボードの表示: `kubectl proxy` を実行した後、このコマンドが出力するアドレスをブラウザーで開きます (URL に `/ui` を追加して Kubernetes ダッシュボードに移動します)。
- *default* という名前の既定の Azure Dev Spaces スペースで実行中のサービスを一覧表示します: `kubectl get services --namespace=default`

