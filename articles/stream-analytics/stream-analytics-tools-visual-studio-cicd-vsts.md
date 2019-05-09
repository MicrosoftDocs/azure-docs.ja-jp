---
title: Azure DevOps を使用して CI/CD で Azure Stream Analytics ジョブをデプロイする
description: この記事では、Azure DevOps Services を使用して CI/CD で Stream Analytics ジョブをデプロイする方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 9e05e4eab8bd3c307334b62df00dc03e56ce60ad
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727589"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>チュートリアル:Azure Pipelines を使用して CI/CD で Azure Stream Analytics ジョブをデプロイする
このチュートリアルでは、Azure Pipelines を使用して、Azure Stream Analytics ジョブの継続的インテグレーションと継続的配置を設定する方法について説明します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * Azure Pipelines にビルド パイプラインを作成する
> * Azure Pipelines にリリース パイプラインを作成する
> * アプリケーションを自動的にデプロイおよびアップグレードする

## <a name="prerequisites"></a>前提条件
始める前に、以下のものを用意してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) と、**Azure 開発**ワークロードまたは**データの保存と処理**ワークロードをインストールします。
* [Visual Studio で Stream Analytics プロジェクト](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs)を作成します。
* [Azure DevOps](https://visualstudio.microsoft.com/team-services/) 組織を作成します。

## <a name="configure-nuget-package-dependency"></a>NuGet パッケージの依存関係を構成する
任意のマシンで自動ビルドと自動配置を実行するには、NuGet パッケージ `Microsoft.Azure.StreamAnalytics.CICD` を使用する必要があります。 これは、Stream Analytics Visual Studio プロジェクトの継続的インテグレーションおよびデプロイ プロセスをサポートする MSBuild、ローカル実行、デプロイ ツールを提供します。 詳細については、[Stream Analytics の CI/CD ツール](stream-analytics-tools-for-visual-studio-cicd.md)に関するページを参照してください。

**packages.config** をプロジェクト ディレクトリに追加します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>新しい Azure Repos の Git リポジトリで Visual Studio ソリューションを共有する

ビルドを生成できるように、Azure DevOps のプロジェクトで、アプリケーションのソース ファイルを共有します。  

1. Visual Studio の右下隅のステータス バーで **[ソース管理に追加]**、**[Git]** の順に選択して、プロジェクトの新しいローカル Git リポジトリを作成します。 

2. **チーム エクスプローラー**の **[同期]** ビューで、**[Push to Azure DevOps Services]\(Azure DevOps Services へのプッシュ\)** にある **[Git リポジトリの発行]** ボタンを選択します。

   ![[Push to Azure DevOps Services]\(Azure DevOps Services へのプッシュ\) の [Git リポジトリの発行] ボタン](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. 電子メールを確認し、**[Azure DevOps Services Domain]\(Azure DevOps Services ドメイン\)** ドロップダウンからご自身の組織を選択します。 リポジトリ名を入力し、**[リポジトリの発行]** を選択します。

   ![[Git リポジトリのプッシュ] の [リポジトリの発行] ボタン](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    リポジトリを公開すると、ローカル リポジトリと同じ名前の新しいプロジェクトがご自身の組織に作成されます。 既存のプロジェクトでリポジトリを作成するには、**[リポジトリ名]** の横にある **[詳細]** をクリックして、プロジェクトを選択します。 **[See it on the web]\(Web を参照\)** を選択すると、ブラウザーでコードを表示できます。
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Azure DevOps で継続的デリバリーを構成する
Azure Pipelines のビルド パイプラインでは、順次実行されるビルド ステップで構成されたワークフローを記述します。 Azure Pipelines のビルド パイプラインの詳細については、[こちら](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)をご覧ください。 

Azure Pipelines のリリース パイプラインでは、クラスターにアプリケーション パッケージをデプロイするワークフローを記述します。 ビルド パイプラインとリリース パイプラインを併用すると、ソース ファイルから始まり、クラスターでのアプリケーションの実行で終わるワークフロー全体を実行できます。 Azure Pipelines のリリース パイプラインの詳細については、[こちら](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)をご覧ください。

### <a name="create-a-build-pipeline"></a>ビルド パイプラインを作成する
Web ブラウザーを開き、[Azure DevOps](https://app.vsaex.visualstudio.com/) で先ほど作成したプロジェクトに移動します。 

1. **[ビルドとリリース]** タブで、**[ビルド]**、**[+ 新規]** の順に選択します。  **[Azure DevOps Services Git]** と **[続行]** を選択します。
    
    ![Azure DevOps で DevOps Git ソースを選択する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. **[テンプレートの選択]** で **[空のプロセス]** をクリックし、空のパイプラインを使って開始します。
    
    ![DevOps でテンプレートのオプションから空のプロセスを選択する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. **[トリガー]** で **[継続的インテグレーションを有効にする]** トリガー状態をオンにして、継続的インテグレーションを有効にします。  **[保存してキューに登録]** を選択して、ビルドを手動で開始します。 
    
    ![[継続的インテグレーションを有効にする] トリガー状態](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. ビルドは、プッシュ時またはチェックイン時にもトリガーされます。 ビルドの進行状況を確認するには、**[ビルド]** タブに切り替えます。ビルドが正常に実行されることを確認したら、アプリケーションをクラスターにデプロイするリリース パイプラインを定義する必要があります。 ビルド パイプラインの横にある省略記号を右クリックして、**[編集]** を選択します。

5.  **[タスク]** の **[エージェント キュー]** に「Hosted」と入力します。
    
    ![[タスク] メニューでエージェント キューを選択する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. **[フェーズ 1]** で **[+]** をクリックして、**[NuGet]** タスクを追加します。
    
    ![エージェント キューの NuGet タスクを追加する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. **[詳細設定]** を展開して、**[宛先ディレクトリ]** に「`$(Build.SourcesDirectory)\packages`」と入力します。 残りの既定の NuGet 構成値はそのままにします。

   ![NuGet の復元タスクを構成する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. **[フェーズ 1]** で **[+]** をクリックして、**[MSBuild]** タスクを追加します。

   ![エージェント キューの MSBuild タスクを追加する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. **[MSBuild 引数]** を次のように変更します。

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![DevOps で MSBuild タスクを構成する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. **[フェーズ 1]** で **[+]** をクリックして、**[Azure リソース グループの配置]** タスクを追加します。 
    
    ![[Azure リソース グループの配置] タスクの追加](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. **[Azure の詳細]** を展開し、以下のように構成を入力します。
    
    |**設定**  |**推奨値**  |
    |---------|---------|
    |サブスクリプション  |  サブスクリプションを選択します。   |
    |Action  |  リソース グループを作成または更新します。   |
    |リソース グループ  |  リソース グループ名を入力します。   |
    |Template  | <実際のソリューション パス>\bin\Debug\Deploy\\<実際のプロジェクト名>.JobTemplate.json   |
    |Template parameters  | <実際のソリューション パス>\bin\Debug\Deploy\\<実際のプロジェクト名>.JobTemplate.parameters.json   |
    |テンプレート パラメーターのオーバーライド  | オーバーライドするテンプレート パラメーターをテキスト ボックスに入力します。 例えば、–storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre) です。 このプロパティは省略可能です。ただし、主要なパラメーターがオーバーライドされない場合、ビルドでエラーが発生します。    |
    
    ![[Azure リソース グループの配置] のプロパティを設定する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. **[保存してキューに登録]** をクリックして、ビルド パイプラインをテストします。
    
    ![DevOps でビルドを保存してキューに登録する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>ビルド プロセスの失敗
ビルド パイプラインの **[Azure リソース グループの配置]** タスクでテンプレート パラメーターをオーバーライドしなかった場合、null 値の配置パラメーターに関するエラーが発生する可能性があります。 エラーを解決するには、ビルド パイプラインに戻って null 値のパラメーターをオーバーライドします。

   ![DevOps での Stream Analytics ビルド プロセスの失敗](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>リリースをトリガーする変更のコミットとプッシュ
Azure DevOps へのコード変更をチェックインして、継続的インテグレーション パイプラインが機能していることを確認します。    

コードを記述すると、変更内容は Visual Studio によって自動的に追跡されます。 右下のステータス バーで [保留中の変更] アイコンを選択して、ローカル Git リポジトリに変更をコミットします。

1. チーム エクスプローラーの **[変更]** ビューで、更新内容を説明するメッセージを追加し、変更をコミットします。

    ![Visual Studio からリポジトリの変更をコミットする](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. 発行されていない変更のステータス バー アイコンを選択するか、またはチーム エクスプローラーで [同期] ビューを選択します。 **[プッシュ]** を選択して、Azure DevOps のコードを更新します。

    ![Visual Studio から変更をプッシュする](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

Azure DevOps Services へ変更をプッシュすると、ビルドが自動的にトリガーされます。  ビルド パイプラインが正常に完了すると、リリースは自動的に作成され、クラスター上のジョブの更新が開始されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合は、ジョブを停止し、必要なときに再起動することができます。 このジョブの使用を続けない場合は、以下の手順に従って、このチュートリアルで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。  
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

Visual Studio 用の Azure Stream Analytics ツールを使用して継続的インテグレーションと継続的配置のプロセスを設定する方法について確認するには、CI/CD パイプラインの設定に関する記事に進みます。

> [!div class="nextstepaction"]
> [Stream Analytics ツールで継続的に統合および開発する](stream-analytics-tools-for-visual-studio-cicd.md)
