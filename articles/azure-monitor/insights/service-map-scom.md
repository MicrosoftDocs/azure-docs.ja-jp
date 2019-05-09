---
title: サービス マップと System Center Operations Manager の統合 | Microsoft Docs
description: Service Map は、Windows および Linux システム上のアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする、Azure のソリューションです。 この記事では、Operations Manager でサービス マップを使用して、分散アプリケーション ダイアグラムを自動作成する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: magoedte
ms.openlocfilehash: 40e6d6ff6ea8748b525642e5507c80590b322b7a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110105"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>サービス マップと System Center Operations Manager の統合

サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 サービス マップを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして、思うように表示することができます。 サービス マップには、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続が表示されます。その際、エージェントのインストール以外の構成は必要ありません。 詳細については、[サービス マップのドキュメント]( service-map.md)を参照してください。

このサービス マップと System Center Operations Manager との統合を利用すると、サービス マップの動的な依存関係に基づいて、Operation Manager で分散アプリケーション ダイアグラムを自動作成できます。

## <a name="prerequisites"></a>前提条件
* サーバー セットを管理する Operations Manager 管理グループ (2012 R2 以降)。
* Service Map ソリューションが有効化された Log Analytics ワークスペース。
* Operations Manager で管理され、サービス マップにデータを送信する (1 つ以上の) サーバー セット。 Windows および Linux サーバーがサポートされています。
* Log Analytics ワークスペースに関連付けられている Azure サブスクリプションにアクセスできるサービス プリンシパル。 詳細については、「[サービス プリンシパルの作成](#create-a-service-principal)」を参照してください。

## <a name="install-the-service-map-management-pack"></a>サービス マップ管理パックのインストール
Operations Manager とサービス マップの統合を有効にするには、Microsoft.SystemCenter.ServiceMap 管理パック バンドル (Microsoft.SystemCenter.ServiceMap.mpb) をインポートします。 管理パックのバンドルは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=55763)からダウンロードできます。 このバンドルには、次の管理パックが含まれています。
* Microsoft サービス マップ アプリケーション ビュー
* Microsoft System Center サービス マップ Internal
* Microsoft System Center サービス マップ
* Microsoft System Center サービス マップ

## <a name="configure-the-service-map-integration"></a>サービス マップ統合の構成
サービス マップ管理パックをインストールすると、**[管理]** ウィンドウの **[Operations Management Suite]** の下に、**[サービス マップ]** という名前の新しいノードが表示されます。

>[!NOTE]
>[Operations Management Suite はサービスのコレクション](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand)であり Log Analytics に含まれていました。現在は、[Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md) の一部になっています。

サービス マップの統合を構成するには、次の操作を行います。

1. 構成ウィザードを開くには、**[サービス マップの概要]** ウィンドウの **[ワークスペースの追加]** をクリックします。  

    ![[サービス マップの概要] ウィンドウ](media/service-map-scom/scom-configuration.png)

2. **[接続構成ツール]** ウィンドウで、サービス プリンシパルのテナント名または ID、アプリケーション ID (ユーザー名またはクライアント ID とも呼ばれます)、およびパスワードを入力し、**[次へ]** をクリックします。 詳細については、「サービス プリンシパルの作成」を参照してください。

    ![[接続構成ツール] ウィンドウ](media/service-map-scom/scom-config-spn.png)

3. **[サブスクリプションの選択]** ウィンドウで、Azure サブスクリプション、Azure リソース グループ (Log Analytics ワークスペースが含まれるグループ)、Log Analytics ワークスペースを選択し、**[次へ]** をクリックします。

    ![Operations Manager 構成ワークスペース](media/service-map-scom/scom-config-workspace.png)

4. **[Machine Group Selection]\(コンピューター グループの選択\)** ウィンドウで、Operations Manager に同期する Service Map コンピューター グループを選びます。 **[Add/Remove Machine Groups]\(コンピューター グループの追加/削除\)** をクリックして、**[Available Machine Groups]\(使用可能なコンピューター グループ\)** の一覧からグループを選択し、**[追加]** をクリックします。  グループの選択が完了したら、**[OK]** をクリックして完了します。

    ![Operations Manager 構成コンピューター グループ](media/service-map-scom/scom-config-machine-groups.png)

5. **[サーバーの選択]** ウィンドウで、Operations Manager とサービス マップの同期を行うサーバーを指定して、サーバー マップ サーバー グループを構成します。 **[サーバーの追加と削除]** をクリックします。   

    この統合によりサーバーの分散アプリケーション ダイアグラムを作成するには、サーバーが次の条件を満たす必要があります。

   * Operations Manager で管理されている
   * Service Map で管理されている
   * Service Map サーバー グループに登録されている

     ![Operations Manager 構成グループ](media/service-map-scom/scom-config-group.png)

