---
title: Azure Automation Grpahical Runbook SDK の概要
description: この記事では、Azure Automation Graphical Runbook SDK の使用方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a06c190931fdd0f49132f815b153c08ece68c9f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849549"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Azure Automation Grpahical Runbook SDK を使用する

[グラフィック Runbook](automation-graphical-authoring-intro.md) は、基礎となる Windows PowerShell または PowerShell Workflow コードの複雑さを管理するために役立つ Runbook です。 Microsoft Azure Automation Graphical Authoring SDK を使用すると、Azure Automation サービスに使用できるグラフィック Runbook を作成および編集できます。 次のコード スニペットは、コードからグラフィック Runbook を作成する基本的な流れを示しています。

## <a name="pre-requisites"></a>前提条件

まず、`Microsoft.Azure.Automation.GraphicalRunbook.Model` パッケージをプロジェクトにインポートします。

## <a name="create-a-runbook-object-instance"></a>Runbook オブジェクトのインスタンスを作成する

`Orchestrator.GraphRunbook.Model` アセンブリを参照し、`Orchestrator.GraphRunbook.Model.GraphRunbook` クラスのインスタンスを作成します。

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook のパラメーターを追加する

`Orchestrator.GraphRunbook.Model.Parameter` オブジェクトをインスタンス化し、Runbook に追加します。

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>アクティビティとリンクを追加する

適切な種類のアクティビティをインスタンス化し、Runbook に追加します。

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

アクティビティは、`Orchestrator.GraphRunbook.Model` 名前空間の次のクラスによって実装されます。

|クラス  |アクティビティ  |
|---------|---------|
|CommandActivity     | PowerShell コマンド (コマンドレット、関数など) を呼び出します。        |
|InvokeRunbookActivity     | 別の Runbook をインラインで呼び出します。        |
|JunctionActivity     | すべての受信ブランチが完了するまで待ちます。        |
|WorkflowScriptActivity     | Runbook のコンテキストで、(Runbook の種類に応じて) PowerShell または PowerShell Workflow コードのブロックを実行します。 これは強力なツールですが、使いすぎないようにしてください。UI には、このスクリプト ブロックがテキストとして表示されます。実行エンジンは指定されたブロックをブラック ボックスとして扱い、基本的な構文チェックを除き、内容を分析しません。 単一の PowerShell コマンドを呼び出すだけの場合は、CommandActivity を使用することをお勧めします。        |

> [!NOTE]
> 指定のクラスから独自のアクティビティを派生させることはしないでください。Azure Automation では、カスタムのアクティビティ タイプで Runbook を実行できません。

CommandActivity および InvokeRunbookActivity パラメーターは、直接の値ではなく値記述子として指定する必要があります。 値記述子には、実際のパラメーター値の生成方法を指定します。 現在、次の値記述子が用意されています。


|記述子  |定義  |
|---------|---------|
|ConstantValueDescriptor     | ハードコーディングされた定数値を参照します。        |
|RunbookParameterValueDescriptor     | Runbook パラメーターを名前で参照します。        |
|ActivityOutputValueDescriptor     | アップストリームのアクティビティ出力を参照し、あるアクティビティが、別のアクティビティによって生成されたデータに "サブスクライブ" できるようにします。        |
|AutomationVariableValueDescriptor     | Automation 変数資産を名前で参照します。         |
|AutomationCredentialValueDescriptor     | Automation 証明書資産を名前で参照します。        |
|AutomationConnectionValueDescriptor     | Automation 接続資産を名前で参照します。        |
|PowerShellExpressionValueDescriptor     | アクティビティを呼び出す直前に評価される自由形式の PowerShell 式を指定します。  <br/>これは強力なツールですが、使いすぎないようにしてください。UI には、この式がテキストとして表示されます。実行エンジンは指定されたブロックをブラック ボックスとして扱い、基本的な構文チェックを除き、内容を分析しません。 可能であれば、より具体的な値記述子を使用することお勧めします。      |

> [!NOTE]
> 指定のクラスから独自の値記述子を派生させないでください。Azure Automation では、カスタムの値記述子タイプで Runbook を実行できません。

アクティビティを接続するリンクをインスタンス化し、Runbook に追加します。

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Runbook をファイルに保存する

`Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` を使用して Runbook を文字列にシリアル化します。

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

この文字列は、拡張子が **.graphRunbook** のファイルに保存できます。このファイルは Azure Automation にインポートできます。
シリアル化された形式は、`Orchestrator.GraphRunbook.Model.dll` の今後のバージョンで変更される可能性がありますが、 下位互換性は保証されます。つまり、旧バージョンの `Orchestrator.GraphRunbook.Model.dll` でシリアル化された Runbook は、新しいバージョンで逆シリアル化できます。 上位互換性は保証されません。つまり、新しいバージョンでシリアル化された Runbook は、旧バージョンで逆シリアル化できません。

## <a name="next-steps"></a>次の手順

Azure Automation のグラフィック Runbook の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください

