---
title: CI/CD ツールを使用して Azure Stream Analytics ジョブのビルド、テスト、デプロイを自動化する
description: この記事では、Azure Stream Analytics の CI/CD ツールを使用して、Azure Stream Analytics プロジェクトのビルド、テスト、デプロイを自動化する方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 2dbb491e77f132daf7b432f27705eba9e3e3cd3c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036964"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>CI/CD ツールを使用して Azure Stream Analytics ジョブのビルド、テスト、デプロイを自動化する

Azure Stream Analytics の CI/CD npm パッケージを使用すると、Azure Stream Analytics の Visual Studio Code プロジェクトまたは Visual Studio プロジェクトのビルド、テスト、デプロイを自動的に実行することができます。 こうしたプロジェクトは開発ツールを使用して作成できるほか、既存の Stream Analytics ジョブからエクスポートすることもできます。 この記事では、CI/CD システムで npm パッケージを使用する方法について説明します。 Azure Pipelines を使用したデプロイについては、[Azure DevOps を使用して Stream Analytics ジョブの CI/CD パイプラインを作成する方法](set-up-cicd-pipeline.md)に関するページを参照してください。

## <a name="installation"></a>インストール

[パッケージは直接ダウンロードする](https://www.npmjs.com/package/azure-streamanalytics-cicd)か、`npm install -g azure-streamanalytics-cicd` コマンドを使用して[グローバルに](https://docs.npmjs.com/downloading-and-installing-packages-globally)インストールすることができます。 推奨されるのは、コマンドを使用した方法です。このコマンドは、**Azure Pipelines** でビルド パイプラインの PowerShell または Azure CLI スクリプト タスクにも使用できます。

## <a name="build-the-project"></a>プロジェクトのビルド

**asa-streamanalytics-cicd** の npm パッケージには、Stream Analytics [Visual Studio Code プロジェクト](./quick-create-visual-studio-code.md)または [Visual Studio プロジェクト](stream-analytics-quick-create-vs.md)の Azure Resource Manager テンプレートを生成するためのツールが用意されています。 また、この npm パッケージは、Windows、macOS、Linux 上で、Visual Studio Code や Visual Studio をインストールせずに使用することもできます。

パッケージのインストール後、次のコマンドを使用して Stream Analytics プロジェクトをビルドしてください。

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

*build* コマンドにより、キーワード構文チェックが実行され、Azure Resource Manager テンプレートが出力されます。

| パラメーター | 説明 |
|---|---|
| `-project` | Visual Studio Code プロジェクトの **asaproj.json** ファイルまたは Visual Studio プロジェクトの **<プロジェクト名>.asaproj** の絶対パス。 |
| `-outputPath` | Azure Resource Manager テンプレートの出力フォルダーのパス。 指定されていない場合、テンプレートは現在のディレクトリに格納されます。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Stream Analytics プロジェクトが正常にビルドされると、出力フォルダーに次の 2 つのファイルが生成されます。

* Azure Resource Manager テンプレート ファイル

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager パラメーター ファイル

   `[ProjectName].JobTemplate.parameters.json`

parameters.json ファイル内の既定のパラメーターは、Visual Studio Code または Visual Studio プロジェクトの設定から取得されます。 別の環境にデプロイする場合は、パラメーターを適宜置換します。

すべての資格情報の既定値は **null** です。 Azure にデプロイする前に、値を設定する必要があります。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

出力シンクとしての Azure Data Lake Store Gen1 にマネージド ID を使用するには、Azure にデプロイする前に、PowerShell を使用してサービス プリンシパルへのアクセス許可を提供する必要があります。 詳細については、[Resource Manager テンプレートによってマネージド ID を使用した ADLS Gen1 のデプロイを行う](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)方法を確認してください。

## <a name="local-run"></a>ローカルの実行

プロジェクトでローカルの入力ファイルが指定されている場合は、`localrun` コマンドを使用して Stream Analytics スクリプトをローカルで実行することができます。

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| パラメーター | 説明 |
|---|---|
| `-project` | Visual Studio Code プロジェクトの **asaproj.json** ファイルまたは Visual Studio プロジェクトの **<プロジェクト名>.asaproj** のパス。 |
| `-outputPath` | 出力フォルダーのパス。 指定しなかった場合、出力結果ファイルは現在のディレクトリに格納されます。 |
| `-customCodeZipFilePath` | UDF やデシリアライザーなど、C# のカスタム コードが使用されている場合、その ZIP ファイルのパス。 DLL を ZIP ファイルにパッケージして、そのパスを指定してください。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF は、Windows 上でのみ機能します。

## <a name="automated-test"></a>自動テスト

CI/CD npm パッケージを使用して、Stream Analytics スクリプトの自動テストを構成、実行できます。

### <a name="add-a-test-case"></a>テスト ケースを追加する

テスト ケースは、テスト構成ファイルに記述されます。 最初に、`addtestcase` コマンドを使用してテスト ケース テンプレートをテスト構成ファイルに追加してください。 テスト構成ファイルが存在しない場合は、既定で作成されます。

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| パラメーター | 説明 |
|---|---|
| `-project` | Visual Studio Code プロジェクトの **asaproj.json** ファイルまたは Visual Studio プロジェクトの **<プロジェクト名>.asaproj** のパス。 |
| `-testConfigPath` | テスト構成ファイルのパス。 指定しなかった場合は、現在 **asaproj.json** ファイルが置かれているディレクトリの **\test** でファイルが検索されます。既定のファイル名は **testConfig.json** です。 新しいファイルが存在しない場合は作成されます。 |

> [!NOTE]
> 生成された **testConfig.json** ファイルの `Script` 値は、コンテキストを与える目的でのみ使用され、テスト ロジックでは使用されません。 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

テスト構成ファイルが空の場合、次の内容がファイルに書き込まれます。 それ以外の場合は、**TestCases** の配列にテスト ケースが追加されます。 必要な入力構成は、入力構成ファイルに存在すれば、それに従って自動的に入力されます。 それ以外の場合は、既定値が構成されます。 テストを実行する前に、各入力の **FilePath** と必要な出力を指定する必要があります。 構成は手動で変更できます。

テスト検証で特定の出力を無視したい場合は、必要な出力の **Required** フィールドを **false** に設定してください。

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

> [!NOTE]
> 現在、`ScriptType` 要素に使用できる値は `InputMock` のみです。これは、既定値でもあります。 それ以外の値に設定した場合は無視され、既定値 (`InputMock`) が使用されます。 

### <a name="run-a-unit-test"></a>単体テストを実行する

以下のコマンドを使用すると、プロジェクトのテスト ケースを複数実行することができます。 テスト結果の概要は出力フォルダーに生成されます。 このプロセスの終了コードは、すべてのテストに合格した場合は **0**、例外が発生した場合は **-1**、テストで不合格となった場合は **-2** になります。

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| パラメーター | 説明 |
|---|---|
| `-project` | Visual Studio Code プロジェクトの **asaproj.json** ファイルまたは Visual Studio プロジェクトの **<プロジェクト名>.asaproj** のパス。 |
| `-testConfigPath` | テスト構成ファイルのパス。 指定しなかった場合は、現在 **asaproj.json** ファイルが置かれているディレクトリの **\test** でファイルが検索されます。既定のファイル名は **testConfig.json** です。
| `-outputPath` | テスト結果の出力フォルダーのパス。 指定しなかった場合、出力結果ファイルは現在のディレクトリに格納されます。 |
| `-customCodeZipFilePath` | UDF やデシリアライザーなどのカスタム コードが使用されている場合、その ZIP ファイルのパス。 |

すべてのテストが完了すると、テスト結果の概要が JSON 形式で出力フォルダーに生成されます。 概要ファイルは **testResultSummary.json** という名前になります。

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Azure にデプロイ

ビルドから生成される Azure Resource Manager テンプレートとパラメーター ファイルを使用して [Azure にジョブをデプロイ](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)することができます。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の継続的インテグレーションと継続的デプロイ](cicd-overview.md)
* [Azure Pipelines を使用して Stream Analytics ジョブの CI/CD パイプラインを設定する](set-up-cicd-pipeline.md)
