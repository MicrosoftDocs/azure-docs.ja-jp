---
title: Azure Logic Apps からオンプレミスのデータ ソースにアクセスする
description: オンプレミス データ ゲートウェイを作成することによって、ロジック アプリからオンプレミスのデータ ソースに接続する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 09/01/2019
ms.openlocfilehash: dcb0fe4da968408a261e387c636cc548fa757a09
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036667"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps からオンプレミスのデータ ソースに接続する

ロジック アプリからオンプレミスのデータ ソースにアクセスするには、Azure Portal でオンプレミス データ ゲートウェイ リソースを作成します。 その後、ロジック アプリは[オンプレミス コネクタ](../connectors/apis-list.md#on-premises-connectors)を使用できます。 この記事では、[ゲートウェイをダウンロードしてローカル コンピューターにインストール](../logic-apps/logic-apps-gateway-install.md)した*後で* Azure ゲートウェイ リソースを作成する方法を示します。 ゲートウェイの詳細については、「[ゲートウェイのしくみ](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)」を参照してください。

> [!TIP]
> Azure 仮想ネットワークに接続するには、代わりに[*統合サービス環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)を作成することことを考慮してください。 

他のサービスでゲートウェイを使用する方法については、次の記事を参照してください。

* [Microsoft Power BI オンプレミス データ ゲートウェイ](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow オンプレミス データ ゲートウェイ](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps オンプレミス データ ゲートウェイ](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>サポートされるデータ ソース

Azure Logic Apps については、オンプレミス データ ゲートウェイでは、以下のデータ ソース向けの[オンプレミス コネクタ](../connectors/apis-list.md#on-premises-connectors)がサポートされています。

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

ゲートウェイ自体では追加のコストは発生しませんが、これらのコネクタや Azure Logic Apps のその他の操作に [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)が適用されます。

## <a name="prerequisites"></a>前提条件

* [ローカル コンピューターへのオンプレミス データ ゲートウェイのインストール](../logic-apps/logic-apps-gateway-install.md)が完了していること。

* オンプレミス データ ゲートウェイをインストールしたときに使用したものと[同じ Azure アカウントと Azure サブスクリプション](../logic-apps/logic-apps-gateway-install.md#requirements)があること。

* 以前にゲートウェイのインストールを Azure の別のゲートウェイ リソースにリンクしていないこと。

  ゲートウェイ リソースを作成するときに、ゲートウェイ リソースに関連付けるゲートウェイのインストールを選択します。 ゲートウェイ リソースを作成するときに、既にリンクされているゲートウェイのインストールを選択することはできません。

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure ゲートウェイ リソースを作成する

ローカル コンピューターにゲートウェイをインストールした後、ゲートウェイ用の Azure リソースを作成します。 

1. ゲートウェイのインストールに使用したものと同じ Azure アカウントで [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の検索ボックスに「オンプレミスのデータ ゲートウェイ」と入力し、 **[オンプレミスのデータ ゲートウェイ]** を選択します。

   !["オンプレミス データ ゲートウェイ" を見つける](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. **[オンプレミスのデータ ゲートウェイ]** で、 **[追加]** を選択します。

   ![データ ゲートウェイの追加](./media/logic-apps-gateway-connection/add-gateway.png)

1. **[接続ゲートウェイの作成]** で、ゲートウェイ リソースに関する情報を指定します。 完了したら **[作成]** を選択します。

   | プロパティ | 説明 |
   |----------|-------------|
   | **リソース名** | ゲートウェイ リソースの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 |
   | **サブスクリプション** | 自分の Azure サブスクリプション。ゲートウェイのインストールとロジック アプリと同じである必要があります。 既定のサブスクリプションは、サインインするために使用した Azure アカウントに基づきます。 |
   | **リソース グループ** | 使用する [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md) |
   | **Location** | [ゲートウェイのインストール](../logic-apps/logic-apps-gateway-install.md)中にゲートウェイ クラウド サービス向けに選択した場所と同じリージョン。 一致していないと、ゲートウェイのインストールは、選択する**インストール名**の一覧に表示されません。 ロジック アプリの場所は、ゲートウェイ リソースの場所とは異なる場合があります。 |
   | **インストール名** | ゲートウェイのインストールが既に選択されていない場合は、前にインストールしたゲートウェイを選択します。 以前にリンクされたゲートウェイのインストールは、選択するためのこの一覧には表示されません。 |
   |||

   たとえば次のようになります。

   ![詳細を指定してオンプレミス データ ゲートウェイを作成する](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>オンプレミス データに接続する

ゲートウェイ リソースを作成し、Azure サブスクリプションをこのリソースに関連付けた後は、ゲートウェイを使用してロジック アプリとオンプレミス データ ソース間の接続を作成できます。

1. Azure portal で、ロジック アプリ デザイナーでロジック アプリを作成するか開きます。

1. オンプレミス接続をサポートするコネクタ (たとえば、**SQL Server**) を選択します。

1. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** を選択します。 

1. **[ゲートウェイ]** で、作成したゲートウェイ リソースを選択します。

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

1. ロジック アプリのみに関係するすべての API 接続を見つけるには、ロジック アプリのメニューで、 **[開発ツール]** の **[API 接続]** を選択します。
   
   ![ロジック アプリ メニューで、[API 接続] を選択する](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. 目的のゲートウェイ接続を選択し、 **[API 接続の編集]** を選択します。

   > [!TIP]
   > 更新が反映されない場合は、ゲートウェイのインストールで[ゲートウェイの Windows サービス アカウントを停止して再起動](../logic-apps/logic-apps-gateway-install.md#restart-gateway)してみてください。

Azure サブスクリプションに関連付けられているすべての API 接続を見つけるには、次の操作を行います。 

* Azure のメイン メニューから、 **[すべてのサービス]**  >  **[Web]**  >  **[API 接続]** に移動します。
* または、Azure のメイン メニューから **[すべてのリソース]** を選択します。 **[タイプ]** フィルターを **[API 接続]** に設定します。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>ゲートウェイ リソースの削除

別のゲートウェイ リソースを作成するには、ゲートウェイのインストールを別のゲートウェイ リソースにリンクするか、ゲートウェイ リソースを削除します。ゲートウェイ リソースは、ゲートウェイのインストールに影響を与えずに削除できます。 

1. Azure のメイン メニューで、 **[すべてのリソース]** を選択します。 ゲートウェイ リソースを検索して選択します。

1. まだ選択していない場合は、ゲートウェイ リソースのメニューで **[オンプレミスのデータ ゲートウェイ]** を選択します。 ゲートウェイ リソース ツールバーで、 **[削除]** を選択します。

   例:

   ![ゲートウェイの削除](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q**: Azure でゲートウェイ リソースを作成するときにゲートウェイ インストールが表示されないはなぜですか? <br/>
**A**: この問題は、次の理由で発生することがあります。

* ゲートウェイ インストールが登録済みで、Azure の別のゲートウェイ リソースによって既に要求されている。 ゲートウェイ インストールは、そのゲートウェイ リソースが作成された後、インスタンスの一覧に表示されません。 Azure portal でゲートウェイの登録を確認するには、"*すべて*" の Azure サブスクリプションについて、種類が**オンプレミス データ ゲートウェイ**であるすべての Azure リソースを確認します。

* ゲートウェイをインストールしたユーザーの Azure AD ID が、Azure portal にサインインしたユーザーのものと異なる。 ゲートウェイをインストールしたのと同じ ID でサインインしていることを確認してください。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>次の手順

* [ロジック アプリをセキュリティで保護する](./logic-apps-securing-a-logic-app.md)
* [ロジック アプリの接続の例とシナリオ](./logic-apps-examples-and-scenarios.md)
