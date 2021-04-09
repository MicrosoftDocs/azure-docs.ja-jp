---
title: Azure Functions を使用して Azure DevTest Labs を拡張する | Microsoft Docs
description: Azure Functions を使用して Azure DevTest Labs を拡張する方法について学習します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 620cda83094ee65f421a5529a9d5b51e505ec48e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501160"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Azure Functions を使用した DevTest Labs の拡張
Azure Functions を使用して、DevTest Labs で既にサポートされているシナリオ以外の追加のシナリオをサポートできます。 Azure Functions を使用して、ビジネス固有のニーズに合わせてサービスの組み込み機能を拡張できます。 次の一覧に、考えられるシナリオをいくつか示します。 この記事では、これらのサンプル シナリオの 1 つを実装する方法について説明します。

- ラボ内の仮想マシン (VM) の最上位レベルの概要を提供する
- [リモート デスクトップ ゲートウェイを使用するようにラボを構成する](configure-lab-remote-desktop-gateway.md)
- 社内サポート ページのコンプライアンス レポート
- サブスクリプションで高いアクセス許可が必要な操作をユーザーが完了できるようにする
- [DevTest Labs イベントに基づいてワークフローを開始する](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>概要
[Azure Functions](../azure-functions/functions-overview.md) は Azure のサーバーレス コンピューティング プラットフォームです。 DevTest Labs を使用したソリューションで Azure Functions を使用すると、独自のカスタム コードを使用して既存の機能を拡張することができます。 Azure Functions の詳細については、[Azure Functions のドキュメント](../azure-functions/functions-overview.md)を参照してください。 この記事では、DevTest Labs の要件を満たすことや、シナリオを完成させるのに、Azure Functions がどのように役立つのかを説明するために、ラボの VM の最上位レベルの概要を提供する例を次のように使用します。

**要件/シナリオ例**:ユーザーは、ラボ内のすべての VM に関する詳細 (オペレーティング システム、所有者、適用されるすべての成果物など) を表示できます。  また、**Windows Update の適用** の成果物が最近適用されていない場合は、簡単に適用する方法があります。

このシナリオを完成させるには、次の図に示すように 2 つの関数を使用します。  

![全体的なフロー](./media/extend-devtest-labs-azure-functions/flow.png)

これらのサンプル関数のソース コードは、[DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions)にあります (C# と PowerShell のどちらの実装でも使用できます)。

- **UpdateInternalSupportPage**:この関数では、DevTest Labs に対してクエリが行われ、仮想マシンの詳細を使用して社内サポート ページが直接更新されます。
- **ApplyWindowsUpdateArtifact**:ラボ内の VM の場合、この関数では、**Windows Update** の成果物が適用されます。

## <a name="how-it-works"></a>しくみ
ユーザーが DevTest Labs で **[社内サポート]** ページを選択すると、VM、ラボ所有者、およびサポート連絡先に関する情報が事前に入力されたページが表示されます。  

**[Select here to refresh]\(更新するにはここを選択\)** ボタンを選択すると、ページから、1 番目の Azure Functions が呼び出されます。**UpdateInternalSupportPage**。 この関数では、DevTest Labs に情報が照会され、新しい情報で **[社内サポート]** ページが書き換えられます。

Windows Update の成果物が最近適用されていない VM に対して実行できる追加のアクションがあります。VM に Windows 更新プログラムを適用するボタンがあります。 VM に対して * **[Windows Update の実行]** ボタンを選択すると、ページから、2 番目の Azure Functions が呼び出されます。**ApplyWindowsUpdateArtifact**。 この関数では、仮想マシンが実行されているかどうかを確認され、実行されている場合は、[Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) の成果物が直接適用されます。

## <a name="step-by-step-walkthrough"></a>ステップバイステップのチュートリアル
このセクションでは、 **[社内サポート]** ページの更新に必要な Azure リソースの設定を順を追って説明します。 このチュートリアルでは、DevTest Labs の拡張の例を示します。 このパターンは他のシナリオに使用できます。

### <a name="step-1-create-a-service-principal"></a>手順 1:サービス プリンシパルの作成 
最初の手順では、ラボが含まれるサブスクリプションへのアクセス許可を持つサービス プリンシパルを取得します。 サービス プリンシパルは、パスワードベースの認証を使用する必要があります。 これは [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)、[Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)、または [Azure portal](../active-directory/develop/howto-create-service-principal-portal.md) で実行できます。 使用するサービス プリンシパルが既にある場合は、この手順を省略できます。

サービス プリンシパルの **アプリケーション ID**、**キー**、および **テナント ID** をメモしておきます。 このチュートリアルで後ほど必要になります。 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>手順 2:サンプルをダウンロードして Visual Studio 2019 で開く
[C# Azure Functions サンプル](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)のコピーをローカルにダウンロードします (リポジトリを複製するか、[ここ](https://github.com/Azure/azure-devtestlab/archive/master.zip)からリポジトリをダウンロードします)。  

1. Visual Studio 2019 でサンプル ソリューションを開きます。  
1. Visual Studio の **Azure 開発** ワークロードをインストールします (まだインストールされていない場合)。 **[ツール]**  ->  **[ツールと機能の取得]** メニュー項目からインストールできます。

    ![[Azure の開発] ワークロード](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. ソリューションをビルドします。 **[ビルド]** 、 **[ソリューションのビルド]** メニュー項目の順に選択します。

### <a name="step-3-deploy-the-sample-to-azure"></a>手順 3:サンプルを Azure にデプロイする
Visual Studio の **ソリューション エクスプローラー** ウィンドウで、**AzureFunctions** プロジェクトを右クリックし、 **[発行]** を選択します。 ウィザードに従って、新規または既存の Azure Functions アプリへの発行を完了します。 Visual studio を使用した Azure Functions の開発とデプロイの詳細については、[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md)に関する記事を参照してください。

![[発行] ダイアログ](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>手順 4:アプリケーション設定を収集する
関数が発行されたら、Azure portal からこれらの関数の URL を取得する必要があります。 

1. [Azure Portal](https://portal.azure.com) に移動します。 
1. 関数アプリを見つけます。
1. **[関数アプリ]** ページで、関数を選択します。 
1. 次の画像に示すように、 **[関数の URL の取得]** を選択します。 

    ![Azure 関数の URL](./media/extend-devtest-labs-azure-functions/function-url.png)
4. URL をコピーして保存します。 他の Azure Functions についても同じ手順を繰り返します。 

アプリケーション ID、キー、テナント ID などのサービス プリンシパルに関する追加情報も必要になります。


### <a name="step-5--update-application-settings"></a>手順 5:アプリケーション設定を更新する
Visual Studio で、Azure Functions を発行したら、 **[アクション]** で **[Azure App Service の設定を編集する]** を選択します。 次のアプリケーション設定 (リモート) を更新します。

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (既定値は 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![アプリケーションの設定](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>手順 6:Azure Functions をテストする
このチュートリアルの最後の手順では、Azure Functions をテストします。  

1. 手順 3 で作成した関数アプリの **UpdateInternalSupportPage** 関数に移動します。 
1. ページの右側にある **[テスト]** を選択します。 
1. ルート プロパティ (LABNAME、RESOURCEGROUPNAME、および SUBSCRIPTIONID) に入力します。
1. **[実行]** を選択して関数を実行します。  

    この関数により、指定したラボの社内サポート ページが更新されます。 ユーザーが次回、関数を直接呼び出すためのボタンも含まれています。

    ![関数のテスト](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>次のステップ
Azure Functions は、既に組み込まれているもの以外の DevTest Labs の機能を拡張するのに役立ち、お客様のチーム固有の要件を満たすのに役立ちます。 このパターンは、さらに多くのことをカバーするために拡張および拡大できます。  DevTest Labs の詳細については、次の記事を参照してください。 

- [DevTest Labs の企業向け参照アーキテクチャ](devtest-lab-reference-architecture.md)
- [よく寄せられる質問](devtest-lab-faq.md)
- [DevTest Labs のスケールアップ](devtest-lab-guidance-scale.md)
- [PowerShell を使用した DevTest Labs の自動化](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








