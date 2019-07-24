---
title: Jenkins と Azure の概要
description: Azure で Jenkins ビルドをホストし、オートメーション サーバーをデプロイします。また、Azure のコンピューティング リソースとストレージ リソースを使用することで、継続的インテグレーションとデプロイ (CI/CD) パイプラインを拡張します。
ms.service: jenkins
keywords: Jenkins, Azure, 開発, 概要
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: overview
ms.date: 07/25/2018
ms.openlocfilehash: 86d32726280cce12888f125c65254a7b02166704
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194752"
---
# <a name="azure-and-jenkins"></a>Azure と Jenkins

[Jenkins](https://jenkins.io/) は、ソフトウェア プロジェクトの継続的インテグレーションと配信 (CI/CD) を設定する際に使用される、広く普及しているオープンソースのオートメーション サーバーです。 Azure で Jenkins デプロイをホストしたり、Azure リソースを使用して、既存の Jenkins 構成を拡張したりできます。 Azure へのアプリケーションの CI/CD を簡素化する Jenkins プラグインも使用できます。

この記事は、Jenkins での Azure 使用の概要のほか、Jenkins ユーザーが利用できる重要な Azure コア機能も扱っています。 お使いの Jenkins サーバーの Azure での共有については、[Azure での Jenkins サーバーの作成](install-jenkins-solution-template.md)に関する記事を参照してください。

## <a name="host-your-jenkins-servers-in-azure"></a>Azure で Jenkins サーバーをホストする

Azure で Jenkins をホストすることで、ビルドの自動化を集中管理し、ソフトウェア プロジェクト拡大のニーズに合わせてデプロイを拡大縮小します。 Azure への Jenkins のデプロイには以下を使用できます。
 
- Azure Marketplace の [Jenkins ソリューション テンプレート](install-jenkins-solution-template.md)。
- [Azure 仮想マシン](/azure/virtual-machines/linux/overview)。 VM で Jenkins を作成するには、[チュートリアル](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd)を参照してください。
- [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) で実行されている Kubernetes クラスターについては、こちらの[手順](/azure/container-service/kubernetes/container-service-kubernetes-jenkins)を参照してください。

Azure Jenkins のデプロイは、[Azure Monitor ログ](/azure/log-analytics/log-analytics-overview)と [Azure CLI](/cli/azure) を使用して監視および管理します。

## <a name="scale-your-build-automation-on-demand"></a>ビルド自動化をオン デマンドで拡大縮小する

ジョブとパイプラインのビルド数と複雑性の増加に応じて、既存の Jenkins デプロイにビルド エージェントを追加して、Jenkins のビルド容量を拡張します。 こうしたビルド エージェントを Azure 仮想マシンで実行するには、[Azure VM Agents プラグイン](jenkins-azure-vm-agents.md)を使用します。 詳細については、こちらの[チュートリアル](/azure/jenkins/jenkins-azure-vm-agents)を参照してください。

[Azure サービス プリンシパル](/azure/azure-resource-manager/resource-group-overview)で構成したら、Jenkins のジョブとパイプラインでは、その資格情報を使用することで次が実現します。

- [Azure Storage プラグイン](https://plugins.jenkins.io/windows-azure-storage)を使用して、ビルド アーティファクトを [Azure Storage](/azure/storage/common/storage-introduction) に安全に格納およびアーカイブする。 詳細については、[Jenkins ストレージの操作方法](/azure/storage/common/storage-java-jenkins-continuous-integration-solution)に関するページをご覧ください。
- [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) を使用して Azure リソースを管理および構成する。

## <a name="deploy-your-code-into-azure-services"></a>Azure サービスにコードをデプロイする

Jenkins プラグインを使用して、アプリケーションを Jenkins CI/CD パイプラインの一部として Azure にデプロイします。 [Azure App Service](/azure/app-service/) および [Azure Container Service](/azure/container-service/kubernetes/) にデプロイすることで、基盤となるインフラストラクチャを管理することなく、アプリケーションの更新プログラムをステージング、テスト、およびリリースできます。

 次のサービスおよび環境にデプロイするためのプラグインが用意されています。

- [Azure App Service on Linux](/azure/app-service/containers/app-service-linux-intro)。 作業を開始するには、こちらの[チュートリアル](java-deploy-webapp-tutorial.md)を参照してください。
- [Azure App Service](/azure/app-service/overview)。 作業を開始するには、こちらの[操作方法](deploy-Jenkins-app-service-plugin.md)を参照してください。