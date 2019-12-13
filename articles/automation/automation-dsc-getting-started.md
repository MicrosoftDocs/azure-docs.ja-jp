---
title: Azure Automation State Configuration の使用開始
description: Azure Automation State Configuration (DSC) の最も一般的なタスクの説明と例
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fce9a769dd6d88c9926913d22716666284938c1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850960"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Azure Automation State Configuration の使用開始

この記事では、Azure Automation State Configuration を使用して最も一般的なタスク (構成の作成、インポート、コンパイル、管理するマシンのオンボード、レポートの表示など) を実行する方法について説明します。 Azure Automation State Configuration の概要については、[Azure Automation State Configuration の概要](automation-dsc-overview.md)に関するページをご覧ください。 Desired State Configuration (DSC) のドキュメントについては、「[Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)」を参照してください。

この記事は、Azure Automation State Configuration を使用するための詳しい手順を示しています。 この記事で説明されている手順を実行せずに、既に設定されているサンプル環境を使用する場合は、Resource Manager テンプレート ([Azure Automation マネージド ノード テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration)) を使用できます。 このテンプレートを使用すると、Azure Automation State Configuration で管理される Azure VM を含む、完成した Azure Automation State Configuration 環境が設定されます。

## <a name="prerequisites"></a>前提条件

この記事の例を完了するには、次のものが必要です。

