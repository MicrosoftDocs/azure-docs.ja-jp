---
title: Azure Stream Analytics CI/CD NuGet パッケージを使用する
description: この記事では、Azure Stream Analytics CI/CD NuGet パッケージを使って継続的インテグレーションとデプロイのプロセスを設定する方法を説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: 0b4356c74b2e0c1494456d5d1082efd7b8953a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693377"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>統合と開発に Azure Stream Analytics CI/CD NuGet パッケージを使用する 
この記事では、Azure Stream Analytics CI/CD NuGet パッケージを使って継続的インテグレーションとデプロイのプロセスを設定する方法を説明します。

MSBuild のサポートを得るには、[Visual Studio の Stream Analytics ツール](./stream-analytics-quick-create-vs.md)のバージョン 2.3.0000.0 以降を使います。

NuGet パッケージの[Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) を利用できます。 これは、[Stream Analytics Visual Studio プロジェクト](stream-analytics-vs-tools.md)の継続的インテグレーションおよびデプロイ プロセスをサポートする MSBuild、ローカル実行、デプロイ ツールを提供します。 
> [!NOTE]
> NuGet パッケージは、Visual Studio の Stream Analytics ツールのバージョン 2.3.0000.0 以降でのみ使用できます。 以前のバージョンの Visual Studio Tools で作成されたプロジェクトがある場合は、バージョン 2.3.0000.0 以降で開き、保存します。 その後、新しい機能が有効になります。 

詳しくは、[Visual Studio の Azure Stream Analytics ツール](./stream-analytics-quick-create-vs.md)に関する記事をご覧ください。

## <a name="msbuild"></a>MSBuild
標準の Visual Studio MSBuild エクスペリエンスと同様に、プロジェクトをビルドするオプションは 2 つあります。 プロジェクトを右クリックし、次に **[ビルド]** を選択できます。 コマンド ラインから NuGet パッケージの **MSBuild** を使用することもできます。
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Stream Analytics Visual Studio プロジェクトが正常にビルドされると、**bin/[Debug/Retail]/Deploy** フォルダーに次の 2 つの Azure Resource Manager テンプレート ファイルが生成されます。 

* Resource Manager テンプレート ファイル

   `[ProjectName].JobTemplate.json`

* Resource Manager パラメーター ファイル
   
   `[ProjectName].JobTemplate.parameters.json`

parameters.json ファイルの既定のパラメーターは、Visual Studio プロジェクトの設定から取得されます。 別の環境にデプロイする場合は、パラメーターを適宜置換します。

> [!NOTE]
> すべての資格情報の既定値は、null 値に設定されます。 クラウドにデプロイする前に、値を設定する "**必要があります**"。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
[Resource Manager テンプレート ファイルと Azure PowerShell を使用してデプロイする](../azure-resource-manager/templates/deploy-powershell.md)方法を確認してください。 [Resource Manager テンプレートのパラメーターとしてオブジェクトを使用する](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters)方法を確認してください。

出力シンクとしての Azure Data Lake Store Gen1 にマネージド ID を使用するには、Azure にデプロイする前に、PowerShell を使用してサービス プリンシパルへのアクセス許可を提供する必要があります。 詳細については、[Resource Manager テンプレートによってマネージド ID を使用した ADLS Gen1 のデプロイを行う](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)方法を確認してください。


## <a name="command-line-tool"></a>コマンドライン ツール

### <a name="build-the-project"></a>プロジェクトのビルド
NuGet パッケージには、**SA.exe** というコマンドライン ツールがあります。 継続的インテグレーションと継続的デリバリーのプロセスで使用できる任意のマシンでのプロジェクトのビルドとローカル テストをサポートします。 

デプロイ ファイルは、既定では現在のディレクトリに配置されます。 次の -OutputPath パラメーターを使用して出力パスを指定できます。

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>スクリプトのローカル テスト

プロジェクトで Visual Studio にローカル入力ファイルを指定した場合は、*localrun* コマンド使用して、自動化されたスクリプト テストを実行できます。 出力結果は現在のディレクトリに配置されます。
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Stream Analytics PowerShell API で使用するジョブ定義ファイルを生成する

*arm* コマンドは、ビルドで生成されたジョブ テンプレートとジョブ テンプレート パラメーター ファイルを引数に取ります。 次に、Stream Analytics PowerShell API で使用できるジョブ定義 JSON ファイルにこれらを結合します。

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
例:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>次のステップ

* [クイック スタート: Visual Studio で Azure Stream Analytics クラウド ジョブを作成する](stream-analytics-quick-create-vs.md)
* [Visual Studio で Stream Analytics クエリをローカルでテストする](stream-analytics-vs-tools-local-run.md)
* [Visual Studio で使用する Azure Stream Analytics ジョブの詳細](stream-analytics-vs-tools.md)