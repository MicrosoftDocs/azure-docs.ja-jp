---
title: Azure Pipelines を使用した CI/CD パイプラインの作成 - Team Data Science Process
description: Docker と Kubernetes を使用して、人工知能 (AI) アプリケーション用の継続的インテグレーションと継続的デリバリーのパイプラインを作成します。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/06/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: f07ce8e8834a2804b6a5b7668718c8e6bff00fa6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260662"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Azure Pipelines、Docker、Kubernetes を使用して AI アプリ用の CI/CD パイプラインを作成する

人工知能 (AI) アプリケーションは、事前トレーニング済みの機械学習 (ML) モデルに埋め込まれたアプリケーション コードです。 AI アプリケーションには、常に 2 つの作業の流れがあります。データ サイエンティストは ML モデルを構築し、アプリ開発者はアプリをビルドしてエンド ユーザーが使用できるように公開します。 この記事では、ML モデルをアプリのソース コードに埋め込む AI アプリケーション用の継続的インテグレーションと継続的デリバリー (CI/CD) パイプラインを実装する方法について説明します。 このサンプル コードとチュートリアルでは、シンプルな Python Flask Web アプリケーションを使用して、個人の Azure BLOB ストレージ アカウントから事前トレーニング済みのモデルをフェッチします。 また、AWS S3 ストレージ アカウントも使用できます。

> [!NOTE]
> 次のプロセスは、CI/CD を実行するいくつかの方法のうちの 1 つです。 このツールと前提条件に代わる別のツールや前提条件もあります。

## <a name="source-code-tutorial-and-prerequisites"></a>ソース コード、チュートリアル、前提条件

[ソース コード](https://github.com/Azure/DevOps-For-AI-Apps)と[詳細なチュートリアル](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)を GitHub からダウンロードできます。 チュートリアルの手順に従って、独自のアプリケーション用に CI/CD パイプラインを実装します。

ダウンロードしたソース コードとチュートリアルを使用するには、次の前提条件が必要です。 

- お使いの GitHub アカウントにフォークされた[ソース コード リポジトリ](https://github.com/Azure/DevOps-For-AI-Apps)
- [Azure DevOps 組織](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Azure Container Service for Kubernetes (AKS) クラスター](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- コマンドを実行して AKS クラスターから構成をフェッチするための [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
- [Azure Container Registry (ACR) アカウント](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD パイプラインの概要

新しく Git コミットが行われるたびに、ビルド パイプラインが開始されます。 このビルドにより、BLOB ストレージ アカウントから最新の ML モデルが安全にプルされ、アプリ コードと共に 1 つのコンテナーにパッケージ化されます。 このように、アプリケーション開発とデータ サイエンスの作業の流れを分離することで、運用アプリでは常に最新の ML モデルを使用して最新のコードが実行されるようになります。 アプリがテストに合格すると、パイプラインはビルド イメージを ACR 内の Docker コンテナーに安全に格納します。 その後、リリース パイプラインでは AKS を使用してコンテナーがデプロイされます。 

## <a name="cicd-pipeline-steps"></a>CI/CD パイプラインのステップ

次の図とステップは、CI/CD パイプラインのアーキテクチャを説明しています。

![CI/CD パイプラインのアーキテクチャ](./media/ci-cd-flask/architecture.png)

1. 開発者が、任意の IDE でアプリケーション コードに取り組みます。
2. その開発者は、Azure Repos、GitHub、またはその他の Git ソース管理プロバイダーにコードをコミットします。 
3. これとは別に、データ サイエンティストが ML モデルの開発に取り組みます。
4. このデータ サイエンティストは、完成したモデルをモデル リポジトリ (この場合は BLOB ストレージ アカウント) に公開します。 
5. Azure Pipelines によって、Git コミットに基づいたビルドが開始されます。
6. ビルド パイプラインによって、BLOB ストレージから最新の ML モデルがプルされ、コンテナーが作成されます。
7. パイプラインによって、ビルド イメージが ACR のプライベート イメージ リポジトリにプッシュされます。
8. 成功したビルドに基づいて、リリース パイプラインが開始されます。
9. パイプラインによって、最新のイメージが ACR からプルされ、AKS 上の Kubernetes クラスター全体にデプロイされます。
10. アプリに対するユーザー要求が DNS サーバーを経由します。
11. DNS サーバーはこの要求をロード バランサーに渡し、応答をユーザーに送り返します。

## <a name="see-also"></a>関連項目

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes)
