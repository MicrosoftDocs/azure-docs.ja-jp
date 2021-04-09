---
title: Azure IoT Edge デバイスに対する継続的インテグレーションと継続的配置 - Azure IoT Edge
description: YAML を使用して継続的インテグレーションと継続的配置を設定する - Azure IoT Edge、Azure DevOps、Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9db9997af20fee226214eb12ad32729cab55caa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199248"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Azure IoT Edge デバイスに対する継続的インテグレーションと継続的配置

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Pipelines 内の組み込み Azure IoT Edge タスクを使用して、Azure IoT Edge アプリケーションで DevOps を簡単に導入できます。 この記事では、Azure Pipelines の継続的インテグレーションと継続的配置の機能を使用し、YAML を使用してアプリケーションを迅速かつ効率的に Azure IoT Edge にビルド、テスト、および配置する方法について説明します。 別の方法として、[クラシック エディターを使用](how-to-continuous-integration-continuous-deployment-classic.md)することもできます。

![図 - 開発と運用の CI ブランチと CD ブランチ](./media/how-to-continuous-integration-continuous-deployment/model.png)

この記事では、Azure Pipelines の組み込みの [Azure IoT Edge タスク](/azure/devops/pipelines/tasks/build/azure-iot-edge)を使用して、IoT Edge ソリューション用のビルド パイプラインおよびリリース パイプラインを作成する方法について説明します。 パイプラインに追加される各 Azure IoT Edge タスクは、次の 4 つのアクションのいずれかを実装します。

 | アクション | 説明 |
 | --- | --- |
 | モジュール イメージをビルドする | IoT Edge ソリューション コードを受け取り、コンテナー イメージをビルドします。|
 | モジュール イメージをプッシュする | 指定したコンテナー レジストリにモジュール イメージをプッシュします。 |
 | 配置マニフェストを生成する | deployment.template.json ファイルと変数を受け取り、最終的な IoT Edge 配置マニフェスト ファイルを生成します。 |
 | IoT Edge デバイスにデプロイする | 1 つ以上の IoT Edge デバイスに IoT Edge 展開を作成します。 |

特に指定がない限り、この記事の手順では、タスク パラメーターで使用できるすべての機能については説明しません。 詳細については、「

