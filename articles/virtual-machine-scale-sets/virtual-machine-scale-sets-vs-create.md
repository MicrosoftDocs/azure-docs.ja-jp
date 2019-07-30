---
title: Visual Studio を利用して仮想マシン スケール セットをデプロイする | Microsoft Docs
description: Visual Studio とリソース マネージャーのテンプレートを利用して仮想マシン スケール セットをデプロイする
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204716"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Visual Studio による仮想マシン スケール セットの作成方法
この記事では、Visual Studio の "リソース グループの配置" を使用して Azure 仮想マシン スケール セットをデプロイする方法について説明します。

[Azure 仮想マシン スケール セット](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)は、自動スケールと負荷分散機能を使用して、一連の類似する仮想マシンをデプロイし、管理するための Azure コンピューティング リソースです。 仮想マシン スケール セットのプロビジョニングとデプロイは、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を使用して実行できます。 Azure Resource Manager テンプレートは、Azure CLI、PowerShell、REST を使用してデプロイできます。また、Visual Studio から直接デプロイすることもできます。 Visual Studio には、"Azure リソース グループの配置" プロジェクトの一部としてデプロイできる一連のサンプル テンプレートが用意されています。

Azure リソース グループの配置を使用すると、関連する一連の Azure リソースをグループ化し、1 回のデプロイ操作で発行できます。 制限の詳細については、以下を参照してください。[Visual Studio での Azure リソース グループの作成とデプロイ](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## <a name="pre-requisites"></a>前提条件
Visual Studio で仮想マシン スケール セットをデプロイするには、次のものが必要です。

* Visual Studio 2013 以降
* Azure SDK 2.7、2.8、または 2.9

>[!NOTE]
>以降の手順は、Visual Studio と [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) を使用していることを前提としています。

## <a name="creating-a-project"></a>プロジェクトの作成
1. Visual Studio で **[ファイル]、[新規作成]、[プロジェクト]** の順に選択して、新しいプロジェクトを作成します。
   
    ![File New][file_new]

2. **[Visual C#] の [Cloud]** で **[Azure Resource Manager]** を選択して、Azure Resource Manager テンプレートをデプロイするためのプロジェクトを作成します。
   
    ![Create Project][create_project]

3. テンプレートの一覧から、Linux または Windows の仮想マシン スケール セット テンプレートを選択します。
   
   ![Select Template][select_Template]

4. プロジェクトを作成すると、PowerShell デプロイ スクリプト、Azure Resource Manager テンプレート、仮想マシン スケール セットのパラメーター ファイルが表示されます。
   
    ![ソリューション エクスプローラー][solution_explorer]

## <a name="customize-your-project"></a>プロジェクトのカスタマイズ
これで、アプリケーションのニーズに応じてテンプレートを編集してカスタマイズできます。たとえば、VM 拡張機能のプロパティを追加したり、負荷分散の規則を編集したりできます。 既定では、仮想マシン スケール セット テンプレートは AzureDiagnostics 拡張機能をデプロイするように構成されているので、自動スケール規則を簡単に追加できます。 また、受信 NAT 規則で構成されたパブリック IP アドレスでロード バランサーがデプロイされます。 

ロード バランサーにより、SSH (Linux) または RDP (Windows) で VM インスタンスに接続できます。 フロントエンド ポートの範囲は 50000 からです。 Linux の場合、ポート 50000 に SSH 接続すると、スケール セットの最初の VM のポート 22 にルーティングされます。 ポート 50001 に接続すると、2 つ目の VM のポート 22 にルーティングされます。以降も同様です。

 Visual Studio でテンプレートを編集するときは、JSON アウトラインを使用してパラメーター、変数、リソースを整理することをお勧めします。 スキーマを認識させることによって、デプロイするテンプレートのエラーを事前に Visual Studio で検出することができます。

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>プロジェクトをデプロイする
1. Azure Resource Manager テンプレートをデプロイして、仮想マシン スケール セット リソースを作成します。 プロジェクト ノードを右クリックし、 **[配置]、[新しい配置]** の順に選択します。
   
    ![テンプレートのデプロイ][5deploy_Template]
    
2. [リソース グループに配置する] ダイアログで該当するサブスクリプションを選択します。
   
    ![テンプレートのデプロイ][6deploy_Template]

3. ここから、テンプレートのデプロイ先となる Azure リソース グループを作成できます。
   
    ![New Resource Group][new_resource]

4. 次に、 **[パラメーターの編集]** をクリックして、テンプレートに渡すパラメーターを入力します。 デプロイを作成する際に必要となる、OS のユーザー名とパスワードを指定します。 PowerShell Tools for Visual Studio をインストールしていない場合は、隠された PowerShell コマンド ライン プロンプトを回避するために **[パスワードの保存]** チェック ボックスをオンにするか、[KeyVault のサポート](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)を使用します。
   
    ![Edit Parameters][edit_parameters]

5. **[配置]** をクリックします。 **[出力]** ウィンドウにデプロイの進行状況が表示されます。 このアクションでは、**Deploy-AzureResourceGroup.ps1** スクリプトを実行しています。
   
   ![Output Window][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>仮想マシン スケール セットの確認
デプロイが完了したら、Visual Studio の **Cloud Explorer** で新しい仮想マシン スケール セットを確認できます (一覧を最新の情報に更新してください)。 アプリケーションの開発中に、Visual Studio からクラウド エクスプローラーを使って Azure のリソースを管理できます。 仮想マシン スケール セットは、[Azure Portal](https://portal.azure.com) および [Azure リソース エクスプローラー](https://resources.azure.com/)で確認することもできます。

![Cloud Explorer][cloud_explorer]

 ポータルの優れている点は、Azure のインフラストラクチャを Web ブラウザーを使って視覚的に管理できることです。一方、Azure Resource Explorer の利点は、Azure のリソースを手軽に調査し、デバッグできることです。"インスタンス ビュー" にウィンドウを追加したり、着目しているリソースの PowerShell コマンドを表示したりすることができます。

## <a name="next-steps"></a>次の手順
Visual Studio を使用して仮想マシン スケール セットを正常にデプロイしたら、実際のアプリケーションの要件に合わせてプロジェクトをさらにカスタマイズできます。 たとえば、**Insights** リソースの追加、テンプレートへのインフラストラクチャ (スタンドアロン VM など) の追加、またはカスタム スクリプト拡張機能を使用したアプリケーションのデプロイによって自動スケールを構成します。 サンプル テンプレートは、[Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates) GitHub リポジトリで入手できます ("vmss" で検索してください)。

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
