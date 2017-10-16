---
title: "リソース グループの削除の自動化 | Microsoft Docs"
description: "サブスクリプション内のすべてのリソース グループを削除する Runbook が含まれた、PowerShell Workflow バージョンの Azure Automation のシナリオ。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: b848e345-fd5d-4b9d-bc57-3fe41d2ddb5c
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2016
ms.author: magoedte
ms.openlocfilehash: 8b23e55a597f293b17183e80eea6c2763aabe9ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Azure Automation のシナリオ - リソース グループの削除の自動化
多くのユーザーは、1 つ以上のリソース グループを作成します。 リソース グループは、実稼働アプリケーションの管理に使用することもあれば、開発、テスト、ステージング環境として使用することもあります。 これらのリソースのデプロイを自動化することと、ワンクリックでリソース グループを削除する機能は、まったくの別物です。 Azure Automation を使用することで、この一般的な管理タスクを効率化できます。 これは、MSDN や Microsoft Partner Network Cloud Essentials プログラムなどのメンバー プランを通じて使用制限のある Azure サブスクリプションを使っている場合に役立ちます。

このシナリオは PowerShell Runbook に基づいており、指定した 1 つ以上のリソース グループをサブスクリプションから削除するために使用されます。 Runbook の既定の設定では、手順を実行する前にテストが行われます。 そのため、この手順を完了する準備が整う前にリソース グループを誤って削除してしまうことはありません。   

## <a name="getting-the-scenario"></a>シナリオの取得
このシナリオは、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript)からダウンロードできる PowerShell Runbook で構成されています。 この Runbook は、Azure Portal の [Runbook ギャラリー](automation-runbook-gallery.md)から直接インポートすることもできます。<br><br>

| Runbook | Description |
| --- | --- |
| Remove-ResourceGroup |1 つ以上の Azure リソース グループとそれに関連付けられているリソースをサブスクリプションから削除します。 |

<br>
この Runbook では、次の入力パラメーターを定義します。

| パラメーター | Description |
| --- | --- |
| NameFilter (必須) |名前フィルターを指定して、削除するリソース グループを限定します。 コンマ区切りのリストを使用して複数の値を渡すことができます。<br>フィルターでは大文字と小文字が区別されず、文字列を含むすべてのリソース グループが一致します。 |
| PreviewMode (省略可能) |削除されるリソース グループを確認するために Runbook を実行します。ただし、実際の削除は行われません。<br>Runbook に渡される 1 つ以上のリソース グループが誤って削除されないように、既定では **true** に設定されています。 |

## <a name="install-and-configure-this-scenario"></a>このシナリオのインストールと構成
### <a name="prerequisites"></a>前提条件
この Runbook は、 [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)を使用して認証します。    

### <a name="install-and-publish-the-runbooks"></a>Runbook をインストールして発行する
Runbook をダウンロードした後、[Runbook のインポートの手順](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation)を使用してインポートすることができます。 Runbook が Automation アカウントに正常にインポートされたら、それを発行します。

## <a name="using-the-runbook"></a>Runbook の使用
以下の手順では、この Runbook の実行について説明していきます。これらの手順は、Runbook のしくみを理解するのに役立ちます。 この例では Runbook のテストのみを行い、リソース グループは実際には削除しません。  

1. Azure Portal で Automation アカウントを開き、**[Runbook]** をクリックします。
2. **Remove-ResourceGroup** Runbook を選択し、**[開始]** をクリックします。
3. Runbook を開始すると、**[Runbook の開始]** ブレードが開き、パラメーターを構成できます。 サブスクリプション内にある、テストに使用できるリソース グループの名前を入力します。誤って削除しても悪影響のないものを選んでください。<br> ![Remove-ResouceGroup のパラメーター](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)

   > [!NOTE]
   > 選択したリソース グループが削除されないように、**[Previewmode]** が **true** に設定されていることを確認します。  この Runbook では、Runbook を実行中の Automation アカウントが含まれているリソース グループは削除されないことに**注意してください**。  
   >
   >
4. すべてのパラメーター値を構成したら、**[OK]** をクリックします。Runbook が実行されるよう、キューに配置されます。  

Azure Portal で **Remove-ResourceGroup** Runbook ジョブの詳細を表示するには、Runbook の **[ジョブ]** タイルを選択します。 ジョブの概要として、入力パラメーターと出力ストリーム、さらにジョブに関する全般情報が表示されます。例外が発生した場合は、その情報も表示されます。<br> ![Remove-ResourceGroup Runbook ジョブの状態](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png)

**[ジョブの概要]** には、出力、警告、およびエラー ストリームからのメッセージが表示されます。 Runbook 実行の詳細な結果を表示するには、**[出力]** を選択します。<br> ![Remove-ResourceGroup Runbook の出力結果](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>次のステップ
* 独自の Runbook の作成を開始するには、「[Azure Automation での Runbook の作成またはインポート](automation-creating-importing-runbook.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
