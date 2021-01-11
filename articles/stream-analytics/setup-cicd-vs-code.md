---
title: CI/CD npm パッケージを使用して Azure Stream Analytics ジョブをデプロイする
description: この記事では、Azure Stream Analytics CI/CD npm パッケージを使って継続的インテグレーションとデプロイのプロセスを設定する方法を説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 75db20bdb746e7d15bef56ce7ac0a064993d3f3a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187763"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>CI/CD npm パッケージを使用して Azure Stream Analytics ジョブをデプロイする 

Azure Stream Analytics CI/CD npm パッケージを使って、Stream Analytics ジョブ用に継続的インテグレーションとデプロイのプロセスを設定できます。 この記事では、一般に CI/CD システムで npm パッケージを使用する方法について説明します。また、Azure Pipelines を使用したデプロイの具体的な手順についても説明します。

PowerShell を使用したデプロイの詳細については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)」を参照してください。 [Resource Manager テンプレートのパラメーターとしてオブジェクトを使用する](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)方法を詳しく確認することもできます。

## <a name="build-the-vs-code-project"></a>VS Code プロジェクトをビルドする

**asa-streamanalytics-cicd** npm パッケージを使用して、Azure Stream Analytics ジョブのための継続的インテグレーションと継続的なデプロイを有効にすることができます。 npm パッケージは、[Stream Analytics の Visual Studio Code プロジェクト](quick-create-vs-code.md)の Azure Resource Manager テンプレートを生成するためのツールを提供します。 Visual Studio Code をインストールしなくても、Windows、macOS、Linux で使用できます。

