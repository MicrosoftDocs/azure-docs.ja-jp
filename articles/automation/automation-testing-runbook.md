---
title: "Azure Automation での Runbook のテスト | Microsoft Docs"
description: "Azure Automation で Runbook を発行する前にテストを行い、期待どおりに動作することを確認できます。  この記事では、Runbook のテスト方法と出力の見方について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 7f7db785-52c0-4613-aa12-b02fd32a5182
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5186eb8f1732d533cbceb397b4d8b5224ad773cd


---
# <a name="testing-a-runbook-in-azure-automation"></a>Azure Automation での Runbook のテスト
Runbook のテスト時には [ドラフト バージョン](automation-creating-importing-runbook.md#publishing-a-runbook) が実行され、そのバージョンで行われるすべてのアクションが完了します。 ジョブ履歴は作成されませんが、[テスト出力] ウィンドウに[出力](automation-runbook-output-and-messages.md#output-stream)および[警告とエラー](automation-runbook-output-and-messages.md#message-streams) ストリームが表示されます。 [詳細ストリーム](automation-runbook-output-and-messages.md#message-streams)のメッセージは、[$VerbosePreference 変数](automation-runbook-output-and-messages.md#preference-variables)が [Continue] に設定されている場合のみ [出力] ウィンドウに表示されます。

ドラフト バージョンの実行中でも、Runbook ではワークフローを通常どおり実行して、環境内のリソースに対するすべてのアクションを行います。 このため、Runbook をテストするのは非運用環境のリソースのみにしてください。

[Runbook の種類](automation-runbook-types.md) ごとのテスト手順は同じであり、Azure ポータルのテキスト エディターでのテストとグラフィカル エディターでのテストに違いはありません。  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Azure ポータルで Runbook をテストするには
Azure ポータルではすべての [種類の Runbook](automation-runbook-types.md) を使用することができます。

1. [テキスト エディター](automation-edit-textual-runbook.md)または[グラフィカル エディター](automation-graphical-authoring-intro.md)のどちらかで、ドラフト バージョンの Runbook を開きます。
2. **[テスト]** ボタンをクリックして [テスト] ブレードを開きます。
3. Runbook にパラメーターがある場合は左側のウィンドウに表示され、そこでテストに使用する値を指定することができます。
4. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) に対してテストを実施する場合は、**[実行設定]** を **[ハイブリッド worker]** に変更して対象グループの名前を選択します。  それ以外の場合は、既定の **[Azure]** をそのまま使用してクラウドでテストを実行します。
5. **[開始]** をクリックしてテストを開始します。
6. Runbook が [PowerShell ワークフロー](automation-runbook-types.md#powershell-workflow-runbooks)または[グラフィカル](automation-runbook-types.md#graphical-runbooks)の場合、[出力] ウィンドウの下にあるボタンを使用して、テスト中に Runbook を停止または中断することができます。 Runbook は、中断される場合は中断前に現在のアクティビティを完了します。 Runbook を中断した後で、停止または再開できます。
7. [出力] ウィンドウで Runbook の出力を確認します。

## <a name="next-steps"></a>次のステップ
* Runbook を作成またはインポートする方法については、「 [Azure Automation での Runbook の作成またはインポート](automation-creating-importing-runbook.md)
* グラフィカル作成の詳細については、「 [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)
* ステータス メッセージやエラーを返すように Runbook を構成する方法と、推奨されるベスト プラクティスの詳細については、「 [Azure Automation での Runbook の出力およびメッセージ](automation-runbook-output-and-messages.md)




<!--HONumber=Nov16_HO3-->


