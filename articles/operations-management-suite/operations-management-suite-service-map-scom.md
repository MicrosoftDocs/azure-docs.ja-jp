---
title: "サービス マップと System Center Operations Manager の統合 | Microsoft Docs"
description: "サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする Operations Management Suite (OMS) のソリューションです。  この記事では、SCOM でサービス マップを使用して分散アプリケーション ダイアグラムを自動作成する方法について説明します。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 389c01234acff068dc90f3cdfdc4916a9d76d244
ms.lasthandoff: 03/09/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>サービス マップと System Center Operations Manager の統合
  > [!NOTE]
  > この機能はプライベート プレビュー段階にあるため、実稼働システムで使用すべきではありません。
  > 
  
Operations Management Suite (OMS) の サービス マップでは、Windows および Linux システムのアプリケーション コンポーネントが自動的に検出され、サービス間の通信がマップされます。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 サービス マップは、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続を表示します。エージェントのインストール以外の構成は必要ありません。  詳細については、[サービス マップのドキュメント](operations-management-suite-service-map.md)のページを参照してください。

このサービス マップと System Center Operations Manager (SCOM) との統合を利用すると、サービス マップの動的な依存関係を基にして SCOM で分散アプリケーション ダイアグラムを自動で作成することができます。

## <a name="prerequisites"></a>前提条件
1.    サーバー セットを管理する SCOM 管理グループ。
2.    サービス マップ ソリューションが有効化された OMS ワークスペース。
3.    SCOM で管理されており、サーバー マップへのデータを送信を行う (1 つ以上の) サーバーのセット。  Windows および Linux サーバーがサポートされています。
4.    OMS ワークスペースに関連付けられている Azure サブスクリプションにアクセスできるサービス プリンシパル。  [詳細については、「サービス プリンシパルの作成」をご覧ください](#creating-a-service-principal)。

## <a name="installing-service-map-management-pack"></a>サービス マップ管理パックのインストール
SCOM とサービス マップの統合を行うには、Microsoft.SystemCenter.ServiceMap 管理パック バンドル (Microsoft.SystemCenter.ServiceMap.mpb) をインストールします。  このバンドルには、次の管理パックが含まれています。
* Microsoft ServiceMap アプリケーション ビュー
* Microsoft System Center ServiceMap Internal
* Microsoft System Center ServiceMap Overrides
* Microsoft System Center ServiceMap

## <a name="configuring-the-service-map-integration"></a>サービス マップ統合の構成
1. ServiceMap 管理パックをインストールすると、[管理] ペインの [Operations Management Suite] の下にサービス マップという名前の新しいノードが追加されます。
2. [サービス マップ概要] ペインの [ワークスペースの追加] をクリックして構成ウィザードを開きます。

    ![SCOM 構成ウィザード](media/oms-service-map/scom-configuration.png)

3. ウィザードの最初のステップは、Azure サービス プリンシパルの情報を入力する [接続構成] です。 サービス プリンシパルのテナント名または ID、アプリケーション ID (またはユーザー名かクライアント ID)、パスワードを入力します。  [詳細については、「サービス プリンシパルの作成」をご覧ください](#creating-a-service-principal)。

    ![SCOM での SPN の構成](media/oms-service-map/scom-config-spn.png)

4. 次に、Azure サブスクリプション、Azure リソース グループ (OMS ワークスペースが含まれるグループ)、OMS ワークスペースを選択します。

    ![SCOM でのワークスペースの構成](media/oms-service-map/scom-config-workspace.png)

5. 次に、SCOM とサービス マップの同期を行うサーバーを指定してサーバー マップ サーバー グループを構成します。  [Add/Remove Servers…] \(サーバーの追加または削除…) ボタンを選択します。 この統合によりサーバーの分散アプリケーション ダイアグラムを作成するには、サーバーが 1) SCOM で管理されていること、2) サービス マップで管理されていること、および 3) サービス マップ サーバー グループに登録されていることが必要です。

    ![SCOM でのグループの構成](media/oms-service-map/scom-config-group.png)

6. 省略可能 - OMS と通信する管理サーバーのリソース プールを選択し、[ワークスペースの追加] をクリックします。

    ![SCOM でのリソース プールの構成](media/oms-service-map/scom-config-pool.png)

