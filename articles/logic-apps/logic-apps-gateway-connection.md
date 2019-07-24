---
title: Azure Logic Apps からオンプレミスのデータ ソースにアクセスする | Microsoft Docs
description: オンプレミス データ ゲートウェイを作成することによって、ロジック アプリからオンプレミスのデータ ソースに接続する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 2b9e1c153c3fa9b17145eb6c3c8f3ed02e3bf40f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064068"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps からオンプレミスのデータ ソースに接続する

ロジック アプリからオンプレミスのデータ ソースにアクセスするには、Azure Portal でオンプレミス データ ゲートウェイ リソースを作成します。 その後、ロジック アプリは[オンプレミス コネクタ](../logic-apps/logic-apps-gateway-install.md#supported-connections)を使用できます。 この記事では、[ゲートウェイをダウンロードしてローカル コンピューターにインストール](../logic-apps/logic-apps-gateway-install.md)した "*後で*" Azure ゲートウェイ リソースを作成する方法を示します。 

> [!TIP]
> Azure 仮想ネットワークに接続するには、代わりに[*統合サービス環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)を作成することことを考慮してください。 

他のサービスでゲートウェイを使用する方法については、次の記事を参照してください。

* [Microsoft Power BI オンプレミス データ ゲートウェイ](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow オンプレミス データ ゲートウェイ](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps オンプレミス データ ゲートウェイ](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>前提条件

* [ローカル コンピューターへのデータ ゲートウェイのダウンロードとインストール](../logic-apps/logic-apps-gateway-install.md)は完了しています。

* ゲートウェイのインストールは、まだ Azure のゲートウェイ リソースに関連付けられていません。 ゲートウェイのインストールは、1 つのゲートウェイ リソースのみにリンクすることができます。これを行うには、ゲートウェイ リソースを作成したときにゲートウェイ インストールを選択します。 このリンクにより、他のリソースはこのゲートウェイ インストールを使用できなくなります。

* Azure portal にサインインしてゲートウェイ リソースを作成するときは、以前に[オンプレミス データ ゲートウェイのインストール](../logic-apps/logic-apps-gateway-install.md#requirements)に使用したものと同じサインイン アカウントと、データ ゲートウェイのインストールに使用したものと同じ [Azure サブスクリプション](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer)を必ず使用してください。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* Azure portal でゲートウェイ リソースを作成し、保守するには、[Windows サービス アカウント](../logic-apps/logic-apps-gateway-install.md#windows-service-account)に**共同作成者**以上のアクセス許可が必要です。 オンプレミス データ ゲートウェイは、Windows サービスとして実行され、Windows サービスのログイン資格情報として `NT SERVICE\PBIEgwService` を使用するように設定されています。 

  > [!NOTE]
  > Windows サービス アカウントは、オンプレミスのデータ ソースへの接続に使用するアカウントとも、クラウド サービスへのサインインに使用する職場または学校アカウントとも異なります。

## <a name="download-and-install-gateway"></a>ゲートウェイのダウンロードとインストール

この記事の手順を続行する前に、ゲートウェイが既にローカル コンピューターにインストールされていることを確認してください。
インストールが済んでいない場合は、[オンプレミス データ ゲートウェイのダウンロードとインストール](../logic-apps/logic-apps-gateway-install.md)手順に従います。 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>ゲートウェイ用の Azure リソースの作成

ローカル コンピューターにゲートウェイをインストールした後、ゲートウェイ用の Azure リソースを作成できます。 この手順でも、ゲートウェイ リソースを Azure サブスクリプションに関連付けます。

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。 ゲートウェイをインストールするために使用したのと同じ Azure の職場または学校のメール アドレスを必ず使用してください。

2. Azure のメイン メニューで、**[リソースの作成]** > 
 **[統合]** > **[オンプレミスのデータ ゲートウェイ]** の順に選択します。

   !["オンプレミス データ ゲートウェイ" を見つける](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. **[接続ゲートウェイの作成]** ページで、データ ゲートウェイ リソースに関する次の情報を指定します。

   | プロパティ | 説明 | 
   |----------|-------------|
   | **名前** | ゲートウェイ リソースの名前 | 
   | **サブスクリプション** | Azure サブスクリプションの名前。このサブスクリプションは、ロジック アプリと同じサブスクリプションである必要があります。 既定のサブスクリプションは、サインインするために使用した Azure アカウントに基づきます。 | 
   | **[リソース グループ]** | 関連するリソースを整理するための [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前 | 
   | **場所** | Azure では、この場所は、[ゲートウェイのインストール](../logic-apps/logic-apps-gateway-install.md)中にゲートウェイ クラウド サービス向けに選択したリージョンと同じリージョンに限定されます。 <p>**メモ**:このゲートウェイ リソースの場所がゲートウェイ クラウド サービスの場所に一致していることを確認してください。 一致していないと、ゲートウェイのインストールは、次の手順で選択するインストール済みのゲートウェイの一覧に表示されない可能性があります。 ゲートウェイ リソースとロジック アプリでは、異なるリージョンを使用できます。 | 
   | **インストール名** | ゲートウェイのインストールが既に選択されていない場合は、前にインストールしたゲートウェイを選択します。 | 
   | | | 

   たとえば次のようになります。

   ![詳細を指定してオンプレミス データ ゲートウェイを作成する](./media/logic-apps-gateway-connection/createblade.png)

4. ゲートウェイ リソースを Azure ダッシュボードに追加するには、**[ダッシュボードにピン留めする]** を選択します。 操作が完了したら、**[作成]** を選択します。

   必要なときにゲートウェイを検索したり表示したりするには、Azure のメイン メニューで **[すべてのサービス]** を選択します。 
   検索ボックスに「オンプレミスのデータ ゲートウェイ」と入力し、**[オンプレミスのデータ ゲートウェイ]** を選択します。

   !["オンプレミスのデータ ゲートウェイ" を見つける](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>オンプレミス データに接続する

ゲートウェイ リソースを作成し、Azure サブスクリプションをこのリソースに関連付けた後は、ゲートウェイを使用してロジック アプリとオンプレミス データ ソース間の接続を作成できます。

1. Azure portal で、ロジック アプリ デザイナーでロジック アプリを作成するか開きます。

2. オンプレミス接続をサポートするコネクタ (たとえば、**SQL Server**) を選択します。

3. 接続を設定します。

   1. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** を選択します。 

   2. **[ゲートウェイ]** で、以前に作成したゲートウェイ リソースを選択します。 

      ゲートウェイ接続の場所はロジック アプリと同じリージョンに存在する必要がありますが、異なるリージョンにあるゲートウェイを選択することができます。

   3. 一意の接続名と、その他の必要な情報を入力します。 

      一意の接続名を使用すると、後でその接続を簡単に識別できます。複数の接続を作成する場合は特に便利です。 該当する場合は、ユーザー名の完全修飾ドメイン名も含めてください。
   
      たとえば次のようになります。

      ![ロジック アプリとデータ ゲートウェイ間の接続を作成する](./media/logic-apps-gateway-connection/blankconnection.png)

   4. 操作が完了したら、**[作成]** を選択します。 

これで、ロジック アプリでゲートウェイの接続を使用する準備が整いました。

## <a name="edit-connection"></a>接続の編集

ロジック アプリ向けのゲートウェイ接続を作成した後、その特定の接続の設定を後で更新することができます。

1. ゲートウェイ接続を見つけます。

   * ロジック アプリのみに関係するすべての API 接続を見つけるには、ロジック アプリのメニューで、**[開発ツール]** の **[API 接続]** を選択します。 
   
     ![ロジック アプリに移動し、[API 接続] を選択する](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Azure サブスクリプションに関連付けられているすべての API 接続を見つけるには、次の操作を行います。 

     * Azure のメイン メニューから、**[すべてのサービス]** > **[Web]** > **[API 接続]** に移動します。 
     * または、Azure のメイン メニューから **[すべてのリソース]** を選択します。

2. 目的のゲートウェイ接続を選択し、**[API 接続の編集]** を選択します。

   > [!TIP]
   > 更新が反映されない場合は、[ゲートウェイの Windows サービスを停止して再起動](./logic-apps-gateway-install.md#restart-gateway)してみてください。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>ゲートウェイ リソースの削除

別のゲートウェイ リソースを作成するには、ゲートウェイを別のリソースに関連付けるか、ゲートウェイ リソースを削除します。ゲートウェイ リソースは、ゲートウェイのインストールに影響を与えずに削除できます。 

1. Azure のメイン メニューから **[すべてのリソース]** を選択します。 

2. ゲートウェイ リソースを検索して選択します。

3. まだ選択していない場合は、ゲートウェイ リソースのメニューで **[オンプレミスのデータ ゲートウェイ]** を選択します。 

4. リソースのツール バーで、**[削除]** を選択します。

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>よく寄せられる質問

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [ロジック アプリをセキュリティで保護する](./logic-apps-securing-a-logic-app.md)
* [ロジック アプリの接続の例とシナリオ](./logic-apps-examples-and-scenarios.md)