[パッケージを直接ダウンロードする](https://www.npmjs.com/package/azure-streamanalytics-cicd)ことや、[ コマンドを使用して](https://docs.npmjs.com/downloading-and-installing-packages-globally)グローバルに`npm install -g azure-streamanalytics-cicd`インストールすることができます。 これは推奨される方法です。**Azure Pipelines** でビルド パイプラインの PowerShell または Azure CLI スクリプト タスクにも使用できます。

パッケージをインストールしたら、次のコマンドを使用して Azure Resource Manager のテンプレートを出力します。 **ScriptPath** 引数は、プロジェクト内の **asaql** ファイルへの絶対パスです。 asaproj.json ファイルと JobConfig.json ファイルがスクリプト ファイルと同じフォルダー内にあることを確認します。 **outputPath** が指定されていない場合、テンプレートは、プロジェクトの **bin** フォルダーの下の **Deploy** フォルダーに配置されます。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
例 (MacOS の場合)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Stream Analytics Visual Studio Code プロジェクトが正常にビルドされると、**bin/[Debug/Retail]/Deploy** フォルダーに次の 2 つの Azure Resource Manager テンプレート ファイルが生成されます。 

* Resource Manager テンプレート ファイル

   `[ProjectName].JobTemplate.json`

* Resource Manager パラメーター ファイル

   `[ProjectName].JobTemplate.parameters.json`   

parameters.json ファイルの既定のパラメーターは、Visual Studio Code プロジェクトの設定から取得されます。 別の環境にデプロイする場合は、パラメーターを適宜置換します。

> [!NOTE]
> すべての資格情報の既定値は、null 値に設定されます。 クラウドにデプロイする前に、値を設定する "**必要があります**"。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Azure Pipelines を使用したデプロイ

このセクションでは、npm を使用して Azure Pipelines の[ビルド](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)および[リリース](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) パイプラインを作成する方法について詳しく説明します。

Web ブラウザーを開き、Azure Stream Analytics Visual Studio Code プロジェクトに移動します。

1. 左側のナビゲーション メニューの **[パイプライン]** で、 **[ビルド]** を選択します。 **[新しいパイプライン]** を選択します。

   ![新しい Azure パイプラインの作成](./media/setup-cicd-vs-code/new-pipeline.png)

2. **[従来のエディターを使用する]** を選択して、YAML なしでパイプラインを作成します。

3. ソースの種類、チーム プロジェクト、リポジトリを選択します。 その後 **[続行]** を選択します。

   ![Azure Stream Analytics プロジェクトの選択](./media/setup-cicd-vs-code/select-repo.png)

4. **[テンプレートの選択]** ページで、 **[空のジョブ]** を選択します。

### <a name="add-npm-task"></a>npm タスクの追加

1. **[タスク]** ページで、 **[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 タスク検索に「npm」と入力し、 **[npm]** を選択します。

   ![npm タスクの選択](./media/setup-cicd-vs-code/search-npm.png)

2. タスクに**表示名**を付けます。 **[コマンド]** オプションを "*カスタム*" に変更し、 **[コマンドと引数]** に次のコマンドを入力します。 他の既定のオプションはそのまま使用します。

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![npm タスクの構成の入力](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>コマンド ライン タスクの追加

1. **[タスク]** ページで、 **[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 **[コマンド ライン]** を探します。

2. タスクに**表示名**を指定し、次のスクリプトを入力します。 リポジトリ名とプロジェクト名を使用してスクリプトを変更します。

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![コマンド ライン タスクの構成の入力](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>ファイルのコピー タスクの追加

1. **[タスク]** ページで、 **[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 **[ファイルのコピー]** を探します。 次の構成を入力します。

   |パラメーター|入力|
   |-|-|
   |Display name|ファイルのコピー先: $(build.artifactstagingdirectory)|
   |[同期元フォルダー]|`$(system.defaultworkingdirectory)`| 
   |内容| `**\Deploy\**` |
   |ターゲット フォルダー| `$(build.artifactstagingdirectory)`|

   ![コピー タスクの構成の入力](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>ビルド成果物の公開タスクの追加

1. **[タスク]** ページで、 **[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 **[ビルド成果物の公開]** を探し、黒い矢印アイコン付きオプションを選択します。 

2. 既定の構成は一切変更しないでください。

### <a name="save-and-run"></a>保存と実行

npm、コマンド ライン、ファイルのコピー、およびビルド成果物の発行タスクの追加が完了したら、 **[保存してキューに登録]** を選択します。 メッセージが表示されたら、保存コメントを入力し、 **[保存および実行]** を選択します。

## <a name="release-with-azure-pipelines"></a>Azure Pipelines を使用したリリース

Web ブラウザーを開き、Azure Stream Analytics Visual Studio Code プロジェクトに移動します。

1. 左側のナビゲーション メニューの **[パイプライン]** で、 **[リリース]** を選択します。 **[新しいパイプライン]** を選択します。

2. **[start with an Empty job]\(空のジョブを開始する\)** を選択します。

3. **[Artifacts]\(成果物\)** ボックスで、 **[+ Add an artifact]\(+ 成果物の追加\)** を選択します。 **[ソース]** で、作成したビルド パイプラインを選択し、 **[追加]** を選択します。

   ![ビルド パイプライン成果物の入力](./media/setup-cicd-vs-code/build-artifact.png)

4. **[Stage 1]\(ステージ 1\)** の名前を **[Deploy job to test environment]\(テスト環境にジョブをデプロイ\)** に変更します。

5. 新しいステージを追加して、 **[Deploy job to production environment]\(運用環境にジョブをデプロイ\)** という名前を付けます。

### <a name="add-tasks"></a>タスクの追加

1. タスクのドロップダウンから、 **[Deploy job to test environment]\(テスト環境にジョブをデプロイ\)** を選択します。 

2. **[エージェント ジョブ]** の隣の **[+]** を選択し、 *[Azure リソース グループの配置]* を探します。 次のパラメーターを入力します。

   |設定|値|
   |-|-|
   |Display name| *Deploy myASAJob* (myASAJob をデプロイ)|
   |Azure サブスクリプション| サブスクリプションを選択します。|
   |アクション| *Create or update resource group* (リソース グループを作成または更新)|
   |Resource group| Stream Analytics ジョブを含めるテスト リソース グループの名前を選択します。|
   |場所|テスト リソース グループの場所を選択します。|
   |テンプレートの場所| *Linked artifact* (リンクされた成果物)|
   |Template| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Template parameters|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |テンプレート パラメーターのオーバーライド|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |展開モード|[増分]|

3. タスクのドロップダウンから、 **[Deploy job to production environment]\(運用環境にジョブをデプロイ\)** を選択します。

4. **[エージェント ジョブ]** の隣の **[+]** を選択し、 *[Azure リソース グループの配置]* を探します。 次のパラメーターを入力します。

   |設定|値|
   |-|-|
   |Display name| *Deploy myASAJob* (myASAJob をデプロイ)|
   |Azure サブスクリプション| サブスクリプションを選択します。|
   |アクション| *Create or update resource group* (リソース グループを作成または更新)|
   |Resource group| Stream Analytics ジョブを含める運用リソース グループの名前を選択します。|
   |場所|運用リソース グループの場所を選びます。|
   |テンプレートの場所| *Linked artifact* (リンクされた成果物)|
   |Template| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Template parameters|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |テンプレート パラメーターのオーバーライド|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |展開モード|[増分]|

### <a name="create-release"></a>リリースの作成

リリースを作成するには、右上隅にある **[リリースの作成]** を選択します。

![Azure Pipelines を使用したリリースの作成](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>その他のリソース

出力シンクとしての Azure Data Lake Store Gen1 にマネージド ID を使用するには、Azure にデプロイする前に、PowerShell を使用してサービス プリンシパルへのアクセス許可を提供する必要があります。 詳細については、[Resource Manager テンプレートによってマネージド ID を使用した ADLS Gen1 のデプロイを行う](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)方法を確認してください。


## <a name="next-steps"></a>次のステップ

* [クイック スタート: Visual Studio Code で Azure Stream Analytics クラウド ジョブを作成する (プレビュー)](quick-create-vs-code.md)
* [Test Stream Analytics queries locally with Visual Studio Code (Preview)](visual-studio-code-local-run.md) (Visual Studio Code で Stream Analytics クエリをローカルでテストする (プレビュー))
* [Explore Azure Stream Analytics with Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md) (Visual Studio Code で Azure Stream Analytics の詳細を確認する (プレビュー))
