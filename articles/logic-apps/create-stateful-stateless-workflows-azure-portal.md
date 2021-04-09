---
title: Azure portal で Logic Apps プレビュー ワークフローを作成する
description: Azure portal で Azure Logic Apps プレビューを使用して、自動化と統合のシナリオのためのワークフローを構築して実行します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563960"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Azure Logic Apps プレビューを使用して Azure portal でステートフルとステートレスのワークフローを作成する

> [!IMPORTANT]
> この機能はパブリック プレビュー段階であり、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure Logic Apps プレビュー](logic-apps-overview-preview.md)を使用すると、アプリ、データ、クラウド サービス、システムを対象とする自動化および統合ソリューションを構築できます。そのためには、Azure portal で新しい **ロジック アプリ (プレビュー)** リソースの種類から始めて、"[*ステートフル*" および "*ステートレス*" ワークフロー](logic-apps-overview-preview.md#stateful-stateless)が含まれるロジック アプリを作成して実行します。 この新しいロジック アプリの種類を使用すると、再設計された Azure Logic Apps プレビュー ランタイムを利用する複数のワークフローを構築できます。これにより、Azure だけでなく、Docker コンテナーを含むさまざまなホスティング環境でデプロイおよび実行するための移植性、より優れたパフォーマンス、柔軟性が提供されます。 新しいロジック アプリの種類の詳細については、[Azure Logic Apps プレビューの概要](logic-apps-overview-preview.md)に関するページをご覧ください。

![Azure portal と "ロジック アプリ (プレビュー)" リソース用のワークフロー デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

Azure portal では、新しい **ロジック アプリ (プレビュー)** リソースを作成することによって始めることができます。 また、[Visual Studio Code で Azure Logic Apps (プレビュー) 拡張機能を使用してプロジェクトを作成する](create-stateful-stateless-workflows-visual-studio-code.md)ことによって始めることもできますが、どちらの方法でも、同じ種類のホスティング環境でロジック アプリをデプロイして実行することができます。

また、元のロジック アプリの種類を作成することもできます。 ポータルの開発エクスペリエンスは、元のロジック アプリの種類と新しいものでは異なりますが、Azure サブスクリプションには両方の種類を含めることができます。 Azure サブスクリプションでデプロイされているすべてのロジック アプリを表示してアクセスできますが、アプリはその独自のカテゴリとセクションにまとめられます。

この記事では、**ロジック アプリ (プレビュー)** リソースの種類を使用し、大まかに次のようなタスクを実行することにより、Azure portal でロジック アプリとワークフローを作成する方法について説明します。

* 新しいロジック アプリ リソースを作成し、空のワークフローを追加します。

* トリガーとアクションを追加します。

* ワークフローの実行をトリガーします。

* ワークフローの実行履歴とトリガー履歴を表示します。

* デプロイの後で、Application Insights を有効にするか開きます。

* ステートレス ワークフローの実行履歴を有効にします。

> [!NOTE]
> 現在の既知の問題の詳細については、[GitHub の Logic Apps パブリック プレビューでの既知の問題に関するページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* [Azure ストレージ アカウント](../storage/common/storage-account-overview.md)。これは、**ロジック アプリ (プレビュー)** リソースには Azure Functions の機能が必要であり、[関数アプリに似たストレージ要件](../azure-functions/storage-considerations.md)があるためです。 既存のストレージ アカウントを使用することも、事前に、またはロジック アプリの作成時に、ストレージ アカウントを作成することもできます。

  > [!NOTE]
  > [ステートフル ロジック アプリ](logic-apps-overview-preview.md#stateful-stateless)は、スケジュールへのキューの使用や、テーブルや BLOB へのワークフローの状態の格納など、ストレージ トランザクションの実行に使用します。 これらのトランザクションには、[Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)がかかります。 ステートフル ロジック アプリによって外部ストレージにデータが格納される方法の詳細については、[ステートフルとステートレスの比較](logic-apps-overview-preview.md#stateful-stateless)に関するページを参照してください。

* Docker コンテナーにデプロイするには、既存の Docker コンテナー イメージが必要です。 このイメージを作成するには、たとえば [Azure Container Registry](../container-registry/container-registry-intro.md)、[App Service](../app-service/overview.md)、[Azure Container Instance](../container-instances/container-instances-overview.md) を使用できます。 

* この記事の同じロジック アプリの例をビルドするには、Microsoft の職場または学校アカウントを使用してサインインする Office 365 Outlook 電子メール アカウントが必要です。

  Outlook.com や [Gmail](../connectors/connectors-google-data-security-privacy-policy.md) など、[Azure Logic Apps でサポートされている別の電子メール コネクタ](/connectors/)を使用する場合でもこの例に従うことができ、全体的な手順は同じです。ただし、ユーザー インターフェイスとオプションは一部異なる場合があります。 たとえば、Outlook.com コネクタを使用する場合は、代わりに個人用 Microsoft アカウントを使用してサインインします。

* この記事で作成したロジック アプリの例をテストするには、Request トリガーに呼び出しを送信できるツールが必要です。これは、ロジック アプリの例の最初の手順です。 このようなツールがない場合は、[Postman](https://www.postman.com/downloads/) をダウンロードしてインストールし、使用することができます。

* [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用をサポートする設定を使用してロジック アプリを作成する場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。 ロジック アプリを作成するとき、またはデプロイの後に、それを行うことができます。 Application Insights のインスタンスを用意する必要がありますが、このリソースは、[事前に](../azure-monitor/app/create-workspace-resource.md)、ロジック アプリを作成するときに、またはデプロイの後で、作成することができます。

## <a name="create-the-logic-app-resource"></a>ロジック アプリ リソースを作成する

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure portal の検索ボックスに「`logic app preview`」と入力し、 **[Logic App (preview)]** を選択します。

   ![Azure portal の検索ボックスに "logic app preview" という検索用語が入力され、[Logic App (preview)] リソースが選択されているスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. **[ロジック アプリ (プレビュー)]** ページで、 **[追加]** を選択します。

1. **[ロジック アプリ (プレビュー) の作成]** ページの **[基本]** タブで、ロジック アプリに関するこの情報を入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | ロジック アプリに使用する Azure サブスクリプション。 |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | ロジック アプリと関連リソースを作成する Azure リソース グループ。 このリソース名は、リージョン間で一意である必要があり、文字、数字、ハイフン ( **-** )、アンダースコア ( **_** )、かっこ ( **()** )、ピリオド ( **.** ) のみを含めることができます。 <p><p>この例では、`Fabrikam-Workflows-RG` という名前のリソース グループを作成します。 |
   | **ロジック アプリ名** | はい | <*ロジック アプリ名*> | ロジック アプリに使用する名前。 このリソース名は、リージョン間で一意である必要があり、文字、数字、ハイフン ( **-** )、アンダースコア ( **_** )、かっこ ( **()** )、ピリオド ( **.** ) のみを含めることができます。 <p><p>この例では、`Fabrikam-Workflows` という名前のロジック アプリを作成します。 <p><p>**注**:**ロジック アプリ (プレビュー)** リソースには Azure Functions の機能が利用され、同じアプリ名前付け規則が使用されるため、ロジック アプリの名前には `.azurewebsites.net` というサフィックスが自動的に付けられます。 |
   | **発行** | はい | <*デプロイ環境*> | ロジック アプリのデプロイ先。 **[ワークフロー]** または **[Docker コンテナー]** を選択することで、Azure にデプロイできます。 <p><p>この例では **[ワークフロー]** を使用します。これで、**ロジック アプリ (プレビュー)** リソースが Azure portal にデプロイされます。 <p><p>**注**: **[Docker コンテナー]** を選択する前に、必ず Docker コンテナー イメージを作成してください。 このイメージを作成するには、たとえば [Azure Container Registry](../container-registry/container-registry-intro.md)、[App Service](../app-service/overview.md)、[Azure Container Instance](../container-instances/container-instances-overview.md) を使用できます。 そうすることで、 **[Docker コンテナー]** を選択した後、[ロジック アプリの設定で使用するコンテナーを指定](#set-docker-container)できます。 |
   | **[リージョン]** | はい | <*Azure-region*> | リソース グループとリソースを作成するときに使用する Azure リージョン。 <p><p>この例では **米国西部** を使用します。 |
   |||||

   次に例を示します。

   ![Azure portal と [ロジック アプリ (プレビュー) の作成] ページが示されているスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. 次に、 **[ホスティング]** タブで、ロジック アプリに使用するストレージ ソリューションとホスティング プランに関する次の情報を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **ストレージ アカウント** | はい | <*Azure-storage-account-name*> | ストレージ トランザクションに使用する [Azure ストレージ アカウント](../storage/common/storage-account-overview.md)。 このリソース名は、リージョン間で一意であり、数字と小文字のみを含む 3 から 24 文字である必要があります。 既存のアカウントを選択するか、新しいアカウントを作成します。 <p><p>この例では、`fabrikamstorageacct` という名前のストレージ アカウントを作成します。 |
   | **[プランの種類]** | はい | <*Azure ホスティング プラン*> | ロジック アプリのデプロイに使用する [ホスティング プラン](../app-service/overview-hosting-plans.md)。[ **[Functions Premium]**](../azure-functions/functions-premium-plan.md) または [ **[App Service プラン]** (Dedicated)](../azure-functions/dedicated-plan.md) です。 この選択は、後で利用できる機能と価格レベルに影響します。 <p><p>この例では、 **[App Service プラン]** を使用します。 <p><p>**注**:Azure Functions と同様に、**ロジック アプリ (プレビュー)** リソースの種類には、ホスティング プランと価格レベルが必要です。 このリソースの種類には、従量課金プランはサポートされておらず、使用できません。 詳細については、次のトピックをご覧ください。 <p><p>- [Azure Functions のスケーリングとホスティング](../azure-functions/functions-scale.md) <br>- [App Service の価格の詳細](https://azure.microsoft.com/pricing/details/app-service/) <p><p>たとえば、Functions Premium プランでは、ロジック アプリを作成してデプロイする場合の Azure Functions と同様に、Azure 仮想ネットワークとのプライベートな接続や統合などのネットワーク機能にアクセスできます。 詳細については、次のトピックをご覧ください。 <p><p>- [Azure Functions のネットワーク オプション](../azure-functions/functions-networking-options.md) <br>- [あらゆる場所で実行される Azure Logic Apps - Azure Logic Apps プレビューによるネットワークの可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Windows プラン** | はい | <*プラン名*> | 使用するプラン名。 既存のプランを選択するか、新しいプランの名前を指定します。 <p><p>この例では、 `Fabrikam-Service-Plan`という名前を使用しています。 |
   | **SKU とサイズ** | はい | <*価格レベル*> | ロジック アプリのホスティングに使用する[価格レベル](../app-service/overview-hosting-plans.md)。 選択肢は、前に選択したプランの種類によって異なります。 既定のレベルを変更するには、 **[サイズの変更]** を選択します。 その後、必要なワークロードに基づいて、他の価格レベルを選択できます。 <p><p>この例では、**開発とテスト** のワークロード用に無料の **F1 価格レベル** を使用します。 詳細については、[App Service の価格の詳細](https://azure.microsoft.com/pricing/details/app-service/)に関するページをご確認ください。 |
   |||||

1. 次に、作成とデプロイの設定で [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用がサポートされている場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。

   1. **[監視]** タブの **[Application Insights]** で、 **[Application Insights を有効にする]** を **[はい]** に設定します (まだ選択されていない場合)。

   1. **Application Insights** の設定で、Application Insights の既存のインスタンスを選択するか、新しいインスタンスを作成する場合は、 **[新規作成]** を選択して、使用する名前を指定します。

1. Azure によるロジック アプリの設定の検証が済んだら、 **[確認および作成]** タブで、 **[作成]** を選択します。

   例:

   ![Azure portal と新しいロジック アプリ リソースの設定を示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > **[作成]** を選択した後、検証エラーが発生する場合は、エラーの詳細を開いて確認します。 たとえば、選択したリージョンが、作成しようとしているリソースのクォータに達する場合、別のリージョンの試行が必要になることがあります。

   Azure によるデプロイが完了すると、ロジック アプリは自動的に稼働状態になりますが、ワークフローが存在しないため、まだ何も行われません。

1. デプロイ完了ページで **[リソースに移動]** を選択して、ワークフローの作成を開始できるようにします。 ロジック アプリのデプロイに **[Docker コンテナー]** を選択した場合は、[その Docker コンテナーに関する情報を指定する手順](#set-docker-container)に進みます。

   ![Azure portal と完了したデプロイを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>デプロイに Docker コンテナーを指定する

これらの手順を開始する前に、Docker コンテナー イメージが必要です。 このイメージを作成するには、たとえば [Azure Container Registry](../container-registry/container-registry-intro.md)、[App Service](../app-service/overview.md)、[Azure Container Instance](../container-instances/container-instances-overview.md) を使用できます。 そうすれば、ロジック アプリを作成した後で、Docker コンテナーに関する情報を指定できます。

1. Azure portal で、ロジック アプリ リソースに移動します。

1. ロジック アプリのメニューで、 **[設定]** の下の **[デプロイ センター]** を選択します。

1. **[デプロイ センター]** ペインで、Docker コンテナーの詳細を指定および管理するための手順に従います。

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>空のワークフローを追加する

1. Azure でリソースが開かれたら、ロジック アプリのメニューで **[ワークフロー]** を選択します。 **[ワークフロー]** ツール バーで、 **[追加]** を選択します。

   ![ロジック アプリ リソースのメニューで [ワークフロー] が選択され、ツール バーで [追加] が選択されているスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. **[新しいワークフロー]** ペインが開いたら、ワークフローの名前を指定し、[ **[ステートフル]** または **[ステートレス]**](logic-apps-overview-preview.md#stateful-stateless) いずれかのワークフローの種類を選択します。 完了したら **[作成]** を選択します。

   この例では、`Fabrikam-Stateful-Workflow` という名前の空のステートフル ワークフローを追加します。 ワークフローは既定で有効になりますが、トリガーとアクションを追加するまでは何も行われません。

   ![新しく追加された空のステートフル ワークフロー "Fabrikam-Stateful-Workflow" を示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. 次に、トリガーとアクションを追加できるように、デザイナーで空のワークフローを開きます。

   1. ワークフローの一覧から、空のワークフローを選択します。

   1. ワークフローのメニューの **[開発者]** で、 **[デザイナー]** を選択します。

      デザイナー画面では **[操作を選択してください]** プロンプトが既に表示されて既定で選択されているので、 **[トリガーの追加]** ペインも開いた状態で表示されます。

      ![デザイナー画面で [操作を選択してください] が選択されている、開かれたワークフロー デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>トリガーとアクションを追加する

この例では、次のような手順のワークフローを作成します。

* 組み込みの [Request トリガー](../connectors/connectors-native-reqres.md) **[HTTP 要求の受信時]** 。受信呼び出しまたは要求を受信し、他のサービスまたはロジック アプリが呼び出すことができるエンドポイントを作成します。

* [Office 365 Outlook アクション](../connectors/connectors-create-api-office365-outlook.md) **[メールの送信]** 。

* 組み込みの[応答アクション](../connectors/connectors-native-reqres.md)。応答を送信してデータを呼び出し元に返すために使用します。

### <a name="add-the-request-trigger"></a>要求トリガーを追加する

空のワークフローにトリガーを追加する前に、ワークフロー デザイナーが開いていて、デザイナー画面で **[操作を選択してください]** プロンプトが選択されていることを確認します。

1. デザイナー画面の横にある **[トリガーの追加]** ペインの **[操作を選択してください]** 検索ボックスの下で、 **[組み込み]** タブが選択されていることを確認します。 このタブには、Azure Logic Apps でネイティブに実行されるトリガーが表示されます。

1. **[操作の選択]** 検索ボックスに「`when a http request`」と入力し、 **[HTTP 要求の受信時]** という名前の組み込みの Request トリガーを選択します。

   ![デザイナーと、[HTTP 要求の受信時] トリガーが選択された **[トリガーの追加]** ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   トリガーがデザイナーに表示されると、トリガーの詳細ウィンドウが開き、トリガーのプロパティ、設定、およびその他のアクションが表示されます。

   ![[HTTP 要求の受信時] トリガーが選択されたデザイナーが表示され、トリガー詳細ウィンドウが開いたスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > 詳細ウィンドウが表示されない場合は、デザイナーでトリガーが選択されていることを確認します。

1. デザイナーから項目を削除する必要がある場合は、[デザイナーからの項目の削除に関するこちらの手順のようにします](#delete-from-designer)。

1. 作業を保存するには、デザイナーのツール バーで、 **[保存]** を選択します。

   ワークフローを初めて保存すると、そのワークフローが Request トリガーで開始されるときに、Logic Apps サービスによって、Request トリガーによって作成されるエンドポイントの URL が自動的に生成されます。 後でワークフローをテストするときに、この URL に要求を送信すると、トリガーが起動され、ワークフローの実行が開始されます。

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook アクションを追加する

1. デザイナーで、追加したトリガーの下の **[新しいステップ]** を選択します。

   デザイナーに **[操作を選択してください]** というプロンプトが表示され、次のアクションを選択できるように **[アクションの追加]** ペインが再び表示されます。

   > [!NOTE]
   > **[アクションの追加]** ペインに "Cannot read property 'filter' of undefined" (未定義のプロパティ 'filter' を読み取ることができない) という内容のエラー メッセージが表示される場合は、ワークフローを保存し、ページを再度読み込み、ワークフローを再度開いてから、もう一度やり直します。

1. **[アクションの追加]** ペインの **[操作を選択してください]** 検索ボックスの下で、 **[Azure]** を選択します。 このタブには、Azure で使用可能でデプロイされるマネージド コネクタが表示されます。

   > [!NOTE]
   > **[アクションの追加]** ペインに `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` (アクセス トークンの有効期限 '{token-expiration-date-time}' (UTC) が現在の時間 '{current-date-time}' (UTC) より前です) というエラー メッセージが表示される場合は、ワークフローを保存し、ページを再度読み込み、ワークフローを再度開いてから、アクションをもう一度追加してみます。

   この例では、 **[メールの送信 (V2)]** という名前の Office 365 Outlook アクションを使用します。

   ![デザイナーが表示され、**[アクションの追加]** ペインで Office 365 Outlook の [メールの送信] アクションが選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. アクションの詳細ペインの **[接続の作成]** タブで **[サインイン]** を選択して、メール アカウントへの接続を作成できるようにします。

   ![デザイナーと、[サインイン] が選択された **[メールの送信 (V2)]** 詳細ペインを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. メール アカウントにアクセスする同意を求めるメッセージが表示されたら、アカウントの資格情報でサインインします。

   > [!NOTE]
   > `Failed with error: 'The browser is closed.'. Please sign in again` (エラーで失敗: ブラウザーが開かれていません。再度サインインしてください) という内容のエラー メッセージが表示される場合は、ブラウザーでサードパーティの Cookie がブロックされているかどうかを確認します。 これらの Cookie がブロックされている場合は、Cookie を使用できるサイトの一覧に `https://portal.azure.com` を追加してみてください。 Incognito モードを使用している場合は、そのモードでの作業中に、サードパーティの Cookie がブロックされないことを確認します。
   > 
   > 必要な場合は、ページを再度読み込み、ワークフローを開き、もう一度メール アクションを追加してから、接続を作成してみてください。

   Azure によって接続が作成されると、 **[メールの送信]** アクションがデザイナーに表示され、既定で選択されます。 アクションが選択されていない場合は、アクションを選択して、その詳細ペインも開きます。

1. アクションの詳細ペインの **[パラメーター]** タブで、アクションに必要な情報を指定します。次に例を示します。

   ![デザイナーと、[パラメーター] タブが選択された [メールの送信] 詳細ペインを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **To** | はい | <*your-email-address*> | 電子メールの受信者。テストのためにご自分のメール アドレスを指定できます。 この例では、架空のメール アドレス `sophiaowen@fabrikam.com` を使用しています。 |
   | **件名** | はい | `An email from your example workflow` | メールの件名 |
   | **本文** | はい | `Hello from your example workflow!` | メールの本文の内容 |
   ||||

   > [!NOTE]
   > 詳細ペインの **[設定]** 、 **[静的な結果]** 、または **[実行までの期間]** タブで変更を行うときは、タブを切り替えたり、デザイナーにフォーカスを変更したりする前に、必ず **[完了]** を選択して変更をコミットします。 そうしないと、デザイナーでの変更が保持されません。

1. 作業内容を保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

1. トラフィックを制限する厳しいネットワーク要件またはファイアウォールが環境内にある場合は、ワークフロー内に存在するすべてのトリガーまたはアクション接続に対してアクセス許可を設定する必要があります。 完全修飾名を検索するには 

   そうでない場合、ワークフローをテストするには、[手動で実行をトリガーします](#trigger-workflow)。

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>ファイアウォール アクセス用のドメイン名を検索する

ロジック アプリをデプロイして Azure portal でワークフローを実行する前に、トラフィックを制限する厳しいネットワーク要件またはファイアウォールが環境内にある場合は、論理アプリ内に存在するワークフロー内のすべてのトリガーまたはアクション接続に対するネットワークまたはファイアウォールのアクセス許可を設定する必要があります。

これらの接続の完全修飾ドメイン名 (FQDN) を検索するには、次の手順を実行します。

1. ロジック アプリのメニューの **[ワークフロー]** で、 **[接続]** を選択します。 **[API 接続]** タブで、接続のリソース名を選択します。ここに例を示します。

   ![[接続] および [offic365] 接続リソース名が選択されている、Azure portal とロジック アプリのメニューを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. ブラウザーの右上隅に **[JSON ビュー]** が表示されるようにブラウザーの幅を広げて、 **[JSON ビュー]** を選択します。

   ![[JSON ビュー] が選択されている、Azure portal と [API 接続] ペインを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. `connectionRuntimeUrl` プロパティ値を見つけてコピーし、安全な場所に保存して、この情報を使用してファイアウォールを設定できるようにします。

   ![選択された "connectionRuntimeUrl" プロパティ値を示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. 各接続について、関連する手順を繰り返します。

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>ワークフローをトリガーする

この例では、着信要求が Request トリガーによって受信されるとワークフローが実行され、それはトリガーによって作成されたエンドポイントの URL に送信されます。 この URL は、ワークフローを初めて保存するときに、Logic Apps サービスによって自動的に生成されます。 そのため、この要求を送信してワークフローをトリガーする前に、この URL を見つけておく必要があります。

1. ワークフロー デザイナーで、 **[HTTP 要求の受信時]** という名前の Request トリガーを選択します。

1. 詳細ペインが開いたら、 **[パラメーター]** タブで、 **[HTTP POST の URL]** プロパティを見つけます。 生成された URL をコピーするには、 **[URL のコピー]** (ファイルのコピー アイコン) を選択し、今のところは URL をどこかに保存しておきます。 URL は次の形式になっています。

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Request トリガーと [HTTP POST の URL] プロパティのエンドポイント URL が表示されているデザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   この例では、URL は次のようになります。

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > エンドポイント URL は、ロジックアプリの **[概要]** ペインの **[Workflow URL]\(ワークフロー URL\)** プロパティで確認することもできます。
   >
   > 1. リソース メニューで **[概要]** を選択します。
   > 1. **[概要]** ペインで、 **[Workflow URL]\(ワークフロー URL\)** プロパティを見つけます。
   > 1. エンドポイント URL をコピーするには、エンドポイント URL のテキストの末尾までポインターを移動し、 **[クリップボードにコピー]** (ファイルのコピー アイコン) を選択します。

1. 要求を送信して URL をテストするには、[Postman](https://www.postman.com/downloads/) を開くか、要求を作成して送信するための任意のツールを開きます。

   この例では、Postman を使用して続行します。 詳細については、[Postman の概要](https://learning.postman.com/docs/getting-started/introduction/)に関するページを参照してください。

   1. Postman のツールバーで、 **[新規]** を選択します。

      ![[新規] ボタンが選択された Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. **[新規作成]** ウィンドウの **[構成要素]** で、 **[要求]** を選択します。

   1. **[要求の保存]** ウィンドウの **[要求名]** で、要求の名前を指定します。たとえば、`Test workflow trigger` のように指定します。

   1. **[保存先のコレクションまたはフォルダーの選択]** で、 **[コレクションの作成]** を選択します。

   1. **[すべてのコレクション]** で、要求を整理するために作成するコレクションの名前を指定して Enter キーを押し、 **[<*コレクション名*> に保存]** を選択します。 この例では、`Logic Apps requests` というコレクション名を使用しています。

      Postman の要求ペインが開き、Request トリガーのエンドポイント URL に要求を送信できるようになります。

      ![要求ウィンドウを開いた Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. 要求ペインで、既定の要求メソッドとして現在は **[GET]** が表示されている、メソッド一覧の横にあるアドレス ボックスに、先ほどコピーした URL を貼り付けて、 **[送信]** を選択します。

      ![アドレス ボックスにエンドポイント URL が入力され、[送信] ボタンが選択されている Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      トリガーが発生すると、例のワークフローが実行され、次の例のようなメールが送信されます。

      ![例に記載されている Outlook の電子メールを示すスクリーンショット](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>実行履歴を確認する

ステートフル ワークフローの場合は、各ワークフローの実行後に、実行全体、トリガー、各アクションの状態およびそれらの入力と出力が含まれる、実行履歴を表示することができます。 Azure portal では、実行履歴とトリガーの履歴が、ロジック アプリ レベルではなくワークフロー レベルで表示されます。 実行履歴のコンテキスト外でトリガー履歴を確認するには、「[トリガー履歴を確認する](#view-trigger-histories)」を参照してください。

1. Azure portal のワークフローのメニューで、 **[監視]** を選択します。

   **[監視]** ペインに、そのワークフローの実行履歴が表示されます。

   ![ワークフローの [監視] ペインと実行履歴を示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > 最新の実行状態が表示されない場合は、 **[監視]** ペインのツール バーで **[更新]** を選択します。 条件が満たされなかったか、データが見つからなかったためにスキップされたトリガーに対しては、実行は行われません。

   | 実行の状態 | 説明 |
   |------------|-------------|
   | **Aborted** | 外部に問題が発生したため、実行が停止したか、または完了しませんでした。たとえば、システムの停止や Azure サブスクリプションの中断などです。 |
   | **取り消し済み** | 実行がトリガーされ、開始されましたが、取り消し要求を受け取りました。 |
   | **Failed** | 実行中に少なくとも 1 つのアクションが失敗しました。 ワークフローの後続のアクションは、エラーを処理するように設定されていません。 |
   | **実行中** | 実行がトリガーされ、進行中です。ただし、この状態は、[アクション制限](logic-apps-limits-and-config.md)または[現在の料金プラン](https://azure.microsoft.com/pricing/details/logic-apps/)によって制限されている実行に対しても表示されます。 <p><p>**ヒント**:[診断ログ](monitor-logic-apps-log-analytics.md)を設定すると、発生するスロットル イベントに関する情報を取得することができます。 |
   | **Succeeded** | 実行は成功しました。 いずれかのアクションが失敗した場合、ワークフロー内の後続のアクションによってそのエラーが処理されます。 |
   | **タイムアウト** | 現在の継続時間が実行継続時間の制限を超えたため、実行がタイムアウトしました。これは、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行の継続時間は、実行の開始時刻とその開始時刻での実行継続時間の制限を使用して計算されます。 <p><p>**注**:実行の継続時間が現在の *"実行履歴の保持期間の制限"* も超えている場合は、毎日のクリーンアップ ジョブによって実行履歴から実行が消去されます。これも、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行がタイムアウトしたか完了したかにかかわらず、保持期間は常に、実行の開始時刻と "*現在の*" 保持期間の制限を使用して計算されます。 そのため、処理中の実行の継続時間制限を短くすると、実行はタイムアウトします。ただし、実行が実行履歴に残るか消去されるかは、実行の継続時間が保持期間の制限を超えているかどうかに基づいて決まります。 |
   | **待機中** | たとえば、その前のワークフロー インスタンスがまだ実行中である等の理由で、実行が開始されていないか、または一時停止しています。 |
   |||

1. 実行の各ステップの状態を確認するには、確認する実行を選択します。

   実行の詳細ビューが開き、実行の各ステップの状態が表示されます。

   ![ワークフローの各ステップの状態が表示された実行の詳細ビューを示すスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   ワークフローの各ステップに付けられる状態は次のとおりです。

   | アクションの状態 | アイコン | 説明 |
   |---------------|------|-------------|
   | **Aborted** | !["中止" 状態のアクションのアイコン][aborted-icon] | 外部に問題が発生したため、アクションが停止したか、または完了しませんでした。たとえば、システムの停止や Azure サブスクリプションの中断などです。 |
   | **取り消し済み** | !["キャンセル" 状態のアクションのアイコン][cancelled-icon] | アクションは実行中でしたが、取り消し要求を受け取りました。 |
   | **失敗** | !["失敗" 状態のアクションのアイコン][failed-icon] | アクションに失敗しました。 |
   | **実行中** | !["実行中" 状態のアクションのアイコン][running-icon] | アクションは現在実行中です。 |
   | **Skipped** | !["スキップ" 状態のアクションのアイコン][skipped-icon] | 直前のアクションが失敗したため、アクションはスキップされました。 アクションには、現在のアクションを実行する前に、前のアクションが正常に完了している必要がある `runAfter` 条件が含まれています。 |
   | **Succeeded** | !["成功" 状態のアクションのアイコン][succeeded-icon] | アクションに成功しました。 |
   | **再試行により成功** | !["再試行により成功" 状態のアクションのアイコン][succeeded-with-retries-icon] | アクションは成功しましたが、1 回以上の再試行が行われました。 再試行履歴を確認するには、実行履歴の詳細ビューでその操作を選択し、入力と出力を表示します。 |
   | **タイムアウト** | !["タイムアウト" 状態のアクションのアイコン][timed-out-icon] | アクションの設定によって指定されたタイムアウト制限により、アクションが停止しました。 |
   | **待機中** | !["待機中" 状態のアクションのアイコン][waiting-icon] | 呼び出し元からの受信要求を待機している Webhook アクションに適用されます。 |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. 特定のステップの入力と出力を確認するには、そのステップを選択します。

   ![選択した "メールの送信" アクションでの入力と出力が示されているスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. そのステップの未加工の入出力をさらに確認するには、 **[未加工入力の表示]** または **[未加工出力の表示]** を選択します。

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>トリガー履歴を確認する

ステートフル ワークフローの場合は、[実行履歴のコンテキスト](#view-run-history)とは別に、実行ごとのトリガー履歴を確認できます。これには、トリガーの状態と入力および出力が含まれます。 Azure portal では、トリガー履歴と実行履歴が、ロジック アプリ レベルではなくワークフロー レベルで表示されます。 この履歴データを見つけるには、これらの手順に従います。

1. Azure portal のワークフローのメニューで、 **[開発者]** の下の **[トリガー履歴]** を選択します。

   **[トリガー履歴]** ペインには、ワークフローの実行のトリガー履歴が表示されます。

1. 特定のトリガー履歴を確認するには、その実行の ID を選択します。

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>デプロイの後で Application Insights を有効にするか開く

ワークフローの実行中に、ロジック アプリによって他のイベントと共にテレメトリが出力されます。 このテレメトリを使用して、ワークフローの実行状況や、Logic Apps ランタイムのさまざまな方法での動作を、より明確に把握することができます。 [Application Insights](../azure-monitor/app/app-insights-overview.md) を使用してワークフローを監視でき、ほぼリアルタイムのテレメトリ (ライブ メトリック) が提供されます。 この機能を使用すると、このデータを使用して問題の診断、アラートの設定、グラフの作成を行うときに、エラーやパフォーマンスの問題をより簡単に調査できます。

ロジック アプリの作成とデプロイの設定で [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用がサポートされている場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。 Azure portal でロジック アプリを作成するとき、またはデプロイの後に、それを行うことができます。 Application Insights のインスタンスを用意する必要がありますが、このリソースは、[事前に](../azure-monitor/app/create-workspace-resource.md)、ロジック アプリを作成するときに、またはデプロイの後で、作成することができます。

デプロイされたロジック アプリで Application Insights を有効にするには、または既に有効になっている場合に Application Insights ダッシュボードを開くには、次の手順のようにします。

1. Azure portal で、デプロイされたロジック アプリを見つけます。

1. ロジック アプリのメニューの **[設定]** で、 **[Application Insights]** を選択します。

1. Application Insights が有効になっていない場合は、 **[Application Insights]** ペインで、 **[Application Insights を有効にする]** を選択します。 ペインが更新されたら、下部にある **[適用]** を選択します。

   Application Insights が有効になっている場合は、 **[Application Insights]** ペインで、 **[Application Insights データの表示]** を選択します。

Application Insights が開いたら、ロジック アプリのさまざまなメトリックを確認できます。 詳細については、次のトピックをご覧ください。

* [あらゆる場所で実行される Azure Logic Apps - Application Insights で監視する - パート 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [あらゆる場所で実行される Azure Logic Apps - Application Insights で監視する - パート 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>ステートレス ワークフローの実行履歴を有効にする

ステートレス ワークフローをさらに簡単にデバッグするには、そのワークフローの実行履歴を有効にした後、完了したら実行履歴を無効にすることができます。 Azure portal で以下の手順のようにします。または、Visual Studio Code で作業している場合は、[Visual Studio Code でのステートフルおよびステートレス ワークフローの作成](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) で、ご利用の **[ロジック アプリ (プレビュー)]** リソースを検索して開きます。

1. ロジック アプリのメニューにある **[設定]** で、 **[構成]** を選択します。

1. **[アプリケーションの設定]** タブで、 **[新しいアプリケーション設定]** を選択します。

1. **[アプリケーション設定の追加/編集]** ウィンドウで、 **[名前]** ボックスに次の操作オプションの名前を入力します。 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. **[値]** ボックスに、「`WithStatelessRunHistory`」と入力します。

   次に例を示します。

   ![Azure portal とロジック アプリ (プレビュー) リソースを示すスクリーンショット。[構成] > [新しいアプリケーション設定] > [アプリケーション設定の追加/編集] ウィンドウが開かれ、"Workflows.{yourWorkflowName}.OperationOptions" オプションが "WithStatelessRunHistory" に設定されている。](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. このタスクを完了するには、 **[OK]** を選択します。 **[構成]** ペインのツール バーで、 **[保存]** を選択します。

1. 完了時に実行履歴を無効にするには、`Workflows.{yourWorkflowName}.OperationOptions` プロパティを `None` に設定するか、プロパティとその値を削除します。

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>デザイナーから項目を削除する

デザイナーからワークフロー内の項目を削除するには、次のいずれかの手順のようにします。

* 項目を選択し、項目のショートカット メニューを開いて (Shift + F10)、 **[削除]** を選択します。 **[OK]** を選択して確認します。

* 項目を選択して、Delete キーを押します。 **[OK]** を選択して確認します。

* 項目を選択すると、その項目の詳細ペインが開きます。 ペインの右上隅で、省略記号 **[...]** メニューを開き、 **[削除]** を選択します。 **[OK]** を選択して確認します。

  ![詳細ペインが開いてデザイナーに選択した項目が表示され、省略記号ボタンと [削除] コマンドが選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > 省略記号メニューが表示されない場合は、右上隅にある省略記号 **[...]** ボタンが詳細ペインに表示されるよう、ブラウザーのウィンドウを十分に広げます。

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>問題とエラーのトラブルシューティング

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>前に作成したワークフローのデザイナー ピッカーに新しいトリガーとアクションがない

Azure Logic Apps プレビューでは、 **[XML の検証]** や **[XML の変換]** など、Azure 関数の操作、Liquid の操作、XML の操作に対する組み込みアクションがサポートされています。 ただし、以前に作成したロジック アプリでは、ロジック アプリで古いバージョンの拡張機能バンドル `Microsoft.Azure.Functions.ExtensionBundle.Workflows` が使用されている場合、これらの操作がデザイナーに表示されないことがあります。

この問題を解決するには、次の手順に従って古いバージョンを削除し、拡張機能バンドルを最新バージョンに自動的に更新できるようにします。

> [!NOTE]
> この特定の解決方法が特定されるのは、Azure portal を使用して作成した **ロジック アプリ (プレビュー)** リソースだけであり、Visual Studio Code と Azure Logic Apps (プレビュー) 拡張機能を使用して作成およびデプロイしたロジック アプリには適用されません。 [サポートされているトリガーとアクションが Visual Studio Code のデザイナーに表示されない場合](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions)に関するページを参照してください。

1. Azure portal でロジック アプリを停止します。

   1. ロジック アプリのメニューで、 **[概要]** を選択します。

   1. **[概要]** ペインのツール バーで、 **[停止]** を選択します。

1. ロジック アプリのメニューの **[開発ツール]** で、 **[高度なツール]** を選択します。

1. **[高度なツール]** ペインで、 **[移動]** を選択します。これにより、ロジック アプリ用の Kudu 環境が開きます。

1. Kudu のツール バーで、 **[Debug console]\(デバッグ コンソール\)** メニューを開き、 **[CMD]** を選択します。 

   コンソール ウィンドウが開き、コマンド プロンプトを使用してバンドル フォルダーを参照できるようになります。 または、コンソール ウィンドウが表示されているディレクトリ構造を参照することもできます。

1. 次のフォルダーを参照します。そこには、既存のバンドルのバージョン管理されたフォルダーが含まれています。

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 既存のバンドルのバージョン フォルダーを削除します。 コンソール ウィンドウで、次のコマンドを実行します。`{bundle-version}` を既存のバージョンに置き換えます。

   `rm -rf {bundle-version}`

   例: `rm -rf 1.1.3`

   > [!TIP]
   > "アクセス許可が拒否されました" や "ファイルが使用されています" などのエラーが発生する場合は、ブラウザーでページを更新し、フォルダーが削除されるまで前の手順をもう一度試してください。

1. Azure portal でロジック アプリの **[概要]** ページに戻り、 **[再起動]** を選択します。

   ポータルにより、最新のバンドルが自動的に取得されて使用されます。

## <a name="next-steps"></a>次の手順

このパブリック プレビューを使用したお客様からのご意見をお待ちしております。

* バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
* 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/lafeedback)。
