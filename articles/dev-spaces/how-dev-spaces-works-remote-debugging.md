---
title: Azure Dev Spaces でのコードのリモート デバッグのしくみ
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces による Azure Kubernetes Service でのリモート デバッグのプロセスについて説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975044"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Azure Dev Spaces でのコードのリモート デバッグのしくみ

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces には、Kubernetes アプリケーションを迅速に反復処理してデバッグし、Azure Kubernetes Service (AKS) クラスターでチームと共同作業を行うための複数の方法が用意されています。 開発スペースでプロジェクトを実行すると、Azure Dev Spaces により AKS で実行中のアプリケーションをアタッチしてデバッグする手段が提供されます。

この記事では、Dev Spaces でのリモート デバッグのしくみについて説明します。

## <a name="debug-your-code"></a>コードをデバッグする

Java、.NET Core、および Node.js アプリケーションの場合、Visual Studio Code または Visual Studio を使用して、実行中のアプリケーションを開発スペースで直接デバッグできます。 Visual Studio Code と Visual Studio には、開発スペースに接続してアプリケーションを起動し、デバッガーをアタッチするためのツールが用意されています。 `azds prep` の実行後、Visual Studio Code または Visual Studio でプロジェクトを開くことができます。 Visual Studio Code または Visual Studio では、`azds prep` の実行とは別の接続用の独自の構成ファイルが生成されます。 Visual Studio Code または Visual Studio 内から、ブレークポイントを設定し、開発スペースに対してアプリケーションを起動できます。

![コードのデバッグ](media/get-started-node/debug-configuration-nodejs2.png)

Visual Studio Code または Visual Studio を使用してデバッグ用にアプリケーションを起動すると、`azds up` の実行と同じ方法で起動および開発スペースへの接続が処理されます。 また、Visual Studio Code および Visual Studio のクライアント側ツールには、デバッグ用の特定の情報を含む追加のパラメーターが用意されています。 このパラメーターには、デバッガー イメージの名前、デバッガー イメージ内のデバッガーの場所、およびアプリケーションのコンテナー内でのデバッガー フォルダーのマウント先が含まれています。

デバッガー イメージは、クライアント側ツールによって自動的に指定されます。 `azds prep` の実行時に Dockerfile と Helm チャートの生成に使用したのと同じ方法が使用されます。 アプリケーションのイメージにマウントされたデバッガーは `azds exec` を使用して実行されます。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](how-dev-spaces-works.md)
