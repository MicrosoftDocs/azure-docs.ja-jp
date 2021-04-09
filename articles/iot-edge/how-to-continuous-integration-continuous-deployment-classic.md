---
title: Azure IoT Edge デバイスに対する継続的インテグレーションと継続的配置 (クラシック エディター) - Azure IoT Edge
description: クラシック エディターを使用して継続的インテグレーションと継続的配置を設定する - Azure IoT Edge、Azure DevOps、Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 218c0f345e4ea453a2300b3de85ac8856a09c6ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199287"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Azure IoT Edge デバイスに対する継続的インテグレーションと継続的配置 (クラシック エディター)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Pipelines 内の組み込み Azure IoT Edge タスクを使用して、Azure IoT Edge アプリケーションで DevOps を簡単に導入できます。 この記事では、Azure Pipelines の継続的インテグレーションと継続的配置の機能を使用し、クラシック エディターを使用してアプリケーションを迅速かつ効率的に Azure IoT Edge にビルド、テスト、および配置する方法について説明します。 代わりに、[YAML を使用](how-to-continuous-integration-continuous-deployment.md)することもできます。

![図 - 開発と運用の CI ブランチと CD ブランチ](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

この記事では、Azure Pipelines の組み込みの [Azure IoT Edge タスク](/azure/devops/pipelines/tasks/build/azure-iot-edge)を使用して、IoT Edge ソリューション用のビルド パイプラインおよびリリース パイプラインを作成する方法について説明します。 パイプラインに追加される各 Azure IoT Edge タスクは、次の 4 つのアクションのいずれかを実装します。

 | アクション | 説明 |
 | --- | --- |
 | モジュール イメージをビルドする | IoT Edge ソリューション コードを受け取り、コンテナー イメージをビルドします。|
 | モジュール イメージをプッシュする | 指定したコンテナー レジストリにモジュール イメージをプッシュします。 |
 | 配置マニフェストを生成する | deployment.template.json ファイルと変数を受け取り、最終的な IoT Edge 配置マニフェスト ファイルを生成します。 |
 | IoT Edge デバイスにデプロイする | 1 つ以上の IoT Edge デバイスに IoT Edge 展開を作成します。 |

特に指定がない限り、この記事の手順では、タスク パラメーターで使用できるすべての機能については説明しません。 詳細については、「

* [タスクのバージョン](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **詳細** - 該当する場合は、ビルドしないモジュールを指定します。
* [管理オプション](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [環境変数](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [出力変数](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>前提条件

* Azure Repos リポジトリ。 ない場合は、[プロジェクト内に新しい Git リポジトリを作成する](/azure/devops/repos/git/create-new-repo)ことができます。 この記事では、**IoTEdgeRepo** というリポジトリを作成しました。
* リポジトリにコミットおよびプッシュされた IoT Edge ソリューション。 この記事をテストするための新しいサンプル ソリューションを作成する場合は、[Visual Studio Code でのモジュールの開発とデバッグ](how-to-vs-code-develop-module.md)および[Visual Studio での C# モジュールの開発とデバッグ](./how-to-visual-studio-develop-module.md)に関するページの手順に従ってください。 この記事では、**IoTEdgeSolution** という名前のソリューションをリポジトリに作成しました。これには **filtermodule** という名前のモジュールのコードが含まれています。

   この記事では、必要なのは、Visual Studio Code または Visual Studio のいずれかにある IoT Edge テンプレートで作成されたソリューション フォルダーだけです。 続ける前にこのコードをビルド、プッシュ、デプロイ、またはデバッグする必要はありません。 これらのプロセスは Azure Pipelines で設定します。

   新しいソリューションを作成する場合は、最初にリポジトリをローカルで複製します。 その後で、ソリューションを作成するときに、リポジトリ フォルダー内に直接作成することができます。 そこから新しいファイルを簡単にコミットおよびプッシュできます。

* モジュール イメージをプッシュできるコンテナー レジストリ。 [Azure Container Registry](../container-registry/index.yml) またはサード パーティ製のレジストリを使用することができます。
* テストと運用環境のデプロイの個々のステージをテストするための少なくとも 2 つの IoT Edge デバイスのある、アクティブな Azure [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 クイック スタートの記事に従って、[Linux](quickstart-linux.md) または [Windows](quickstart.md) 上に IoT Edge デバイスを作成することができます

## <a name="create-a-build-pipeline-for-continuous-integration"></a>継続的インテグレーションのためのビルド パイプラインを作成する

このセクションでは、新しいビルド パイプラインを作成します。 サンプルの IoT Edge ソリューションへの変更をチェックインするときに自動的に実行し、ビルド ログを発行するように、パイプラインを構成します。

1. ご自身の Azure DevOps 組織 (`https://dev.azure.com/{your organization}`) にサインインして、IoT Edge ソリューションのリポジトリが含まれているプロジェクトを開きます。

    ![DevOps プロジェクトを開く](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. プロジェクトの左側ペインのメニューで、 **[パイプライン]** を選択します。 ページの中央にある **[パイプラインの作成]** を選択します。 または、既にビルド パイプラインがある場合は、右上にある **[新しいパイプライン]** ボタンを選択します。

    ![新しいビルド パイプラインを作成する](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. **[コードはどこにありますか?]** ページの下部で、 **[クラシック エディターを使用]** を選択します。 YAML を使用してプロジェクトのビルド パイプラインを作成する場合は、[YAML のガイド](how-to-continuous-integration-continuous-deployment.md)を参照してください。

    ![[クラシック エディターを使用] を選択する](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. プロンプトに従ってパイプラインを作成します。

   1. 新しいビルド パイプラインのソース情報を入力します。 ソースとして **[Azure Repos Git]** を選択し、IoT Edge ソリューションのコードが配置されているプロジェクト、リポジトリ、ブランチを選択します。 その後、 **[続行]** を選択します。

      ![パイプライン ソースの選択](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. テンプレートではなく、**[空のジョブ]** を選択します。

      ![ビルド パイプラインの作成を空のジョブから開始する](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. パイプラインが作成されると、パイプライン エディターが表示されます。 ここでは、パイプラインの名前、エージェント プール、およびエージェントの指定を変更できます。

   Microsoft によってホストされているプール、またはユーザーが管理する自己ホスト型プールを選択できます。

   パイプラインの説明で、ターゲット プラットフォームに基づいて適切なエージェントの指定を選択します。

   * Linux コンテナー用にプラットフォーム amd64 でモジュールをビルドする場合、 **[ubuntu-16.04]** を選択します。

   * Windows 1809 コンテナー用にプラットフォーム amd64 でモジュールをビルドする場合は、[Windows 上にセルフホステッド エージェントを設定](/azure/devops/pipelines/agents/v2-windows)する必要があります。

   * Linux コンテナー用にプラットフォーム arm32v7 または arm64 でモジュールをビルドする場合は、[Linux 上にセルフホステッド エージェントを設定](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)する必要があります。

    ![ビルド エージェントの指定の構成](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. パイプラインは、**エージェント ジョブ 1** というジョブで事前に構成されています。 プラス記号 ( **+** ) を選択して、ジョブに 4 つのタスクを追加します ( **[Azure IoT Edge]** 2 回、 **[ファイルのコピー]** 1 回、 **[ビルド成果物の発行]** 1 回)。 各タスクを検索し、タスクの名前の上にマウス ポインターを移動すると、 **[追加]** ボタンが表示されます。

   ![Azure IoT Edge タスクを追加する](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   4 つのすべてのタスクを追加すると、エージェントのジョブは次の例のようになります。

   ![ビルド パイプラインの 4 つのタスク](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. 最初の **Azure IoT Edge** タスクを選択して編集します。 このタスクでは、指定したターゲット プラットフォームを使用してソリューション内のすべてのモジュールをビルドします。 次の値を使用してタスクを編集します。

    | パラメーター | 説明 |
    | --- | --- |
    | 表示名 | 表示名は、アクション フィールドが変更されると自動的に更新されます。 |
    | アクション | **[モジュール イメージをビルドする]** を選択します。 |
    | .template.json ファイル | 省略記号 (**...**) を選択し、IoT Edge ソリューションが含まれているリポジトリ内の **deployment.template.json** ファイルに移動します。 |
    | 既定のプラットフォーム | ターゲットの IoT Edge デバイスに基づいて、モジュールの適切なオペレーティング システムを選択します。 |
    | 出力変数 | deployment.json ファイルによって生成されるファイル パスに関連付ける参照名を指定します (**edge** など)。 |

   これらの構成では、`module.json` ファイルで定義されているイメージ リポジトリとタグを使用して、モジュール イメージに名前とタグを付けます。 また、 **[Build module images]\(モジュール イメージのビルド\)** は、`module.json` ファイルで定義するのと同じ値に変数を置き換えるのにも役立ちます。 Visual Studio または Visual Studio Code では、`.env` ファイルに実際の値を指定します。 Azure Pipelines では、 **[パイプライン変数]** タブで値を設定します。パイプライン エディターのメニューの **[変数]** タブを選択し、次のように名前と値を構成します。

    * **ACR_ADDRESS**:Azure Container Registry の **ログイン サーバー** 値。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。

    プロジェクトに他の変数がある場合は、このタブでその名前と値を指定できます。 **[Build module images]\(モジュール イメージのビルド\)** では、`${VARIABLE}` 形式の変数のみが認識されます。 `**/module.json` ファイルでこの形式を使用していることを確認してください。

8. 2 番目の **Azure IoT Edge** タスクを選択して編集します。 このタスクでは、すべてのモジュール イメージが選択したコンテナー レジストリにプッシュされます。

    | パラメーター | 説明 |
    | --- | --- |
    | 表示名 | 表示名は、アクション フィールドが変更されると自動的に更新されます。 |
    | アクション | **[モジュール イメージをプッシュする]** を選択します。 |
    | コンテナー レジストリの種類 | 既定の種類 (`Azure Container Registry`) を使用します。 |
    | Azure サブスクリプション | サブスクリプションを選択します。 |
    | Azure Container Registry | モジュール イメージを格納するために使用する、コンテナー レジストリの種類を選択します。 選択したレジストリの種類に応じて、フォームが変わります。 **[Azure Container Registry]** を選択した場合は、ドロップダウン リストを使用して、Azure サブスクリプションとコンテナー レジストリの名前を選択します。 **[汎用コンテナー レジストリ]** を選択した場合は、**[新規]** を選択してレジストリ サービス接続を作成します。 |
    | .template.json ファイル | 省略記号 (**...**) を選択し、IoT Edge ソリューションが含まれているリポジトリ内の **deployment.template.json** ファイルに移動します。 |
    | 既定のプラットフォーム | ターゲットの IoT Edge デバイスに基づいて、モジュールの適切なオペレーティング システムを選択します。 |
    | レジストリ資格情報を配置マニフェストに追加する | 配置マニフェストに Docker イメージをプッシュするためのレジストリ資格情報を追加するには、true を指定します。 |

   モジュール イメージをホストするコンテナー レジストリが複数ある場合は、このタスクを複製し、別のコンテナー レジストリを選択し、 **[詳細]** 設定の **[モジュールをバイパス]** を使用して、この特定のレジストリ用ではないイメージをバイパスする必要があります。

9. **[Copy Files]\(ファイルのコピー\)** タスクを選択してそれを編集します。 このタスクは、成果物のステージング ディレクトリにファイルをコピーする場合に使用します。

    | パラメーター | 説明 |
    | --- | --- |
    | 表示名 | 既定の名前を使用するか、カスタマイズします |
    | ソース フォルダー | コピーするファイルが含まれているフォルダー。 |
    | 内容 | `deployment.template.json` と `**/module.json` の 2 つの行を追加します。 これらの 2 つのファイルは、IoT Edge 配置マニフェストを生成するための入力となります。 |
    | ターゲット フォルダー | 変数 `$(Build.ArtifactStagingDirectory)` を指定します。 内容については、「[ビルド変数](/azure/devops/pipelines/build/variables#build-variables)」を参照してください。 |

10. **[Publish Build Artifacts]** タスクを選択して編集します。 タスクにビルド成果物のステージング ディレクトリ パスを指定し、そのパスがリリース パイプラインに発行できるようにします。

    | パラメーター | 説明 |
    | --- | --- |
    | 表示名 | 既定の名前を使用するか、カスタマイズします。 |
    | 公開するためのパス | 変数 `$(Build.ArtifactStagingDirectory)` を指定します。 詳細については、「[ビルド変数](/azure/devops/pipelines/build/variables#build-variables)」を参照してください。 |
    | アーティファクト名 | 既定の名前 (**drop**) を使用します。 |
    | 成果物の公開場所 | 既定の場所を使用します:**Azure Pipelines** |

11. **[トリガー]** タブを開いて、**[継続的インテグレーションを有効にする]** チェック ボックスをオンにします。 ご自身のコードを含むブランチを含まれていることを確認します。

    ![継続的インテグレーション トリガーを有効にする](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. **[保存してキューに登録]** ドロップダウンから **[保存]** を選択します。

これでこのパイプラインは、新しいコードをリポジトリにプッシュすると自動的に実行されるように構成されました。 最後のタスクであるパイプライン成果物の発行では、リリース パイプラインをトリガーします。 次のセクションに進んで、リリース パイプラインをビルドします。

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>パイプラインで **階層型配置** を使用する場合、Azure DevOps の Azure IoT Edge タスクでは、階層型配置はまだサポートされていません。
>
>ただし、[Azure DevOps の Azure CLI タスク](/azure/devops/pipelines/tasks/deploy/azure-cli)を使用すると、階層型配置として配置を作成できます。 **インライン スクリプト** 値については、[az iot edge deployment create コマンド](/cli/azure/ext/azure-iot/iot/edge/deployment)を使用できます。
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>次のステップ

* 「[IoT Edge 用の Azure DevOps Starter](how-to-devops-starter.md)」に関する記事の IoT Edge DevOps のベスト プラクティスのサンプル
* 「[1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて](module-deployment-monitoring.md)」で IoT Edge デプロイについて理解します。
* 「[Deploy and monitor IoT Edge modules at scale (IoT Edge モジュールを大規模にデプロイして監視する)](how-to-deploy-at-scale.md)」で、デプロイを作成、更新、または削除するための手順を学習してください。