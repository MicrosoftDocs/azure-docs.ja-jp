---
title: Azure DevOps Projects の概要 | Microsoft Docs
description: Azure DevOps Projects の価値について説明します
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: fed4f0976dd118186c122b0152f255d0c452c0ce
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404489"
---
# <a name="overview-of-azure-devops-projects"></a>Azure DevOps Projects の概要

 Azure DevOps Projects を利用すると、Azure を使い始めるのが簡単になります。 Azure portal での簡単な手順により、任意の Azure サービス上で目的のアプリを起動するのに役立ちます。 

 DevOps Projects では、アプリケーションの開発、デプロイ、および監視に必要なすべてのものが設定されます。 DevOps Projects ダッシュボードを使用すると、Azure portal の単一のビューからコードのコミット、ビルド、およびデプロイのすべてを監視できます。

## <a name="why-should-i-use-devops-projects"></a>DevOps Projects を使用する理由

  DevOps Projects では、Azure への継続的インテグレーション (CI) および継続的デリバリー (CD) パイプライン全体のセットアップが自動化されます。  既存のコードで開始するか、用意されているいずれかのサンプル アプリケーションを使用することができます。 次に、そのアプリケーションをさまざまな Azure サービス (Virtual Machines、App Service、Azure Kubernetes Servies (AKS)、Azure SQL Database、Azure Service Fabric など) にすばやくデプロイできます。  

  DevOps Projects によって、DevOps パイプラインの初期構成のすべての作業が行われます。たとえば、初期 Git リポジトリの設定、CI/CD パイプラインの構成、監視用の Application Insights リソースの作成、Azure portal 上の DevOps Projects ダッシュボードの作成によるソリューション全体の単一ビューの提供などです。

DevOps Projects を使用して、次のようなことができます。

* アプリケーションを Azure に短時間で展開します
* CI/CD パイプラインのセットアップを自動化します
* CI/CD パイプラインの適切なセットアップ方法を見て理解します
* 特定のシナリオに基づいてリリース パイプラインをさらにカスタマイズします

## <a name="how-do-i-use-devops-projects"></a>DevOps Projects を使用する方法

  DevOps Projects は Azure portal から利用できます。 他の Azure リソースを作成するときと同じように、ポータルから DevOps Projects リソースを作成します。 DevOps Projects では、さまざまな構成オプションに対して、ウィザードに似たステップ バイ ステップのエクスペリエンスが提供されます。  

初期セットアップの一部として、いくつかの構成オプションを選びます。 設定できるオプションは次のとおりです。

* 用意されているサンプル アプリを使うか、独自のコードを作成します
* アプリの言語を選びます
* 言語に基づいてアプリのフレームワークを選びます
* Azure サービス (デプロイ ターゲット) を選びます
* 新しい Azure DevOps 組織を作成するか、既存の組織を使用します 
* Azure サブスクリプションを選びます
* Azure サービスの場所を選びます
* Azure サービスのさまざまな価格レベルからいずれかを選びます

DevOps Projects を使用した後は、Azure portal 上の DevOps Projects ダッシュボードから一元的に、すべてのリソースを削除することもできます。

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps Projects と Azure DevOps 統合

DevOps Projects では、Azure DevOps の機能が利用されています。 DevOps Projects によって、Azure Pipelines で CI/CD パイプラインをセットアップするために必要なすべての作業が自動化されます。 新規または既存の Azure DevOps 組織に Git リポジトリが作成され、サンプル アプリケーションまたは既存のコードが新しい Git リポジトリにコミットされます。  

また、この自動化では、新しいコードがコミットされるたびにビルドが開始するように、ビルドに対する CI トリガーも確立されます。 DevOps Projects によって CD トリガーが作成され、成功した新しいすべてのビルドが、選択した Azure サービスにデプロイされます。  

ビルド パイプラインとリリース パイプラインは、他のシナリオに合わせてカスタマイズできます。 また、他のプロジェクトで使用するために、ビルド パイプラインとリリース パイプラインを複製することもできます。

DevOps Projects を作成した後は、次のことができます。

* ビルドとリリースのパイプラインをカスタマイズします
* Pull request を使ってコードのフローを管理し、高い品質を維持します
* コードをマージして品質の水準を引き上げる前に、各コミットをテストしてビルドします
* アプリケーションと共にバックログと問題を追跡します

## <a name="how-do-i-start-using-devops-projects"></a>DevOps Projects を使い始める方法

* [DevOps Projects の作業開始](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>DevOps Projects のビデオ

* [Azure DevOps Projects を使用した CI/CD の作成](https://www.youtube.com/watch?v=NuYDAs3kNV8)
