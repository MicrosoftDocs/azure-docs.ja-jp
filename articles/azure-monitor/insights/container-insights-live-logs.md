---
title: コンテナー用 Azure Monitor のログをリアルタイムで表示する | Microsoft Docs
description: この記事では、コンテナー用 Azure Monitor によるコンテナー ログ (stdout と stderr) およびイベントを、kubectl を使用せずにリアルタイム ビューで表示する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: magoedte
ms.openlocfilehash: 7fd9248fd38054b7f0e1fad2888d8b0d4cf2e60c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274229"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>ログとイベントをリアルタイムで表示する方法 (プレビュー)
コンテナー用 Azure Monitor (現在、プレビュー段階) には、kubectl コマンドを実行せずに、Azure Kubernetes Service (AKS) コンテナーのログ (stdout と stderr) とイベントのライブ ビューを提供する機能が含まれています。 いずれかのオプションを選択すると、 **[ノード]** 、 **[コントローラー]** 、 **[コンテナー]** ビューのパフォーマンス データ テーブルの下に新しいウィンドウが表示されます。 ここにはコンテナー エンジンによって生成されたライブ ログおよびイベントが表示され、リアルタイムでの問題のトラブルシューティングに一層役立てることができます。

>[!NOTE]
>この機能を動作させるには、クラスター リソースへの**共同作成者**アクセスが必要です。
>

ライブ ログでは、次の 3 つの方法でログへのアクセスが制御されます。

1. Kubernetes RBAC 認証なしの AKS が有効
2. Kubernetes RBAC 認証を使って AKS が有効
3. Azure Active Directory (AD) SAML ベースのシングル サインオンを使って AKS が有効

## <a name="kubernetes-cluster-without-rbac-enabled"></a>RBAC なしの Kubernetes クラスターが有効
 
お使いの Kubernetes クラスターが Kubernetes RBAC 認証で構成されていない場合、または Azure AD シングル サインオンと統合されていない場合は、これらの手順に従う必要はありません。 Kubernetes 認証では kube-api が使用されるため、読み取り専用のアクセス許可が必要です。

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC 認証
Kubernetes RBAC 認証を有効にした場合は、クラスター ロール バインディングを適用する必要があります。 次の例の手順は、この yaml 構成テンプレートからクラスター ロール バインディングを構成する方法を示しています。 

1. yaml ファイルをコピーして貼り付け、LogReaderRBAC.yaml として保存します。  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. 初めてこれを構成する場合は、次のコマンドを実行して、クラスター ロール バインディングを作成します: `kubectl create -f LogReaderRBAC.yaml`。 ライブ イベント ログが導入される前に、あらかじめライブ ログのサポート (プレビュー) を有効にしていた場合、お使いの構成を更新するには、次のコマンドを実行します: `kubectl apply -f LogReaderRBAC.yml`。

## <a name="configure-aks-with-azure-active-directory"></a>Azure Active Directory で AKS を構成する

