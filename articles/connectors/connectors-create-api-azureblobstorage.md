---
title: Azure Blob Storage に接続する
description: Azure Logic Apps を使用して Azure ストレージ アカウントに BLOB を作成して管理します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677725"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Azure Blob Storage に BLOB を作成して管理する

この記事では、ロジック アプリ内から Azure Blob Storage コネクタを使用して Azure ストレージ アカウントに BLOB として格納されているファイルにアクセスして管理する方法を示します。 その方法で、ファイルを管理するためのタスクとワークフローを自動化するロジック アプリを作成できます。 たとえば、ストレージ アカウントに対してファイルを作成、取得、更新、および削除するロジック アプリをビルドできます。

更新されるツールが Azure Web サイト上にあるとします。 それはロジック アプリのトリガーとして機能します。 そのイベントが発生したときに、BLOB ストレージ コンテナー内のいくつかのファイルをロジック アプリに更新させることができます。更新はロジック アプリで実行されるアクションです。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。 コネクタ固有の技術情報については、[Azure Blob Storage コネクタ リファレンス](/connectors/azureblobconnector/)に関する記事を参照してください。

> [!IMPORTANT]
> 同じリージョンにある場合、ロジック アプリはファイア ウォールの背後にあるストレージ アカウントに直接アクセスできません。 回避策として、お使いのロジック アプリとストレージアカウントを別のリージョンに格納してください。 Azure Logic Apps からファイアウォールの背後にあるストレージ アカウントへのアクセスを有効にする方法の詳細については、このトピックの後半の「[ファイアウォールの背後にあるストレージ アカウントにアクセスする](#storage-firewalls)」を参照してください。

<a name="blob-storage-limits"></a>

## <a name="limits"></a>制限

* 既定では、Azure Blob Storage アクションは *50 MB 以下* のファイルの読み取りまたは書き込みが可能です。 Azure Blob Storage のアクションは、50 MB から 1024 MB までのファイルを処理するため、[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)をサポートしています。 **BLOB コンテンツの取得** アクションは、暗黙的にチャンクを使用します。

* Azure Blob Storage トリガーではチャンクはサポートされていません。 ファイルのコンテンツを要求すると、トリガーによって 50 MB 以下のファイルのみが選択されます。 50 MB より大きいファイルを取得するには、次のパターンに従います。

  * ファイルのプロパティを返す Azure Blob Storage トリガーを使用します (**BLOB が追加または変更されたとき (プロパティのみ)** など)。

  * Azure Blob Storage トリガーの **BLOB コンテンツの取得** アクションに従います。これは、暗黙的にチャンクを使用してファイル全体を読み取ります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [Azure ストレージ アカウントとストレージ コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md)

* Azure Blob Storage にアクセスする必要があるロジック アプリ。 Azure Blob Storage トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Blob Storage トリガーを追加する

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

この例では、BLOB のプロパティがストレージ コンテナーに追加されるか BLOB のプロパティが更新された場合に **BLOB が追加または変更されたとき (プロパティのみ)** トリガーを使用して、ロジック アプリのワークフローを開始する方法を示します。

1. [Azure Portal](https://portal.azure.com) または Visual Studio で、Logic Apps デザイナーを開いて、空のロジック アプリを作成します。 この例では、Azure Portal を使用します。

2. 検索ボックスに、フィルターとして「azure blob」と入力します。 トリガーの一覧から、目的のトリガーを選択します。

   この例では、**BLOB が追加または変更されたとき (プロパティのみ)** トリガーを使用します。

   ![Azure Blob Storage トリガーを選択する](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. 接続の詳細の入力を求められたら、[BLOB ストレージ接続を今すぐ作成](#create-connection)します。 接続が既に存在する場合は、トリガーに必要な情報を指定します。

   この例では、監視するコンテナーとフォルダーを選択します。

   1. **[コンテナー]** ボックスで、フォルダー アイコンを選択します。

   2. フォルダーの一覧で、右山かっこ ( **>** ) を選択し、目的のフォルダーを参照して選択します。

      ![トリガーで使用するストレージ フォルダーを選択する](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. トリガーがフォルダーの変更をチェックする間隔と頻度を選択します。

4. 操作が完了したら、デザイナーのツールバーで、**[保存]** を選択します。

5. トリガーの結果を使用して実行するタスクの 1 つまたは複数のアクションをロジック アプリに追加する操作に進みます。

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>BLOB ストレージ アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例では、ロジック アプリは、[繰り返しトリガー](../connectors/connectors-native-recurrence.md)を使用して起動されます。

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。 この例では、Azure Portal を使用します。

2. Logic Apps デザイナーのトリガーまたはアクションで、**[新しいステップ]** を選択します。

   ![ロジック アプリのワークフローに新しいステップを追加する](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   既存のステップの間にアクションを追加するには、接続矢印の上にマウスを移動します。 表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

3. 検索ボックスに、フィルターとして「azure blob」と入力します。 アクションの一覧から、目的のアクションを選択します。

   この例では、**BLOB コンテンツの取得** アクションを使用します。

   ![Azure Blob Storage アクションを選択する](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. 接続の詳細の入力を求められたら、[Azure Blob Storage 接続を今すぐ作成](#create-connection)します。
接続が既に存在する場合は、アクションに必要な情報を指定します。

   この例では、目的のファイルを選択します。

   1. **[BLOB]** ボックスで、フォルダー アイコンを選択します。
  
      ![アクションで使用するストレージ フォルダーを選択する](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. BLOB の **ID** 番号に基づいて目的のファイルを見つけて選択します。 この **ID** 番号は、前に説明した Blob Storage トリガーで返される BLOB のメタデータ内で見つけることができます。

5. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。
ロジック アプリをテストするには、選択したフォルダーに BLOB が含まれていることを確認します。

この例では、BLOB の内容の取得のみが実行されます。 内容を表示するには、別のコネクタを使用して、BLOB のファイルを作成する別のアクションを追加します。 たとえば、BLOB の内容に基づいてファイルを作成する OneDrive アクションを追加します。

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>ストレージ アカウントに接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 接続を作成するよう求められたら、この情報を入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **Connection Name** | はい | <*connection-name*> | 作成する接続の名前 |
   | **ストレージ アカウント** | はい | <*storage-account*> | リストからストレージ アカウントを選択します。 |
   ||||

   次に例を示します。

   ![Azure BLOB ストレージ アカウント接続を作成する](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. 準備ができたら、 **[作成]** を選択します

1. 接続を作成したら、続いて [Blob Storage トリガーを追加](#add-trigger)するか、[Blob Storage アクションを追加](#add-action)します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、トリガー、アクション、制限などのこのコネクタの技術的詳細については、[コネクタの参照ページ](/connectors/azureblobconnector/)を参照してください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)が使用されます。

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>ファイアウォールの背後にあるストレージ アカウントにアクセスする

[ファイアウォールとファイアウォール規則](../storage/common/storage-network-security.md)を使用してアクセスを制限することで、Azure ストレージ アカウントにネットワーク セキュリティを追加できます。 ただし、この設定では、ストレージ アカウントへのアクセスを必要とする Azure およびその他の Microsoft サービスにおいて問題が生じます。 データセンター内のローカル通信では内部 IP アドレスが抽象化されるため、IP 制限を使用してファイアウォール規則を設定することはできません。 詳細については、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](../storage/common/storage-network-security.md)に関する記事を参照してください。

Azure Blob Storage コネクタまたは他のソリューションを使用して Azure Logic Apps からファイアウォールの背後にあるストレージ アカウントにアクセスするためのさまざまなオプションを次に示します。

* Azure Storage BLOB コネクタ

  * [他のリージョンにあるストレージ アカウントにアクセスする](#access-other-regions)
  * [信頼された仮想ネットワーク経由でストレージ アカウントにアクセスする](#access-trusted-virtual-network)

* その他のソリューション

  * [マネージド ID を使用して、信頼されたサービスとしてストレージ アカウントにアクセスする](#access-trusted-service)
  * [Azure API Management 経由でストレージ アカウントにアクセスする](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>同じリージョンのストレージ アカウントへのアクセスに関する問題

ロジック アプリは、同じリージョンにある場合、ファイア ウォールの背後にあるストレージ アカウントに直接アクセスできません。 回避策として、お使いのロジック アプリとお使いのストレージ アカウントを別のリージョンに格納し、[ご自分のリージョンのマネージド コネクタが送信 IP アドレスにアクセス](../logic-apps/logic-apps-limits-and-config.md#outbound)できるようにします。

> [!NOTE]
> このソリューションは、Azure Table Storage コネクタと Azure Queue Storage コネクタには該当しません。 お使いの Table Storage または Queue Storage にアクセスするには、代わりに組み込みの HTTP トリガーとアクションを使用してください。

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>信頼された仮想ネットワーク経由でストレージ アカウントにアクセスする

自分が管理する Azure 仮想ネットワークにストレージ アカウントを配置した後、その仮想ネットワークを信頼された仮想ネットワークの一覧に追加することができます。 [信頼された仮想ネットワーク](../virtual-network/virtual-networks-overview.md)を介して対象のロジック アプリがストレージ アカウントにアクセスできるようにするには、仮想ネットワーク内のリソースに接続できる[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) にそのロジック アプリをデプロイする必要があります。 その後、その ISE 内のサブネットを、信頼された項目の一覧に追加できます。 Blob Storage コネクタなどの Azure Storage コネクタでは、ストレージ コンテナーに直接アクセスできます。 この設定は、ISE からサービス エンドポイントを使用するのと同じエクスペリエンスです。

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>マネージド ID を使用して、信頼されたサービスとしてストレージ アカウントにアクセスする

Microsoft の信頼されたサービスがファイアウォールを介してストレージ アカウントにアクセスできるようにするには、それらのサービスについてストレージ アカウントで例外を設定します。 この解決策によって、[認証用のマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) をサポートする Azure サービスは、信頼されたサービスとして、ファイアウォールの背後にあるストレージ アカウントにアクセスできます。 具体的には、グローバルなマルチテナント Azure 内のロジック アプリがこれらのストレージ アカウントにアクセスできるよう、まずロジック アプリで[マネージド ID のサポートを有効にします](../logic-apps/create-managed-service-identity.md)。 次に、自分のロジック アプリで HTTP アクションまたはトリガーを使用し、[ロジック アプリのマネージド ID を使用するよう認証の種類を設定します](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 このシナリオでは、HTTP アクションまたはトリガー "*のみ*" を使用できます。

例外とマネージド ID のサポートを設定するには、これらの一般的な手順に従います。

1. 自分のストレージ アカウントの **[設定]** で、**[ファイアウォールと仮想ネットワーク]** を選択します。 **[許可するアクセス元]** で、**[選択されたネットワーク]** オプションを選択して、関連する設定を表示します。

1. **[例外]** で、**[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択し、**[保存]** を選択します。

   ![Microsoft の信頼されたサービスを許可する例外を選択する](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. 自分のロジック アプリの設定で、[マネージド ID のサポートを有効にします](../logic-apps/create-managed-service-identity.md)。

1. 自分のロジック アプリのワークフローで、ストレージ アカウントまたはエンティティにアクセスするための HTTP アクションまたはトリガーを追加および設定します。

   > [!IMPORTANT]
   > Azure ストレージ アカウントへの発信 HTTP アクションまたはトリガー呼び出しの場合は、要求ヘッダーに `x-ms-version` プロパティと、ストレージ アカウントで実行したい操作の API バージョンが含まれていることを確認します。 詳細については、「[マネージド ID を使用してアクセスを認証する](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)」と「[Azure Storage サービスのバージョン管理](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)」を参照してください。

1. そのアクションで、認証に使用する[マネージド ID を選択](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)します。

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Azure API Management 経由でストレージ アカウントにアクセスする

[API Management](../api-management/api-management-key-concepts.md) に専用階層を使用する場合、API Management を使用し、ファイアウォールを介して後者の IP アドレスを許可することで、Storage API に面することができます。 基本的には、API Management によって使用される Azure 仮想ネットワークをストレージ アカウントのファイアウォール設定に追加します。 その後、API Management アクションまたは HTTP アクションを使用し、Azure Storage API を呼び出すことができます。 ただし、このオプションを選択した場合、認証プロセスを自分で処理する必要があります。 詳細については、[単純なエンタープライズ統合アーキテクチャ](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)を参照してください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。