* [タスクのバージョン](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **詳細** - 該当する場合は、ビルドしないモジュールを指定します。
* [管理オプション](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [環境変数](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [出力変数](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>前提条件

* Azure Repos リポジトリ。 ない場合は、[プロジェクト内に新しい Git リポジトリを作成する](/azure/devops/repos/git/create-new-repo)ことができます。 この記事では、**IoTEdgeRepo** というリポジトリを作成しました。
* リポジトリにコミットおよびプッシュされた IoT Edge ソリューション。 この記事をテストするための新しいサンプル ソリューションを作成する場合は、[Visual Studio Code でのモジュールの開発とデバッグ](how-to-vs-code-develop-module.md)および[Visual Studio での C# モジュールの開発とデバッグ](./how-to-visual-studio-develop-module.md)に関するページの手順に従ってください。 この記事では、**IoTEdgeSolution** という名前のソリューションをリポジトリに作成しました。これには **filtermodule** という名前のモジュールのコードが含まれています。

   この記事では、必要なのは、Visual Studio Code または Visual Studio のいずれかにある IoT Edge テンプレートで作成されたソリューション フォルダーだけです。 続ける前にこのコードをビルド、プッシュ、デプロイ、またはデバッグする必要はありません。 これらのプロセスは Azure Pipelines で設定します。

   新しいソリューションを作成する場合は、最初にリポジトリをローカルで複製します。 その後で、ソリューションを作成するときに、リポジトリ フォルダー内に直接作成することができます。 そこから新しいファイルを簡単にコミットおよびプッシュできます。

* モジュール イメージをプッシュできるコンテナー レジストリ。 [Azure Container Registry](../container-registry/index.yml) またはサード パーティ製のレジストリを使用することができます。
* テストと運用環境のデプロイの個々のステージをテストするための少なくとも 2 つの IoT Edge デバイスのある、アクティブな Azure [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 クイック スタートの記事に従って、[Linux](quickstart-linux.md) または [Windows](quickstart.md) 上に IoT Edge デバイスを作成することができます

Azure Repos の詳細については、[Visual Studio と Azure Repos でのコードの共有](/azure/devops/repos/git/share-your-code-in-git-vs)に関するページを参照してください

## <a name="create-a-build-pipeline-for-continuous-integration"></a>継続的インテグレーションのためのビルド パイプラインを作成する

このセクションでは、新しいビルド パイプラインを作成します。 サンプルの IoT Edge ソリューションへの変更をチェックインするときに自動的に実行し、ビルド ログを発行するように、パイプラインを構成します。

1. ご自身の Azure DevOps 組織 (`https://dev.azure.com/{your organization}`) にサインインして、IoT Edge ソリューションのリポジトリが含まれているプロジェクトを開きます。

   ![DevOps プロジェクトを開く](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. プロジェクトの左側ペインのメニューで、 **[パイプライン]** を選択します。 ページの中央にある **[パイプラインの作成]** を選択します。 または、既にビルド パイプラインがある場合は、右上にある **[新しいパイプライン]** ボタンを選択します。

    ![[新しいパイプライン] ボタンを使用して新しいビルド パイプラインを作成する](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. **[コードはどこにありますか?]** ページで、 **[Azure Repos Git `YAML`]** を選択します。 クラシック エディターを使用してプロジェクトのビルド パイプラインを作成する場合は、[クラシック エディターのガイド](how-to-continuous-integration-continuous-deployment-classic.md)を参照してください。

4. パイプラインを作成するリポジトリを選択します。

    ![ビルド パイプラインのリポジトリを選択する](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. **[パイプラインの構成]** ページで、 **[スタート パイプライン]** を選択します。 このパイプラインの作成に使用する Azure Pipelines の YAML ファイルが既に存在する場合は、 **[既存の Azure Pipelines の YAML ファイル]** を選択して、リポジトリ内のファイルへのブランチとパスを指定することができます。

    ![[スタート パイプライン] または [既存の Azure Pipelines の YAML ファイル] を選択して、ビルド パイプラインの構成を開始する](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. **[パイプラインの YAML の確認]** ページで、既定の名前 `azure-pipelines.yml` をクリックすると、パイプラインの構成ファイルの名前を変更できます。

   **[アシスタントの表示]** を選択して、 **[タスク]** パレットを開きます。

    ![[アシスタントの表示] を選択して、[タスク] パレットを開く](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. タスクを追加するには、YAML の末尾にカーソルを合わせるか、タスクの指示を追加する場所にカーソルを合わせます。 **[Azure IoT Edge]** を検索して選択します。 次のように、タスクのパラメーターを入力します。 その後、 **[追加]** を選択します。

   | パラメーター | 説明 |
   | --- | --- |
   | アクション | **[モジュール イメージをビルドする]** を選択します。 |
   | .template.json ファイル | IoT Edge ソリューションが含まれているリポジトリ内の **deployment.template.json** ファイルへのパスを指定します。 |
   | 既定のプラットフォーム | ターゲットの IoT Edge デバイスに基づいて、モジュールの適切なオペレーティング システムを選択します。 |

    ![タスク パレットを使用して、パイプラインにタスクを追加する](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > 各タスクを追加すると、追加された行がエディターによって自動的に強調表示されます。 誤って上書きされないようにするには、さらにタスクを追加する前に、行の選択を解除して、次のタスクを新たに追加できる状態にします。

8. 次のパラメーターを使用してこの手順を繰り返し、さらに 3 つのタスクを追加します。

   * タスク:**Azure IoT Edge**

       | パラメーター | 説明 |
       | --- | --- |
       | アクション | **[モジュール イメージをプッシュする]** を選択します。 |
       | コンテナー レジストリの種類 | 既定の種類を使用します:**Azure Container Registry**。 |
       | Azure サブスクリプション | サブスクリプションを選択します。 |
       | Azure Container Registry | パイプラインに使用するレジストリを選択します。 |
       | .template.json ファイル | IoT Edge ソリューションが含まれているリポジトリ内の **deployment.template.json** ファイルへのパスを指定します。 |
       | 既定のプラットフォーム | ターゲットの IoT Edge デバイスに基づいて、モジュールの適切なオペレーティング システムを選択します。 |

   * タスク:**ファイルをコピーする**

       | パラメーター | 説明 |
       | --- | --- |
       | [同期元フォルダー] | コピー元のソース フォルダー。 空にすると、リポジトリのルートとなります。 ファイルがリポジトリに存在しない場合は、変数を使用します。 例: `$(agent.builddirectory)`.
       | 内容 | `deployment.template.json` と `**/module.json` の 2 つの行を追加します。 |
       | ターゲット フォルダー | 変数 `$(Build.ArtifactStagingDirectory)` を指定します。 内容については、「[ビルド変数](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables)」を参照してください。 |

   * タスク:**ビルド成果物の公開**

       | パラメーター | 説明 |
       | --- | --- |
       | 公開するためのパス | 変数 `$(Build.ArtifactStagingDirectory)` を指定します。 内容については、「[ビルド変数](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables)」を参照してください。 |
       | アーティファクト名 | 既定の名前 (`drop`) を指定します。 |
       | 成果物の公開場所 | 既定の場所 (`Azure Pipelines`) を使用します。 |

9. 右上にある **[保存および実行]** ドロップダウンから **[保存]** を選択します。

10. この YAML パイプラインでは、継続的インテグレーションのトリガーが既定で有効になっています。 これらの設定を編集する場合は、パイプラインを選択し、右上の **[編集]** をクリックします。 右上の **[実行]** ボタンの横にある **[その他の操作]** を選択し、 **[トリガー]** に移動します。 パイプライン名の下の **[継続的インテグレーション]** が有効と表示されます。 トリガーの詳細を確認するには、 **[YAML の継続的インテグレーション トリガーをここからオーバーライドする]** ボックスをオンにします。

    ![パイプラインのトリガー設定を確認するには、[その他の操作] の [トリガー] を参照してください](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

次のセクションに進んで、リリース パイプラインをビルドします。

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>次のステップ

* 「[IoT Edge 用の Azure DevOps Starter](how-to-devops-starter.md)」に関する記事の IoT Edge DevOps のベスト プラクティスのサンプル
* 「[1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて](module-deployment-monitoring.md)」で IoT Edge デプロイについて理解します。
* 「[Deploy and monitor IoT Edge modules at scale (IoT Edge モジュールを大規模にデプロイして監視する)](how-to-deploy-at-scale.md)」で、デプロイを作成、更新、または削除するための手順を学習してください。