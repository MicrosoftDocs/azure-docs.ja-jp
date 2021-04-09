---
title: 統合サービス環境にリソースを追加する
description: ロジック アプリ、統合アカウント、カスタム コネクタ、およびマネージド コネクタを統合サービス環境 (ISE) に追加する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675206"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps で統合サービス環境 (ISE) にリソースを追加する

[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成したら、ロジック アプリ、統合アカウント、コネクタなどのリソースを追加して、Azure 仮想ネットワーク内のリソースにアクセスできるようにします。 たとえば、ISE の作成後に使用可能になる ISE マネージド コネクタは、ロジック アプリ デザイナーに自動的には表示されません。 これらの ISE コネクタを使用するには、手動で[これらのコネクタを追加して ISE にデプロイ](#add-ise-connectors-environment)し、ロジック アプリ デザイナーに表示されるようにする必要があります。

> [!IMPORTANT]
> ISE でロジック アプリと統合アカウントを連携させるには、両方とも "*同じ ISE*" を場所として使用する必要があります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ロジック アプリを実行するために作成した ISE。 ISE がない場合は、[まず ISE を作成します](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

* ISE にデプロイされたリソースを更新したり、そのようなリソースを作成または追加したりするには、その ISE における所有者または共同作成者のロールが割り当てられているか、ISE に関連付けられている Azure リソース グループまたは Azure サブスクリプションからアクセス許可が継承されている必要があります。 所有者、共同作成者、継承されたアクセス許可のいずれも持たないユーザーには、統合サービス環境の共同作成者ロールまたは統合サービス環境の開発者ロールを割り当てることができます。 詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>ロジック アプリを作成する

統合サービス環境 (ISE) で実行されるロジック アプリを構築するには、次の手順を実行します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[ロジック アプリ]**  >  **[追加]** の順に選択します。

   ![ISE に新しいロジック アプリを追加する](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 作成するロジック アプリに関する情報を指定します。次に例を示します。

   ![[ロジック アプリ] のスクリーンショット。[作成] ウィンドウにサンプル情報が入力されています。](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **名前** | はい | 作成するロジック アプリの名前 |
   | **サブスクリプション** | はい | 使用する Azure サブスクリプションの名前。 |
   | **リソース グループ** | はい | 使用する Azure リソース グループ (新規または既存) の名前 |
   | **場所** | はい | **[統合サービス環境]** で、使用する ISE を選択します (まだ選択していない場合)。 <p><p> **重要**:統合アカウントでロジック アプリを使用するには、どちらも同じ ISE を使用する必要があります。 |
   ||||

1. 完了したら **[作成]** を選択します。

1. 引き続き[通常の方法でロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

   ISE を使用するときに、マルチテナント Logic Apps サービスと比較して、トリガーとアクションがどのように機能するか、およびそれらがどのようにラベル付けされるかの違いについては、[ISE での分離とマルチテナントの概要](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)に関するページを参照してください。

1. ISE でロジック アプリと API 接続を管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>統合アカウントを作成する

作成時に選択された [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に基づいて、ISE には追加費用なしで特定の統合アカウントの使用が含まれます。 統合サービス環境 (ISE) に存在するロジック アプリは、同じ ISE に存在する統合アカウントのみを参照できます。 そのため、ISE で統合アカウントがロジック アプリと連携するには、統合アカウントとロジック アプリの両方で、場所として "*同じ環境*" を使用する必要があります。 統合アカウントと ISE の詳細については、「[ISE との統合アカウント](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)」を参照してください。

ISE を使用する統合アカウントを作成するには、次の手順を実行します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[統合アカウント]**  >  **[追加]** の順に選択します。

   ![ISE に新しい統合アカウントを追加する](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 作成するロジック アプリに関する情報を指定します。次に例を示します。

   ![統合サービス環境を選択する](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **名前** | はい | 作成する統合アカウントの名前 |
   | **サブスクリプション** | はい | 使用する Azure サブスクリプションの名前 |
   | **リソース グループ** | はい | 使用する Azure リソース グループ (新規または既存) の名前 |
   | **価格レベル** | はい | 統合アカウントに使用する価格レベル |
   | **場所** | はい | **[統合サービス環境]** で、ロジック アプリが使用するものと同じ ISE を選択します (まだ選択していない場合)。 <p><p> **重要**:ロジック アプリで統合アカウントを使用するには、どちらも同じ ISE を使用する必要があります。 |
   ||||

1. 完了したら **[作成]** を選択します。

1. [通常の方法でロジック アプリを統合アカウントにリンクします](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. [取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)や[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)などのリソースを統合アカウントに追加して続行します。

1. ISE で統合アカウントを管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE のコネクタを追加する

ISE を作成すると、マネージド ISE コネクタはロジック アプリ デザイナーのコネクタ ピッカーに自動的には表示されません。 これらの ISE コネクタを使用するには、手動でこれらのコネクタを追加して ISE にデプロイし、ロジック アプリ デザイナーに表示されるようにする必要があります。

> [!IMPORTANT]
> マネージド ISE コネクタは現在のところ、[タグ](../azure-resource-manager/management/tag-support.md)に対応していません。 タグ付けを強制するポリシーを設定した場合、ISE コネクタを追加しようとすると、次の例のようなエラーで失敗する可能性があります。
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> そのため、ISE コネクタを追加するには、ポリシーを無効にするか、削除する必要があります。 

1. ISE メニューの **[設定]** で、 **[マネージド コネクタ]** を選択します。 ツールバーの **[追加]** を選択します。

   ![マネージド コネクタを表示する](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **[新しいマネージド コネクタの追加]** ウィンドウの **[コネクタの検索]** ボックスの一覧を開きます。 使用したいが、まだ ISE にデプロイされていない ISE コネクタを選択します。 **［作成］** を選択します

   ![ISE にデプロイする ISE コネクタを選択します](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   選択できるのは、適格であっても、まだ ISE にデプロイされていない ISE コネクタだけです。 自分の ISE に既にデプロイされているコネクタは選択不可の状態で表示されます。

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>カスタム コネクタを作成する

ISE でカスタム コネクタを使用するには、ISE 内から直接これらのカスタム コネクタを作成します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[カスタム コネクタ]**  >  **[追加]** の順に選択します。

   ![カスタム コネクタの作成](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. カスタム コネクタに使用する名前、Azure サブスクリプション、および Azure リソース グループ (新規または既存) を入力します。

1. **[統合サービス環境]** セクションの **[場所]** 一覧から、ロジック アプリで使用しているものと同じ ISE を選択し、 **[作成]** を選択します。次に例を示します。

   ![[Logic Apps カスタム コネクタの作成] ウィンドウのスクリーンショット。サンプル情報が入力されています。](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 新しいカスタム コネクタを選択し、 **[編集]** を選択します。次に例を示します。

   ![カスタム コネクタを選択して編集する](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. [OpenAPI 定義](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)または [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector) から通常の方法でコネクタを作成して続行します。

1. ISE でカスタム コネクタを管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [統合サービス環境を管理する](../logic-apps/ise-manage-integration-service-environment.md)
