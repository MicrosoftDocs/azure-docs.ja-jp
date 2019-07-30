---
title: Azure Container Registry のコンテンツの形式
description: Azure Container Registry 内でサポートされているコンテンツの形式について説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310688"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry 内でサポートされているコンテンツの形式

Azure Container Registry 内のプライベート リポジトリを使用して、次のコンテンツ形式の 1 つを管理できます。 

## <a name="docker-compatible-container-images"></a>Docker と互換性のあるコンテナー イメージ

以下の Docker コンテナー イメージの形式がサポートされています。

* [Docker Image Manifest V2、Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2、Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 1 つの "image:tag" 参照の下にマルチプラットフォーム イメージを格納することをレジストリに許可するマニフェスト一覧が含まれます

## <a name="oci-images"></a>OCI イメージ

Azure Container Registry では、[Open Container Initiative (OCI) イメージ形式の仕様](https://github.com/opencontainers/image-spec/blob/master/spec.md)に準拠するイメージもサポートされています。 パッケージ形式には、[Singularity Image Format (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/) が含まれています。

## <a name="helm-charts"></a>Helm チャート

Azure Container Registry では、Kubernetes 用のアプリケーションをすばやく管理およびデプロイするために使用されるパッケージ形式である [Helm チャート](https://helm.sh/)のリポジトリをホストすることができます。 [Helm クライアント](https://docs.helm.sh/using_helm/#installing-helm) バージョン 2.11.0 以降がサポートされています。

## <a name="next-steps"></a>次の手順

* Azure Container Registry を使用してイメージを[プッシュおよびプル](container-registry-get-started-docker-cli.md)する方法を確認します。

* [ACR タスク](container-registry-tasks-overview.md)を使用して、コンテナー イメージのビルドとテストを行います。 

* [Moby BuildKit](https://github.com/moby/buildkit) を使用して、OCI 形式のコンテナーを作成およびパッケージ化します。

* Azure Container Registry にホストされる [Helm リポジトリ](container-registry-helm-repos.md)を設定します。 


