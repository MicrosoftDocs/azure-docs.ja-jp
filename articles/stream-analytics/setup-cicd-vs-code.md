---
title: Azure Stream Analytics CI/CD npm パッケージを使用する
description: この記事では、Azure Stream Analytics CI/CD npm パッケージを使って継続的インテグレーションとデプロイのプロセスを設定する方法を説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173342"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Stream Analytics CI/CD npm パッケージを使用する
この記事では、Azure Stream Analytics CI/CD npm パッケージを使って継続的インテグレーションとデプロイのプロセスを設定する方法を説明します。

## <a name="build-the-vs-code-project"></a>VS コード プロジェクトをビルドする

**asa-streamanalytics-cicd** npm パッケージを使用して、Azure Stream Analytics ジョブのための継続的インテグレーションと継続的なデプロイを有効にすることができます。 npm パッケージは、[Stream Analytics の Visual Studio Code プロジェクト](quick-create-vs-code.md)の Azure Resource Manager テンプレートを生成するためのツールを提供します。 Visual Studio Code をインストールしなくても、Windows、macOS、Linux で使用できます。

[パッケージをダウンロード](https://www.npmjs.com/package/azure-streamanalytics-cicd)したら、次のコマンドを使用して Azure Resource Manager のテンプレートを出力します。 **ScriptPath** 引数は、プロジェクト内の **asaql** ファイルへの絶対パスです。 asaproj.json ファイルと JobConfig.json ファイルがスクリプト ファイルと同じフォルダー内にあることを確認します。 **outputPath** が指定されていない場合、テンプレートは、プロジェクトの **bin** フォルダーの下の **Deploy** フォルダーに配置されます。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
例 (MacOS の場合)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Stream Analytics Visual Studio Code プロジェクトが正常にビルドされると、**bin/[Debug/Retail]/Deploy** フォルダーに次の 2 つの Azure Resource Manager テンプレート ファイルが生成されます。 

*  Resource Manager テンプレート ファイル

       [ProjectName].JobTemplate.json 

*  Resource Manager パラメーター ファイル

       [ProjectName].JobTemplate.parameters.json   

parameters.json ファイルの既定のパラメーターは、Visual Studio Code プロジェクトの設定から取得されます。 別の環境にデプロイする場合は、パラメーターを適宜置換します。

> [!NOTE]
> すべての資格情報の既定値は、null 値に設定されます。 クラウドにデプロイする前に、値を設定する "**必要があります**"。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
[Resource Manager テンプレート ファイルと Azure PowerShell を使用してデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)方法を確認してください。 [Resource Manager テンプレートのパラメーターとしてオブジェクトを使用する](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)方法を確認してください。

出力シンクとしての Azure Data Lake Store Gen1 にマネージド ID を使用するには、Azure にデプロイする前に、PowerShell を使用してサービス プリンシパルへのアクセス許可を提供する必要があります。 詳細については、[Resource Manager テンプレートによってマネージド ID を使用した ADLS Gen1 のデプロイを行う](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)方法を確認してください。
## <a name="next-steps"></a>次の手順

* [クイック スタート:Visual Studio Code で Azure Stream Analytics クラウド ジョブを作成する (プレビュー)](quick-create-vs-code.md)
* [Test Stream Analytics queries locally with Visual Studio Code (Preview)](vscode-local-run.md) (Visual Studio Code で Stream Analytics クエリをローカルでテストする (プレビュー))
* [Explore Azure Stream Analytics with Visual Studio Code (Preview)](vscode-explore-jobs.md) (Visual Studio Code で Azure Stream Analytics の詳細を確認する (プレビュー))
