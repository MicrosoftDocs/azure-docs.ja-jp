---
title: Azure DevOps を使用して Stream Analytics ジョブの CI/CD パイプラインを作成する
description: この記事では、Azure DevOps で Azure Stream Analytics ジョブの CI/CD (継続的インテグレーションと継続的デプロイ) パイプラインを設定する方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: dbd6a1a0c8643adc4918cc15e214e903dfb1ccb6
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775937"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Azure DevOps を使用して Stream Analytics ジョブの CI/CD パイプラインを作成する

この記事では、Azure Stream Analytics CI/CD ツールを使用して、Azure DevOps の[ビルド](/azure/devops/pipelines/get-started/pipelines-get-started) パイプラインと[リリース](/azure/devops/pipelines/release/define-multistage-release-process) パイプラインを作成する方法について説明します。

## <a name="commit-your-stream-analytics-project"></a>Stream Analytics プロジェクトをコミットする

開始する前に、Stream Analytics プロジェクトをソース ファイルとして [Azure DevOps](/azure/devops/user-guide/source-control) リポジトリにコミットします。 この[サンプル リポジトリ](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo)と [Stream Analytics プロジェクト ソース コード](https://dev.azure.com/ASA-CICD-sample/_git/azure-streamanalytics-cicd-demo)を Azure Pipelines で参照できます。

この記事の手順では、Stream Analytics Visual Studio Code プロジェクトを使用します。 Visual Studio プロジェクトを使用している場合は、「[CI/CD ツールを使用して Azure Stream Analytics ジョブのビルド、テスト、デプロイを自動化する](cicd-tools.md)」の手順に従ってください。

## <a name="create-a-build-pipeline"></a>ビルド パイプラインを作成する

このセクションでは、ビルド パイプラインを作成する方法について説明します。 この[ビルドとテストの自動パイプライン](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo/_build) サンプルは、Azure DevOps から参照できます。

1. Web ブラウザーを開き、Azure DevOps で目的のプロジェクトに移動します。  

1. 左側のナビゲーション メニューの **[パイプライン]** で、**[ビルド]** を選択します。 **[新しいパイプライン]** を選択します。

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="新しい Azure パイプラインの作成":::

1. **[従来のエディターを使用する]** を選択して、YAML なしでパイプラインを作成します。

1. ソースの種類、チーム プロジェクト、リポジトリを選択します。 その後、 **[続行]** を選択します。

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Azure Stream Analytics プロジェクトの選択":::

1. **[テンプレートの選択]** ページで、**[空のジョブ]** を選択します。

## <a name="install-npm-package"></a>npm パッケージをインストールする

1. **[タスク]** ページで、**[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 タスク検索に「*npm*」と入力し、 **[npm]** を選択します。

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="npm タスクの選択":::

2. タスクに **表示名** を付けます。 **[コマンド]** オプションを "*カスタム*" に変更し、**[コマンドと引数]** に次のコマンドを入力します。 他の既定のオプションはそのまま使用します。

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="npm タスクの構成の入力":::

ホストされている Linux エージェントを使用する必要がある場合は、次の手順を使用します。
1.  該当する **エージェントの指定** を選択します
   
    :::image type="content" source="media/set-up-cicd-pipeline/select-linux-agent.png" alt-text="エージェントの指定を選択しているスクリーンショット。":::

2.  **[タスク]** ページで、**[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 タスク検索で「*コマンド ライン*」と入力し、 **[コマンド ライン]** を選択します。
   
    :::image type="content" source="media/set-up-cicd-pipeline/cmd-search.png" alt-text="コマンドライン タスクを検索しているスクリーンショット。":::

3.  タスクに **表示名** を付けます。 **[スクリプト]** に次のコマンドを入力します。 他の既定のオプションはそのまま使用します。

      ```bash
      sudo npm install -g azure-streamanalytics-cicd --unsafe-perm=true --allow-root
      ```
      :::image type="content" source="media/set-up-cicd-pipeline/cmd-scripts.png" alt-text="cmd タスクのスクリプトを入力しているスクリーンショット。":::

## <a name="add-a-build-task"></a>ビルド タスクの追加

1. **[変数]** ページで、 **[パイプライン変数]** の **[+ 追加]** を選択します。 次の変数を追加します。 次の値を好みに応じて設定します。

   |変数名|値|
   |-|-|
   |projectRootPath|<プロジェクト名>|
   |outputPath|Output|
   |deployPath|配置|

2. **[タスク]** ページで、**[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 **[コマンド ライン]** を探します。

3. タスクに **表示名** を指定し、次のスクリプトを入力します。 リポジトリ名とプロジェクト名を使用してスクリプトを変更します。

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   以下の画像では、Stream Analytics Visual Studio Code プロジェクトを例として使用しています。

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="コマンド ライン タスクの構成を入力する (Visual Studio Code)":::

## <a name="add-a-test-task"></a>テスト タスクを追加する

1. **[変数]** ページで、 **[パイプライン変数]** の **[+ 追加]** を選択します。 次の変数を追加します。 値は、実際の出力パスとリポジトリ名に合わせて変更してください。

   |変数名|値|
   |-|-|
   |testPath|テスト|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="パイプライン変数を追加する":::

2. **[タスク]** ページで、**[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 **[コマンド ライン]** を探します。

3. タスクに **表示名** を指定し、次のスクリプトを入力します。 スクリプトは、実際のプロジェクト ファイル名とテスト構成ファイルのパスに合わせて変更してください。 

   テスト ケースを追加して構成する方法について詳しくは、[自動テストの手順](cicd-tools.md#automated-test)を参照してください。

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="コマンド ライン タスクの構成を入力する":::

## <a name="add-a-copy-files-task"></a>ファイルのコピー タスクを追加する

テスト概要ファイルと Azure Resource Manager テンプレート ファイルを成果物フォルダーにコピーするには、ファイルのコピー タスクを追加する必要があります。 

1. **[タスク]** ページで、 **[Agent job 1]\(エージェントジョブ 1\)** の横にある **+** を選択します。 **[ファイルのコピー]** を探します。 次の構成を入力します。 **[コンテンツ]** に `**` を割り当てることにより、テスト結果のすべてのファイルがコピーされます。

   |パラメーター|入力|
   |-|-|
   |表示名|ファイルのコピー先: $(build.artifactstagingdirectory)|
   |[同期元フォルダー]|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |内容| `**` |
   |ターゲット フォルダー| `$(build.artifactstagingdirectory)`|

2. **[管理オプション]** を展開します。 **[このタスクを実行]** の **[前のいずれかのタスクが失敗した場合でも、ビルドがキャンセルされていなければ]** を選択します。

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="コピー タスクの構成の入力":::

## <a name="add-a-publish-build-artifacts-task"></a>ビルド成果物の公開タスクを追加する

1. **[タスク]** ページで、**[Agent job 1]\(エージェントジョブ 1\)** の横にあるプラス記号を選択します。 **[ビルド成果物の公開]** を探し、黒い矢印アイコン付きオプションを選択します。

2. **[管理オプション]** を展開します。 **[このタスクを実行]** の **[前のいずれかのタスクが失敗した場合でも、ビルドがキャンセルされていなければ]** を選択します。

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="公開タスクの構成を入力する":::

## <a name="save-and-run"></a>保存と実行

npm パッケージ、コマンド ライン、ファイルのコピー、およびビルド成果物の公開タスクの追加が完了したら、 **[保存してキューに登録]** を選択します。 メッセージが表示されたら、保存コメントを入力し、**[保存および実行]** を選択します。 テスト結果は、パイプラインの **[Summary]\(概要\)** ページからダウンロードできます。

## <a name="check-the-build-and-test-results"></a>ビルドとテストの結果を確認する

テスト概要ファイルと Azure Resource Manager テンプレート ファイルは、 **[Published]\(公開済み\)** フォルダーにあります。

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="ビルドとテストの結果を確認する":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="成果物を確認する":::

## <a name="release-with-azure-pipelines"></a>Azure Pipelines を使用したリリース

このセクションでは、リリース パイプラインを作成する方法について説明します。 この[リリース パイプライン](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2) サンプルは、Azure DevOps から参照できます。

Web ブラウザーを開き、Azure Stream Analytics Visual Studio Code プロジェクトに移動します。

1. 左側のナビゲーション メニューの **[パイプライン]** で、**[リリース]** を選択します。 **[新しいパイプライン]** を選択します。

2. **[start with an Empty job]\(空のジョブを開始する\)** を選択します。

3. **[Artifacts]\(成果物\)** ボックスで、**[+ Add an artifact]\(+ 成果物の追加\)** を選択します。 **[ソース]** で、作成したビルド パイプラインを選択し、 **[追加]** を選択します。

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="ビルド パイプライン成果物の入力":::

4. **[Stage 1]\(ステージ 1\)** の名前を **[Deploy job to test environment]\(テスト環境にジョブをデプロイ\)** に変更します。

5. 新しいステージを追加して、**[Deploy job to production environment]\(運用環境にジョブをデプロイ\)** という名前を付けます。

### <a name="add-deploy-tasks"></a>デプロイ タスクを追加する

1. タスクのドロップダウンから、**[Deploy job to test environment]\(テスト環境にジョブをデプロイ\)** を選択します。

2. **[エージェント ジョブ]** の隣の **[+]** を選択し、 **[ARM テンプレートのデプロイ]** を探します。 次のパラメーターを入力します。

   |パラメーター|値|
   |-|-|
   |表示名| *myASAProject をデプロイする*|
   |Azure サブスクリプション| サブスクリプションを選択します。|
   |アクション| *Create or update resource group* (リソース グループを作成または更新)|
   |Resource group| Stream Analytics ジョブを含めるテスト リソース グループの名前を選択します。|
   |場所|テスト リソース グループの場所を選択します。|
   |テンプレートの場所| Linked artifact (リンクされた成果物)|
   |Template| $(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |テンプレート パラメーター|$(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |テンプレート パラメーターのオーバーライド|-<ARM テンプレート パラメーター> "<実際の値>"。 パラメーターは、 **[変数]** を使用して定義できます。|
   |展開モード|増分|

3. タスクのドロップダウンから、**[Deploy job to production environment]\(運用環境にジョブをデプロイ\)** を選択します。

4. **[エージェント ジョブ]** の隣の **[+]** を選択し、 *[ARM テンプレートのデプロイ]* を探します。 次のパラメーターを入力します。

   |パラメーター|値|
   |-|-|
   |表示名| *myASAProject をデプロイする*|
   |Azure サブスクリプション| サブスクリプションを選択します。|
   |アクション| *Create or update resource group* (リソース グループを作成または更新)|
   |Resource group| Stream Analytics ジョブを含める運用リソース グループの名前を選択します。|
   |場所|運用リソース グループの場所を選びます。|
   |テンプレートの場所| *Linked artifact* (リンクされた成果物)|
   |Template| $(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |テンプレート パラメーター|$(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |テンプレート パラメーターのオーバーライド|-<ARM テンプレート パラメーター> "<実際の値>"|
   |展開モード|増分|

### <a name="create-a-release"></a>リリースを作成する

リリースを作成するには、右上隅にある **[リリースの作成]** を選択します。

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Azure Pipelines を使用したリリースの作成":::

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の継続的インテグレーションと継続的デプロイ](cicd-overview.md)
* [CI/CD ツールを使用して Azure Stream Analytics ジョブのビルド、テスト、デプロイを自動化する](cicd-tools.md)