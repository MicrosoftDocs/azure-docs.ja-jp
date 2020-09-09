---
title: Azure Automation Grpahical Runbook SDK (プレビュー) を使用する
description: この記事では、Azure Automation Graphical Runbook SDK (プレビュー) の使用方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 969e60cd08a65adb1dd731aa7c6c3f9872e288fd
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835038"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk-preview"></a>Azure Automation Grpahical Runbook SDK (プレビュー) を使用する

[グラフィック Runbook](automation-graphical-authoring-intro.md) は、基礎となる Windows PowerShell または PowerShell Workflow コードの複雑さを管理するために役立ちます。 Microsoft Azure Automation Graphical Authoring SDK を使用すると、Azure Automation に使用できるグラフィック Runbook を作成および編集できます。 この記事では、コードからグラフィック Runbook を作成するための基本的な手順について説明します。

## <a name="prerequisites"></a>前提条件

[SDK](https://www.microsoft.com/download/details.aspx?id=50734) をダウンロードして `Orchestrator.GraphRunbook.Model.dll` パッケージをインポートします。

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
> 指定のクラスから独自のアクティビティを派生させることはしないでください。 Azure Automation では、カスタムのアクティビティ タイプで Runbook を実行できません。

`CommandActivity` パラメーターと `InvokeRunbookActivity` パラメーターを値記述子として指定する必要があります。直接値は指定できません。 値記述子は、実際のパラメーター値の生成方法を指定します。 現在、次の値記述子が用意されています。


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
> 指定のクラスから独自の値記述子を派生させないでください。 Azure Automation では、カスタムの値記述子タイプで Runbook を実行できません。

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

この文字列をファイルに保存するには、 **.graphrunbook** 拡張機能を使用します。 対応する Runbook を Azure Automation にインポートできます。
シリアル化された形式は、`Orchestrator.GraphRunbook.Model.dll` の今後のバージョンで変更される可能性がありますが、 下位互換性は保証されます。つまり、旧バージョンの `Orchestrator.GraphRunbook.Model.dll` でシリアル化された Runbook は、新しいバージョンで逆シリアル化できます。 上位互換性は保証されません。つまり、新しいバージョンでシリアル化された Runbook は、旧バージョンで逆シリアル化できません。

## <a name="next-steps"></a>次のステップ

詳細については、[Azure Automation でのグラフィカル Runbook の作成](automation-graphical-authoring-intro.md)に関する記事を参照してください。