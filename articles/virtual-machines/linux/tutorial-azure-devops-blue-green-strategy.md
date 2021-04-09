---
title: チュートリアル - Azure Linux 仮想マシンのカナリア デプロイを構成する
description: このチュートリアルでは、継続的デプロイ (CD) パイプラインを設定する方法について説明します。 このパイプラインは、ブルーグリーン デプロイ戦略を使用して Azure Linux 仮想マシンのグループを更新します。
author: moala
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
ms.openlocfilehash: 4545891cce926f049673cd2c2380a8309f2e71a1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552589"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>チュートリアル - Azure Linux 仮想マシンのブルーグリーン デプロイ戦略を構成する

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>サービスとしてのインフラストラクチャ (IaaS) - CI/CD を構成する

Azure Pipelines では、仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。 Azure VM に対する継続的デリバリー パイプラインは、Azure portal から構成できます。

この記事では、ブルーグリーン戦略を使用して複数マシンをデプロイする CI/CD パイプラインを設定する方法について説明します。 Azure portal では、他にも[ローリング](./tutorial-devops-azure-pipelines-classic.md)や[カナリア](./tutorial-azure-devops-canary-strategy.md)などの戦略がサポートされます。

### <a name="configure-cicd-on-virtual-machines"></a>仮想マシンで CI/CD を構成する

[配置グループ](/azure/devops/pipelines/release/deployment-groups)には、ターゲットとして仮想マシンを追加できます。 それらを複数マシン更新のターゲットにすることができます。 マシンへのデプロイ後は、配置グループ内の **[デプロイ履歴]** を表示します。 このビューで、VM からパイプラインに、そこからさらにコミットまで追跡できます。

### <a name="blue-green-deployments"></a>ブルーグリーン デプロイ

ブルーグリーン デプロイでは、同一のスタンバイ環境を設けることでダウンタイムを短くします。 どの時点でも、稼動する環境は 1 つだけです。

新リリースに向けた準備の過程で、テストの最終ステージをグリーン環境で行います。 グリーン環境でソフトウェアが正常に動作していれば、トラフィックを切り替えて、すべての受信要求をグリーン環境に誘導します。 ブルー環境はアイドルとなります。

仮想マシンへのブルーグリーン デプロイは、Azure portal から継続的デリバリー オプションを使用して構成することができます。 その詳細な手順は次のとおりです。

1. Azure portal にサインインして仮想マシンに移動します。
1. VM 設定の一番左のペインで、 **[Continuous delivery]\(継続的デリバリー\)** を選択します。 次に、 **[構成]** を選択します。

   ![[Continuous delivery]\(継続的デリバリー\) ペインと [構成] ボタン](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 構成パネルで、 **[Azure DevOps 組織]** を選択して既存のアカウントを選択するか、新たに作成します。 次に、パイプラインを構成するプロジェクトを選択します。  

   ![[継続的デリバリー] パネル](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 配置グループは、物理環境を表すデプロイ ターゲット マシンの論理的な集まりです。 例として、開発、テスト、UAT、運用があります。 新しい配置グループを作成することも、既存の配置グループを選択することもできます。
1. 仮想マシンにデプロイするパッケージを発行するビルド パイプラインを選択します。 発行されたパッケージには、そのルート フォルダーの deployscripts フォルダーに、deploy.ps1 または deploy.sh という名前のデプロイ スクリプトが格納されていなければならないことに注意してください。 このデプロイ スクリプトがパイプラインによって実行されます。
1. **[配置方法]** で **[ブルーグリーン]** を選択します。
1. ブルーグリーン デプロイに含める VM に "blue (ブルー)" または "green (グリーン)" タグを追加します。 VM がスタンバイロール用である場合は、"green (グリーン)" のタグを付けます。 それ以外の場合は、"blue (ブルー)" タグを付けます。

   ![[継続的デリバリー] パネルの [配置方法] の値で [ブルーグリーン] を選択](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. **[OK]** を選択して、仮想マシンにデプロイする継続的デリバリー パイプラインを構成します。

   ![ブルーグリーン パイプライン](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. 仮想マシンのデプロイの詳細が表示されます。 リンクを選択すると、Azure DevOps のリリース パイプラインに移動することができます。 リリース パイプラインの **[編集]** を選択すると、パイプラインの構成が表示されます。 パイプラインには、次の 3 つのフェーズがあります。

   1. このフェーズは配置グループ フェーズです。 アプリケーションはスタンバイ VM にデプロイされます。スタンバイ VM は、"green (グリーン)" としてタグ付けされます。
   1. このフェーズでは、パイプラインが一時停止します。パイプラインは、実行を再開するために、手動での介入を待ちます。 パイプラインの実行は、"green (グリーン)" タグの VM に対するデプロイの安定性をユーザーが手動で確認した後に再開できます。
   1. このフェーズでは、VM の "blue (ブルー)" タグと "green (グリーン)" タグが入れ替わります。 旧バージョンのアプリケーションがインストールされている VM が "green (グリーン)" としてタグ付けされます。 次回パイプラインが実行されるときは、それらの VM にアプリケーションがデプロイされます。

      ![ブルーグリーン デプロイ タスクの [配置グループ] ペイン](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. デプロイ スクリプトの実行タスクでは、既定で deploy.ps1 または deploy.sh デプロイ スクリプトが実行されます。スクリプトは、発行されたパッケージのルート フォルダーの deployscripts フォルダーにあります。 選択したビルド パイプラインによって、パッケージのルート フォルダーにデプロイが発行されていることを確認してください。

   ![[アーティファクト] ペインに表示される deployscripts フォルダーの deploy.sh](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>その他のデプロイ戦略

- [ローリング デプロイ戦略を構成する](./tutorial-devops-azure-pipelines-classic.md)
- [カナリア デプロイ戦略を構成する](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Azure は簡単に始めることができます。 Azure DevOps Projects を使用すれば、次の情報を選択する 3 つのステップだけで、どの Azure サービスでもアプリケーションの実行を開始できます。

- アプリケーションの言語
- ランタイム
- Azure サービス

[詳細については、こちらを参照してください](https://azure.microsoft.com/features/devops-projects/)。

## <a name="additional-resources"></a>その他のリソース

- [Azure DevOps Projects を使用して Azure 仮想マシンにデプロイする](../../devops-project/azure-devops-project-vms.md)
- [Azure 仮想マシン スケール セットへのアプリの継続的デプロイを導入する](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)