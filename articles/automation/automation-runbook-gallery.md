---
title: PowerShell ギャラリーの Azure Automation Runbook とモジュールを使用する
description: この記事では、PowerShell ギャラリーにある Microsoft およびコミュニティからの Runbook とモジュールを使用する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452783"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>PowerShell ギャラリーの Runbook とモジュールを使用する

Azure Automation で独自の Runbook およびモジュールを作成するのではなく、マイクロソフトやコミュニティによって既に作成されているシナリオにアクセスできます。 PowerShell Runbook と[モジュール](#modules-in-powershell-gallery) は PowerShell ギャラリーから、[Python Runbook](#use-python-runbooks) は Azure Automation GitHub 組織から取得できます。 [開発したシナリオ](#add-a-powershell-runbook-to-the-gallery)を共有することにより、コミュニティに貢献することもできます。

> [!NOTE]
> TechNet スクリプト センターは廃止されています。 Runbook ギャラリーのスクリプト センターにあるすべての Runbook が [Automation GitHub 組織](https://github.com/azureautomation)に移動されました。詳細については、[ここ](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)を参照してください。

## <a name="runbooks-in-powershell-gallery"></a>PowerShell ギャラリーの Runbook

[PowerShell ギャラリー](https://www.powershellgallery.com/packages)では、Microsoft やコミュニティからさまざまな Runbook が提供されており、それを Azure Automation にインポートできます。 使用するには、ギャラリーから Runbook をダウンロードするか、Azure portal でギャラリーから、または Automation アカウントから、Runbook を直接インポートできます。

> [!NOTE]
> PowerShell ギャラリーではグラフィカル Runbook はサポートされていません。

PowerShell ギャラリーから直接インポートできるのは、Azure portal を使用した場合のみです。 PowerShell を使用してこの機能を実行することはできません。

> [!NOTE]
> PowerShell ギャラリーから取得した Runbook は、内容を検証し、運用環境でインストールおよび実行するときは細心の注意を払う必要があります。

## <a name="modules-in-powershell-gallery"></a>PowerShell ギャラリーのモジュール

PowerShell モジュールには、Runbook で使用できるコマンドレットが含まれています。 Azure Automation でインストールできる既存のモジュールは [PowerShell ギャラリー](https://www.powershellgallery.com)から入手できます。 このギャラリーは Azure portal から起動できます。また、モジュールを Azure Automation に直接インストールするか、手動でダウンロードしてインストールすることができます。

## <a name="common-scenarios-available-in-powershell-gallery"></a>PowerShell ギャラリーで使用できる一般的なシナリオ

以下の一覧には、一般的なシナリオをサポートするいくつかの Runbook が含まれています。 Azure Automation チームによって作成された Runbook の詳細な一覧については、[AzureAutomationTeam のプロファイル](https://www.powershellgallery.com/profiles/AzureAutomationTeam)のページを参照してください。

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - すべてのモジュールの最新バージョンを PowerShell ギャラリーから Automation アカウントにインポートします。
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - ジョブの状態とジョブ ストリームを含む Azure Automation ログを受信するように Azure Diagnostics と Log Analytics を構成します。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Windows Azure 仮想マシンからリモート ファイルをコピーします。
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - ローカル ファイルを Azure 仮想マシンにコピーします。

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Azure portal で Runbook ギャラリーから PowerShell Runbook をインポートする

1. Azure ポータルで、Automation アカウントを開きます。
1. **[プロセス オートメーション]** の下の **[Runbook ギャラリー]** を選択します。
1. **[Source:PowerShell Gallery]** \(ソース: PowerShell ギャラリー\)を選択します。 参照可能な Runbook のリストが表示されます。
1. リストの上にある検索ボックスを使用すると、リストを絞り込むことができます。また、フィルターを使用して、発行元や種類で表示を絞り込んだり、並べ替えたりすることもできます。 必要なギャラリー アイテムを探し、選択して詳細を表示します。

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Runbook ギャラリーを参照します。" lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. アイテムをインポートするには、詳細ブレードの **[インポート]** をクリックします。

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Runbook ギャラリー アイテムの詳細を表示します。" lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. 必要に応じて Runbook の名前を変更し、 **[OK]** をクリックして Runbook をインポートします。
1. Automation アカウントの **[Runbook]** タブに Runbook が表示されます。

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>Azure portal を使用して GitHub から PowerShell Runbook をインポートする

1. Azure ポータルで、Automation アカウントを開きます。
1. **[プロセス オートメーション]** の下の **[Runbook ギャラリー]** を選択します。
1. **[ソース: GitHub]** を選択します。
1. リストの上にあるフィルターを使用して、発行元や種類で表示を絞り込んだり、並べ替えたりすることができます。 必要なギャラリー アイテムを探し、選択して詳細を表示します。

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="GitHub ギャラリーを参照します。" lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. アイテムをインポートするには、詳細ブレードの **[インポート]** をクリックします。

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="GitHub ギャラリーの Runbook の詳細ビュー。" lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. 必要に応じて Runbook の名前を変更し、 **[OK]** をクリックして Runbook をインポートします。
1. Automation アカウントの **[Runbook]** タブに Runbook が表示されます。

## <a name="add-a-powershell-runbook-to-the-gallery"></a>ギャラリーに PowerShell Runbook を追加する

他のユーザーにも役に立つと思われる Runbook を PowerShell ギャラリーに追加してください。 PowerShell ギャラリーでは、PowerShell モジュールと PowerShell スクリプトを受け付けています。 Runbook を追加するには、[PowerShell ギャラリーにアップロード](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)します。

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Azure portal でモジュール ギャラリーからモジュールをインポートする

1. Azure ポータルで、Automation アカウントを開きます。
1. **[共有リソース]** の **[モジュール]** を選択して、モジュールの一覧を開きます。
1. ページの上部にある **[ギャラリーを参照]** をクリックします。

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="モジュール ギャラリーのビュー。" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. [ギャラリーの参照] ページでは、検索ボックスを使用して、次のいずれかのフィールで一致するものを見つけることができます。

   * モジュール名
   * Tags
   * Author
   * コマンドレット/DSC リソース名

1. 目的のモジュールを探し、選択して詳細を表示します。

   特定のモジュールにドリルダウンすると、詳細を表示できます。 この情報には、PowerShell ギャラリーへのリンク、必要な依存関係、モジュールに含まれるすべてのコマンドレットまたは DSC リソースが含まれています。

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="ギャラリーのモジュールの詳細ビュー。" lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. モジュールを Azure Automation に直接インストールするには、 **[インポート]** をクリックします。
1. [インポート] ペインに、インポートするモジュールの名前が表示されます。 すべての依存関係がインストールされている場合は、 **[OK]** ボタンがアクティブになります。 依存関係が存在しない場合は、このモジュールをインポートする前に、その依存関係をインポートする必要があります。
1. [インポート] ペインで **[OK]** をクリックしてモジュールをインポートします。 Azure Automation がモジュールをアカウントにインポートしている間に、モジュールとコマンドレットについてのメタデータが抽出されます。 各アクティビティを抽出する必要があるため、このアクションには数分かかる場合があります。
1. モジュールをデプロイ中であることが最初に通知され、プロセスが完了すると完了通知が表示されます。
1. モジュールがインポートされた後は、使用可能なアクティビティを確認できます。 Runbook のモジュール リソースと DSC リソースを使用できます。

> [!NOTE]
> PowerShell Core だけをサポートするモジュールは、Azure Automation でサポートされておらず、Azure portal にインポートしたり、PowerShell ギャラリーから直接展開したりすることはできません。

## <a name="use-python-runbooks"></a>Python Runbook を使用する

Python Runbook は、[Azure Automation GitHub 組織](https://github.com/azureautomation)で利用できます。 GitHub リポジトリに投稿するときに、タグ **[(GitHub トピック): Python3]** を追加します。

## <a name="request-a-runbook-or-module"></a>Runbook またはモジュールを要求する

[ユーザーの声](https://feedback.azure.com/forums/246290-azure-automation/)に要求を送信できます。  Runbook の作成について支援が必要な場合、または PowerShell について質問がある場合は、[Microsoft Q&A 質問ページ](/answers/topics/azure-automation.html)に質問を投稿してください。

## <a name="next-steps"></a>次のステップ

* PowerShell Runbook の使用を開始するには、「[チュートリアル:PowerShell Runbook を作成する](learn/automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* Runbook を操作するには、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」を参照してください。
* PowerShell の詳細については、[PowerShell のドキュメント](/powershell/scripting/overview)を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
