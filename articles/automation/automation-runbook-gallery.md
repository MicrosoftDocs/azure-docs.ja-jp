---
title: Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー
description: Microsoft やコミュニティからの Runbook とモジュールを Azure Automation 環境にインストールして使用できます。  この記事では、これらのリソースにアクセスしたり、自分の Runbook をギャラリーに投稿したりする方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94910d0f42ad6b208cac54dd2826cbd2d917504b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850722"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー

Azure Automation で独自の Runbook およびモジュールを作成するのではなく、マイクロソフトやコミュニティによって既に作成されているシナリオにアクセスできます。

PowerShell Runbook と[モジュール](#modules-in-powershell-gallery) は PowerShell ギャラリーから、[Python Runbook](#python-runbooks) はスクリプト センター ギャラリーから取得できます。 開発したシナリオを共有することにより、コミュニティに貢献することもできます。ギャラリーへの Runbook の追加を参照してください

## <a name="runbooks-in-powershell-gallery"></a>PowerShell ギャラリーの Runbook

[PowerShell ギャラリー](https://www.powershellgallery.com/packages)では、Microsoft やコミュニティからさまざまな Runbook が提供されており、それを Azure Automation にインポートできます。 使用するには、ギャラリーから Runbook をダウンロードするか、Azure portal でギャラリーから、または Automation アカウントから、Runbook を直接インポートできます。

PowerShell ギャラリーから直接インポートできるのは、Azure portal を使用した場合のみです。 PowerShell を使用してこの機能を実行することはできません。

> [!NOTE]
> PowerShell ギャラリーから取得した Runbook は、内容を検証し、運用環境でインストールおよび実行するときは細心の注意を払う必要があります。

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Azure portal で Runbook ギャラリーから PowerShell Runbook をインポートするには

1. Azure ポータルで、Automation アカウントを開きます。
2. **[プロセス オートメーション]** の **[Runbook ギャラリー]** をクリックします。
3. **[Source:PowerShell Gallery]** \(ソース: PowerShell ギャラリー\)を選択します。
4. 必要なギャラリー アイテムを探し、選択して詳細を表示します。 左側に、発行元と種類に関する検索パラメーターを追加で入力できます。

   ![[ギャラリーの参照]](media/automation-runbook-gallery/browse-gallery.png)

5. **[ソース プロジェクトの表示]** をクリックして、 [TechNet スクリプト センター](https://gallery.technet.microsoft.com/)の項目を表示します。
6. 項目をインポートするには、項目クリックして詳細を表示し、 **[インポート]** ボタンをクリックします。

   ![[インポート] ボタン](media/automation-runbook-gallery/gallery-item-detail.png)

7. 必要に応じて Runbook の名前を変更し、 **[OK]** をクリックして Runbook をインポートします。
8. Runbook が、Automation アカウントの **[Runbook]** タブに表示されます。

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>ギャラリーへの PowerShell Runbook の追加

他のユーザーにも役に立つと思われる Runbook を PowerShell ギャラリーに追加してください。 PowerShell ギャラリーでは、PowerShell モジュールと PowerShell スクリプトを受け付けています。 Runbook を追加するには、[PowerShell ギャラリーにアップロード](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)します。

> [!NOTE]
> PowerShell ギャラリーではグラフィカル Runbook はサポートされていません。

## <a name="modules-in-powershell-gallery"></a>PowerShell ギャラリーのモジュール

PowerShell モジュールには Runbook で使用できるコマンドレットが含まれ、Azure Automation でインストールできる既存のモジュールを [PowerShell ギャラリー](https://www.powershellgallery.com)から入手できます。 このギャラリーは Azure portal から起動でき、Azure Automation に直接インストールします。 ダウンロードして手動でインストールすることもできます。

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Azure ポータルで Automation モジュール ギャラリーからモジュールをインポートするには

1. Azure ポータルで、Automation アカウントを開きます。
2. **[共有リソース]** の **[モジュール]** を選択して、モジュールの一覧を開きます。
3. ページの上部にある **[ギャラリーを参照]** をクリックします。

   ![モジュール ギャラリー](media/automation-runbook-gallery/modules-blade.png)

4. **[ギャラリーを参照]** ページで、次のフィールドで検索できます。

   * モジュール名
   * タグ
   * 作成者
   * コマンドレット/DSC リソース名

5. 目的のモジュールを探し、選択して詳細を表示します。

   特定のモジュールにドリルダウンすると、詳細を表示できます。 この情報には、PowerShell ギャラリーへのリンク、必要な依存関係、モジュールに含まれるすべてのコマンドレットまたは DSC リソースが含まれています。

   ![PowerShell モジュールの詳細](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. モジュールを Azure Automation に直接インストールするには、 **[インポート]** ボタンをクリックします。
7. [インポート] ボタンをクリックすると、 **[インポート]** ウィンドウに、インポートしようとしているモジュール名が表示されます。 すべての依存関係がインストールされている場合は、 **[OK]** ボタンがアクティブになります。 依存関係が存在しない場合は、このモジュールをインポートする前に、その依存関係をインポートする必要があります。
8. **[インポート]** ページで **[OK]** をクリックしてモジュールをインポートします。 Azure Automation がモジュールをアカウントにインポートしている間に、モジュールとコマンドレットについてのメタデータが抽出されます。 各アクティビティを抽出する必要があるため、このアクションには数分かかる場合があります。
9. モジュールをデプロイ中であることが最初に通知され、プロセスが完了すると完了通知が表示されます。
10. モジュールがインポートされた後は、使用可能なアクティビティを確認できます。 そのリソースを、Runbook と Desired State Configuration で使用することができます。

> [!NOTE]
> PowerShell Core だけをサポートするモジュールは、Azure Automation でサポートされておらず、Azure portal にインポートしたり、PowerShell ギャラリーから直接展開したりすることはできません。

## <a name="python-runbooks"></a>Python Runbook

Python Runbook は、[スクリプト センター ギャラリー](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)にあります。 Python Runbook をスクリプト センター ギャラリーに投稿するには、 **[Upload a contribution]** \(投稿のアップロード\) をクリックします。 投稿をアップロードするときに、タグ **Python** を確実に追加してください。

> [!NOTE]
> [スクリプト センター](https://gallery.technet.microsoft.com/scriptcenter)にコンテンツをアップロードするには、最低 100 ポイントが必要です。

## <a name="requesting-a-runbook-or-module"></a>Runbook またはモジュールの要求

[ユーザーの声](https://feedback.azure.com/forums/246290-azure-automation/)に要求を送信できます。  Runbook の作成について支援が必要な場合、または PowerShell について質問がある場合は、[フォーラム](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)に質問を投稿してください。

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Runbook ギャラリーで使用できる一般的なソリューション

以下の一覧には、一般的なシナリオに対する解決策となるいくつかの Runbook が含まれています。 Azure Automation チームによって作成された Runbook の詳細な一覧については、[AzureAutomationTeam のプロファイル](https://www.powershellgallery.com/profiles/AzureAutomationTeam)のページを参照してください。

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Automation アカウント内のすべてのモジュールの PowerShell ギャラリーに最新バージョンをインポートします。
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - このスクリプトでは、ジョブの状態とジョブ ストリームを含む Azure Automation ログを受信するように Azure Diagnostics と Log Analytics を構成します。
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - この Runbook では、Windows Azure 仮想マシンからリモート ファイルがコピーされます。
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - この Runbook では、ローカル ファイルを Azure 仮想マシンにコピーします。

## <a name="next-steps"></a>次の手順

* Runbook の使用を開始するには、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」をご覧ください
* Runbook 用の PowerShell と PowerShell ワークフローとの違いについては、「 [PowerShell ワークフローについて](automation-powershell-workflow.md)
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)に関するページを参照してください。
