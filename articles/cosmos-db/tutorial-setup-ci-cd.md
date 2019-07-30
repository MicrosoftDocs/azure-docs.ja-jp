---
title: Azure Cosmos DB エミュレーター ビルド タスクを使用して CI/CD パイプラインを設定する
description: Azure DevOps で Cosmos DB エミュレーター ビルド タスクを使用してビルドとリリースのワークフローを設定する方法のチュートリアル
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: dad99a7e3d0463263e912aa05b5312edbcb89c0b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597673"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Azure DevOps で Azure Cosmos DB エミュレーター ビルド タスクを使用して CI/CD パイプラインを設定する

Azure Cosmos DB エミュレーターでは、Azure Cosmos DB サービスを開発目的でエミュレートするローカル環境を利用できます。 エミュレーターを使用すれば、Azure サブスクリプションを作成したりコストをかけたりせずに、アプリケーションをローカルで開発してテストできます。 

Azure DevOps 用の Azure Cosmos DB エミュレーター ビルド タスクでは、CI 環境で同じことができます。 ビルド タスクを使用すると、ビルドとリリースのワークフローの一環として、エミュレーターに対してテストを実行できます。 タスクによって、既に実行中のエミュレーターが含まれた Docker コンテナーが起動され、ビルド定義の残りの部分で使用されるエンドポイントが提供されます。 エミュレーターのインスタンスは必要な数だけ作成して開始できます。これらのインスタンスはそれぞれ、別のコンテナーで実行されます。 

この記事では、Azure DevOps で ASP.NET アプリケーション用の CI パイプラインを設定し、Cosmos DB エミュレーター ビルド タスクを使用してテストを実行する方法について説明します。 同様のアプローチを使用して、Node.js または Python アプリケーション用の CI パイプラインを設定できます。 

## <a name="install-the-emulator-build-task"></a>エミュレーター ビルド タスクのインストール

ビルド タスクを使用するには、最初にそれを Azure DevOps 組織にインストールする必要があります。 [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) で拡張機能 **Azure Cosmos DB Emulator** を見つけて、 **[Get it free]\(無料で入手\)** をクリックします。

![Azure DevOps Marketplace で Azure Cosmos DB Emulator ビルド タスクを見つけてインストールする](./media/tutorial-setup-ci-cd/addExtension_1.png)

次に、拡張機能のインストール先となる組織を選択します。 

