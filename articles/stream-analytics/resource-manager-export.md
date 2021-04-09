---
title: Azure Stream Analytics ジョブの Azure Resource Manager テンプレートをエクスポートする
description: この記事では、Azure Stream Analytics ジョブの Azure Resource Manager テンプレートをエクスポートする方法について説明します。
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: aa17d83dcc14675db5ff6aa4597314baffbffdbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015421"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Azure Stream Analytics ジョブの Azure Resource Manager テンプレートをエクスポートする

[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を使用すると、インフラストラクチャをコードとして実装できます。 テンプレートは JavaScript Object Notation (JSON) ファイルであり、リソースのインフラストラクチャと構成が定義されています。 デプロイするリソースとそれらのリソースのプロパティを指定します。

Azure Resource Manager テンプレートをエクスポートすることによって、Azure Stream Analytics ジョブを再デプロイできます。

## <a name="open-a-job-in-vs-code"></a>VS Code でジョブを開く

テンプレートをエクスポートする前に、まず既存の Stream Analytics ジョブを Visual Studio Code で開く必要があります。 

ジョブをローカル プロジェクトにエクスポートするには、Azure portal の **Stream Analytics Explorer** で、エクスポートするジョブを特定します。 **[クエリ]** ページから、 **[Visual Studio で開く]** を選択します。 次に、 **[Visual Studio Code]** を選択します。

![Visual Studio Code で Stream Analytics ジョブを開く](./media/resource-manager-export/open-job-vs-code.png)

Visual Studio Code を使用した Stream Analytics ジョブの管理の詳細については、[Visual Studio Code のクイック スタート](quick-create-visual-studio-code.md)を参照してください。

## <a name="compile-the-script"></a>スクリプトをコンパイルする 

次の手順では、ジョブ スクリプトを Azure Resource Manager テンプレートにコンパイルします。 スクリプトをコンパイルする前に、ジョブに少なくとも 1 つの入力と 1 つの出力が構成されていることを確認します。 入力または出力が構成されていない場合は、最初に入力と出力を構成する必要があります。

1. Visual Studio Code で、ジョブの *Transformation.asaql* ファイルに移動します。

   ![Visual Studio Code の Transformation.asaql ファイル](./media/resource-manager-export/transformation-asaql.png)

1. *Transformation.asaql* ファイルを右クリックし、メニューから **[ASA:Compile Script]\(ASA: スクリプトのコンパイル\)** を選択します。

1. Stream Analytics ジョブのワークスペースに **[Deploy]** フォルダーが表示されることに注意してください。

1. *JobTemplate.json* ファイルを調べます。これは、デプロイに使用される Azure Resource Management テンプレートです。

## <a name="complete-the-parameters-file"></a>パラメーター ファイルを完成する

次に、Azure Resource Management テンプレートのパラメーター ファイルを完成します。

1. Visual Studio Code で Stream Analytics ジョブ ワークスペースの **[Deploy]** フォルダーにある *JobTemplate.parameters.json* ファイルを開きます。

1. 入力キーと出力キーが null であることに注意してください。 null 値を入力リソースと出力リソースの実際のアクセス キーに置き換えます。

1. パラメーター ファイルを保存します。

## <a name="deploy-using-templates"></a>テンプレートを使用してデプロイする

前のセクションで生成した Azure Resource Manager のテンプレートを使用して Azure Stream Analytics ジョブをデプロイする準備ができました。

PowerShell ウィンドウで、次のコマンドを実行します。 *ResourceGroupName*、*TemplateFile*、および *TemplateParameterFile* は、必ず実際のリソース グループ名、およびジョブ ワークスペースの **[Deploy] フォルダー** 内の *JobTemplate.json* と *JobTemplate.parameters.json* ファイルへの完全ファイル パスに置き換えてください。

PowerShell が構成されていない場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)の手順に従ってください。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>次のステップ

* [Visual Studio Code を使用してライブ入力で Azure Stream Analytics ジョブをローカルでテストする](visual-studio-code-local-run-live-input.md)

* [Visual Studio Code で使用する Azure Stream Analytics ジョブの詳細 (プレビュー)](visual-studio-code-explore-jobs.md)