---
title: Visual Studio を利用して仮想マシン スケールをデプロイする
description: Visual Studio とリソース マネージャーのテンプレートを利用して仮想マシン スケール セットをデプロイする
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2db281f1b3278e37deca8486971a7f7a83f85ccf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86503083"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Visual Studio による仮想マシン スケール セットの作成方法

この記事では、Visual Studio のリソース グループの配置を使用して、Azure 仮想マシン スケール セットをデプロイする方法について説明します。

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) は、自動スケーリングと負荷分散機能を使用して、一連の類似する仮想マシンをデプロイし、管理するための Azure コンピューティング リソースです。 仮想マシン スケール セットのプロビジョニングとデプロイは、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を使用して実行できます。 Azure Resource Manager テンプレートは、Azure CLI、PowerShell、REST を使用してデプロイでき、Visual Studio から直接デプロイすることもできます。 Visual Studio には、Azure リソース グループの配置プロジェクトの一部としてデプロイできる一連のサンプル テンプレートが用意されています。

Azure リソース グループの配置を使用すると、関連する一連の Azure リソースをグループ化し、1 回のデプロイ操作で発行できます。 詳細については、「[Visual Studio での Azure リソース グループの作成とデプロイ](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Visual Studio で Virtual Machine Scale Sets をデプロイするには、次の前提条件が必要です。

* Visual Studio 2013 以降
* Azure SDK 2.7、2.8、または 2.9

>[!NOTE]
>この記事では、Visual Studio 2019 と [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) を使用します。

## <a name="create-a-project"></a>プロジェクトを作成する<a name="creating-a-project"></a> 

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、C# の **[Azure リソース グループ]** を選択し、**[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、名前を入力し、**[作成]** を選択します。

    ![プロジェクトの名前を指定して作成する](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. テンプレートの一覧から、**Windows 仮想マシン スケール セット** テンプレートまたは **Linux 仮想マシン スケール セット** テンプレートを選択します。 **[OK]** を選択します。

   ![仮想マシン テンプレートを作成する](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

プロジェクトを作成した後、**ソリューション エクスプローラー** には、PowerShell デプロイ スクリプト、Azure Resource Manager テンプレート、仮想マシン スケール セットのパラメーター ファイルが含まれるようになります。

## <a name="customize-your-project"></a>プロジェクトのカスタマイズ

これで、テンプレートを編集して、アプリケーションのニーズに合わせてカスタマイズできるようになります。 仮想マシン拡張機能のプロパティを追加したり、負荷分散規則を編集したりすることができます。 既定では、仮想マシン スケール セット テンプレートは **AzureDiagnostics** 拡張機能をデプロイするように構成されているので、自動スケール規則を簡単に追加できます。 また、テンプレートでは、インバウンド NAT 規則で構成されたパブリック IP アドレスでロード バランサーがデプロイされます。

ロード バランサーにより、SSH (Linux) または RDP (Windows) で仮想マシン インスタンスに接続できます。 フロントエンド ポートの範囲は 50000 からです。 Linux の場合、ポート 50000 に SSH 接続すると、負荷分散により、スケール セット内の最初の仮想マシンのポート 22 にルーティングされます。 ポート 50001 に接続すると、2 つ目の仮想マシンのポート 22 にルーティングされます。以降も同様です。

 Visual Studio でテンプレートを編集するときは、**JSON アウトライン** を使用することをお勧めします。 パラメーター、変数、リソースを整理ことができます。 スキーマを認識させることによって、デプロイするテンプレートのエラーを事前に Visual Studio で検出することができます。

![JSON Explorer](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>プロジェクトをデプロイする

Azure Resource Manager テンプレートをデプロイして、仮想マシン スケール セット リソースを作成します。

1. **ソリューション エクスプローラー** でプロジェクトを右クリックし、**[配置]** > **[新規]** を選択します。

    ![プロジェクトのデプロイ](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. **[リソース グループに配置する]** で、使用するサブスクリプションを選択し、リソース グループを選択します。 必要な場合は、リソース グループを作成できます。

1. 次に、**[パラメーターの編集]** を選択して、テンプレートに渡すパラメーターを入力します。

   ![サブスクリプションとリソース グループを入力する](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. オペレーティング システムのユーザー名とパスワードを指定します。 デプロイを作成するにはこれらの値が必要です。 PowerShell Tools for Visual Studio がインストールされていない場合は、**パスワードの保存** を指定するチェック ボックスをオンにして PowerShell コマンド プロンプトが非表示にならないようにするか、または [KeyVault のサポート](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)を使用します。 **[保存]** を選択して続行します。

    ![デプロイのパラメーターを編集する](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. **[リソース グループに配置する]** で、**[配置]** を選択します。 これにより、**Deploy-AzureResourceGroup.ps1** スクリプトが実行されます。 **[出力]** ウィンドウにデプロイの進行状況が表示されます。

   ![出力で結果が表示される](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>仮想マシン スケール セットを確認する<a name="exploring-your-virtual-machine-scale-set"></a>

**[表示]**  >  **[Cloud Explorer]** を選択して、新しい仮想マシン スケール セットを表示します。 必要に応じて、**[すべてを最新の情報に更新]** を使用します。

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** を使用すると、アプリケーションの開発中に、Visual Studio で Azure のリソースを管理できます。 仮想マシン スケール セットは、[Azure Portal](https://portal.azure.com) および [Azure リソース エクスプローラー](https://resources.azure.com/)で確認することもできます。

 ポータルでは、Web ブラウザーで Azure インフラストラクチャを管理する最適な方法が提供されます。 Azure Resource Explorer では、Azure リソースを探してデバッグする簡単な方法が提供されます。 Azure Resource Explorer にはインスタンス ビューが用意されており、見ているリソースの PowerShell コマンドも表示されます。

## <a name="next-steps"></a>次のステップ

Visual Studio を使用して Virtual Machine Scale Sets を正常にデプロイしたら、実際のアプリケーションの要件に合わせてプロジェクトをさらにカスタマイズできます。 たとえば、**Insights** リソースを追加して自動スケーリングを構成します。 スタンドアロン仮想マシンなどのテンプレートにインフラストラクチャを追加したり、カスタム スクリプト拡張機能を使用してアプリケーションをデプロイしたりすることができます。 テンプレートの適切なサンプルを、[Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates)の GitHub リポジトリで入手できます。 `vmss` を検索します。
