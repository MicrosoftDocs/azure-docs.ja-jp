---
title: Azure Pipelines のビルドとリリースのパイプライン内で DevTest Labs を使用する
description: Azure Pipelines のビルドとリリースのパイプライン内で Azure DevTest Labs を使用する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d04ed5dd7bebac0c8f24deb9145c3d2e4b77122e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88080336"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Azure Pipelines のビルドとリリースのパイプライン内で DevTest Labs を使用する
この記事では、Azure Pipelines のビルドとリリースのパイプライン内で DevTest Labs を使用する方法について説明します。 

## <a name="overall-flow"></a>全体的なフロー
基本的なフローは、次のタスクを実行する **ビルド パイプライン** を持つことです。

1. アプリケーション コードをビルドします。
1. DevTest Labs で基本環境を作成します。
1. カスタム情報で環境を更新します。
1. DevTest Labs 環境にアプリケーションをデプロイします。
1. コードをテストします。 

ビルドが正常に完了すると、**リリース パイプライン** はビルド成果物を使用してステージングまたは運用環境をデプロイします。 

必要な前提の 1 つは、テスト済みのエコシステムを再作成するために必要なすべての情報が、ビルド成果物 (Azure リソースの構成を含む) 内で利用できることです。 Azure リソースを使用するとコストが発生するため、企業はこれらのリソースの使用を制御または追跡したいと考えています。 場合によっては、リソースの作成と構成に使用される Azure Resource Manager テンプレートは、IT のような別の部門によって管理されることがあります。 また、これらのテンプレートが別のリポジトリに格納されている場合があります。 これは、ビルドを作成してテストし、運用環境でシステムを適切に再作成するには、コードと構成の両方をビルド成果物内に保存する必要があるという興味深い状況につながります。 

ビルド/テスト フェーズ中に DevTest Labs を使用すると、Azure Resource Manager テンプレートとサポート ファイルをビルド ソースに追加できます。これにより、リリース フェーズ中に、テストに使用される正確な構成が運用環境にデプロイされます。 適切な構成を使用した **Azure DevTest Labs 環境の作成** タスクでは、ビルド成果物内に Resource Manager テンプレートが保存されます。 この例では、「[チュートリアル: Azure App Service での .NET Core および SQL Database の Web アプリの作成](../app-service/tutorial-dotnetcore-sqldb-app.md)」のコードを使用して、Azure で Web アプリをデプロイしてテストします。

![全体的なフロー](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Azure リソースの設定
事前に作成する必要がある項目がいくつかあります。

- 2 つのリポジトリ。 最初の 1 つには、チュートリアルからのコードと、2 つの VM が追加された Resource Manager テンプレートが含まれます。 2 つ目には、Azure Resource Manager の基本テンプレート (既存の構成) が含まれます。
- 運用環境のコードと構成をデプロイするためのリソース グループ。
- ラボは、ビルド パイプラインの[構成リポジトリへの接続](devtest-lab-create-environment-from-arm.md)を使用して設定する必要があります。 DevTest Labs がテンプレートを認識してデプロイできるようにするには、Resource Manager テンプレートを metadata.json とともに azuredeploy.json として構成リポジトリにチェックインさせる必要があります。

ビルド パイプラインにより、DevTest Labs 環境が作成され、テスト用のコードがデプロイされます。

## <a name="set-up-a-build-pipeline"></a>ビルド パイプラインの設定
Azure Pipelines で、「[チュートリアル: Azure App Service での .NET Core および SQL Database の Web アプリの作成](../app-service/tutorial-dotnetcore-sqldb-app.md)」のコードを使用して、ビルド パイプラインを作成します。 **ASP.NET Core** テンプレートを使用します。これにより、コードをビルド、テスト、発行するために必要なタスクが設定されます。

![ASP.NET テンプレートの選択](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

DevTest Labs で環境を作成し、環境にデプロイするには、3 つのタスクを追加する必要があります。

![3 つのタスクを含むパイプライン](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>環境の作成タスク
環境の作成タスク (**Azure DevTest Labs 環境の作成** タスク) で、ドロップダウン リストを使用して次の値を選択します。

- Azure サブスクリプション
- ラボの名前
- リポジトリの名前
- テンプレートの名前 (環境が格納されているフォルダーを示します) 

情報を手動で入力するのではなく、ページのドロップダウン リストを使用することをお勧めします。 情報を手動で入力する場合は、完全修飾の Azure リソース ID を入力します。 このタスクでは、リソース ID ではなくフレンドリ名が表示されます。 

環境名は、DevTest Labs 内に表示される表示名です。 ビルドごとに一意の名前にする必要があります。 例: **TestEnv$(Build.BuildId)** 。 

パラメーター ファイルまたはパラメーターのいずれかを指定して、Resource Manager テンプレートに情報を渡すことができます。 

**[Create output variables based on the environment template output]\(環境テンプレートの出力に基づいて出力変数を作成する\)** オプションを選択し、参照名を入力します。 この例では、参照名として「**BaseEnv**」を入力します。 この BaseEnv は、次のタスクを構成するときに使用します。 

![Azure DevTest Labs 環境の作成タスク](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>環境設定タスク
2 番目のタスク (**Azure DevTest Labs 環境の設定** タスク) は、既存の DevTest Labs 環境を更新することです。 環境の作成タスクでは、このタスクの環境名を構成するために使用される **BaseEnv.environmentResourceId** が出力されます。 この例の Resource Manager テンプレートには、**adminUserName** と **adminPassword** の 2 つのパラメーターがあります。 

![Azure DevTest Labs 環境の設定タスク](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>App Service のデプロイ タスク
3 番目のタスクは、**App Service のデプロイ** タスクです。 アプリの種類が **Web アプリ** に設定され、App Service 名が **$(WebSite)** に設定されます。

![App Service のデプロイ タスク](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>リリース パイプラインの設定
**Azure のデプロイ: リソース グループの作成または更新** と **Azure App Service のデプロイ** という 2 つのタスクを使用してリリース パイプラインを作成します。 

最初のタスクでは、リソース グループの名前と場所を指定します。 テンプレートの場所は、リンクされた成果物です。 Resource Manager テンプレートにリンクされたテンプレートが含まれている場合は、カスタム リソース グループのデプロイを実装する必要があります。 テンプレートは、パブリッシュされたドロップの成果物に含まれています。 Resource Manager テンプレートのテンプレート パラメーターをオーバーライドします。 残りの設定は既定値のままでかまいません。 

2 番目のタスク **Azure App Service のデプロイ** では、Azure サブスクリプションを指定し、**アプリの種類** に **Web アプリ** を選択し、**App Service 名** に **$(WebSite)** を選択します。 残りの設定は既定値のままでかまいません。 

## <a name="test-run"></a>テストの実行
これで両方のパイプラインが設定されたので、手動でビルドをキューに入れて、それが動作することを確認します。 次の手順では、ビルドに適切なトリガーを設定し、ビルドをリリース パイプラインに接続します。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [Azure Pipelines の継続的インテグレーションと配信パイプラインに Azure DevTest Labs を統合する](devtest-lab-integrate-ci-cd.md)
- [環境を Azure Pipelines CI/CD パイプラインに統合する](integrate-environments-devops-pipeline.md)