6. 省略可能:Log Analytics と通信する管理サーバーのリソース プールを選択し、**[ワークスペースの追加]** をクリックします。

    ![Operations Manager 構成リソース プール](media/service-map-scom/scom-config-pool.png)

    Log Analytics ワークスペースの構成および登録には時間がかかる場合があります。 構成が完了すると、Operations Manager と、最初の Service Map の同期が開始されます。

    ![Operations Manager 構成リソース プール](media/service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>サービス マップの監視
Log Analytics ワークスペースが接続されると、Operations Manager コンソールの **[監視]** ウィンドウに、Service Map という名前の新しいフォルダーが表示されます。

![Operations Manager の [監視] ウィンドウ](media/service-map-scom/scom-monitoring.png)

Service Map フォルダーには 4 つのノードがあります。
* **アクティブなアラート**:Operations Manager と Service Map の間の通信に関するアクティブなアラートの一覧が表示されます。  このアラートは、Operations Manager に同期されている Log Analytics アラートではないことにご注意ください。

* **サーバー**:Service Map と同期するように構成されている監視対象サーバーの一覧が表示されます。

    ![Operations Manager の [監視] の [サーバー] ウィンドウ](media/service-map-scom/scom-monitoring-servers.png)

* **マシン グループの依存関係ビュー**:Service Map から同期されているすべてのマシン グループが一覧表示されます。 グループをクリックすると、その分散アプリケーション ダイアグラムを確認できます。

    ![Operations Manager の分散アプリケーション ダイアグラム](media/service-map-scom/scom-group-dad.png)

* **サーバーの依存関係ビュー**:Service Map から同期されているすべてのサーバーが一覧表示されます。 サーバーをクリックすると、その分散アプリケーション ダイアグラムを確認できます。

    ![Operations Manager の分散アプリケーション ダイアグラム](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>ワークスペースを編集または削除
構成済みワークスペースは、**[サービス マップ概要]** ウィンドウ (**[管理]** ウィンドウ > **[Operations Management Suite]**  >  **[サービス]**) で編集または削除できます。

>[!NOTE]
>[Operations Management Suite はサービスのコレクション](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand)であり Log Analytics に含まれていました。現在は、[Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md) の一部になっています。

現時点で構成できる Log Analytics ワークスペースは 1 つのみです。

![Operations Manager の [ワークスペースの編集] ウィンドウ](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>規則とオーバーライドの構成
サービス マップから定期的に情報を取得するために、_Microsoft.SystemCenter.ServiceMapImport.Rule_ という規則が作成されます。 同期のタイミングを変更するには、この規則のオーバーライドを構成します (**[作成]** ウィンドウ &gt; **[規則]** > **[Microsoft.SystemCenter.ServiceMapImport.Rule]**)。

![Operations Manager の [Overrides properties (プロパティのオーバーライド)] ウィンドウ](media/service-map-scom/scom-overrides.png)

* **Enabled**:自動更新を有効または無効にします。
* **IntervalMinutes**:更新間隔をリセットします。 既定の間隔は 1 時間です。 サーバー マップの同期の頻度を上げるには、この値を変更します。
* **TimeoutSeconds**:要求がタイムアウトになるまでの時間をリセットします。
* **TimeWindowMinutes**:データに対するクエリの時間枠をリセットします。 既定の時間枠は 60 分です。 サービス マップで許可されている最大値は 60 分です。

## <a name="known-issues-and-limitations"></a>既知の問題と制限

現在の設計には次の問題と制限があります。
* 1 つの Log Analytics ワークスペースにのみ接続することができます。
* **[作成]** ウィンドウでサーバーを Service Map サーバー グループに手動で追加できますが、そのサーバーのマップは、即座には同期されません。  次回の同期サイクル中に Service Map から同期されます。
* 管理パックによって作成された分散アプリケーション ダイアグラムに変更を加える場合は、Service Map による次回同期の際に、これらの変更が上書きされる可能性があります。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成
サービス プリンシパル作成に関する Azure の公式ドキュメントについては、次を参照してください。
* [PowerShell を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure Portal を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>フィードバック
サービス マップやこのドキュメントについてフィードバックはありますか。 [User Voice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)を是非ご利用ください。このページでは、機能を提案したり、既存の提案に投票したりすることができます。
