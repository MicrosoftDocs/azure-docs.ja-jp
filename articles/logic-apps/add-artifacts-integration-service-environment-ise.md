---
title: Azure Logic Apps で統合サービス環境 (ISE) に成果物を追加する
description: 統合サービス環境 (ISE) にロジック アプリ、カスタム コネクタ、および統合アカウントを追加して、パブリックすなわち "グローバル" の Azure から分離されたプライベートな状態を維持しながら、Azure 仮想ネットワーク (VNET) にアクセスできるようにします
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 8e2e9d30fa24e56711a0d6800a8ca9a34e53c12e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967221"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps で統合サービス環境 (ISE) に成果物を追加する

[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成したら、ロジック アプリ、統合アカウント、コネクタなどの成果物を追加して、Azure 仮想ネットワーク内のリソースにアクセスできるようにします。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ロジック アプリを実行するために作成した ISE。 ISE がない場合は、[まず ISE を作成します](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>ロジック アプリを作成する

統合サービス環境 (ISE) で実行されるロジック アプリを構築するには、次の手順を実行します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[ロジック アプリ]**  >  **[追加]** の順に選択します。

   ![ISE に新しいロジック アプリを追加する](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   または

   Azure のメイン メニューで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

1. ロジック アプリに使用する名前、Azure サブスクリプション、および Azure リソース グループ (新規または既存) を入力します。

1. **[統合サービス環境]** セクションの **[場所]** 一覧で、ご使用の ISE を選択します。次に例を示します。

   ![統合サービス環境を選択する](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > ロジック アプリを統合アカウントで使用する場合は、それらのロジック アプリと統合アカウントで同じ ISE を使用する必要があります。

1. 引き続き[通常の方法でロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

   ISE を使用するときに、グローバル Logic Apps サービスと比較して、トリガーとアクションがどのように機能するか、およびそれらがどのようにラベル付けされるかの違いについては、[ISE での分離とグローバルの概要](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)に関するページを参照してください。

1. ISE でロジック アプリと API 接続を管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>統合アカウントを作成する

作成時に選択された [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に基づいて、ISE には追加費用なしで特定の統合アカウントの使用が含まれます。 統合サービス環境 (ISE) に存在するロジック アプリは、同じ ISE に存在する統合アカウントのみを参照できます。 そのため、ISE で統合アカウントがロジック アプリと連携するには、統合アカウントとロジック アプリの両方で、場所として "*同じ環境*" を使用する必要があります。 統合アカウントと ISE の詳細については、「[ISE との統合アカウント](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment
)」を参照してください。

ISE を使用する統合アカウントを作成するには、次の手順を実行します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[統合アカウント]**  >  **[追加]** の順に選択します。

   ![ISE に新しい統合アカウントを追加する](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   または

   Azure のメイン メニューから、 **[リソースの作成]**  >  **[統合]**  >  **[統合アカウント]** の順に選択します。

1. 統合アカウントに使用する名前、Azure サブスクリプション、Azure リソース グループ (新規または既存)、および価格レベルを指定します。

1. **[統合サービス環境]** セクションの **[場所]** 一覧から、ロジック アプリで使用しているものと同じ ISE を選択します。次に例を示します。

   ![統合サービス環境を選択する](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [通常の方法でロジック アプリを統合アカウントにリンクします](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. [取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)や[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)などの成果物を統合アカウントに追加して続行します。

1. ISE で統合アカウントを管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE のコネクタを追加する

自分の ISE で使用できる (ただし自分の ISE にはデプロイされない) Microsoft のマネージド コネクタを追加できます。

1. ISE メニューの **[設定]** で、 **[マネージド コネクタ]** を選択します。 ツールバーの **[追加]** を選択します。

   ![マネージド コネクタを表示する](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **[新しいマネージド コネクタの追加]** ウィンドウの **[コネクタの検索]** ボックスの一覧を開きます。 希望するコネクタが利用可能である場合、そのコネクタを選択し、 **[作成]** を選択します。

   ボックスの一覧に表示されるのは、候補となるコネクタのうち、自分の ISE にはデプロイされないコネクタだけです。 自分の ISE に既にデプロイされているコネクタは選択不可の状態で表示されます。

   ![候補となるコネクタを選択する](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>カスタム コネクタを作成する

ISE でカスタム コネクタを使用するには、ISE 内から直接これらのカスタム コネクタを作成します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[カスタム コネクタ]**  >  **[追加]** の順に選択します。

   ![カスタム コネクタの作成](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. カスタム コネクタに使用する名前、Azure サブスクリプション、および Azure リソース グループ (新規または既存) を入力します。

1. **[統合サービス環境]** セクションの **[場所]** 一覧から、ロジック アプリで使用しているものと同じ ISE を選択し、 **[作成]** を選択します。次に例を示します。

   ![統合サービス環境を選択する](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. 新しいカスタム コネクタを選択し、 **[編集]** を選択します。次に例を示します。

   ![カスタム コネクタを選択して編集する](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. [OpenAPI 定義](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)または [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector) から通常の方法でコネクタを作成して続行します。

1. ISE でカスタム コネクタを管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* [統合サービス環境を管理する](../logic-apps/ise-manage-integration-service-environment.md)
