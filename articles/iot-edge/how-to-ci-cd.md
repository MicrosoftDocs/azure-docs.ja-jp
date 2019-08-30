---
title: 継続的インテグレーションと継続的配置 - Azure IoT Edge | Microsoft Docs
description: 継続的インテグレーションと継続的配置を設定する - Azure IoT Edge、Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e14025a5a7a3e81404498638d6f6f9c5ff18ed58
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650731"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge に対する継続的インテグレーションと継続的配置

Azure Pipelines 内の組み込み Azure IoT Edge タスクを使用して、Azure IoT Edge アプリケーションで DevOps を簡単に導入できます。 この記事では、Azure Pipelines の継続的インテグレーションと継続的配置の機能を使用して、アプリケーションを迅速かつ効率的に Azure IoT Edge にビルド、テスト、配置する方法について説明します。 

![図 - 開発と運用の CI ブランチと CD ブランチ](./media/how-to-ci-cd/cd.png)

この記事では、Azure Pipelines の組み込みの Azure IoT Edge タスクを使用して、IoT Edge ソリューション用の 2 つのパイプラインを作成する方法について説明します。 Azure IoT Edge タスクでは、次の 4 つのアクションを使用できます。
   - **[Azure IoT Edge] - [Build module images]\(モジュール イメージのビルド\)** は、IoT Edge ソリューション コードを取得し、コンテナー イメージをビルドします。
   - **[Azure IoT Edge] - [Push module images]\(モジュール イメージのプッシュ\)** は、指定したコンテナー レジストリにモジュール イメージをプッシュします。
   - **[Azure IoT Edge] - [Generate deployment manifest]\(配置マニフェストの生成\)** は、deployment.template.json ファイルと変数を受け取り、最終的な IoT Edge 配置マニフェスト ファイルを生成します。
   - **[Azure IoT Edge] - [Deploy to IoT Edge devices]\(IoT Edge デバイスへの配置\)** は、単一または複数の IoT Edge デバイスへの IoT Edge デプロイの作成をサポートします。

## <a name="prerequisites"></a>前提条件

