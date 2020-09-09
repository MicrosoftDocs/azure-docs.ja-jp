---
title: Azure DevOps Starter の概要 | Microsoft Docs
description: Azure portal の単一のビューからアプリを起動、デプロイ、管理するのに、Azure DevOps Starter がどのように役立つかについて説明します。
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
ms.openlocfilehash: 91e62cae242279e1923fc31970c233ec70bf7f11
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461342"
---
# <a name="overview-of-azure-devops-starter"></a>Azure DevOps Starter の概要

 Azure DevOps Starter を利用すると、Azure での作業を簡単に開始できるようになります。 Azure portal での簡単な手順により、任意の Azure サービス上で目的のアプリを起動するのに役立ちます。 

 DevOps Starter では、アプリケーションの開発、デプロイ、および監視に必要なすべてのものが設定されます。 DevOps Starter ダッシュボードを使用すると、Azure portal の単一のビューからコードのコミット、ビルド、およびデプロイのすべてを監視できます。

## <a name="advantages-of-using-devops-starter"></a>DevOps Starter を使用する利点

  DevOps Starter では、Azure への継続的インテグレーション (CI) および継続的デリバリー (CD) パイプライン全体のセットアップが自動化されます。  既存のコードで開始するか、用意されているいずれかのサンプル アプリケーションを使用することができます。 次に、そのアプリケーションをさまざまな Azure サービス (Virtual Machines、App Service、Azure Kubernetes Servies (AKS)、Azure SQL Database、Azure Service Fabric など) にすばやくデプロイできます。  

  DevOps Starter によって、DevOps パイプラインの初期構成のすべての作業が行われます。たとえば、初期 Git リポジトリの設定、CI/CD パイプラインの構成、監視用の Application Insights リソースの作成、Azure portal 上の DevOps Projects ダッシュボードの作成によるソリューション全体の単一ビューの提供などです。

DevOps Starter を使用して、次のようなことができます。

* アプリケーションを Azure に短時間で展開します
* CI/CD パイプラインのセットアップを自動化します
* CI/CD パイプラインの適切なセットアップ方法を見て理解します
* 特定のシナリオに基づいてリリース パイプラインをさらにカスタマイズします

## <a name="how-to-use-devops-starter"></a>DevOps Starter の使用方法

  DevOps Starter は Azure portal から利用できます。 他の Azure リソースを作成するときと同じように、ポータルから DevOps Starter リソースを作成します。 DevOps Projects では、さまざまな構成オプションに対して、ウィザードに似たステップ バイ ステップのエクスペリエンスが提供されます。  

初期セットアップの一部として、いくつかの構成オプションを選びます。 設定できるオプションは次のとおりです。

* 用意されているサンプル アプリを使うか、独自のコードを作成します
* アプリの言語を選びます
* 言語に基づいてアプリのフレームワークを選びます
* Azure サービス (デプロイ ターゲット) を選びます
* 新しい Azure DevOps 組織を作成するか、既存の組織を使用します 
* Azure サブスクリプションを選びます
* Azure サービスの場所を選びます
* Azure サービスのさまざまな価格レベルからいずれかを選びます

DevOps Starter を使用した後は、Azure portal 上の DevOps Starter ダッシュボードから一元的に、すべてのリソースを削除することもできます。

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter と Azure DevOps 統合

DevOps Starter では、Azure DevOps の機能が利用されています。 DevOps Starter によって、Azure Pipelines で CI/CD パイプラインをセットアップするために必要なすべての作業が自動化されます。 新規または既存の Azure DevOps 組織に Git リポジトリが作成され、サンプル アプリケーションまたは既存のコードが新しい Git リポジトリにコミットされます。  

また、この自動化では、新しいコードがコミットされるたびにビルドが開始するように、ビルドに対する CI トリガーも確立されます。 DevOps Starter によって CD トリガーが作成され、成功した新しいすべてのビルドが、選択した Azure サービスにデプロイされます。  

ビルド パイプラインとリリース パイプラインは、他のシナリオに合わせてカスタマイズできます。 また、他のプロジェクトで使用するために、ビルド パイプラインとリリース パイプラインを複製することもできます。

DevOps Starter を作成した後は、次のことができます。

* ビルドとリリースのパイプラインをカスタマイズします
* Pull request を使ってコードのフローを管理し、高い品質を維持します
* コードをマージして品質の水準を引き上げる前に、各コミットをテストしてビルドします
* アプリケーションと共にバックログと問題を追跡します

## <a name="getting-started-with-devops-starter"></a>DevOps Starter を使用した作業の開始

* [DevOps Starter を使用して作業を開始する](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>DevOps Starter のビデオ

* [Azure DevOps Starter を使用した CI/CD の作成](https://www.youtube.com/watch?v=NuYDAs3kNV8)
