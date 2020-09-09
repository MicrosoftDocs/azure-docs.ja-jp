---
title: PowerShell ギャラリーの Azure Automation Runbook とモジュールを使用する
description: この記事では、PowerShell ギャラリーにある Microsoft およびコミュニティからの Runbook とモジュールを使用する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: f2bf058ddce81ab9f04e97787a4dc93e44036b1b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186114"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>PowerShell ギャラリーの Runbook とモジュールを使用する

Azure Automation で独自の Runbook およびモジュールを作成するのではなく、マイクロソフトやコミュニティによって既に作成されているシナリオにアクセスできます。 PowerShell Runbook と[モジュール](#modules-in-powershell-gallery) は PowerShell ギャラリーから、[Python Runbook](#use-python-runbooks) はスクリプト センター ギャラリーから取得できます。 [開発したシナリオ](#add-a-powershell-runbook-to-the-gallery)を共有することにより、コミュニティに貢献することもできます。 

## <a name="runbooks-in-powershell-gallery"></a>PowerShell ギャラリーの Runbook

[PowerShell ギャラリー](https://www.powershellgallery.com/packages)では、Microsoft やコミュニティからさまざまな Runbook が提供されており、それを Azure Automation にインポートできます。 使用するには、ギャラリーから Runbook をダウンロードするか、Azure portal でギャラリーから、または Automation アカウントから、Runbook を直接インポートできます。

> [!NOTE]
> PowerShell ギャラリーではグラフィカル Runbook はサポートされていません。

PowerShell ギャラリーから直接インポートできるのは、Azure portal を使用した場合のみです。 PowerShell を使用してこの機能を実行することはできません。

> [!NOTE]
> PowerShell ギャラリーから取得した Runbook は、内容を検証し、運用環境でインストールおよび実行するときは細心の注意を払う必要があります。

## <a name="modules-in-powershell-gallery"></a>PowerShell ギャラリーのモジュール

PowerShell モジュールには Runbook で使用できるコマンドレットが含まれ、Azure Automation でインストールできる既存のモジュールを [PowerShell ギャラリー](https://www.powershellgallery.com)から入手できます。 このギャラリーは Azure portal から起動でき、Azure Automation に直接インストールします。 ダウンロードして手動でインストールすることもできます。

## <a name="common-scenarios-available-in-powershell-gallery"></a>PowerShell ギャラリーで使用できる一般的なシナリオ

以下の一覧には、一般的なシナリオをサポートするいくつかの Runbook が含まれています。 Azure Automation チームによって作成された Runbook の詳細な一覧については、[AzureAutomationTeam のプロファイル](https://www.powershellgallery.com/profiles/AzureAutomationTeam)のページを参照してください。

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - すべてのモジュールの最新バージョンを PowerShell ギャラリーから Automation アカウントにインポートします。
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - ジョブの状態とジョブ ストリームを含む Azure Automation ログを受信するように Azure Diagnostics と Log Analytics を構成します。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Windows Azure 仮想マシンからリモート ファイルをコピーします。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - ローカル ファイルを Azure 仮想マシンにコピーします。

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Azure portal で Runbook ギャラリーから PowerShell Runbook をインポートする

1. Azure ポータルで、Automation アカウントを開きます。
2. **[プロセス オートメーション]** の下の **[Runbook ギャラリー]** を選択します。
3. **[Source:PowerShell Gallery]** \(ソース: PowerShell ギャラリー\)を選択します。
4. 必要なギャラリー アイテムを探し、選択して詳細を表示します。 左側に、発行元と種類に関する検索パラメーターを追加で入力できます。

   ![[ギャラリーの参照]](media/automation-runbook-gallery/browse-gallery.png)

5. **[ソース プロジェクトの表示]** をクリックして、 [TechNet スクリプト センター](https://gallery.technet.microsoft.com/)の項目を表示します。
6. 項目をインポートするには、それをクリックして詳細を表示し、 **[インポート]** をクリックします。

   ![[インポート] ボタン](media/automation-runbook-gallery/gallery-item-detail.png)

7. 必要に応じて Runbook の名前を変更し、 **[OK]** をクリックして Runbook をインポートします。
8. Automation アカウントの **[Runbook]** タブに Runbook が表示されます。

## <a name="add-a-powershell-runbook-to-the-gallery"></a>ギャラリーに PowerShell Runbook を追加する

他のユーザーにも役に立つと思われる Runbook を PowerShell ギャラリーに追加してください。 PowerShell ギャラリーでは、PowerShell モジュールと PowerShell スクリプトを受け付けています。 Runbook を追加するには、[PowerShell ギャラリーにアップロード](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)します。

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Azure portal でモジュール ギャラリーからモジュールをインポートする

1. Azure ポータルで、Automation アカウントを開きます。
2. **[共有リソース]** の **[モジュール]** を選択して、モジュールの一覧を開きます。
3. ページの上部にある **[ギャラリーを参照]** をクリックします。

   ![モジュール ギャラリー](media/automation-runbook-gallery/modules-blade.png)

4. [ギャラリーを参照] ページで、次のフィールドで検索できます。

   * モジュール名
   * Tags
   * Author
   * コマンドレット/DSC リソース名

5. 目的のモジュールを探し、選択して詳細を表示します。

   特定のモジュールにドリルダウンすると、詳細を表示できます。 この情報には、PowerShell ギャラリーへのリンク、必要な依存関係、モジュールに含まれるすべてのコマンドレットまたは DSC リソースが含まれています。

   ![PowerShell モジュールの詳細](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. モジュールを Azure Automation に直接インストールするには、 **[インポート]** をクリックします。
7. [インポート] ペインに、インポートするモジュールの名前が表示されます。 すべての依存関係がインストールされている場合は、 **[OK]** ボタンがアクティブになります。 依存関係が存在しない場合は、このモジュールをインポートする前に、その依存関係をインポートする必要があります。
8. [インポート] ペインで **[OK]** をクリックしてモジュールをインポートします。 Azure Automation がモジュールをアカウントにインポートしている間に、モジュールとコマンドレットについてのメタデータが抽出されます。 各アクティビティを抽出する必要があるため、このアクションには数分かかる場合があります。
9. モジュールをデプロイ中であることが最初に通知され、プロセスが完了すると完了通知が表示されます。
10. モジュールがインポートされた後は、使用可能なアクティビティを確認できます。 Runbook のモジュール リソースと DSC リソースを使用できます。

> [!NOTE]
> PowerShell Core だけをサポートするモジュールは、Azure Automation でサポートされておらず、Azure portal にインポートしたり、PowerShell ギャラリーから直接展開したりすることはできません。

## <a name="use-python-runbooks"></a>Python Runbook を使用する

Python Runbook は、[スクリプト センター ギャラリー](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)にあります。 Python Runbook をスクリプト センター ギャラリーに投稿するには、 **[Upload a contribution]** \(投稿のアップロード\) をクリックします。 投稿をアップロードするときに、タグ `Python` を必ず追加してください。

> [!NOTE]
> [スクリプト センター](https://gallery.technet.microsoft.com/scriptcenter)にコンテンツをアップロードするには、最低 100 ポイントが必要です。

## <a name="request-a-runbook-or-module"></a>Runbook またはモジュールを要求する

[ユーザーの声](https://feedback.azure.com/forums/246290-azure-automation/)に要求を送信できます。  Runbook の作成について支援が必要な場合、または PowerShell について質問がある場合は、[Microsoft Q&A 質問ページ](/answers/topics/azure-automation.html)に質問を投稿してください。

## <a name="next-steps"></a>次のステップ

* PowerShell Runbook の使用を開始するには、「[チュートリアル:PowerShell Runbook を作成する](learn/automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* Runbook を操作するには、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」を参照してください。
* PowerShell の詳細については、[PowerShell のドキュメント](/powershell/scripting/overview)を参照してください。
* * PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)」をご覧ください。
