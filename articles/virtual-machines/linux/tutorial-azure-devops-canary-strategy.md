---
title: チュートリアル - Azure Linux Virtual Machines のカナリア デプロイを構成する
description: このチュートリアルでは、継続的デプロイ (CD) パイプラインを設定する方法について説明します。 このパイプラインは、カナリア デプロイ戦略を使用して Azure Linux 仮想マシンのグループを更新します。
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
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552509"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>チュートリアル - Azure Linux Virtual Machines のカナリア デプロイ戦略を構成する

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>サービスとしてのインフラストラクチャ (IaaS) - CI/CD を構成する

Azure Pipelines では、仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。 Azure VM に対する継続的デリバリー パイプラインは、Azure portal から構成できます。

この記事では、カナリア戦略を使用して複数マシンをデプロイする CI/CD パイプラインを設定する方法について説明します。 Azure portal では、他にも[ローリング](./tutorial-devops-azure-pipelines-classic.md)や[ブルーグリーン](./tutorial-azure-devops-blue-green-strategy.md)などの戦略がサポートされます。

### <a name="configure-cicd-on-virtual-machines"></a>仮想マシンで CI/CD を構成する

[配置グループ](/azure/devops/pipelines/release/deployment-groups)には、ターゲットとして仮想マシンを追加できます。 それらを複数マシン更新のターゲットにすることができます。 マシンへのデプロイ後は、配置グループ内の **[デプロイ履歴]** を表示します。 このビューで、VM からパイプラインに、そこからさらにコミットまで追跡できます。

### <a name="canary-deployments"></a>カナリア デプロイ

カナリア デプロイでは、一部の少数のユーザーにゆっくり変更を展開することによりリスクを軽減します。 新バージョンへの信頼度が上がってきたら、インフラストラクチャ内のより多くのサーバーにリリースし、より多くのユーザーを誘導することができます。

仮想マシンへのカナリア デプロイは、Azure portal から継続的デリバリー オプションを使用して構成することができます。 その詳細な手順は次のとおりです。

1. Azure portal にサインインして仮想マシンに移動します。
1. VM 設定の一番左のペインで、 **[Continuous delivery]\(継続的デリバリー\)** を選択します。 次に、 **[構成]** を選択します。

   ![[Continuous delivery]\(継続的デリバリー\) ペインと [構成] ボタン](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 構成パネルで、 **[Azure DevOps 組織]** を選択して既存のアカウントを選択するか、新たに作成します。 次に、パイプラインを構成するプロジェクトを選択します。  

   ![[継続的デリバリー] パネル](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 配置グループは、物理環境を表すデプロイ ターゲット マシンの論理的な集まりです。 例として、開発、テスト、UAT、運用があります。 新しい配置グループを作成することも、既存の配置グループを選択することもできます。
1. 仮想マシンにデプロイするパッケージを発行するビルド パイプラインを選択します。 発行されたパッケージには、そのルート フォルダーの deployscripts フォルダーに、deploy.ps1 または deploy.sh という名前のデプロイ スクリプトが格納されていなければならないことに注意してください。 このデプロイ スクリプトがパイプラインによって実行されます。
1. **[配置方法]** で **[カナリア]** を選択します。
1. カナリア デプロイに含める VM に "canary (カナリア)" タグを追加します。 カナリア デプロイの成功後のデプロイに含める VM には "prod (運用)" タグを追加します。 タグがあることで、特定のロールを持つ VM のみをターゲットにすることができます。

   ![[継続的デリバリー] パネルの [配置方法] の値で [カナリア] を選択](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. **[OK]** を選択して、仮想マシンにデプロイする継続的デリバリー パイプラインを構成します。

   ![カナリア パイプライン](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. 仮想マシンのデプロイの詳細が表示されます。 リンクを選択すると、Azure DevOps のリリース パイプラインに移動することができます。 リリース パイプラインの **[編集]** を選択すると、パイプラインの構成が表示されます。 パイプラインには、次の 3 つのフェーズがあります。

   1. このフェーズは配置グループ フェーズです。 "カナリア" としてタグ付けされた VM にアプリケーションがデプロイされます。
   1. このフェーズでは、パイプラインが一時停止します。パイプラインは、実行を再開するために、手動での介入を待ちます。
   1. これも配置グループ フェーズです。 今度は、"prod (運用)" としてタグ付けされた VM に更新プログラムがデプロイされます。

      ![カナリア デプロイ タスクの [配置グループ] ペイン](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. パイプラインの実行を再開する前に、少なくとも 1 つの VM が "prod (運用)" としてタグ付けされていることを確認してください。 パイプラインの 3 番目のフェーズでは、"prod (運用)" タグが付けられた VM にのみアプリケーションがデプロイされます。

1. デプロイ スクリプトの実行タスクでは、既定で deploy.ps1 または deploy.sh デプロイ スクリプトが実行されます。スクリプトは、発行されたパッケージのルート フォルダーの deployscripts フォルダーにあります。 選択したビルド パイプラインによって、パッケージのルート フォルダーにデプロイが発行されていることを確認してください。

   ![[アーティファクト] ペインに表示される deployscripts フォルダーの deploy.sh](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>その他のデプロイ戦略
- [ローリング デプロイ戦略を構成する](./tutorial-devops-azure-pipelines-classic.md)
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