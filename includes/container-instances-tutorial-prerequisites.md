---
title: インクルード ファイル
description: インクルード ファイル
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 10fb9e8169b7f4159ccbf4a0ff36021f6033f811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75552421"
---
このチュートリアルを完了するには、次の要件を満たす必要があります。

**Azure CLI**:ローカル コンピューターに Azure CLI バージョン 2.0.29 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

**Docker**:このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 `docker` コマンドなど、Docker のコア概念を基本的に理解している必要があります。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview][docker-get-started]」(Docker の概要) を参照してください。

**Docker**:このチュートリアルを完了するには、Docker がローカル環境にインストールされている必要があります。 Docker には、[macOS][docker-mac]、[Windows][docker-windows]、[Linux][docker-linux] 上で Docker 環境の構成を行うパッケージが用意されています。

> [!IMPORTANT]
> Azure Cloud Shell には Docker デーモンが含まれていないため、このチュートリアルを完了するためには、Azure CLI と Docker エンジンの両方を自分で *ローカル コンピューター* にインストールする *必要があります*。 このチュートリアルで Azure Cloud Shell を使用することはできません。

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