* Azure Repos リポジトリ。 ない場合は、[プロジェクト内に新しい Git リポジトリを作成する](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)ことができます。
* リポジトリにコミットおよびプッシュされた IoT Edge ソリューション。 この記事をテストするための新しいサンプル ソリューションを作成する場合は、[Visual Studio Code でのモジュールの開発とデバッグ](how-to-vs-code-develop-module.md)および[Visual Studio での C# モジュールの開発とデバッグ](how-to-visual-studio-develop-csharp-module.md)に関するページの手順に従ってください。
   * この記事では、必要なのは、Visual Studio Code または Visual Studio のいずれかにある IoT Edge テンプレートで作成されたソリューション フォルダーだけです。 続ける前にこのコードをビルド、プッシュ、デプロイ、またはデバッグする必要はありません。 これらのプロセスは Azure Pipelines で設定します。 
   * 新しいソリューションを作成する場合は、最初にリポジトリをローカルで複製します。 その後で、ソリューションを作成するときに、リポジトリ フォルダー内に直接作成することができます。 そこから新しいファイルを簡単にコミットおよびプッシュできます。 
* モジュール イメージをプッシュできるコンテナー レジストリ。 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) またはサード パーティ製のレジストリを使用することができます。 
* 少なくともテストと運用環境のデプロイの個々のステージをテストするための IoT Edge デバイスのある、アクティブな [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 クイック スタートの記事に従って、[Linux](quickstart-linux.md) または [Windows](quickstart.md) 上に IoT Edge デバイスを作成することができます


Azure Repos の詳細については、[Visual Studio と Azure Repos でのコードの共有](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)に関するページを参照してください

## <a name="configure-continuous-integration"></a>継続的インテグレーションを構成する
このセクションでは、新しいビルド パイプラインを作成します。 サンプルの IoT Edge ソリューションへの変更をチェックインするときに自動的に実行し、ビルド ログを発行するように、パイプラインを構成します。

>[!NOTE]
>この記事では、Azure DevOps ビジュアル デザイナーを使用します。 このセクションの手順を実行する前に、YAML パイプライン作成の新しいエクスペリエンスのプレビュー機能をオフにしてください。 
>1. Azure DevOps で、プロファイル アイコンを選択し、 **[プレビュー機能]** を選択します。
>2. **[YAML パイプライン作成の新しいエクスペリエンス]** をオフにします。 
>
>詳細については、「[ビルド パイプラインを作成する](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline)」を参照してください。

1. ご自身の Azure DevOps 組織 (**https:\//dev.azure.com/{your organization}/** ) にサインインして、IoT Edge ソリューションのリポジトリが含まれているプロジェクトを開きます。

   この記事では、**IoTEdgeRepo** というリポジトリを作成しました。 そのリポジトリには、**filtermodule** という名前のモジュール用のコードがある **IoTEdgeSolution** が含まれています。 

   ![DevOps プロジェクトを開く](./media/how-to-ci-cd/init-project.png)

2. プロジェクト内の Azure Pipelines に移動します。 **[ビルド]** タブを開いて、 **[新しいパイプライン]** を選択します。 または、既にビルド パイプラインがある場合は、 **[新規]** ボタンを選択します。 次に、 **[新しいビルド パイプライン]** を選択します。

    ![新しいビルド パイプラインを作成する](./media/how-to-ci-cd/add-new-build.png)

3. プロンプトに従ってパイプラインを作成します。 

   1. 新しいビルド パイプラインのソース情報を入力します。 ソースとして **[Azure Repos Git]** を選択し、IoT Edge ソリューションのコードが配置されているプロジェクト、リポジトリ、ブランチを選択します。 その後、 **[続行]** を選択します。 

      ![パイプライン ソースの選択](./media/how-to-ci-cd/pipeline-source.png)

   2. テンプレートではなく、 **[空のジョブ]** を選択します。 

      ![空のプロセスを開始する](./media/how-to-ci-cd/start-with-empty.png)

4. パイプラインが作成されると、パイプライン エディターが表示されます。 パイプラインの説明で、ターゲット プラットフォームに基づいて適切なエージェント プールを選択します。 
    
   * Linux コンテナー用にプラットフォーム amd64 でモジュールをビルドする場合、 **[Hosted Ubuntu 1604]** を選択します。

   * Windows 1809 コンテナー用にプラットフォーム amd64 でモジュールをビルドする場合は、[Windows 上にセルフホステッド エージェントを設定](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)する必要があります。

   * Linux コンテナー用にプラットフォーム arm32v7 または arm64 でモジュールをビルドする場合は、[Linux 上にセルフホステッド エージェントを設定](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)する必要があります。
    
     ![ビルド エージェント プールの構成](./media/how-to-ci-cd/configure-env.png)

5. パイプラインは、**エージェント ジョブ 1** というジョブで事前に構成されています。 プラス記号 ( **+** ) を選択して、ジョブに 3 つのタスクを追加します ( **[Azure IoT Edge]** 2 回、 **[Copy Files]\(ファイルのコピー\)** 1 回、 **[Publish Build Artifacts]\(ビルド成果物の発行\)** 1 回)。 (各タスクの名前の上にポインターを置くと、 **[追加]** ボタンが表示されます。)

   ![Azure IoT Edge タスクを追加する](./media/how-to-ci-cd/add-iot-edge-task.png)

   4 つのすべてのタスクを追加すると、エージェントのジョブは次の例のようになります。
    
   ![ビルド パイプラインの 3 つのタスク](./media/how-to-ci-cd/add-tasks.png)

6. 最初の **Azure IoT Edge** タスクを選択して編集します。 このタスクでは、指定したターゲット プラットフォームを使用してソリューション内のすべてのモジュールをビルドします。

   * **表示名**:既定値の **[Azure IoT Edge - Build module images]** を受け入れます。
   * **アクション**:既定値の **[Build module images]** を受け入れます。 
   * **.template.json ファイル**:省略記号 ( **...** ) を選択し、IoT Edge ソリューションが含まれているリポジトリ内の **deployment.template.json** ファイルに移動します。 
   * **既定のプラットフォーム**:ターゲットの IoT Edge デバイスに基づいて、モジュールの適切なプラットフォームを選択します。 
   * **出力変数**:出力変数には、deployment.json ファイルが生成されるファイル パスの構成に使用できる参照名が含まれます。 参照名を **edge** のような覚えやすい名前に設定します。 

7. 2 番目の **Azure IoT Edge** タスクを選択して編集します。 このタスクでは、すべてのモジュール イメージが選択したコンテナー レジストリにプッシュされます。

   * **表示名**:表示名は、アクション フィールドが変更されると自動的に更新されます。 
   * **アクション**:ドロップダウン リストを使用して、 **[Push module images]** を選択します。 
   * **コンテナー レジストリの種類**:モジュール イメージを格納するために使用する、コンテナー レジストリの種類を選択します。 選択したレジストリの種類に応じて、フォームが変わります。 **[Azure Container Registry]** を選択した場合は、ドロップダウン リストを使用して、Azure サブスクリプションとコンテナー レジストリの名前を選択します。 **[汎用コンテナー レジストリ]** を選択した場合は、 **[新規]** を選択してレジストリ サービス接続を作成します。 
   * **.template.json ファイル**:省略記号 ( **...** ) を選択し、IoT Edge ソリューションが含まれているリポジトリ内の **deployment.template.json** ファイルに移動します。 
   * **既定のプラットフォーム**:組み込みのモジュール イメージと同じプラットフォームを選択します。

   モジュール イメージをホストするコンテナー レジストリが複数ある場合は、このタスクを複製し、別のコンテナー レジストリを選択し、詳細設定の **[Bypass module(s)]\(モジュールをバイパス\)** を使用して、この特定のレジストリ用ではないイメージをバイパスする必要があります。

8. **[Copy Files]\(ファイルのコピー\)** タスクを選択してそれを編集します。 このタスクは、成果物のステージング ディレクトリにファイルをコピーする場合に使用します。

   * **表示名**:Copy Files to:Drop folder
   * **内容**:このセクションには、`deployment.template.json` と `**/module.json` の 2 つの行を配置します。 この 2 種類のファイルは、IoT Edge 配置マニフェストを生成するための入力です。 ビルド成果物のステージング フォルダーにコピーし、リリース パイプライン用に発行する必要があります。
   * **[Target Folder]\(ターゲット フォルダー\)** :変数 `$(Build.ArtifactStagingDirectory)` を指定します。 内容については、「[ビルド変数](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)」を参照してください。

9. **[Publish Build Artifacts]** タスクを選択して編集します。 タスクにビルド成果物のステージング ディレクトリ パスを指定し、そのパスがリリース パイプラインに発行できるようにします。
   
   * **表示名**:Publish Artifact: drop
   * **[Path to publish]\(発行するためのパス\)** :変数 `$(Build.ArtifactStagingDirectory)` を指定します。 内容については、「[ビルド変数](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)」を参照してください。
   * **[成果物名]** : drop
   * **[Artifact publish location]\(成果物の発行場所\)** :Azure Pipelines


10. **[トリガー]** タブを開いて、 **[継続的インテグレーションを有効にする]** チェック ボックスをオンにします。 ご自身のコードを含むブランチを含まれていることを確認します。

    ![継続的インテグレーション トリガーを有効にする](./media/how-to-ci-cd/configure-trigger.png)

11. **[保存]** ボタンを使用して、新しいビルド パイプラインを保存します。

これでこのパイプラインは、新しいコードをリポジトリにプッシュすると自動的に実行されるように構成されました。 最後のタスクであるパイプライン成果物の発行では、リリース パイプラインをトリガーします。 次のセクションに進んで、リリース パイプラインをビルドします。 

## <a name="configure-continuous-deployment"></a>継続的なデプロイを構成する
このセクションでは、ビルド パイプラインで成果物を削除すると自動的に実行されるように構成されたリリース パイプラインを作成します。その後、Azure Pipelines にデプロイ ログが表示されます。

新しいパイプラインを作成し、新しいステージを追加します。 

1. **[リリース]** タブで、 **[+ 新しいパイプライン]** を選択します。 または、既にリリース パイプラインがある場合は、 **[+ 新規]** ボタンを選択し、 **[+ 新しいリリース パイプライン]** を選択します。  

    ![リリース パイプラインの追加](./media/how-to-ci-cd/add-release-pipeline.png)

2. テンプレートを選択するよう求められたら、 **[空のジョブ]** を選択します。

    ![空のジョブを開始する](./media/how-to-ci-cd/start-with-empty-job.png)

3. **ステージ 1** という 1 つのステージを持つ新しいリリース パイプラインが初期化されます。 ステージ 1 の名前を「**dev**」に変更し、それをテスト環境として扱います。 通常、継続的配置パイプラインには、**dev**、**staging**、**prod** の複数のステージがあります。ご使用の DevOps 手法に基づいてさらに作成することができます。 名前を変更した後、ステージの詳細ウィンドウを閉じます。 

4. ビルド パイプラインによって発行されているビルド成果物に、リリースをリンクします。 成果物領域で **[追加]** をクリックします。

   ![成果物の追加](./media/how-to-ci-cd/add-artifacts.png)  
    
5. **[成果物ページの追加]** で、ソースの種類として **[ビルド]** を選択します。 次に、作成したプロジェクトとビルド パイプラインを選択します。 その後、 **[追加]** を選択します。

   ![ビルド成果物を追加する](./media/how-to-ci-cd/add-an-artifact.png)

6. 成果物トリガーを開き、切り替えを選択して継続的デプロイ トリガーを有効にします。 これで、新しいビルドが利用可能になるたびに新しいリリースが作成されるようになります。

   ![継続的配置トリガーを構成する](./media/how-to-ci-cd/add-a-trigger.png)

7. **dev** ステージは、1 つのジョブ、0 個のタスクで、事前に構成されています。 パイプライン メニューから **[タスク]** を選択し、 **[dev]** ステージを選択します。  このステージのタスクを構成するジョブとタスクの数を選択します。

    ![dev タスクを構成する](./media/how-to-ci-cd/view-stage-tasks.png)

8. **dev** ステージでは、既定の**エージェント ジョブ**が表示されます。 エージェント ジョブに関する詳細を構成することはできますが、デプロイ タスクはプラットフォームに左右されないため、 **[エージェント プール]** では **[Hosted VS2017]** と **[Hosted Ubuntu 1604]** のいずれか (または自分で管理している他のエージェント) を使用できます。 

9. プラス記号 ( **+** ) を選択して、2 つのタスクを追加します。 **[Azure IoT Edge]** を検索して 2 回追加します。

    ![dev 用にタスクを追加する](./media/how-to-ci-cd/add-task-qa.png)

10. 最初の **Azure IoT Edge** タスクを選択し、次の値を使用して構成します。

    * **表示名**:表示名は、アクション フィールドが変更されると自動的に更新されます。 
    * **アクション**:ドロップダウン リストを使用して、 **[Generate deployment manifest]\(配置マニフェストの生成\)** を選択します。 アクションの値を変更すると、タスクの表示名も一致するように更新されます。
    * **.template.json ファイル**:パス `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` を設定します。 このパスはビルド パイプラインから発行されます。
    * **既定のプラットフォーム**:モジュール イメージを作成するときに、同じ値を選択します。
    * **出力パス**:パス `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` を設定します。 このパスは、最終的な IoT Edge 配置マニフェスト ファイルです。

    これらの構成によって、`deployment.template.json` ファイル内のモジュール イメージ URL を置き換えることができます。 また、 **[Generate deployment manifest]\(配置マニフェストの生成\)** は、`deployment.template.json` ファイルで定義したのと同じ値に変数を置き換えるのにも役立ちます。 VS/VS Code では、`.env` ファイルに実際の値を指定します。 Azure Pipelines では、[Release Pipeline Variables]\(リリース パイプライン変数\) タブで値を設定します。[変数] タブに移動し、次のように名前と値を構成します。

    * **ACR_ADDRESS**:Azure Container Registry SLA のアドレス。 
    * **ACR_PASSWORD**:Azure Container Registry のパスワード。
    * **ACR_USER**:Azure Container Registry のユーザー名。

    プロジェクトに他の変数がある場合は、このタブでその名前と値を指定できます。 **[Generate deployment manifest]\(配置マニフェストの生成\)** で認識できるのは `${VARIABLE}` フレーバー内の変数だけです。`*.template.json` ファイルでこれを使用していることを確認してください。

    ![リリース パイプラインの変数を構成する](./media/how-to-ci-cd/configure-variables.png)

10. 2 つ目の **[Azure IoT Edge]** タスクを選択し、次の値を使用して構成します。

    * **表示名**:表示名は、アクション フィールドが変更されると自動的に更新されます。 
    * **アクション**:ドロップダウン リストを使用して、 **[Deploy to IoT Edge devices]\(IoT Edge デバイスへの配置\)** を選択します。 アクションの値を変更すると、タスクの表示名も一致するように更新されます。
    * **Azure サブスクリプション**:IoT Hub が含まれているサブスクリプションを選択します。
    * **IoT Hub 名**:IoT ハブを選択します。 
    * **1 つまたは複数のデバイスを選択**:リソース パイプラインを 1 つのデバイスまたは複数のデバイスのどちらにデプロイするかを選択します。 
      * 1 つのデバイスにデプロイする場合は、**IoT Edge デバイス ID** を入力します。 
      * 複数のデバイスにデプロイする場合は、デバイスの**ターゲット条件**を指定します。 ターゲット条件とは、IoT Hub 内の一連の IoT Edge デバイスと突き合わせるフィルターです。 デバイス タグを条件として使用する場合は、対応するデバイス タグを IoT Hub デバイス ツインに合わせて更新する必要があります。 詳細設定で、**IoT Edge デプロイ ID** と **IoT Edge デプロイの優先順位**を更新します。 複数のデバイスのデプロイを作成する方法の詳細については、[IoT Edge 自動デプロイについての理解](module-deployment-monitoring.md)に関するページを参照してください。
    * [詳細設定] を展開し、 **[IoT Edge deployment ID]\(IoT Edge 配置 ID\)** を選択して、変数 `$(System.TeamProject)-$(Release.EnvironmentName)` を入力します。 これにより、プロジェクトとリリース名がその IoT Edge 配置 ID を使用してマッピングされます。

11. **[保存]** を選択して、新しいリリース パイプラインの変更内容を保存します。 メニューで **[パイプライン]** を選択して、パイプライン ビューに戻ります。 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>ビルド パイプラインとリリース パイプラインの IoT Edge CI/CD を検証する

ビルド ジョブをトリガーするには、コミットをソース コード リポジトリにプッシュするか、手動でトリガーできます。 このセクションでは、CI/CD パイプラインを手動でトリガーして、その動作をテストします。 その後、デプロイが成功したことを確認します。

1. この記事の冒頭で作成したビルド パイプラインに移動します。 

2. 次のスクリーンショットに示されているように、 **[キュー]** ボタンを選択することで、ビルド パイプラインのビルド ジョブをトリガーできます。

    ![手動トリガー](./media/how-to-ci-cd/manual-trigger.png)

3. 進捗状況を確認するビルド ジョブを選択します。 ビルド パイプラインが正常に完了すると、**dev** ステージへのリリースがトリガーされます。 

    ![ビルド ログ](./media/how-to-ci-cd/build-logs.png)

4. **dev** へのリリースが成功すると、IoT Edge デバイスを対象とする IoT Edge デプロイが作成されます。

    ![dev へのリリース](./media/how-to-ci-cd/pending-approval.png)

5. リリース ログを確認するには、 **[dev]** ステージをクリックします。

    ![リリース ログ](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>次の手順
* [IoT Edge 用のAzure DevOps プロジェクト](how-to-devops-project.md)内の IoT Edge DevOps のベスト プラクティスのサンプル
* 「[1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて](module-deployment-monitoring.md)」で IoT Edge デプロイについて理解します。
* 「[Deploy and monitor IoT Edge modules at scale (IoT Edge モジュールを大規模にデプロイして監視する)](how-to-deploy-monitor.md)」で、デプロイを作成、更新、または削除するための手順を学習してください。