> [!NOTE]
> 拡張機能を Azure DevOps 組織にインストールするには、アカウント所有者またはプロジェクト コレクション管理者である必要があります。 アクセス許可はないものの、アカウント メンバーである場合は、代わりに拡張機能を要求できます。 [詳細情報。](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

![拡張機能のインストール先となる Azure DevOps 組織を選択する](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>ビルド定義の作成

拡張機能をインストールしたら、Azure DevOps アカウントにサインインし、プロジェクト ダッシュボードから目的のプロジェクトを見つけます。 [ビルド パイプライン](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)をプロジェクトに追加するか、既存のビルド パイプラインを変更することができます。 ビルド パイプラインが既にある場合は、[ビルド定義へのエミュレーター ビルド タスクの追加](#addEmulatorBuildTaskToBuildDefinition)に関するセクションまでスキップしてかまいません。

1. 新しいビルド定義を作成するには、Azure DevOps で **[ビルド]** タブに移動します。 **[+新規]** を選択します。 \> **[新しいビルド パイプライン]** を選択します

   ![新しいビルド パイプラインを作成する](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. 目的の**ソース**、**チーム プロジェクト**、**リポジトリ**、および**手動のビルドとスケジュールされたビルドの既定のブランチ**を選択します。 必要なオプションを選択した後、 **[続行]** を選択します

   ![ビルド パイプラインのチーム プロジェクト、リポジトリ、およびブランチを選択する](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. 最後に、ビルド パイプラインに使用したいテンプレートを選択します。 このチュートリアルでは、**ASP.NET** テンプレートを選択します。 

> [!NOTE]
> CI の一環として前のタスクで手動でインストールしていない場合、この CI 用に選択するエージェント プールには Docker for Windows をインストールしておく必要があります。 エージェント プールの選択については、[Microsoft によってホストされているエージェント](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml)の記事を参照してください。`Hosted VS2017` または `Hosted VS2019` から始めることをお勧めします。 

これで、Azure Cosmos DB エミュレーター ビルド タスクを使用するために設定できるビルド パイプラインが作成されました。 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>ビルド パイプラインへのタスクの追加

1. ビルド パイプラインにタスクを追加する前に、エージェント ジョブを追加する必要があります。 ビルド パイプラインに移動します。 **[...]** を選択し、 **[エージェント ジョブを追加する]** を選択します。

1. 次に、エミュレーター ビルド タスクを追加するために、エージェント ジョブの横にある **[+]** 記号を選択します。 検索ボックスで **cosmos** を検索し、**Azure Cosmos DB Emulator** を選択してエージェント ジョブに追加します。 このビルド タスクは、Cosmos DB エミュレーターのインスタンスが既に実行されている状態でコンテナーを起動します。 Azure Cosmos DB Emulator タスクは、エミュレーターが実行状態であることを想定している他のタスクの前に配置する必要があります。

   ![エミュレーター ビルド タスクをビルド定義に追加する](./media/tutorial-setup-ci-cd/addExtension_3.png)

このチュートリアルでは、テストの実行前にエミュレーターを使用できるようにするために、先頭にタスクを追加します。

## <a name="configure-tests-to-use-the-emulator"></a>エミュレーターを使用するテストの構成

次に、エミュレーターを使用するテストを構成します。 エミュレーター ビルド タスクによって、"CosmosDbEmulator.Endpoint" という環境変数がエクスポートされます。ビルド パイプライン内の後続のタスクは、このエンドポイントに対して要求を発行できます。 

このチュートリアルでは、[Visual Studio Test タスク](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md)を使用して、 **.runsettings** ファイルを通じて構成された単体テストを実行します。 単体テストの設定の詳細については、[ドキュメント](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017)を参照してください。 このドキュメントで使用する Todo アプリケーションの完全なコード サンプルは [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) から入手できます

以下は、アプリケーションの単体テストに渡されるパラメーターを定義する **.runsettings** ファイルの例です。 使用されている `authKey` 変数が、エミュレーターの[既知のキー](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests)であることに注意してください。 この `authKey` はエミュレーター ビルド タスクで必要なキーであり、 **.runsettings** ファイルで定義する必要があります。

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Azure Cosmos DB の MongoDB 用 API を使用するアプリケーション用の CI/CD パイプラインを設定する場合、MongoDB 接続文字列には、既定でポート番号 10255 が含まれています。 ただし、このポートは現在開いていないため、接続を確立する代替としてポート 10250 を使用して必要があります。 Azure Cosmos DB の MongoDB 用 API の接続文字列は、サポートされるポート番号が 10255 ではなく 10250 点であること以外は同じです。

`TestRunParameters` のこれらのパラメーターは、アプリケーションのテスト プロジェクトに含まれている `TestContext` プロパティを介して参照されます。 Cosmos DB に対して実行するテストの例を次に示します。

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Visual Studio Test タスクの [実行オプション] に移動します。 **[設定ファイル]** オプションで、 **.runsettings** ファイルを使用して構成されたテストを指定します。 **[テスト実行パラメーターのオーバーライド]** オプションで、「`-endpoint $(CosmosDbEmulator.Endpoint)`」と入力します。 これを行うことで、 **.runsettings** ファイルで定義されたエンドポイントではなく、エミュレーター ビルド タスクのエンドポイントを参照するよう、Test タスクが構成されます。  

![エミュレーター ビルド タスクのエンドポイントを使ってエンドポイント変数をオーバーライドする](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>ビルドを実行します

次に、ビルドを**保存してキューに登録**します。 

![ビルドを保存して実行する](./media/tutorial-setup-ci-cd/runBuild_1.png)

ビルドが開始されたら、Cosmos DB エミュレーター タスクによって、エミュレーターがインストールされた Docker イメージのプルが開始されたことを確認します。 

![ビルドを保存して実行する](./media/tutorial-setup-ci-cd/runBuild_4.png)

ビルドが完了したら、ビルド タスクの後に Cosmos DB エミュレーターに対してすべてのテストが実行され、成功することを確認します。

![ビルドを保存して実行する](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>次の手順

エミュレーターを使用して開発とテストをローカルで行う方法の詳細については、「[ローカルの開発とテストでの Azure Cosmos DB Emulator の使用](https://docs.microsoft.com/azure/cosmos-db/local-emulator)」を参照してください。

エミュレーター SSL 証明書のエクスポートについては、「[Java、Python、および Node.js で使用する Azure Cosmos DB Emulator 証明書のエクスポート](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)」を参照してください。