ユーザー認証に Azure Active Directory (AD) を使うように AKS を構成できます。 これを初めて構成する場合は、「[Azure Active Directory と Azure Kubernetes Service を統合する](../../aks/azure-ad-integration.md)」を参照してください。 その手順の中で[クライアント アプリケーション](../../aks/azure-ad-integration.md#create-the-client-application)を作成するには、次のものを指定します。

- **リダイレクト URI (省略可能)** : これは **Web** アプリケーションの種類であり、ベース URL の値は `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` である必要があります。
- アプリケーションを登録した後、 **[概要]** ページの左側のウィンドウから **[認証]** を選択します。 **[認証]** ページで、 **[詳細設定]** の **[アクセス トークン]** と **[ID トークン]** を暗黙的に許可してから、変更内容を保存します。

>[!NOTE]
>シングル サインオン用の Azure Active Directory での認証の構成は、新しい AKS クラスターの最初のデプロイ中にのみ実行できます。 既にデプロイされている AKS クラスターに対して、シングル サインオンを構成することはできません。
  
>[!IMPORTANT]
>更新された URI を使用したユーザー認証のために Azure AD を再構成した場合は、ブラウザーのキャッシュをクリアして、更新された認証トークンが確実にダウンロードおよび適用されるようにします。   

## <a name="view-live-logs-and-events"></a>ライブ ログおよびイベントを表示する

ログ イベントがコンテナー エンジンによって生成されたときに、 **[ノード]** 、 **[コントローラー]** 、および **[コンテナー]** ビューでこれらをリアルタイムに表示できます。 プロパティ ウィンドウで **[ライブ データを表示する (プレビュー)]** オプションを選択すると、パフォーマンス データ テーブルの下にウィンドウが表示され、連続的なストリームとしてログとイベントを表示できます。

![ノード プロパティ ウィンドウのライブ ログの表示オプション](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

ログとイベント メッセージは、ビューで選択されているリソースの種類に基づいて制限されます。

| 表示 | リソースの種類 | ログまたはイベント | 表示されるデータ |
|------|---------------|--------------|----------------|
| Nodes | ノード | Event | ノードが選択されている場合、イベントはフィルター処理されず、クラスター全体の Kubernetes イベントが表示されます。 ウィンドウ タイトルには、クラスターの名前が表示されます。 |
| Nodes | Pod | Event | ポッドが選択されている場合、その名前空間でイベントがフィルター処理されます。 ウィンドウ タイトルには、ポッドの名前空間が表示されます。 | 
| コントローラー | Pod | Event | ポッドが選択されている場合、その名前空間でイベントがフィルター処理されます。 ウィンドウ タイトルには、ポッドの名前空間が表示されます。 |
| コントローラー | コントローラー | Event | コントローラーが選択されている場合、その名前空間でイベントがフィルター処理されます。 ウィンドウ タイトルには、コントローラーの名前空間が表示されます。 |
| ノード/コントローラー/コンテナー | コンテナー | ログ | ウィンドウ タイトルには、コンテナーがグループ化されているポッドの名前が表示されます。 |

AKS クラスターが AAD を使用して SSO で構成されている場合は、そのブラウザー セッションで初めて使用するときに認証が求められます。 ご自身のアカウントを選択し、Azure での認証を完了します。  

認証が成功すると、ライブ ログ ウィンドウが、中央のウィンドウの下部セクションに表示されます。 フェッチ状態インジケーターのウィンドウの一番右に緑色のチェック マークが表示される場合は、データを取得できることを意味します。
    
  ![ライブ ログ ウィンドウの取得済みデータ](./media/container-insights-live-logs/live-logs-pane-01.png)  

検索バーでキー ワードによってフィルター処理して、そのテキストが含まれるログまたはイベントを強調表示できます。また、検索バーの右端にはフィルターに一致した結果の数が表示されます。

  ![ライブ ログ ウィンドウのフィルターの例](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

イベントが表示されているときに、検索バーの右にある **[フィルター]** ピルを使用して結果をさらに制限できます。 選択したリソースに応じて、このピルにより、選択の対象となるポッド、名前空間、またはクラスターが一覧表示されます。  

自動スクロールを中断し、ウィンドウの動作を制御して、読み込まれた新しいデータを手動でスクロールできるようにするには、 **[スクロール]** オプションをクリックします。 自動スクロールを再度有効にするには、 **[スクロール]** オプションをもう一度クリックします。 **[一時停止]** オプションをクリックして、ログまたはイベント データの取得を一時停止することもできます。再開する準備ができたら、 **[プレイ]** をクリックするだけです。  

![ライブ ログ ウィンドウのライブ ビューの一時停止](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

コンテナー ログの履歴を表示するには、Azure Monitor ログに移動して、 **[分析で表示する]** ドロップダウン リストから **[コンテナー ログの表示]** を選択します。

## <a name="next-steps"></a>次の手順
- Azure Monitor を使用して、AKS クラスターの他の側面を監視する方法を引き続き学習するには、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。
- [ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。
