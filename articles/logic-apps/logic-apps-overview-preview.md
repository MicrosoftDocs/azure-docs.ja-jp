---
title: Azure Logic Apps プレビューの概要
description: Azure Logic Apps プレビューは、エンタープライズレベルのシナリオ用にアプリ、データ、サービス、およびシステムを最小限のコードで統合する、自動化されたシングルテナントのステートフルおよびステートレス ワークフローを構築するクラウド ソリューションです。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 7120b6ff17657232c0e614f49b75bb24263712b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636337"
---
# <a name="overview-azure-logic-apps-preview"></a>概要:Azure Logic Apps プレビュー

> [!IMPORTANT]
> この機能はパブリック プレビュー段階であり、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Logic Apps プレビューを使用すると、**Logic App (プレビュー)** の新しいリソースの種類を使用して、シングルテナントのロジック アプリを作成および実行し、アプリ、データ、クラウド サービスおよびシステムにわたる自動化および統合ソリューションを構築できます。 このシングルテナントの種類のロジック アプリを使用すると、Azure だけでなく、Docker コンテナーを含むさまざまなホスティング環境でデプロイおよび実行できる、移植性、より優れたパフォーマンス、および柔軟性がある、再設計された Azure Logic Apps プレビュー ランタイムを利用する [*ステートフル* および *ステートレス* ワークフロー](#stateful-stateless)を複数構築できます。

どうすればよいでしょうか。 再設計されたランタイムには [Azure Functions 機能拡張モデル](../azure-functions/functions-bindings-register.md)が使用されます。このランタイムは、Azure Functions ランタイムの拡張機能としてホストされます。 このアーキテクチャは、Azure Functions が実行されるすべての場所で、シングルテナントの種類のロジック アプリを実行できることを意味します。 再設計されたランタイムをほぼすべてのネットワーク トポロジでホストし、使用可能なコンピューティング サイズを選んでワークフローに必要なワークロードを処理することができます。 詳細については、「[Azure Functions の概要](../azure-functions/functions-overview.md)」および [Azure Functions のトリガーとバインド](../azure-functions/functions-triggers-bindings.md)に関するページを参照してください。

**ロジック アプリ (プレビュー)** リソースの作成は、[Azure portal](create-stateful-stateless-workflows-azure-portal.md) で開始することも、[Azure Logic Apps (プレビュー) 拡張機能を使用して Visual Studio Code でプロジェクトを作成する](create-stateful-stateless-workflows-visual-studio-code.md)ことで行うことができます。 また、Visual Studio Code では、開発環境でワークフローを構築し、"*ローカルで実行する*" ことができます。 シングルテナントの種類のロジック アプリは、ポータルと Visual Studio Code のどちらを使用している場合でも、同じ種類のホスティング環境にデプロイして実行できます。

この概要では次の項目について説明します。

* [Azure Logic Apps プレビュー、Azure Logic Apps マルチテナント環境、および統合サービス環境の違い](#preview-differences)。

* [ステートフルおよびステートレス ワークフロー](#stateful-stateless)の違い ([入れ子になったステートフルおよびステートレス ワークフロー](#nested-behavior)の動作の違いを含む)。

* [このパブリック プレビューの機能](#public-preview-contents)。

* [価格モデルの動作](#pricing-model)。

* [変更された、制限付き、使用できない、またはサポートされていない機能](#limited-unavailable-unsupported)。

* [Azure Logic Apps プレビューの制限](#limits)。

詳細については、これらの他のトピックを参照してください。

* [あらゆる場所で実行される Azure Logic Apps - ランタイムの詳細](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps パブリック プレビューに関する既知の問題 (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Azure Logic Apps プレビューの違い

Azure Logic Apps プレビュー ランタイムには、[Azure Functions](../azure-functions/functions-overview.md) 機能拡張が使用されます。このランタイムは、Azure Functions ランタイムの拡張機能としてホストされます。 このアーキテクチャは、Azure Functions が実行されるすべての場所で、シングルテナントの種類のロジック アプリを実行できることを意味します。 Azure Logic Apps プレビューのランタイムをほぼすべてのネットワーク トポロジでホストし、使用可能なコンピューティング サイズを選択してワークフローに必要なワークロードを処理することができます。 Azure Functions 拡張機能の詳細については、[Web ジョブ SDK: カスタムの入力および出力バインドの作成](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)に関するページを参照してください。

この新しいアプローチの場合、Azure Logic Apps プレビュー ランタイムとワークフローの両方が、同時にパッケージ化できるアプリの一部となります。 この機能を使用すると、ホスティング環境に成果物をコピーしてアプリを起動するだけで、ワークフローをデプロイして実行できます。 また、このアプローチの場合、運用環境に変更をデプロイする前に必要なテストと検証を行うために、ワークフロー プロジェクトに関連するデプロイ パイプラインを構築するためのより標準化されたエクスペリエンスが提供されます。 詳細については、「[あらゆる場所で実行される Azure Logic Apps - ランタイムの詳細](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)」を参照してください。

次の表は、ワークフローが実行されている環境に基づいて、リソースを共有する方法の違いを簡単にまとめたものです。 制限の違いについては、[Azure Logic Apps プレビューの制限](#limits)に関するページを参照してください。

| 環境 | リソースの共有と消費 |
|-------------|----------------------------------|
| Azure Logic Apps (マルチテナント) | "*複数のテナントの顧客からの*" ワークフローでは、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが使用されます。 |
| Azure Logic Apps (プレビュー、シングルテナント) | "*同じロジック アプリのシングルテナント*" のワークフローでは、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが使用されます。 |
| 統合サービス環境 (プレビューでは使用不可) | "*同じ環境*" のワークフローでは、同じ処理 (コンピューティング)、ストレージ、ネットワークなどを共有します。 |
|||

ただし、マルチテナントの Azure Logic Apps 拡張機能を使用して、マルチテナントの種類のロジック アプリを Azure portal と Visual Studio Code で引き続き作成できます。 開発エクスペリエンスは、マルチテナントとシングルテナントの種類のロジック アプリ間で異なりますが、お使いの Azure サブスクリプションには両方の種類を含めることができます。 お使いの Azure サブスクリプションにデプロイされているすべてのロジック アプリを表示してアクセスすることは可能ですが、それらのアプリはそれ独自のカテゴリとセクションにまとめられています。

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>ステートフルおよびステートレス ワークフロー

シングルテナントの種類のロジック アプリでは、同じロジック アプリ内にこれらの種類のワークフローを作成できます。

* *ステートレス*

  前のイベントのデータを保持、確認、または参照する必要がある場合は、ステートフル ワークフローを作成します。 これらのワークフローで、各アクションとその状態の入出力が外部ストレージに保存されます。これにより、各実行が完了した後にその実行の詳細と履歴を確認できます。 ステートフル ワークフローでは、サービス停止が発生した場合に高い回復性を実現できます。 サービスとシステムが復元された後に、中断された実行を保存済みの状態から再構築し、ワークフローを再実行して完了することができます。 ステートフルなワークフローは、最大 1 年間実行を継続できます。

* *ステートレス*

  後で確認できるように外部ストレージに前のイベントのデータを保存、確認、または参照する必要がない場合は、ステートレス ワークフローを作成します。 これらのワークフローでは、このデータを外部ストレージに転送するのではなく、各アクションとその状態の入出力を "*メモリ内でのみ*" 保存します。 その結果、ステートレス ワークフローで通常は 5 分未満に実行時間が短縮され、パフォーマンスが高速化されて応答時間が短くなり、スループットが向上し、実行コストが削減されます。これは、実行の詳細と履歴が外部ストレージに保持されないためです。 しかし、サービス停止が発生した場合、中断された実行は自動的には復元されないため、呼び出し元は中断された実行を手動で再送信する必要があります。 これらのワークフローは同期的にのみ実行できます。

  デバッグをより容易にするために、ステートレス ワークフローの実行履歴を有効にし (この場合、パフォーマンスに何らかの影響があります)、その後、完了時に実行履歴を無効にすることができます。 詳細については、[Visual Studio Code でのステートフルおよびステートレス ワークフローの作成](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)または [Azure portal でのステートフルおよびステートレス ワークフローの作成](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)に関するページを参照してください。

  > [!NOTE]
  > ステートレスなワークフローでは、現在、トリガーではなく Azure にデプロイされている [マネージド コネクタ](../connectors/apis-list.md#managed-api-connectors)に対する *アクション* のみがサポートされています。 ワークフローを開始するには、[組み込みの Request、Event Hubs、または Service Bus トリガー](../connectors/apis-list.md#built-ins)を選択します。 これらのトリガーは、Azure Logic Apps プレビュー ランタイムでネイティブに実行されます。 制限付き、使用できない、またはサポートされていないトリガー、アクション、コネクタの詳細については、「[変更された、制限付き、使用できない、またはサポートされていない機能](#limited-unavailable-unsupported)」を参照してください。

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>ステートフルおよびステートレス ワークフローの入れ子になった動作の違い

[Request トリガー](../connectors/connectors-native-reqres.md)、[HTTP Webhook トリガー](../connectors/connectors-native-webhook.md)、または [ApiConnectionWebhook 型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)であり HTTPS 要求を受信できるマネージド コネクタ トリガーを使用することによって、同じ **ロジック アプリ (プレビュー)** リソースに存在する他のワークフローから [ワークフローを呼び出せるようにする](../logic-apps/logic-apps-http-endpoint.md)ことができます。

親ワークフローで子ワークフローが呼び出された後、入れ子になったワークフローで従うことができる動作パターンを次に示します。

* 非同期ポーリング パターン

  親は最初の呼び出しに対する応答を待機せず、子が実行を終了するまで、子の実行履歴を継続的にチェックします。 既定では、ステートフルなワークフローはこのパターンに従います。これは、[要求タイムアウト制限](../logic-apps/logic-apps-limits-and-config.md)を超える可能性がある、長時間実行される子ワークフローに適しています。

* 同期パターン ("ファイア アンド フォーゲット")

  子は `202 ACCEPTED` 応答を直ちに返すことによって呼び出しを確認し、親は子の結果を待たずに次のアクションに進みます。 代わりに、子の実行が終了すると、親は結果を受信します。 応答アクションを含まない子のステートフルなワークフローは、常に同期パターンに従います。 子ワークフローがステートフルである場合は、実行履歴を確認することができます。

  この動作を有効にするには、ワークフローの JSON 定義で、`operationOptions` プロパティを `DisableAsyncPattern` に設定します。 詳細については、[トリガーとアクションの種類 (操作オプション)](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) に関するページを参照してください。

* トリガーと待機

  子ワークフローがステートレスである場合、親は子からの結果を返す応答を待機します。 このパターンは、組み込みの [HTTP トリガーまたはアクション](../connectors/connectors-native-http.md)を使用して子ワークフローを呼び出す場合と同様に機能します。 応答アクションを含まないステートレスな子ワークフローは直ちに `202 ACCEPTED` 応答を返しますが、親は次のアクションに進む前に子が終了するまで待機します。 これらの動作は、ステートレスな子ワークフローにのみ適用されます。

次の表では、親と子がステートフル、ステートレス、または混合のどのワークフロー型であるかに基づいて、子ワークフローの動作を指定します。

| 親ワークフロー | 子ワークフロー | 子の動作 |
|-----------------|----------------|----------------|
| ステートレス | ステートレス | `"operationOptions": "DisableAsyncPattern"` 設定を使用した非同期または同期 |
| ステートレス | ステートレス | トリガーと待機 |
| ステートレス | ステートレス | Synchronous |
| ステートレス | ステートレス | トリガーと待機 |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>機能

Azure Logic Apps プレビューには、現在および追加の機能が多数含まれています。以下に例を示します。

* サービスとしてのソフトウェア (SaaS) およびサービスとしてのプラットフォーム (PaaS) のアプリとサービス用の [400 以上のコネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors)や、オンプレミス システム用のコネクタから、ロジック アプリとそのワークフローを作成できます。

  * 一部のマネージド コネクタは組み込みバージョンとして使用できるようになりました。これは、組み込みのトリガーおよびアクション (要求トリガーや HTTP アクションなど) と同様に動作し、Azure Logic Apps プレビュー ランタイムでネイティブに実行されます。 たとえば、これらの新しい組み込みコネクタには、Azure Service Bus、Azure Event Hubs、SQL Server、MQ などがあります。

    > [!NOTE]
    > 組み込みの SQL Server コネクタの場合、 **[クエリの実行]** アクションのみが、[オンプレミス データ ゲートウェイ](logic-apps-gateway-connection.md)を必要とせずに Azure 仮想ネットワークに直接接続できます。

  * 必要なすべてのサービス用に自分独自の組み込みコネクタを作成するには、[プレビュー リリースの機能拡張フレームワーク](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)を使用します。 Azure Service Bus や SQL Server などの組み込みコネクタと似た、しかしプレビューでは現在サポートされていない[カスタム コネクタ](../connectors/apis-list.md#custom-apis-and-connectors)とは異なるこれらのコネクタでは、より高いスループット、短い待機時間、ローカル接続が実現され、プレビュー ランタイムと同じプロセスでネイティブに実行されます。

    作成機能は現在 Visual Studio Code でのみ使用できますが、既定では有効になっていません。 これらのコネクタを作成するには、[自分のプロジェクトを拡張バンドルベース (Node.js) から NuGet パッケージベース (.NET) に切り替え](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring)ます。 詳細については、[あらゆる場所で実行される Azure Logic Apps - 組み込みコネクタの拡張機能](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)に関するページを参照してください。

  * 統合アカウントを使わずに、Liquid 操作と XML 操作に B2B アクションを使用することができます。 これらのアクションを使用するには、Liquid マップ、XML マップ、または XML スキーマが必要です。これらは、Azure portal でのそれぞれのアクションを通じてアップロードすることも、Visual Studio Code プロジェクトの **Artifacts** フォルダーに、それぞれの **Maps** および **Schemas** フォルダーを使用して追加することもできます。

  * Azure Logic Apps サービスがクラウド接続のランタイム エンドポイントに要求を送信するために使用できる Shared Access Signature (SAS) 接続文字列を生成するため、どこからでも実行できるロジック アプリを作成してデプロイします。 Azure にデプロイするときにこれらの値を Azure Key Vault に簡単に格納できるように、Logic Apps サービスによりこれらの接続文字列が他のアプリケーション設定と共に保存されます。

    > [!NOTE]
    > 既定では、**ロジック アプリ (プレビュー)** リソースには、実行時に接続を認証するために自動的に有効にされる [システム割り当てマネージド ID](../logic-apps/create-managed-service-identity.md) があります。 この ID は、接続の作成時に使用する認証資格情報または接続文字列とは異なります。 この ID を無効にした場合、接続は実行時に機能しません。 この設定を表示するには、ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。

* メモリ内でのみ実行されるステートレス ワークフローを使用してロジック アプリを作成します。これにより、処理がより迅速になり、応答がより速くなり、スループットが向上し、実行にかかるコストも少なくなります。これは、実行履歴やアクション間のデータが外部ストレージに保持されないためです。 必要に応じて、デバッグを容易にするために実行履歴を有効にすることができます。 詳細については、「[ステートフルおよびステートレスのロジック アプリ](#stateful-stateless)」を参照してください。

* Visual Studio Code 開発環境でローカルにロジック アプリとそのワークフローを実行、テスト、デバッグします。

  ロジック アプリを実行してテストする前に、ワークフローの **workflow.json** ファイル内にブレークポイントを追加して使用することで、デバッグをより簡単に行うことができます。 しかし、ブレークポイントは、現時点ではトリガーではなく、アクションに対してのみサポートされます。 詳細については、[Visual Studio Code でのステートフルおよびステートレス ワークフローの作成](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)に関するページを参照してください。

* ロジック アプリとそのワークフローを Visual Studio Code から、Azure や [Docker コンテナー](/dotnet/core/docker/introduction)などのさまざまなホスティング環境に直接発行またはデプロイします。

* Azure サブスクリプションとロジック アプリの設定でサポートされている場合は、[Application Insights](../azure-monitor/app/app-insights-overview.md) を使用してロジック アプリに対して診断ログとトレース機能を有効にします。

* [Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md)を使用して、ロジック アプリを作成してデプロイする場合の Azure Functions と同様に、Azure 仮想ネットワークとのプライベートな接続や統合などのネットワーク機能にアクセスします。 詳細については、次のトピックをご覧ください。

  * [Azure Functions のネットワーク オプション](../azure-functions/functions-networking-options.md)

  * [あらゆる場所で実行される Azure Logic Apps - Azure Logic Apps プレビューによるネットワークの可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* シングルテナントの **Logic Apps (プレビュー)** リソースの個々のワークフローで使用されるマネージド接続用のアクセス キーを再生成します。 このタスクでは、[マルチテナントの **Logic Apps** のリソースに対するのと同じ手順を実行しますが、ロジック アプリのリソース レベルではなく、個々のワークフロー レベルで実行します](logic-apps-securing-a-logic-app.md#regenerate-access-keys)。

* マルチテナントのデザイナーと同じ手順で、シングルテナントのデザイナーに並列分岐を追加します。

詳細については、「[変更された、制限付き、使用できない、またはサポートされていない機能](#limited-unavailable-unsupported)」と、[GitHub の Logic Apps パブリック プレビューでの既知の問題に関するページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)を参照してください。

<a name="pricing-model"></a>

## <a name="pricing-model"></a>価格モデル

Azure portal でシングルテナントの種類のロジック アプリを作成するか、Visual Studio Code からデプロイする場合は、お使いのロジック アプリで使用するホスティング プラン ([App Service または Premium](../azure-functions/functions-scale.md)) を選ぶ必要があります。 このプランによって、ロジック アプリの実行に適用される価格モデルが決まります。 App Service プランを選択した場合は、[価格レベル](../app-service/overview-hosting-plans.md)も選ぶ必要があります。

"*ステートフル*" ワークフローには [外部ストレージ](../azure-functions/storage-considerations.md#storage-account-requirements)が使用されるため、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)は、Azure Logic Apps プレビュー ランタイムで実行されるストレージ トランザクションに適用されます。 たとえば、キューはスケジュール設定に使用され、テーブルと BLOB はワークフローの状態の格納に使用されます。

> [!NOTE]
> パブリック プレビュー期間中は、App Service でロジック アプリを実行しても、選択したプランの他に "*追加*" 料金は発生しません。

シングルテナントのリソースの種類に該当する価格モデルの詳細については、次のトピックを参照してください。

* [Azure Functions のスケールとホスティング](../azure-functions/functions-scale.md)
* [Azure App Service でアプリをスケールアップする](../app-service/manage-scale-up.md)
* [Azure Functions の価格の詳細](https://azure.microsoft.com/pricing/details/functions/)
* [App Service の価格の詳細](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure Storage の価格の詳細](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>変更された、制限付き、使用できない、またはサポートされていない機能

Azure Logic Apps プレビューでは、これらの機能が変更されているか、現在制限されているか、使用できないか、またはサポートされていません。

* **OS のサポート**: 現在、Visual Studio Code のデザイナーは Linux OS で動作しませんが、Logic Apps Preview ランタイムを使用するロジック アプリを Linux ベースの仮想マシンにデプロイすることはできます。 現在のところ、Windows または macOS の Visual Studio Code でロジック アプリを構築してから、Linux ベースの仮想マシンにデプロイすることができます。

* **トリガーとアクション**: 組み込みのトリガーとアクションは、Azure Logic Apps プレビュー ランタイムでネイティブに実行されますが、マネージド コネクタは Azure にデプロイされます。 スライディング ウィンドウやバッチなど、一部の組み込みトリガーは使用できません。

  ワークフローを開始するには、[組み込みの Recurrence、Request、HTTP、HTTP Webhook、Event Hubs、または Service Bus トリガー](../connectors/apis-list.md)を使用します。 デザイナーでは、組み込みのトリガーとアクションは **[組み込み]** タブに表示されますが、マネージド コネクタのトリガーとアクションは **[Azure]** タブに表示されます。

  > [!NOTE]
  > Visual Studio Code でローカルに実行するには、Webhook ベースのトリガーとアクションに追加の設定が必要です。 詳細については、[Visual Studio Code でのステートフルおよびステートレス ワークフローの作成](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)に関するページを参照してください。

  * "*ステートレス ワークフロー*" の場合、トリガーを選択したときに **[Azure]** タブは表示されません。これは、選択できるのが [マネージド コネクタのトリガーではなく、"*アクション*"](../connectors/apis-list.md#managed-api-connectors) のみであるためです。 Azure によってデプロイされたマネージド コネクタをステートレス ワークフローに対して有効にすることはできますが、デザイナーには、追加するマネージド コネクタのトリガーはいずれも表示されません。

  * "*ステートフル ワークフロー*" の場合、以下の一覧に使用できないと示されているトリガーとアクションを除き、[マネージド コネクタのトリガーとアクション](../connectors/apis-list.md#managed-api-connectors)の両方を使用できます。

  * これらのトリガーとアクションは、変更されたか、現在制限されているか、サポートされていないか、使用できません。

    * [オンプレミス データ ゲートウェイの "*トリガー*"](../connectors/apis-list.md#on-premises-connectors) は使用できませんが、ゲートウェイのアクションは使用 "*できます*"。

    * 組み込みアクションである [[Azure Functions] - [Azure 関数を選択する]](logic-apps-azure-functions.md) は、 **[Azure Function Operations - Call an Azure function]\([Azure 関数の操作] - [Azure 関数を呼び出す]\)** になりました。 このアクションは、現在、**HTTP トリガー** テンプレートから作成された関数に対してのみ機能します。

      Azure portal では、ユーザー エクスペリエンスを通じて接続を作成することによってアクセスできる HTTP トリガー関数を選択できます。 関数アクションの JSON 定義をコード ビューまたは **workflow.json** ファイルで調べる場合は、そのアクションで `connectionName` 参照を使用して関数を参照します。 このバージョンの場合、関数の情報が接続として抽象化されます。これは、接続の作成後に使用できるようになるプロジェクトの **connections.json** ファイルで確認できます。

      > [!NOTE]
      > シングルテナントのバージョンでは、関数アクションではクエリ文字列認証のみがサポートされています。 Azure Logic Apps プレビューの場合、接続の確立時に関数から既定のキーが取得され、そのキーがアプリの設定に格納され、関数を呼び出すときにそのキーが認証に使用されます。
      >
      > マルチテナント バージョンと同様に、(たとえば、ポータルの Azure Functions エクスペリエンスを通じて) このキーを更新した場合、無効なキーが原因で関数アクションは機能しません。 この問題を解決するには、呼び出す関数への接続を再作成するか、新しいキーを使用してアプリの設定を更新する必要があります。

    * 組み込みアクションである [[インライン コード] - [JavaScript コードの実行]](logic-apps-add-run-inline-code.md) は、 **[Inline Code Operations - Run in-line JavaScript]\([インライン コードの操作] - [インライン JavaScript の実行]\)** になりました。

      * インライン コードの操作アクションでは、統合アカウントは不要になりました。

      * macOS および Linux の場合、**インライン コードの操作** は現在、Visual Studio Code で Azure Logic Apps (プレビュー) 拡張機能を使用する場合にサポートされます。

      * **インライン コード操作** アクションで変更を行う場合に、ロジック アプリを再起動する必要がなくなりました。

      * **インライン コード操作** アクションには [更新された制限](logic-apps-overview-preview.md#inline-code-limits)があります。

    * [統合アカウントに対する組み込みの B2B トリガーとアクション](../connectors/apis-list.md#integration-account-connectors)の一部は使用できません。たとえば、**フラット ファイル** のエンコードやデコード アクションです。

    * 組み込みアクションである [[Azure Logic Apps - Choose a Logic App workflow]\([Azure Logic Apps] - [ロジック アプリ ワークフローを選択する]\)](logic-apps-http-endpoint.md) は、 **[Workflow Operations - Invoke a workflow in this workflow app]\([ワークフロー操作] - [このワークフロー アプリでワークフローを呼び出す]\)** になりました。

* 現在、プレビューで[カスタム コネクタ](../connectors/apis-list.md#custom-apis-and-connectors)はサポートされていません。

* **ホスティング プランの可用性**: Azure portal でシングルテナントの **Logic App (プレビュー)** のリソースの種類を作成するか、Visual Studio Code からデプロイするかにかかわらず、Azure では Premium または App Service ホスティング プランのみを使用できます。 このリソースの種類のデプロイについては、従量課金ホスティング プランは使用できず、サポートもされていません。 Visual Studio Code から Docker コンテナーにデプロイできますが、[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) にはデプロイできません。

* **Visual Studio Code でのデバッグのブレークポイント**: ワークフローの **workflow.json** ファイル内にブレークポイントを追加して使用することはできますが、ブレークポイントは現時点ではトリガーではなく、アクションに対してのみサポートされます。 詳細については、[Visual Studio Code でのステートフルおよびステートレス ワークフローの作成](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)に関するページを参照してください。

* **ズーム コントロール**: 現在、デザイナーでズーム コントロールは使用できません。

* **トリガーの履歴と実行履歴**: **ロジック アプリ (プレビュー)** のリソースの種類の場合、トリガーの履歴と実行履歴は、Azure portal でロジック アプリ レベルではなくワークフロー レベルで表示されます。 この履歴データを見つけるには、これらの手順に従います。

   * 実行履歴を表示するには、ロジック アプリでワークフローを開きます。 ワークフローのメニューで、 **[開発者]** の下の **[モニター]** を選択します。

   * トリガーの履歴を確認するには、ロジック アプリでワークフローを開きます。 ワークフローのメニューで、 **[開発者]** の下の **[トリガー履歴]** を選択します。

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>厳格なネットワークおよびファイアウォールのシナリオでトラフィックを許可する

お使いの環境に、トラフィックを制限する厳しいネットワーク要件またはファイアウォールがある場合、お使いのロジック アプリのワークフローですべてのトリガーまたはアクション接続にアクセスを許可する必要があります。

これらの接続の完全修飾ドメイン名 (FQDN) は、次のトピックの該当セクションでご確認ください。

* [シングルテナント ロジック アプリのファイヤーウォールのアクセス許可 - Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [シングルテナント ロジック アプリのファイヤーウォールのアクセス許可 - Azure portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>更新された制限

Azure Logic Apps プレビューの多くの制限は、[マルチテナント Azure Logic Apps の制限](logic-apps-limits-and-config.md)と同じままですが、Azure Logic Apps プレビューではこれらの制限が変更されています。

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP タイムアウトの制限

1 回の着信呼び出しまたは発信呼び出しの場合、これらのトリガーとアクションのタイムアウト制限は 230 秒 (3.9 分) になります。

* 送信要求: HTTP トリガー、HTTP アクション
* 受信要求: Request トリガー、HTTP Webhook トリガー、HTTP Webhook アクション

これに対して、ロジック アプリとそのワークフローが実行される他の環境におけるこれらのトリガーとアクションのタイムアウト制限は以下のようになります。

* マルチテナント Azure Logic Apps: 120 秒 (2 分)
* 統合サービス環境: 240 秒 (4 分)

詳細については、「[HTTP 制限](logic-apps-limits-and-config.md#http-limits)」を参照してください。

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>マネージド コネクタ

マネージド コネクタは、接続ごとに 1 分あたり 50 の要求に制限されます。 コネクタの調整に関する問題に対処する場合は、「[Azure Logic Apps で調整に関する問題 (429 - "要求が多すぎます" エラー) を処理する](handle-throttling-problems-429-errors.md#connector-throttling)」を参照してください。

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>インライン コードの操作 (JavaScript コードの実行)

1 つのロジック アプリ定義の場合、インライン コードの操作アクションである [**JavaScript コードの実行**](logic-apps-add-run-inline-code.md)にはこれらの更新された制限があります。

* コード文字の最大数は、1,024 文字から 10 万文字に増えています。

* コードを実行する最長期間は、5 秒から 15 秒に増えています。

詳細については、[ロジック アプリ定義の制限](logic-apps-limits-and-config.md#definition-limits)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

* [ Azure portal でステートフルおよびステートレス ワークフローを作成する](create-stateful-stateless-workflows-azure-portal.md)
* [Visual Studio Code でステートフルおよびステートレス ワークフローを作成する](create-stateful-stateless-workflows-visual-studio-code.md)
* [GitHub の Logic Apps パブリック プレビューでの既知の問題に関するページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

また、Azure Logic Apps プレビューでのエクスペリエンスについて、ご意見をお聞かせください。

* バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
* 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/lafeedback)。
