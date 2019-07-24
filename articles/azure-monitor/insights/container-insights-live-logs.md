---
title: コンテナー用 Azure Monitor のログをリアルタイムで表示する | Microsoft Docs
description: この記事では、コンテナー用 Azure Monitor によるコンテナー ログ (stdout と stderr) を、kubectl を使用せずにリアルタイム ビューで表示する方法について説明します。
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
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: 6fe8cccf60e60ada34e3b7847964958cf6e03c4a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904748"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>コンテナー用 Azure Monitor でコンテナー ログをリアルタイムで表示する方法 (プレビュー)
この機能は現在プレビュー段階ですが、これにより kubectl コマンドを実行せずに、お使いの Azure Kubernetes Service (AKS) のコンテナー ログ (stdout と stderr) をリアルタイム ビューで確認できます。 このオプションを選択すると、**[コンテナー]** ビューのコンテナー パフォーマンス データ テーブルの下に新しいウィンドウが表示されます。  ここには、リアルタイムでの問題のトラブルシューティングをさらに支援するために、コンテナー エンジンによって生成されたライブ ログが表示されます。 この機能を動作させるには、クラスター リソースへの**共同作成者**アクセスが必要です。

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
        resources: ["pods/log"] 
        verbs: ["get"] 
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

2. `kubectl create -f LogReaderRBAC.yaml` コマンドを実行して、クラスター ロール バインディングを作成します。 

## <a name="configure-aks-with-azure-active-directory"></a>Azure Active Directory で AKS を構成する
ユーザー認証に Azure Active Directory (AD) を使うように AKS を構成できます。 これを初めて構成する場合は、「[Azure Active Directory と Azure Kubernetes Service を統合する](../../aks/aad-integration.md)」を参照してください。 [クライアント アプリケーション](../../aks/aad-integration.md#create-client-application)を作成し、**リダイレクト URI** を指定する手順を実行しているときに、別の URI をリスト `https://ininprodeusuxbase.microsoft.com/*` に追加する必要があります。  

>[!NOTE]
>シングル サインオン用の Azure Active Directory での認証構成は、新しい AKS クラスターを最初にデプロイするときにのみ実行できます。 既にデプロイされている AKS クラスターに対して、シングル サインオンを構成することはできません。  
> 

## <a name="view-live-logs"></a>ライブ ログを表示する
**コンテナー**を表示しているときに、**コンテナー ログを表示**するか、**コンテナー ライブ ログを表示**できます。  **[コンテナー ライブ ログの表示]** を選択すると、新しいウィンドウがコンテナー パフォーマンス データ テーブルの下に表示され、コンテナー エンジンによって生成されたライブ ログが示されます。このログはリアルタイムでの問題のトラブルシューティングに役立ちます。  
1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. **[Microsoft Azure]** メニューで、**[モニター]**、**[コンテナー]** の順に選択します。  
3. **[Monitored container]\(監視対象コンテナー\)** ビューの下にある一覧から、コンテナーを選択します。  
4. **[コンテナー]** を選択すると、選択したコンテナーのプロパティ パネルに、**[コンテナー ライブ ログの表示]** リンクが表示されます。  
5. AKS クラスターが AAD を使用して SSO で構成されている場合は、そのブラウザー セッションで初めて使用するときに認証が求められます。 ご自身のアカウントを選択し、Azure での認証を完了します。  

認証が成功すると、ライブ ログ ウィンドウが、中央のウィンドウの下部セクションに表示されます。 フェッチ状態インジケーターのウィンドウの一番右に緑色のチェック マークが表示される場合は、データを取得できることを意味します。
    
  ![ライブ ログ ウィンドウの取得済みデータ](./media/container-insights-live-logs/live-logs-pane-01.png)  

検索バーで、キーワードによってフィルター処理して、ログ内のテキストを強調表示できます。   

  ![ライブ ログ ウィンドウのフィルターの例](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

自動スクロールを中断し、ウィンドウの動作を制御して、読み込まれた新しいログ データを手動でスクロールできるようにするには、**[スクロール]** オプションをクリックします。  自動スクロールを再度有効にするには、**[スクロール]** オプションをもう一度クリックします。  **[一時停止]** オプションをクリックして、ログ データの取得を一時停止することもできます。再開する準備ができたら、**[プレイ]** をクリックするだけです。  

![ライブ ログ ウィンドウのライブ ビューの一時停止](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>次の手順
Azure Monitor を使用して、AKS クラスターの他の側面を監視する方法を引き続き学習するには、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。
