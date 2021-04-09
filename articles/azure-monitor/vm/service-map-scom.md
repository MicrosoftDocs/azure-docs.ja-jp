---
title: VM insights のマップを Operations Manager と統合する | Microsoft Docs
description: VM insights によって Windows および Linux のシステム上のアプリケーション コンポーネントが自動的に検出され、サービス間の通信がマップされます。 この記事では、マップ機能を使用して、Operations Manager で分散アプリケーション ダイアグラムを自動的に作成する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 3a7d0d49313cb524a5bf39add5c9a55862dcad47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046892"
---
# <a name="integrate-system-center-operations-manager-with-vm-insights-map-feature"></a>System Center Operations Manager と VM insights のマップ機能を統合する

VM insights では、Azure またはお客様の環境で実行している Windows および Linux 仮想マシン (VM) で検出されたアプリケーション コンポーネントを確認できます。 このマップ機能と System Center Operations Manager との統合を利用すると、VM insights の動的依存関係マップに基づいた分散アプリケーション ダイアグラムを Operation Manager で自動的に作成できます。 この記事では、この機能をサポートするように System Center Operations Manager 管理グループを構成する方法について説明します。

>[!NOTE]
>Service Map を既にデプロイ済みの場合は、VM insights でマップを表示できます。これには、VM の正常性とパフォーマンスを監視する追加機能が含まれます。 VM insights のマップ機能は、スタンドアロンの Service Map ソリューションに取って代わる予定です。 詳細については、「[VM insights の概要](../vm/vminsights-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* System Center Operations Manager の管理グループ (2012 R2 以降)。
* VM insights をサポートするように構成された Log Analytics ワークスペース。
* Operations Manager によって監視され、Log Analytics ワークスペースにデータを送信している 1 つ以上の Windows および Linux の仮想マシンまたは物理コンピューター。 Operations Manager の管理グループに報告する Linux サーバーは、Azure Monitor に直接接続するように構成する必要があります。 詳細については、「[Log Analytics エージェントを使用してログ データを収集する](../agents/log-analytics-agent.md)」の概要を確認してください。
* Log Analytics ワークスペースに関連付けられている Azure サブスクリプションにアクセスできるサービス プリンシパル。 詳細については、「[サービス プリンシパルの作成](#create-a-service-principal)」を参照してください。

## <a name="install-the-service-map-management-pack"></a>サービス マップ管理パックのインストール

Operations Manager とマップ機能の統合を有効にするには、Microsoft.SystemCenter.ServiceMap 管理パック バンドル (Microsoft.SystemCenter.ServiceMap.mpb) をインポートします。 管理パックのバンドルは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=55763)からダウンロードできます。 このバンドルには、次の管理パックが含まれています。

* Microsoft サービス マップ アプリケーション ビュー
* Microsoft System Center サービス マップ Internal
* Microsoft System Center サービス マップ
* Microsoft System Center サービス マップ

## <a name="configure-integration"></a>統合の構成

Service Map 管理パックをインストールすると、Operations Manager オペレーション コンソールの **[管理]** ウィンドウ内の **[Operations Management Suite]** の下に **[Service Map]** という名前の新しいノードが表示されます。

>[!NOTE]
>[Operations Management Suite はサービスのコレクション](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)で、Log Analytics が含まれていました。現在は、[Azure Monitor](../overview.md) の一部になっています。

VM insights のマップの統合を構成するには、次の操作を行います。

1. 構成ウィザードを開くには、 **[サービス マップの概要]** ウィンドウの **[ワークスペースの追加]** をクリックします。  

    ![[サービス マップの概要] ウィンドウ](media/service-map-scom/scom-configuration.png)

2. **[接続構成ツール]** ウィンドウで、サービス プリンシパルのテナント名または ID、アプリケーション ID (ユーザー名またはクライアント ID とも呼ばれます)、およびパスワードを入力し、 **[次へ]** をクリックします。 詳細については、「サービス プリンシパルの作成」を参照してください。

    ![[接続構成ツール] ウィンドウ](media/service-map-scom/scom-config-spn.png)

3. **[サブスクリプションの選択]** ウィンドウで、Azure サブスクリプション、Azure リソース グループ (Log Analytics ワークスペースが含まれるグループ)、Log Analytics ワークスペースを選択し、 **[次へ]** をクリックします。

    ![Operations Manager 構成ワークスペース](media/service-map-scom/scom-config-workspace.png)

4. **[Machine Group Selection]\(コンピューター グループの選択\)** ウィンドウで、Operations Manager に同期する Service Map コンピューター グループを選びます。 **[Add/Remove Machine Groups]\(コンピューター グループの追加/削除\)** をクリックして、 **[Available Machine Groups]\(使用可能なコンピューター グループ\)** の一覧からグループを選択し、 **[追加]** をクリックします。  グループの選択が完了したら、 **[OK]** をクリックして完了します。

    ![Operations Manager 構成コンピューター グループ](media/service-map-scom/scom-config-machine-groups.png)

5. **[サーバーの選択]** ウィンドウで、Operations Manager とマップ機能の間の同期を行うサーバーを指定して、Service Map サーバー グループを構成します。 **[サーバーの追加と削除]** をクリックします。

    この統合によりサーバーの分散アプリケーション ダイアグラムを作成するには、サーバーが次の条件を満たす必要があります。

   * Operations Manager によって監視されている
   * VM insights を使用して構成されている Log Analytics ワークスペースに報告するように構成されている
   * Service Map サーバー グループに登録されている

     ![Operations Manager 構成グループ](media/service-map-scom/scom-config-group.png)

6. 省略可能:Log Analytics と通信するすべての管理サーバーのリソース プールを選択し、 **[ワークスペースの追加]** をクリックします。

    ![[すべての管理サーバーのリソース プール] が選択されている [Microsoft Operations Management Suite ワークスペースを追加します] の [サーバー プール] 画面のスクリーンショット。](media/service-map-scom/scom-config-pool.png)

    Log Analytics ワークスペースの構成および登録には時間がかかる場合があります。 構成が完了すると、Operations Manager は、マップの最初の同期を開始します。

    ![ワークスペースが追加されたことを確認する [Microsoft Operations Management Suite ワークスペースを追加します] の [完了] 画面のスクリーンショット。](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>統合の監視

Log Analytics ワークスペースが接続されると、Operations Manager オペレーション コンソールの **[監視]** ウィンドウに、Service Map という名前の新しいフォルダーが表示されます。

![Operations Manager の [監視] ウィンドウ](media/service-map-scom/scom-monitoring.png)

Service Map フォルダーには 4 つのノードがあります。

* **アクティブなアラート**:Operations Manager と Azure Monitor の間の通信に関するすべてのアクティブなアラートの一覧が表示されます。  

  >[!NOTE]
  >これらのアラートは、Operations Manager と同期された Log Analytics アラートではなく、Service Map 管理パックで定義されているワークフローに基づいて管理グループに生成されます。

* **サーバー**: VM insights のマップ機能と同期するように構成されている監視対象サーバーの一覧が表示されます。

    ![Operations Manager の [監視] の [サーバー] ウィンドウ](media/service-map-scom/scom-monitoring-servers.png)

* **マシン グループの依存関係ビュー**:マップ機能から同期されているすべてのマシン グループが一覧表示されます。 グループをクリックすると、その分散アプリケーション ダイアグラムを確認できます。

    ![各マシン グループのイメージと、それらの間の依存関係を示す線を含む図を示している Service Map のスクリーンショット。](media/service-map-scom/scom-group-dad.png)

* **サーバーの依存関係ビュー**:マップ機能から同期されているすべてのサーバーが一覧表示されます。 サーバーをクリックすると、その分散アプリケーション ダイアグラムを確認できます。

    ![各サーバーのイメージと、それらの間の依存関係を示す線を含む図を示している Service Map のスクリーンショット。](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>ワークスペースを編集または削除

構成済みワークスペースは、 **[サービス マップ概要]** ウィンドウ ( **[管理]** ウィンドウ > **[Operations Management Suite]**  >  **[サービス]** ) で編集または削除できます。

> [!NOTE]
> [Operations Management Suite はサービスのコレクション](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)であり Log Analytics に含まれていました。現在は、[Azure Monitor](../overview.md) の一部になっています。

現在のこのリリースで構成できる Log Analytics ワークスペースは 1 つのみです。

![Operations Manager の [ワークスペースの編集] ウィンドウ](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>規則とオーバーライドの構成

*Microsoft.SystemCenter.ServiceMapImport.Rule* という規則が VM insights のマップ機能から定期的に情報を取得します。 同期間隔を変更するには、規則をオーバーライドして、パラメーター **IntervalMinutes** の値を変更できます。

![Operations Manager の [Overrides properties (プロパティのオーバーライド)] ウィンドウ](media/service-map-scom/scom-overrides.png)

* **有効**: 自動更新を有効または無効にします。
* **IntervalMinutes**:更新間隔を指定します。 既定の間隔は 1 時間です。 マップの同期の頻度を上げるには、この値を変更できます。
* **TimeoutSeconds**:要求がタイムアウトになるまでの時間を指定します。
* **TimeWindowMinutes**:データに対するクエリの時間枠を指定します。 既定値は 60 分です。これは許容される最大間隔です。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

現在の設計には次の問題と制限があります。

* 1 つの Log Analytics ワークスペースにのみ接続することができます。
* **[作成]** ウィンドウでサーバーを Service Map サーバー グループに手動で追加できますが、そのサーバーのマップは、即座には同期されません。 これらは、次の同期サイクル中に Azure Monitor for VMs のマップ機能から同期されます。
* 管理パックによって作成された分散アプリケーション ダイアグラムに変更を加える場合は、VM insights による次回同期の際に、これらの変更が上書きされる可能性があります。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

サービス プリンシパル作成に関する Azure の公式ドキュメントについては、次を参照してください。

* [PowerShell を使用してサービス プリンシパルを作成する](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Azure CLI を使用してサービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Azure Portal を使用してサービス プリンシパルを作成する](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>検索候補

VM insights のマップ機能との統合、またはこのドキュメントについてフィードバックがある場合は、 [User Voice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)を是非ご利用ください。このページでは、機能を提案したり、既存の提案に投票したりすることができます。

