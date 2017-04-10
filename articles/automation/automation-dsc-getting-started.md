---
title: "Azure Automation DSC の使用 | Microsoft Docs"
description: "Azure Automation Desired State Configuration (DSC) の最も一般的なタスクの説明と例"
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;eslesar
translationtype: Human Translation
ms.sourcegitcommit: 98f5a017221f0aaba04e2b90afc674e4c42d1bb3
ms.openlocfilehash: 282cb0a7705d0650852699ed5fe09f61fda42d0f


---
# <a name="getting-started-with-azure-automation-dsc"></a>Azure Automation DSC の使用
このトピックでは、Azure Automation Desired State Configuration (DSC) を使用して最も一般的なタスク (構成の作成、インポート、コンパイル、管理するマシンのオンボード、レポートの表示など) を実行する方法について説明します。 Azure Automation DSC の概要については、「 [Azure Automation DSC の概要](automation-dsc-overview.md)」を参照してください。 DSC のドキュメントについては、「 [Windows PowerShell Desired State Configuration の概要](https://msdn.microsoft.com/PowerShell/dsc/overview)」を参照してください。

このトピックでは、Azure Automation DSC を使用するための詳しい手順を示しています。 このトピックで説明されている手順を実行せずに、既に設定されているサンプル環境を使用する場合は、 [次の ARM テンプレート](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup)を使用できます。 このテンプレートを使用すると、Azure Automation DSC で管理される Azure VM を含む、完成した Azure Automation DSC 環境が設定されます。

## <a name="prerequisites"></a>前提条件
このトピックの例を完了するには、次のものが必要です。

* Azure Automation アカウント。 Azure Automation 実行アカウントの作成手順については、 [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)に関するページをご覧ください。
* Windows Server 2008 R2 以降を実行している Azure Resource Manager VM (クラシックではない)。 VM の作成手順については、「 [Azure ポータルで初めての Windows 仮想マシンを作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>DSC 構成を作成する
ここでは、ノードの割り当て方法に応じて、 [Web-Server](https://msdn.microsoft.com/powershell/dsc/configurations) Windows 機能 (IIS) が存在するかどうかを確認する、簡単な **DSC 構成** を作成します。

1. Windows PowerShell ISE (または任意のテキスト エディター) を起動します。
2. 次のテキストを入力します。
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
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
3. ファイルを `TestConfig.ps1`という名前で保存します。

この構成は、各ノード ブロックで 1 つのリソース ([WindowsFeature リソース](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource)) を呼び出します。このリソースが、**Web-Server** 機能が存在するかどうかを確認します。

## <a name="importing-a-configuration-into-azure-automation"></a>Azure Automation に構成をインポートする
次に、この構成を Automation アカウントにインポートします。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC 構成]** をクリックします。
4. **[DSC 構成]** ブレードで、**[構成を追加]** をクリックします。
5. **[構成のインポート]** ブレードで、コンピューター上の `TestConfig.ps1` ファイルを参照します。
   
    ![**[構成のインポート]** ブレードのスクリーンショット](./media/automation-dsc-getting-started/AddConfig.png)
6. **[OK]**をクリックします。

## <a name="viewing-a-configuration-in-azure-automation"></a>Azure Automation で構成を表示する
インポートした構成は、Azure ポータルで表示できます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC 構成]** をクリックします。
4. **[DSC 構成]** ブレードで、**[TestConfig]** (前の手順でインポートした構成の名前) をクリックします。
5. **[TestConfig の構成]** ブレードで、**[構成ソースの表示]** をクリックします。
   
    ![Screenshot of the TestConfig configuration blade](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    **[TestConfig Configuration source (TestConfig の構成ソース)]** ブレードが開き、構成の PowerShell コードが表示されます。

## <a name="compiling-a-configuration-in-azure-automation"></a>Azure Automation で構成をコンパイルする
目的の状態をノードに適用する前に、その状態を定義する DSC 構成を 1 つ以上のノード構成 (MOF ドキュメント) にコンパイルし、Automation DSC プル サーバーに配置する必要があります。 Azure Automation DSC での構成のコンパイルの詳細については、「 [Azure Automation DSC での構成のコンパイル](automation-dsc-compile.md)」を参照してください。 構成のコンパイルの詳細については、「 [DSC 構成](https://msdn.microsoft.com/PowerShell/DSC/configurations)」を参照してください。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC 構成]** をクリックします。
4. **[DSC 構成]** ブレードで、**[TestConfig]** (前にインポートした構成の名前) をクリックします。
5. **[TestConfig の構成]** ブレードで **[コンパイル]** をクリックし、**[はい]** をクリックします。 これにより、コンパイル ジョブが開始されます。
   
    ![Screenshot of the TestConfig configuration blade highlighting compile button](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Azure Automation で構成をコンパイルすると、作成されたノード構成 MOF すべてが自動的にプル サーバーにデプロイされます。
> 
> 

## <a name="viewing-a-compilation-job"></a>コンパイル ジョブを表示する
コンパイルを開始すると、**[構成]** ブレードの **[コンパイル ジョブ]** タイルでコンパイル ジョブを確認できます。 **[コンパイル ジョブ]** タイルには、現在実行中のジョブ、完了したジョブ、失敗したジョブが表示されます。 コンパイル ジョブのブレードを開くと、ジョブに関する情報が表示されます。これには、発生したエラーと警告、構成で使用されている入力パラメーター、コンパイル ログが含まれています。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC 構成]** をクリックします。
4. **[DSC 構成]** ブレードで、**[TestConfig]** (前にインポートした構成の名前) をクリックします。
5. **[TestConfig の構成]** ブレードの **[コンパイル ジョブ]** タイルで、表示されているジョブのいずれかをクリックします。 コンパイル ジョブの開始日付のラベルが付いた **[コンパイル ジョブ]** ブレードが開きます。
   
    ![Screenshot of the Compilation Job blade](./media/automation-dsc-getting-started/CompilationJob.png)
6. **[コンパイル ジョブ]** ブレードで任意のタイルをクリックすると、そのジョブの詳細が表示されます。

## <a name="viewing-node-configurations"></a>ノード構成を表示する
コンパイル ジョブが正常に完了すると、1 つ以上の新しいノード構成が作成されます。 ノード構成とは、プル サーバーにデプロイされ、1 つ以上のノードがプルして適用できるようになる MOF ドキュメントです。 **[DSC ノード構成]** ブレードでは、Automation アカウントのノード構成を確認できます。 ノード構成の名前は、*ConfigurationName*.*NodeName* という形式です。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC ノード構成]** をクリックします。
   
    ![Screenshot of the DSC Node Configurations blade](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Azure Automation DSC を使用して管理のために Azure VM をオンボードする
Azure Automation DSC を使用すると、Azure VM (クラシックと Resource Manager の両方)、オンプレミスの VM、Linux マシン、AWS VM、オンプレミスの物理マシンを管理できます。 このトピックでは、Azure Resource Manager VM のオンボードの方法のみを説明します。 他の種類のマシンのオンボードの詳細については、「 [Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Azure Automation DSC を使用して管理のために Azure Resource Manager VM をオンボードするには
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC ノード]** をクリックします。
4. **[DSC ノード]** ブレードで、**[Azure VM の追加]** をクリックします。
   
    ![Screenshot of the DSC Nodes blade highlighting the Add Azure VM button](./media/automation-dsc-getting-started/OnboardVM.png)
5. **[Azure VM の追加]** ブレードで、**[オンボードする仮想マシンの選択]** をクリックします。
6. **[VM の選択]** ブレードで、オンボードする VM を選択し、**[OK]** をクリックします。
   
   > [!IMPORTANT]
   > これは、Windows Server 2008 R2 以降を実行している Azure Resource Manager VM である必要があります。
   > 
   > 
7. **[Azure VM の追加]** ブレードで、**[登録データの構成]** をクリックします。
8. **[登録]** ブレードの **[ノード構成名]** ボックスに、VM に適用するノード構成の名前を入力します。 これは、Automation アカウントのノード構成の名前と正確に一致する必要があります。 この時点では、名前の入力は省略可能です。 ノードのオンボード後に、割り当てられたノード構成を変更できます。
   **[必要に応じてノードを再起動する]** チェック ボックスをオンにし、**[OK]** をクリックします。
   
    ![Screenshot of the Registration blade](./media/automation-dsc-getting-started/RegisterVM.png)
   
    指定したノード構成は、**[構成モードの頻度]** に指定された間隔で VM に適用されます。また、VM は、**[更新頻度]** に指定された間隔でノード構成に対する更新をチェックします。 これらの値の使用方法の詳細については、「[ローカル構成マネージャーの構成](https://msdn.microsoft.com/PowerShell/DSC/metaConfig)」を参照してください。
9. **[Azure VM の追加]** ブレードで、**[作成]** をクリックします。

Azure によって VM のオンボード処理が開始されます。 処理が完了すると、Automation アカウントの **[DSC ノード]** ブレードに VM が表示されます。

## <a name="viewing-the-list-of-dsc-nodes"></a>DSC ノードの一覧を表示する
管理のためにオンボードされたすべてのマシンの一覧を Automation アカウントの **[DSC ノード]** ブレードで表示できます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC ノード]** をクリックします。

## <a name="viewing-reports-for-dsc-nodes"></a>DSC ノードのレポートを表示する
Azure Automation DSC が管理対象ノードの整合性チェックを実行するたびに、ノードはプル サーバーに状態レポートを送信します。 これらのレポートは、そのノードのブレードに表示できます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC ノード]** をクリックします。
4. **[レポート]** タイルで、一覧にあるレポートのいずれかをクリックします。
   
    ![Screenshot of the Report blade](./media/automation-dsc-getting-started/NodeReport.png)

各レポートのブレードでは、対応する整合性チェックについて次の状態情報を表示できます。

* レポートの状態。ノードが "準拠" かどうか、構成が "失敗" かどうか、ノードが "非準拠" (ノードが **applyandmonitor** モードでも、マシンが望ましい状態でない場合) かどうか。
* 整合性チェックの開始時刻。
* 整合性チェックの合計実行時間。
* 整合性チェックの種類。
* エラー コードやエラー メッセージを含むすべてのエラー。 
* 構成で使用されているすべての DSC リソースと各リソースの状態 (ノードがそのリソースの望ましい状態であるかどうか)。各リソースをクリックすると、そのリソースの詳細情報を表示できます。
* ノードの名前、IP アドレス、構成モード。

また、 **[生レポートの表示]** をクリックすると、ノードがサーバーに送信する実際のデータを表示することもできます。 このデータの使用方法の詳細については、「 [DSC レポート サーバーの使用](https://msdn.microsoft.com/powershell/dsc/reportserver)」を参照してください。

ノードがオンボードされてから、最初のレポートが使用可能になるまで、しばらく時間がかかることがあります。 ノードのオンボードから最初のレポートが表示されるまで、最大で 30 分の待つことが必要になる場合があります。

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>別のノード構成にノードを再割り当てする
最初に割り当てたものとは別のノード構成を使用するようにノードを割り当てることができます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC ノード]** をクリックします。
4. **[DSC ノード]** ブレードで、再割り当てするノードの名前をクリックします。
5. そのノードのブレードで、 **[Assign node (ノードの割り当て)]**をクリックします。
   
    ![Screenshot of the Node blade highlighting the Assign Node button](./media/automation-dsc-getting-started/AssignNode.png)
6. **[ノード構成の割り当て]** ブレードで、ノードの割り当て先となるノード構成を選択し、**[OK]** をクリックします。
   
    ![Screenshot of the Assign Node Configuration blade](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>ノードの登録を解除する
ノードを Azure Automation DSC で管理する必要がなくなった場合は、ノードの登録を解除することができます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューの **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
3. **[Automation アカウント]** ブレードで、**[DSC ノード]** をクリックします。
4. **[DSC ノード]** ブレードで、登録解除するノードの名前をクリックします。
5. そのノードのブレードで、 **[登録解除]**をクリックします。
   
    ![Screenshot of the Node blade highlighting the Unregister button](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>関連記事
* [Azure Automation DSC の概要](automation-dsc-overview.md)
* [Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)
* [Windows PowerShell Desired State Configuration の概要](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC cmdlets (Azure Automation DSC コマンドレット)](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure Automation DSC cmdlets (Azure Automation DSC の価格)](https://azure.microsoft.com/pricing/details/automation/)




<!--HONumber=Nov16_HO4-->


