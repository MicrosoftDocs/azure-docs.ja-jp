---
title: Azure 用 DevOps Starter の概要 | Microsoft Docs
description: DevOps Starter の価値について説明します
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330650"
---
# <a name="overview-of-devops-starter"></a>DevOps Starter の概要

 DevOps Starter を利用すると、GitHub Actions または Azure DevOps を使用して Azure を使い始めるのが簡単になります。 Azure portal での簡単な手順により、任意の Azure サービス上で目的のアプリを起動するのに役立ちます。 

 DevOps Starter では、アプリケーションの開発、デプロイ、および監視に必要なすべてのものが設定されます。 DevOps Starter ダッシュボードを使用すると、Azure portal の単一のビューからコードのコミット、ビルド、およびデプロイのすべてを監視できます。

## <a name="advantages-of-using-devops-starter"></a>DevOps Starter を使用する利点

  DevOps starter では、デプロイを自動化するために、2 つの CI/CD プロバイダーがサポートされています
  * [GitHub のアクション](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter では、Azure へのアプリケーションの継続的インテグレーション (CI) および継続的デリバリー (CD) 全体の設定が自動化されます。  既存のコードで開始するか、用意されているいずれかのサンプル アプリケーションを使用することができます。 次に、そのアプリケーションをさまざまな Azure サービス (Virtual Machines、App Service、Azure Kubernetes Servies (AKS)、Azure SQL Database、Azure Service Fabric など) にすばやくデプロイできます。  

  DevOps Starter によって、DevOps パイプラインの初期構成のすべての作業が行われます。たとえば、初期 Git リポジトリの設定、CI/CD パイプラインの構成、監視用の Application Insights リソースの作成、Azure portal 上の DevOps Starter ダッシュボードの作成によるソリューション全体の単一ビューの提供などです。

DevOps Starter を使用して、次のようなことができます。

* アプリケーションを Azure に短時間で展開します
* CI/CD ワークフローまたはパイプラインの設定を自動化します
* CI/CD ワークフローまたはパイプラインの適切な設定方法を見て理解します
* 特定のシナリオに基づいてリリース パイプラインをさらにカスタマイズします

## <a name="how-to-use-devops-starter"></a>DevOps Starter の使用方法

  DevOps Starter は Azure portal から利用できます。 他の Azure リソースを作成するときと同じように、ポータルから DevOps Starter リソースを作成します。 DevOps Projects では、さまざまな構成オプションに対して、ウィザードに似たステップ バイ ステップのエクスペリエンスが提供されます。  

初期セットアップの一部として、いくつかの構成オプションを選びます。 設定できるオプションは次のとおりです。

* 優先する CI/CD プロバイダーを選択します
* 用意されているサンプル アプリを使用するか、独自のコードを作成します (Azure DevOps の場合のみ)
* アプリの言語を選びます
* 言語に基づいてアプリのフレームワークを選びます
* Azure サービス (デプロイ ターゲット) を選びます
* GitHub または Azure DevOps 組織を選択します
* Azure サブスクリプションを選びます
* Azure サービスの場所を選びます
* Azure サービスのさまざまな価格レベルからいずれかを選びます

DevOps Starter を作成した後は、次のことができます。

* GitHub ワークフローまたは Azure DevOps パイプラインをカスタマイズします
* Pull request を使ってコードのフローを管理し、高い品質を維持します
* コードをマージして品質の水準を引き上げる前に、各コミットをテストしてビルドします

DevOps Starter を使用した後は、Azure portal 上の DevOps Starter ダッシュボードから一元的に、すべてのリソースを削除することもできます。

## <a name="devops-starter-and-github-integration"></a>DevOps Starter と GitHub の統合

DevOps Starter では、CI/CD プロバイダーとして GitHub Actions がサポートされるようになりました。 GitHub Actions を使用する CI/CD ワークフローを設定するために GitHub で必要なすべての作業が自動化されます。 既存の GitHub 組織に GitHub リポジトリが作成され、サンプル アプリケーションが新しい GitHub リポジトリにコミットされます。  

また、この自動化では、新しいコードがコミットされるたびにワークフロー内のビルド ジョブおよびデプロイ ジョブを開始するように、ワークフローに対するトリガーも確立されます。 アプリケーションは、選択した Azure サービスにデプロイされます。 GitHub ワークフローは、追加のシナリオに合わせてカスタマイズできます。 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter と Azure DevOps 統合

Azure DevOps を使用する DevOps Starter によって、Azure Pipelines で CI/CD パイプラインを設定するために必要なすべての作業が自動化されます。 新規または既存の Azure DevOps 組織に Git リポジトリが作成され、サンプル アプリケーションまたは既存のコードが新しい Git リポジトリにコミットされます。  

また、この自動化では、新しいコードがコミットされるたびにビルドが開始するように、ビルドに対する CI トリガーも確立されます。 DevOps Starter によって CD トリガーが作成され、成功した新しいすべてのビルドが、選択した Azure サービスにデプロイされます。  

ビルド パイプラインとリリース パイプラインは、他のシナリオに合わせてカスタマイズできます。 また、他のプロジェクトで使用するために、ビルド パイプラインとリリース パイプラインを複製することもできます。

## <a name="getting-started-with-devops-starter"></a>DevOps Starter を使用した作業の開始

* [DevOps Starter を使用して作業を開始する](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps Starter のビデオ

* [Azure DevOps Starter を使用した CI/CD の作成](https://www.youtube.com/watch?v=NuYDAs3kNV8)
