---
title: オンプレミスのデータ ソースにアクセスする
description: Azure でデータ ゲートウェイ リソースを作成することにより、Azure Logic Apps からオンプレミスのデータ ソースに接続します
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 356e63bb0a749ad0f41d886e75971e9b05c7f9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99218996"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps からオンプレミスのデータ ソースに接続する

["*オンプレミス データ ゲートウェイ*" をローカル コンピューターにインストール](../logic-apps/logic-apps-gateway-install.md)した後、ロジック アプリからオンプレミスのデータ ソースにアクセスするには、その前に Azure でゲートウェイ インストール用のゲートウェイ リソースを作成する必要があります。 その後は、Azure Logic Apps で利用できる[オンプレミスのコネクタ](../connectors/apis-list.md#on-premises-connectors)に使用するトリガーとアクションで、このゲートウェイ リソースを選択できます。 Azure Logic Apps では、データ ゲートウェイを介した読み取り操作と書き込み操作がサポートされます。 ただし、これらの操作には、[ペイロードのサイズに制限](/data-integration/gateway/service-gateway-onprem#considerations)があります。

この記事では、以前に[ローカル コンピューターにインストールされたゲートウェイ](../logic-apps/logic-apps-gateway-install.md)の Azure ゲートウェイ リソースを作成する方法を示します。 ゲートウェイの詳細については、「[ゲートウェイのしくみ](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)」を参照してください。

> [!TIP]
> ゲートウェイを使用せずに、Azure 仮想ネットワーク内のオンプレミス リソースに直接アクセスするには、代わりに [*統合サービス環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)を作成することを検討します。 

他のサービスでゲートウェイを使用する方法については、次の記事を参照してください。

* [Microsoft Power Automate オンプレミス データ ゲートウェイ](/power-automate/gateway-reference)
* [Microsoft Power BI オンプレミス データ ゲートウェイ](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps オンプレミス データ ゲートウェイ](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>サポートされるデータ ソース

Azure Logic Apps では、オンプレミス データ ゲートウェイで、これらのデータ ソースの[オンプレミス コネクタ](../connectors/apis-list.md#on-premises-connectors)がサポートされます。

* BizTalk Server 2016
* ファイル システム
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

REST または SOAP を使用すると、HTTP または HTTPS 経由でデータ ソースに接続する[カスタム コネクタ](../logic-apps/custom-connector-overview.md)を作成することもできます。 ゲートウェイ自体では追加コストは発生しませんが、これらのコネクタや Azure Logic Apps のその他の操作に [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)が適用されます。

## <a name="prerequisites"></a>前提条件

* 既に[ローカル コンピューターにオンプレミス データ ゲートウェイをインストール](../logic-apps/logic-apps-gateway-install.md)している。 このゲートウェイ インストールは、このインストールにリンクするゲートウェイ リソースを作成する前に存在している必要があります。

* ゲートウェイのインストールに使用したものと[同じ Azure アカウントとサブスクリプション](../logic-apps/logic-apps-gateway-install.md#requirements)がある。 この Azure アカウントは、1 つの [Azure Active Directory (Azure AD) テナントまたはディレクトリ](../active-directory/fundamentals/active-directory-whatis.md#terminology)にのみ属している必要があります。 Azure でゲートウェイ リソースを作成できるのはゲートウェイ管理者だけなので、同じ Azure アカウントとサブスクリプションを使用して Azure でゲートウェイ リソースを作成する必要があります。 現在、サービス プリンシパルはサポートされていません。

  * Azure でゲートウェイ リソースを作成するときは、そのゲートウェイ リソースだけにリンクする、ゲートウェイのインストールを選択します。 各ゲートウェイ インストールは、1 つのゲートウェイ インストールだけにリンクできます。 別のゲートウェイ リソースと既に関連付けられているゲートウェイ インストールを選択することはできません。

  * ロジック アプリとゲートウェイ リソースは、同じ Azure サブスクリプション内に存在する必要はありません。 ゲートウェイ リソースを使用できるトリガーとアクションでは、ゲートウェイ リソースがある別の Azure サブスクリプションを選択できます。ただし、そのサブスクリプションがロジック アプリと同じ Azure AD テナントまたはディレクトリに存在する場合に限ります。 また、別の管理者が設定できるゲートウェイに対する管理者権限も必要です。 詳細については、[データ ゲートウェイ: PowerShell を使用した Automation - パート 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) および [PowerShell: データ ゲートウェイ - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser) に関する記事を参照してください。

    > [!NOTE]
    > 現時点では、複数のサブスクリプション間でゲートウェイ リソースやインストールを共有することはできません。 製品のフィードバックを送信するには、[Microsoft Azure フィードバック フォーラム](https://feedback.azure.com/forums/34192--general-feedback)を参照してください。

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure ゲートウェイ リソースを作成する

ローカル コンピューターにゲートウェイをインストールした後、ゲートウェイ用の Azure リソースを作成します。

1. ゲートウェイのインストールに使用したものと同じ Azure アカウントで [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の検索ボックスに「オンプレミスのデータ ゲートウェイ」と入力し、 **[オンプレミスのデータ ゲートウェイ]** を選択します。

   !["オンプレミス データ ゲートウェイ" を見つける](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. **[オンプレミスのデータ ゲートウェイ]** で、 **[追加]** を選択します。

   ![データ ゲートウェイ用の新しい Azure リソースを追加する](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. **[接続ゲートウェイの作成]** で、ゲートウェイ リソースに関する情報を指定します。 完了したら **[作成]** を選択します。

   | プロパティ | 説明 |
   |----------|-------------|
   | **リソース名** | アルファベット、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、またはピリオド (`.`) のみを含む、ゲートウェイ リソースの名前を指定します。 |
   | **サブスクリプション** | ゲートウェイのインストールに使用された、Azure アカウントの Azure サブスクリプションを選択します。 既定のサブスクリプションは、サインインするために使用した Azure アカウントに基づきます。 |
   | **リソース グループ** | 使用する [Azure リソース グループ](../azure-resource-manager/management/overview.md) |
   | **場所** | [ゲートウェイのインストール](../logic-apps/logic-apps-gateway-install.md)中にゲートウェイ クラウド サービス用に選択されたのと同じリージョンまたは場所。 それ以外の場合、ゲートウェイのインストールは、 **[インストール名]** の一覧に表示されません。 ロジック アプリの場所は、ゲートウェイ リソースの場所とは異なる場合があります。 |
   | **インストール名** | これらの条件が満たされた場合にのみ一覧に表示される、ゲートウェイのインストールを選択します。 <p><p>- ゲートウェイのインストールが、作成するゲートウェイ リソースと同じリージョンを使用する。 <br>- ゲートウェイのインストールが、別の Azure ゲートウェイ リソースにリンクされていない。 <br>- ゲートウェイのインストールが、ゲートウェイ リソースの作成に使用しているものと同じ Azure アカウントにリンクされている。 <br>- Azure アカウントが、1 つの [Azure Active Directory (Azure AD) テナントまたはディレクトリ](../active-directory/fundamentals/active-directory-whatis.md#terminology)に属しており、ゲートウェイのインストールに使用したものと同じアカウントである。 <p><p>詳細については、「[よく寄せられる質問](#faq)」セクションを参照してください。 |
   |||

   ゲートウェイ リソースと同じリージョンにあり、同じ Azure アカウントにリンクされているゲートウェイのインストール例を以下に示します。

   ![データ ゲートウェイ リソースを作成するための詳細を指定する](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>オンプレミス データに接続する

ゲートウェイ リソースを作成し、Azure サブスクリプションをこのリソースに関連付けた後は、ゲートウェイを使用してロジック アプリとオンプレミス データ ソース間の接続を作成できます。

1. Azure portal で、ロジック アプリ デザイナーでロジック アプリを作成するか開きます。

1. オンプレミス接続をサポートするコネクタ (たとえば、**SQL Server**) を選択します。

1. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** を選択します。

1. **[ゲートウェイ]** の **[サブスクリプション]** の一覧から、目的のゲートウェイ リソースがある Azure サブスクリプションを選択します。

   ロジック アプリとゲートウェイ リソースは、同じ Azure サブスクリプション内に存在する必要はありません。 それぞれにゲートウェイ リソースがある他の Azure サブスクリプションから選択できますが、これらのサブスクリプションがロジック アプリと同じ Azure AD テナントまたはディレクトリに存在し、ゲートウェイに対する管理者権限があり、別の管理者が設定できる場合に限ります。 詳細については、[データ ゲートウェイ: PowerShell を使用した Automation - パート 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) および [PowerShell: データ ゲートウェイ - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser) に関する記事を参照してください。
  
1. 選択したサブスクリプションで使用可能なゲートウェイ リソースを示す **[接続ゲートウェイ]** の一覧から、目的のゲートウェイ リソースを選択します。 各ゲートウェイ リソースは、1 つのゲートウェイ インストールにリンクされています。

   > [!NOTE]
   > ロジック アプリの場所とゲートウェイ リソースの場所が異なる場合があるため、ゲートウェイの一覧には他のリージョンのゲートウェイ リソースが含まれています。 

1. 一意の接続名とその他の必要な情報を指定します。これは、作成する接続によって異なります。

   一意の接続名を使用すると、後でその接続を簡単に識別できます。複数の接続を作成する場合は特に便利です。 該当する場合は、ユーザー名の完全修飾ドメイン名も含めてください。

   たとえば次のようになります。

   ![ロジック アプリとデータ ゲートウェイ間の接続を作成する](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 完了したら **[作成]** を選択します。

これで、ロジック アプリでゲートウェイの接続を使用する準備が整いました。

## <a name="edit-connection"></a>接続の編集

ゲートウェイ接続の設定を更新するには、接続を編集します。

1. ロジック アプリのみの API 接続をすべて見つけるには、ロジック アプリのメニューで、 **[開発ツール]** の **[API 接続]** を選択します。

   ![ロジック アプリ メニューで、[API 接続] を選択する](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 目的のゲートウェイ接続を選択し、 **[API 接続の編集]** を選択します。

   > [!TIP]
   > 更新が反映されない場合は、ゲートウェイのインストールで[ゲートウェイの Windows サービス アカウントを停止して再起動](../logic-apps/logic-apps-gateway-install.md#restart-gateway)してみてください。

Azure サブスクリプションに関連付けられているすべての API 接続を見つけるには、次の操作を行います。

* Azure portal メニューから、 **[すべてのサービス]**  >  **[Web]**  >  **[API 接続]** を選択します。
* または、Azure portal メニューから **[すべてのリソース]** を選択します。 **[タイプ]** フィルターを **[API 接続]** に設定します。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>ゲートウェイ リソースの削除

別のゲートウェイ リソースを作成するには、ゲートウェイのインストールを別のゲートウェイ リソースにリンクするか、ゲートウェイ リソースを削除します。ゲートウェイ リソースは、ゲートウェイのインストールに影響を与えずに削除できます。

1. Azure portal メニューから **[すべてのリソース]** を選択するか、任意のページから検索し、 **[すべてのリソース]** を選択します。 ゲートウェイ リソースを検索して選択します。

1. まだ選択していない場合は、ゲートウェイ リソースのメニューで **[オンプレミスのデータ ゲートウェイ]** を選択します。 ゲートウェイ リソース ツールバーで、 **[削除]** を選択します。

   次に例を示します。

   ![Azure でゲートウェイ リソースを削除する](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q**: Azure でゲートウェイ リソースを作成するときにゲートウェイ インストールが表示されないのはなぜですか? <br/>
**A**: この問題は、次の理由で発生することがあります。

* お使いの Azure アカウントが、ローカル コンピューターでのゲートウェイのインストールに使用したものと同じアカウントではありません。 ゲートウェイのインストールに使用したものと同じ ID を使用して、Azure portal にサインインしていることを確認してください。 Azure でゲートウェイ リソースを作成できるのは、ゲートウェイ管理者のみです。 現在、サービス プリンシパルはサポートされていません。

* お使いの Azure アカウントは、1 つの [Azure AD テナントまたはディレクトリ](../active-directory/fundamentals/active-directory-whatis.md#terminology)だけに属していません。 ゲートウェイのインストール時に使用したものと同じ Azure AD テナントまたはディレクトリを使用していることを確認してください。

* ゲートウェイ リソースとゲートウェイのインストールが、同じリージョンに存在していません。 ただし、ロジック アプリの場所は、ゲートウェイ リソースの場所と異なっていてもかまいません。

* ゲートウェイのインストールは、別のゲートウェイ リソースに既に関連付けられています。 各ゲートウェイ リソースは、1 つの Azure アカウントおよびサブスクリプションのみリンクできる、1 つのゲートウェイ インストールにだけリンクできます。 そのため、別のゲートウェイ リソースと既に関連付けられているゲートウェイ インストールを選択することはできません。 これらのインストールは、 **[インストール名]** の一覧に表示されません。

  Azure portal でゲートウェイの登録を確認するには、"*すべて*" の Azure サブスクリプションで、リソースの種類が **オンプレミス データ ゲートウェイ** である Azure リソースをすべて見つけます。 ゲートウェイのインストールと他のゲートウェイ リソースとのリンクを解除する場合は、「[ゲートウェイ リソースの削除](#change-delete-gateway-resource)」を参照してください。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>次のステップ

* [ロジック アプリをセキュリティで保護する](./logic-apps-securing-a-logic-app.md)
* [ロジック アプリの接続の例とシナリオ](./logic-apps-examples-and-scenarios.md)
