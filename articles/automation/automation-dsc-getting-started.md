---
title: Azure Automation State Configuration の使用を開始する
description: この記事では、Azure Automation State Configuration で最も一般的なタスクの実行方法について説明します。
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051500"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Azure Automation State Configuration の使用を開始する

この記事では、Azure Automation State Configuration を使用して最も一般的なタスク (構成の作成、インポート、コンパイル、管理するマシンのオンボード、レポートの表示など) を実行する手順について説明します。 State Configuration の概要については、[State Configuration の概要](automation-dsc-overview.md)に関するページを参照してください。 Desired State Configuration (DSC) のドキュメントについては、「[Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)」を参照してください。

この記事で説明されている手順を実行せずに、既に設定されているサンプル環境を使用する場合は、[Azure Automation マネージド ノード テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration)を使用できます。 このテンプレートを使用すると、State Configuration (DSC) で管理される Azure VM を含む、完全な State Configuration (DSC) 環境が設定されます。

## <a name="prerequisites"></a>前提条件

この記事の例を完了するには、次のものが必要です。

- Azure Automation アカウント。 Automation アカウントとその要件の詳細については、「[Automation アカウントの認証の概要](./automation-security-overview.md)」を参照してください。
- [サポートされているオペレーティング システム](automation-dsc-overview.md#operating-system-requirements)を実行している Azure Resource Manager VM (クラシックではない)。 VM の作成手順については、「 [Azure ポータルで初めての Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)

## <a name="create-a-dsc-configuration"></a>DSC 構成を作成する

ここでは、ノードの割り当て方法に応じて、[Web-Server](/powershell/scripting/dsc/configurations/configurations) Windows 機能 (IIS) が存在するかどうかを確認する、簡単な **DSC 構成** を作成します。

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
1. ファイルを **TestConfig.ps1** として保存します。

この構成では、各ノード ブロック内で 1 つのリソース ([WindowsFeature リソース](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)) が呼び出されます。 このリソースにより、**Web サーバー** 機能の有無が確認されます。

## <a name="import-a-configuration-into-azure-automation"></a>Azure Automation に構成をインポートする

次に、この構成を Automation アカウントにインポートします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** を選択します。
1. [状態の構成 (DSC)] ページで **[構成]** タブをクリックし、 **[追加]** をクリックします。
1. [構成のインポート] ペインで、コンピューター上の `TestConfig.ps1` ファイルを参照します。

   ![**[構成のインポート]** ブレードのスクリーンショット](./media/automation-dsc-getting-started/AddConfig.png)

1. **[OK]** をクリックします。

## <a name="view-a-configuration-in-azure-automation"></a>Azure Automation で構成を表示する

インポートした構成は、Azure ポータルで表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** を選択します。
1. [状態の構成 (DSC)] ページで **[構成]** タブをクリックし、**TestConfig** をクリックします。 これは、前の手順でインポートした構成の名前です。
1. [TestConfig の構成] ペインで、 **[構成ソースの表示]** をクリックします。

   ![Screenshot of the TestConfig configuration blade](./media/automation-dsc-getting-started/ViewConfigSource.png)

   TestConfig の構成ソース ペインが開き、構成の PowerShell コードが表示されます。

## <a name="compile-a-configuration-in-azure-automation"></a>Azure Automation で構成をコンパイルする

目的の状態をノードに適用する前に、その状態を定義する DSC 構成を 1 つ以上のノード構成 (MOF ドキュメント) にコンパイルし、Automation DSC プル サーバーに配置する必要があります。 State Configuration (DSC) での構成のコンパイルの詳細については、[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)に関するページをご覧ください。
構成のコンパイルの詳細については、「 [DSC 構成](/powershell/scripting/dsc/configurations/configurations)」を参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで **[構成]** タブをクリックし、**TestConfig** をクリックします。 これは、前にインポートした構成の名前です。
1. TestConfig の構成ペインで **[コンパイル]** をクリックし、 **[はい]** をクリックします。 これにより、コンパイル ジョブが開始されます。

   ![[コンパイル] ボタンが強調表示されている [TestConfig の構成] ページのスクリーンショット](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Azure Automation で構成をコンパイルすると、作成されたすべてのノード構成 MOF ファイルが自動的にプル サーバーにデプロイされます。

## <a name="view-a-compilation-job"></a>コンパイル ジョブを表示する

コンパイルを開始すると、 **[構成]** ページの **[コンパイル ジョブ]** タイルでコンパイル ジョブを確認できます。 **[コンパイル ジョブ]** タイルには、現在実行中のジョブ、完了したジョブ、失敗したジョブが表示されます。 コンパイル ジョブのペインを開くと、ジョブに関する情報が表示されます。これには、発生したエラーと警告、構成で使用されている入力パラメーター、コンパイル ログが含まれています。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで **[構成]** タブをクリックし、**TestConfig** をクリックします。 これは、前にインポートした構成の名前です。
1. **[コンパイル ジョブ]** で、表示するコンパイル ジョブを選択します。 コンパイル ジョブの開始日付のラベルが付いた [コンパイル ジョブ] ペインが開きます。

   ![[コンパイル ジョブ] ページのスクリーンショット](./media/automation-dsc-getting-started/CompilationJob.png)

1. [コンパイル ジョブ] ペインで任意のタイルをクリックすると、そのジョブの詳細が表示されます。

## <a name="view-node-configurations"></a>ノード構成を表示する

コンパイル ジョブが正常に完了すると、1 つ以上の新しいノード構成が作成されます。 ノード構成とは、プル サーバーにデプロイされ、1 つ以上のノードがプルして適用できるようになる MOF ドキュメントです。 [状態の構成 (DSC)] ページでは、Automation アカウントのノード構成を確認できます。 ノード構成の名前は、`ConfigurationName.NodeName` という形式です。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで **[コンパイル済み構成]** タブをクリックします。

   ![[コンパイル済み構成] タブのスクリーンショット](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>State Configuration を使用して管理のために Azure Resource Manager VM を有効にする

State Configuration を使用すると、Azure VM (クラシックと Resource Manager の両方)、オンプレミスの VM、Linux マシン、AWS VM、オンプレミスの物理マシンを管理できます。 この記事では、Azure Resource Manager VM を有効にする方法のみを説明します。 他の種類のマシンの有効化の詳細については、[Azure Automation State Configuration による管理のためのマシンの有効化](automation-dsc-onboarding.md)に関するページをご覧ください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで、 **[ノード]** タブを選択し、 **[+ 追加]** をクリックします。

   ![[Azure VM の追加] ボタンが強調表示された [DSC ノード] ページのスクリーンショット](./media/automation-dsc-getting-started/OnboardVM.png)

1. [仮想マシン] ペインで、自分の VM を選択します。
1. [仮想マシン] 詳細ペインで、 **[+ 接続]** をクリックします。

   > [!IMPORTANT]
   > VM は、[サポートされているオペレーティング システム](automation-dsc-overview.md#operating-system-requirements)が実行されている Azure Resource Manager VM である必要があります。

2. [登録] ページの **[ノード構成名]** ボックスで、VM に適用するノード構成の名前を選択します。 この時点では、名前の入力は省略可能です。 ノードの有効化後に、割り当てられたノード構成を変更できます。

3. **[必要に応じてノードを再起動する]** チェック ボックスをオンにし、 **[OK]** をクリックします。

   ![Screenshot of the Registration blade](./media/automation-dsc-getting-started/RegisterVM.png)

   指定したノード構成は、 **[構成モードの頻度]** の値で指定されている間隔で VM に適用されます。 VM では、 **[更新頻度]** の値によって指定されている間隔で、ノード構成の更新が確認されます。 これらの値の使用方法の詳細については、「[ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaConfig)」を参照してください。

Azure によって VM の有効化処理が開始されます。 処理が完了すると、Automation アカウントの [状態の構成 (DSC)] ページの **[ノード]** タブに VM が表示されます。

## <a name="view-the-list-of-managed-nodes"></a>マネージド ノードの一覧を表示する

Automation アカウントの [状態の構成 (DSC)] ページの **[ノード]** タブで、管理のために有効化されたマシンの一覧を表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで、 **[ノード]** タブをクリックします。

## <a name="view-reports-for-managed-nodes"></a>マネージド ノードのレポートを表示する

State Configuration でマネージド ノードの整合性チェックが実行されるたびに、そのノードから状態レポートがプル サーバーに送信されます。 これらのレポートは、そのノードのページに表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで、 **[ノード]** タブをクリックします。ここでは、構成の状態の概要と各ノードの詳細を確認できます。

   ![ノード ページのスクリーンショット](./media/automation-dsc-getting-started/NodesTab.png)

1. **[ノード]** タブで、ノード レコードをクリックしてレポートを開きます。 レポートの詳細を表示するレポートをクリックします。

   ![Screenshot of the Report blade](./media/automation-dsc-getting-started/NodeReport.png)

各レポートのブレードでは、対応する整合性チェックについて次の状態情報を表示できます。

- レポートの状態。 次のいずれかの値になります。
    * 準拠している - ノードはチェックに準拠しています。
   * 失敗 - 構成をチェックできませんでした。
   * 準拠していない - ノードは `ApplyandMonitor` モードであり、マシンは適切な状態ではありません。
- 整合性チェックの開始時刻。
- 整合性チェックの合計実行時間。
- 整合性チェックの種類。
- エラー コードやエラー メッセージを含むすべてのエラー。
- 構成で使用されているすべての DSC リソースと各リソースの状態 (ノードがそのリソースの望ましい状態であるかどうか)。 各リソースをクリックすると、そのリソースの詳細情報を表示できます。
- ノードの名前、IP アドレス、構成モード。

また、 **[生レポートの表示]** をクリックすると、ノードがサーバーに送信する実際のデータを表示することもできます。
このデータの使用方法の詳細については、「 [DSC レポート サーバーの使用](/powershell/scripting/dsc/pull-server/reportserver)」を参照してください。

ノードが有効化されてから、最初のレポートが使用可能になるまで、しばらく時間がかかることがあります。 ノードの有効化から最初のレポートが表示されるまで、最大で 30 分の待つことが必要になる場合があります。

## <a name="reassign-a-node-to-a-different-node-configuration"></a>別のノード構成にノードを再割り当てする

最初に割り当てたものとは別のノード構成を使用するようにノードを割り当てることができます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで、 **[ノード]** タブをクリックします。
1. **[ノード]** タブで、再割り当てするノードの名前をクリックします。
1. そのノードのページで、 **[ノード構成の割り当て]** をクリックします。

    ![[ノード構成の割り当て] ボタンが強調表示されている [ノード] 詳細ページのスクリーンショット](./media/automation-dsc-getting-started/AssignNode.png)

1. [ノード構成の割り当て] ページで、ノードの割り当て先となるノード構成を選択し、 **[OK]** をクリックします。

    ![[ノード構成の割り当て] ページのスクリーンショット](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>ノードを登録解除する

ノードを State Configuration で管理する必要がなくなった場合は、ノードの登録を解除できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで、 **[ノード]** タブをクリックします。
1. **[ノード]** ページで、登録解除するノードの名前をクリックします。
1. そのノードのペインで、 **[登録解除]** をクリックします。

    ![[登録解除] ボタンが強調表示されている [ノード] 詳細ページのスクリーンショット](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>次のステップ

- 概要については、[Azure Automation State Configuration の概要](automation-dsc-overview.md)に関するページを参照してください。
- 環境で VM の機能を有効にするには、「[Azure Automation State Configuration を有効にする](automation-dsc-onboarding.md)」を参照してください。
- PowerShell DSC については、「[Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)」をご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
