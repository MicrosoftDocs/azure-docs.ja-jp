---
title: チュートリアル - Azure Linux 仮想マシンのローリング デプロイを構成する
description: このチュートリアルでは、継続的デプロイ (CD) パイプラインを設定する方法について説明します。 このパイプラインは、ローリング デプロイ戦略を使用して Azure Linux 仮想マシンのグループを増分更新します。
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: dd47250989be5c31d5f0ade2b602b9d6af535d83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564001"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>チュートリアル - Azure Linux 仮想マシンのローリング デプロイ戦略を構成する

Azure DevOps は、Azure リソースの DevOps プロセスの各段階を自動化する組み込みの Azure サービスです。 Azure と Azure DevOps を使用すれば、仮想マシン、Web アプリ、Kubernetes など、アプリで使用されるリソースが何であれ、Infrastructure as Code (IaC)、継続的インテグレーション、継続的なテスト、継続的デリバリー、継続的監視を導入することができます。

![Azure portal の [サービス] で [Azure DevOps] を選択したところ](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>サービスとしてのインフラストラクチャ (IaaS) - CI/CD を構成する

Azure Pipelines では、仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。 Azure VM に対する継続的デリバリー パイプラインは、Azure portal から構成できます。

この記事では、複数マシンをローリング デプロイするための CI/CD パイプラインを Azure portal から設定する方法について説明します。 Azure portal では、他にも[カナリア](./tutorial-azure-devops-canary-strategy.md)や[ブルーグリーン](./tutorial-azure-devops-blue-green-strategy.md)などの戦略がサポートされます。

### <a name="configure-cicd-on-virtual-machines"></a>仮想マシンで CI/CD を構成する

[配置グループ](/azure/devops/pipelines/release/deployment-groups)には、ターゲットとして仮想マシンを追加できます。 それらを複数マシン更新のターゲットにすることができます。 マシンへのデプロイ後は、配置グループ内の **[デプロイ履歴]** を表示します。 このビューで、VM からパイプラインに、そこからさらにコミットまで追跡できます。

### <a name="rolling-deployments"></a>ローリング デプロイ

ローリング デプロイでは、アプリケーションの以前のバージョンのインスタンスがイテレーションのたびに置き換えられます。 固定された一連のマシン (ローリング セット) 上のアプリケーションが、新しいバージョンのインスタンスに置き換わります。 次のチュートリアルでは、仮想マシンへのローリング アップデートを構成する方法を紹介します。

仮想マシンへのローリング アップデートは、Azure portal 内で継続的デリバリー オプションを使用して構成できます。 その詳細な手順は次のとおりです。

1. Azure portal にサインインして仮想マシンに移動します。
1. VM 設定の一番左のペインで、 **[Continuous delivery]\(継続的デリバリー\)** を選択します。 次に、 **[構成]** を選択します。

   ![[Continuous delivery]\(継続的デリバリー\) ペインと [構成] ボタン](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 構成パネルで、 **[Azure DevOps 組織]** を選択して既存のアカウントを選択するか、新たに作成します。 次に、パイプラインを構成するプロジェクトを選択します。  

   ![[継続的デリバリー] パネル](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 配置グループは、物理環境を表すデプロイ ターゲット マシンの論理的な集まりです。 例として、開発、テスト、UAT、運用があります。 新しい配置グループを作成することも、既存の配置グループを選択することもできます。
1. 仮想マシンにデプロイするパッケージを発行するビルド パイプラインを選択します。 発行されたパッケージには、そのルート フォルダーの deployscripts フォルダーに、deploy.ps1 または deploy.sh という名前のデプロイ スクリプトが格納されていなければならないことに注意してください。 このデプロイ スクリプトがパイプラインによって実行されます。
1. **[配置方法]** で **[ローリング]** を選択します。
1. 必要に応じて、対応するロールのタグを各マシンに付けることができます。 たとえば、"web" や "db" といったタグが考えられます。 特定のロールを持つ VM のみをターゲットとする場合に、これらのタグが役立ちます。
1. 継続的デリバリー パイプラインを構成するには、 **[OK]** を選択します。
1. 構成の完了後、対象の仮想マシンにデプロイするよう継続的デリバリー パイプラインが構成されます。  

   ![[継続的デリバリー] パネルに表示される [デプロイ履歴]](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. 仮想マシンのデプロイの詳細が表示されます。 リンクを選択してパイプラインに移動するか、 **[Release-1]** を選択してデプロイを表示するか、または **[編集]** を選択してリリース パイプラインの定義を編集します。

1. 複数の VM を構成している場合は、他の VM についても手順 2. から手順 4. を繰り返して配置グループに追加します。 パイプラインの実行が既に存在する配置グループを選択すれば、その配置グループに VM が追加されます。 新しいパイプラインは作成されません。
1. 構成が完了したら、パイプラインの定義を選択し、Azure DevOps 組織に移動して、リリース パイプラインの **[編集]** を選択します。

   ![ローリング パイプラインの編集](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. **[dev]\(開発\)** ステージの **[1 job, 1 task]\(1 ジョブ、1 タスク\)** を選択します。 **[デプロイ]** フェーズを選択します。

   ![ローリング パイプライン タスクで [デプロイ] タスクを選択したところ](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. 一番右の構成ペインで、各イテレーションで並列にデプロイするマシンの数を指定できます。 複数のマシンに一度にデプロイする場合は、スライダーを使用してマシン数をパーセンテージで指定できます。  

1. デプロイ スクリプトの実行タスクでは、既定で deploy.ps1 または deploy.sh デプロイ スクリプトが実行されます。スクリプトは、発行されたパッケージのルート フォルダーの deployscripts フォルダーにあります。

   ![[アーティファクト] ペインに表示される deployscripts フォルダーの deploy.sh](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>その他のデプロイ戦略

- [カナリア デプロイ戦略を構成する](./tutorial-azure-devops-canary-strategy.md)
- [ブルーグリーン デプロイ戦略を構成する](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Azure は簡単に始めることができます。 Azure DevOps Projects を使用すれば、次の情報を選択する 3 つのステップだけで、どの Azure サービスでもアプリケーションの実行を開始できます。

- アプリケーションの言語
- ランタイム
- Azure サービス
 
[詳細については、こちらを参照してください](https://azure.microsoft.com/features/devops-projects/)。
 
## <a name="additional-resources"></a>その他のリソース

- [Azure DevOps Projects を使用して Azure 仮想マシンにデプロイする](../../devops-project/azure-devops-project-vms.md)
- [Azure 仮想マシン スケール セットへのアプリの継続的デプロイを導入する](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)