7. OMS ワークスペースの構成および登録にはしばらくかかります。 構成が完了すると、SCOM で最初のサービス マップと OMS の同期が開始されます。

    ![SCOM でのリソース プールの構成](media/oms-service-map/scom-config-success.png)

**注:** 既定の同期間隔は 60 分に設定されています。 同期間隔は、上書きを構成することで変更することができます。 また、[作成] ペインからサービス マップ サーバー グループにサーバーを手動で追加することもできます ([作成] ペイン の [グループ] をクリックし、"Service Map Servers Group" (サービス マップ サーバー グループ) を検索します)。 これらのサーバーのサーバー マップは、(構成されている同期間隔に基づく) 次の同期時に同期されます。

## <a name="monitoring-service-map"></a>サービス マップの監視
OMS ワークスペースが接続されると、SCOM コンソールの [監視] ペインにサービス マップと言う名前の新しいフォルダーが表示されます。
![SCOM での監視](media/oms-service-map/scom-monitoring.png)

サービス マップ フォルダーには 3 つのノードがあります。
### <a name="all-alerts"></a>すべてのアラート:
このノードには、SCOM と OMS のサービス マップ ソリューション間の通信に関するアラートがすべて表示されます。

**注:** これらは、SCOM で表示される OMS アラートではありません。
### <a name="servers"></a>サーバー:
このノードには、サービス マップと同期するように構成されている監視対象サーバーの一覧が表示されます。

![SCOM でのサーバーの監視](media/oms-service-map/scom-monitoring-servers.png)

### <a name="server-dependency-views"></a>Server Dependency Views (サーバー依存関係ビュー):
このビューには、サービス マップと同期されているすべてのサーバーの一覧が表示されます。 サーバーをクリックすると、その分散アプリケーション ダイアグラムを確認できます。

![SCOM 分散アプリケーション ダイアグラム](media/oms-service-map/scom-dad.png)

## <a name="editdelete-workspace"></a>ワークスペースの編集/削除:
構成済みのワークスペースは、[サービス マップ概要] ペインからを編集または削除できます ([管理] ペイン --> [Operations Management Suite] --> [サービス ])。  現時点で構成できる OMS ワークスペースは 1 つのみであることに注意してください。

![SCOM でのワークスペースの編集](media/oms-service-map/scom-edit-workspace.png)

## <a name="configuring-rules-and-overrides"></a>規則と上書きの構成:
サービス マップから定期的に情報を取得するために、**_Microsoft.SystemCenter.ServiceMap.Import.Rule**_ 規則が作成されます。  この規則の上書きを構成することで、同期のタイミングを変更できます。
[作成] ペイン --> [規則] --> [Microsoft.SystemCenter.ServiceMapImport.Rule] の順にクリックします。

![SCOM の上書き](media/oms-service-map/scom-overrides.png)
* **Enabled** – 自動更新を有効または無効にします。 
* **IntervalSeconds** – 更新の間隔です。  既定値は 1 です。 サーバー マップの同期頻度を上げるにはこの値を変更します。
* **TimeoutSeconds** – 要求がタイムアウトになるまでの時間です。 
* **TimeWindowMinutes** – データに対するクエリの範囲です。  既定値は 60 分枠です。 最大値は (サービス マップの上限である) 1 時間です。

## <a name="known-issueslimitations"></a>既知の問題と制限事項:
現在の設計には次の問題と制限があります。
1. [作成] ペインから “サービス マップ サーバー グループ” にサーバーを手動で追加できますが、これらのサーバーのマップがサービス マップから同期されるのは次の同期サイクル時のみです (既定では 60 分。 この同期のタイミングは上書き可能です)。 
2. 接続できる OMS ワークスペースは 1 つだけです。

## <a name="creating-a-service-principal"></a>サービス プリンシパルの作成
次のリンクから、3 通りのサービス プリンシパルの作成方法を紹介する Azure の公式ドキュメントを参照できます。
* [PowerShell でサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI でサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure Portal でサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>フィードバック
サービス マップやこのドキュメントについてフィードバックはありますか。  [User Voice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)では、機能を提案したり、既存の提案に投票することができます。