- Azure Automation アカウント。 Azure Automation 実行アカウントの作成手順については、 [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)に関するページをご覧ください。
- [サポートされているオペレーティング システム](automation-dsc-overview.md#operating-system-requirements)を実行している Azure Resource Manager VM (クラシックではない)。 VM の作成手順については、「 [Azure ポータルで初めての Windows 仮想マシンを作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>DSC 構成を作成する

ここでは、ノードの割り当て方法に応じて、[Web-Server](/powershell/scripting/dsc/configurations/configurations) Windows 機能 (IIS) が存在するかどうかを確認する、簡単な **DSC 構成**を作成します。

1. [VSCode](https://code.visualstudio.com/docs) (または任意のテキスト エディター) を開始します。
1. 次のテキストを入力します。

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. ファイルを `TestConfig.ps1`という名前で保存します。

この構成は、各ノード ブロックで 1 つのリソース ([WindowsFeature リソース](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)) を呼び出します。このリソースが、**Web-Server** 機能が存在するかどうかを確認します。

## <a name="importing-a-configuration-into-azure-automation"></a>Azure Automation に構成をインポートする

次に、この構成を Automation アカウントにインポートします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ページで、 **[構成管理]** の **[状態の構成 (DSC)]** を選択します。
1. **[状態の構成 (DSC)]** ページで **[構成]** タブをクリックし、 **[+ 追加]** をクリックします。
1. **[構成のインポート]** ページで、コンピューター上の `TestConfig.ps1` ファイルを参照します。

   ![**[構成のインポート]** ブレードのスクリーンショット](./media/automation-dsc-getting-started/AddConfig.png)

1. Click **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Azure Automation で構成を表示する

インポートした構成は、Azure ポータルで表示できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ページで、 **[構成管理]** の **[状態の構成 (DSC)]** を選択します。
1. **[状態の構成 (DSC)]** ページで、 **[構成]** タブをクリックし、 **[TestConfig]** (前の手順でインポートした構成の名前) をクリックします。
1. **[TestConfig の構成]** ページで、 **[構成ソースの表示]** をクリックします。

   ![Screenshot of the TestConfig configuration blade](./media/automation-dsc-getting-started/ViewConfigSource.png)

   **[TestConfig Configuration source (TestConfig の構成ソース)]** ページが開き、構成の PowerShell コードが表示されます。

## <a name="compiling-a-configuration-in-azure-automation"></a>Azure Automation で構成をコンパイルする

目的の状態をノードに適用する前に、その状態を定義する DSC 構成を 1 つ以上のノード構成 (MOF ドキュメント) にコンパイルし、Automation DSC プル サーバーに配置する必要があります。 Azure Automation State Configuration での構成のコンパイルの詳細については、[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)に関するページをご覧ください。
構成のコンパイルの詳細については、「 [DSC 構成](/powershell/scripting/dsc/configurations/configurations)」を参照してください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで、 **[構成]** タブをクリックし、 **[TestConfig]** (前にインポートした構成の名前) をクリックします。
1. **[TestConfig の構成]** ページで **[コンパイル]** をクリックし、 **[はい]** をクリックします。 これにより、コンパイル ジョブが開始されます。

   ![[コンパイル] ボタンが強調表示されている [TestConfig の構成] ページのスクリーンショット](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Azure Automation で構成をコンパイルすると、作成されたノード構成 MOF すべてが自動的にプル サーバーにデプロイされます。

## <a name="viewing-a-compilation-job"></a>コンパイル ジョブを表示する

コンパイルを開始すると、 **[構成]** ページの **[コンパイル ジョブ]** タイルでコンパイル ジョブを確認できます。 **[コンパイル ジョブ]** タイルには、現在実行中のジョブ、完了したジョブ、失敗したジョブが表示されます。 コンパイル ジョブのページを開くと、ジョブに関する情報が表示されます。これには、発生したエラーと警告、構成で使用されている入力パラメーター、コンパイル ログが含まれています。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで、 **[構成]** タブをクリックし、 **[TestConfig]** (前にインポートした構成の名前) をクリックします。
1. **[コンパイル ジョブ]** で、表示するコンパイル ジョブを選択します。 コンパイル ジョブの開始日付のラベルが付いた **[コンパイル ジョブ]** ページが開きます。

   ![[コンパイル ジョブ] ページのスクリーンショット](./media/automation-dsc-getting-started/CompilationJob.png)

1. **[コンパイル ジョブ]** ページで任意のタイルをクリックすると、そのジョブの詳細が表示されます。

## <a name="viewing-node-configurations"></a>ノード構成を表示する

コンパイル ジョブが正常に完了すると、1 つ以上の新しいノード構成が作成されます。 ノード構成とは、プル サーバーにデプロイされ、1 つ以上のノードがプルして適用できるようになる MOF ドキュメントです。 **[状態の構成 (DSC)]** ページでは、Automation アカウントのノード構成を確認できます。 ノード構成の名前は、*ConfigurationName*.*NodeName* という形式です。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ブレードで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで **[コンパイル済み構成]** タブをクリックします。

   ![[コンパイル済み構成] タブのスクリーンショット](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用して管理のために Azure VM をオンボードする

Azure Automation State Configuration を使用すると、Azure VM (クラシックと Resource Manager の両方)、オンプレミスの VM、Linux マシン、AWS VM、オンプレミスの物理マシンを管理できます。 この記事では、Azure Resource Manager VM をオンボードする方法のみを説明します。 他の種類のマシンのオンボードの詳細については、[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)に関するページをご覧ください。

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用して管理のために Azure Resource Manager VM をオンボードするには

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ブレードで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで、 **[ノード]** タブの **[+ 追加]** をクリックします。

   ![[Azure VM の追加] ボタンが強調表示された [DSC ノード] ページのスクリーンショット](./media/automation-dsc-getting-started/OnboardVM.png)

1. **[仮想マシン]** ページで、自分の VM を選択します。
1. **[仮想マシン]** 詳細ページで、 **[+ 接続]** をクリックします。

   > [!IMPORTANT]
   > [サポートされているオペレーティング システム](automation-dsc-overview.md#operating-system-requirements)を実行している Azure Resource Manager VM である必要があります。

2. **[登録]** ページの **[ノード構成名]** ボックスで、VM に適用するノード構成の名前を選択します。 この時点では、名前の入力は省略可能です。 ノードのオンボード後に、割り当てられたノード構成を変更できます。
   **[必要に応じてノードを再起動する]** チェック ボックスをオンにし、 **[OK]** をクリックします。

   ![Screenshot of the Registration blade](./media/automation-dsc-getting-started/RegisterVM.png)

   指定したノード構成は、 **[構成モードの頻度]** に指定された間隔で VM に適用されます。また、VM は、 **[更新頻度]** に指定された間隔でノード構成に対する更新をチェックします。 これらの値の使用方法の詳細については、「[ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaConfig)」を参照してください。

Azure によって VM のオンボード処理が開始されます。 処理が完了すると、Automation アカウントの **[状態の構成 (DSC)]** ページの **[ノード]** タブに VM が表示されます。

## <a name="viewing-the-list-of-managed-nodes"></a>マネージド ノードの一覧を表示する

Automation アカウントの **[状態の構成 (DSC)]** ページの **[ノード]** タブで、管理のためにオンボードされたマシンの一覧を表示できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ブレードで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで、 **[ノード]** タブをクリックします。

## <a name="viewing-reports-for-managed-nodes"></a>マネージド ノードのレポートを表示する

Azure Automation State Configuration でマネージド ノードの整合性チェックが実行されるたびに、そのノードから状態レポートがプル サーバーに送信されます。 これらのレポートは、そのノードのページに表示できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ブレードで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで、 **[ノード]** タブをクリックします。ここでは、構成の状態の概要と各ノードの詳細を確認できます。

   ![ノード ページのスクリーンショット](./media/automation-dsc-getting-started/NodesTab.png)

1. **[ノード]** タブで、ノード レコードをクリックしてレポートを開きます。 レポートの詳細を表示するレポートをクリックします。

   ![Screenshot of the Report blade](./media/automation-dsc-getting-started/NodeReport.png)

各レポートのブレードでは、対応する整合性チェックについて次の状態情報を表示できます。

- レポートの状態。ノードが "準拠" かどうか、構成が "失敗" かどうか、ノードが "非準拠" (ノードが **ApplyandMonitor** モードでも、マシンが望ましい状態でない場合) かどうか。
- 整合性チェックの開始時刻。
- 整合性チェックの合計実行時間。
- 整合性チェックの種類。
- エラー コードやエラー メッセージを含むすべてのエラー。
- 構成で使用されているすべての DSC リソースと各リソースの状態 (ノードがそのリソースの望ましい状態であるかどうか)。各リソースをクリックすると、そのリソースの詳細情報を表示できます。
- ノードの名前、IP アドレス、構成モード。

また、 **[生レポートの表示]** をクリックすると、ノードがサーバーに送信する実際のデータを表示することもできます。
このデータの使用方法の詳細については、「 [DSC レポート サーバーの使用](/powershell/scripting/dsc/pull-server/reportserver)」を参照してください。

ノードがオンボードされてから、最初のレポートが使用可能になるまで、しばらく時間がかかることがあります。 ノードのオンボードから最初のレポートが表示されるまで、最大で 30 分の待つことが必要になる場合があります。

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>別のノード構成にノードを再割り当てする

最初に割り当てたものとは別のノード構成を使用するようにノードを割り当てることができます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ブレードで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで、 **[ノード]** タブをクリックします。
1. **[ノード]** タブで、再割り当てするノードの名前をクリックします。
1. そのノードのページで、 **[ノード構成の割り当て]** をクリックします。

    ![[ノード構成の割り当て] ボタンが強調表示されている [ノード] 詳細ページのスクリーンショット](./media/automation-dsc-getting-started/AssignNode.png)

1. **[ノード構成の割り当て]** ページで、ノードの割り当て先となるノード構成を選択し、 **[OK]** をクリックします。

    ![[ノード構成の割り当て] ページのスクリーンショット](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>ノードの登録を解除する

ノードを Azure Automation DSC で管理する必要がなくなった場合は、ノードの登録を解除することができます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ブレードで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで、 **[ノード]** タブをクリックします。
1. **[ノード]** ページで、登録解除するノードの名前をクリックします。
1. そのノードのページで、 **[登録解除]** をクリックします。

    ![[登録解除] ボタンが強調表示されている [ノード] 詳細ページのスクリーンショット](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>関連記事

- [Azure Automation State Configuration の概要](automation-dsc-overview.md)
- [Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)
- [Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)
- [Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)
- [Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)
