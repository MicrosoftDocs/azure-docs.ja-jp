---
title: Kubernetes ワークロードのワークロード保護
description: Azure Security Center の、Kubernetes ワークロード保護のセキュリティの推奨事項を使用する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: eacca5573c672e9f4485c26b1b580ee4c982c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580754"
---
# <a name="protect-your-kubernetes-workloads"></a>Kubernetes ワークロードを保護する

このページでは、Azure Security Center の、Kubernetes ワークロード保護専用のセキュリティの推奨事項を使用する方法について説明します。

これらの機能の詳細については、[Kubernetes 受付制御を使用したワークロード保護のベスト プラクティス](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)に関する記事をご覧ください。

Azure Defender を有効にすると、Security Center でより多くのコンテナー セキュリティ機能が提供されます。 具体的な内容は次のとおりです。

- [Azure Defender for container registries](defender-for-container-registries-introduction.md) を使用して、コンテナー レジストリの脆弱性をスキャンする
- [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) を使用して、K8s クラスターのリアルタイムの脅威検出アラートを取得する

> [!TIP]
> 表示される可能性がある、Kubernetes クラスターおよびノードに関する "*すべての*" セキュリティの推奨事項の一覧については、推奨事項リファレンス表の [コンピューティング セクション](recommendations-reference.md#recs-compute)をご覧ください。



## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|割り当てを編集するための **所有者** または **セキュリティ管理者**<br>推奨事項を表示するための **閲覧者**|
|環境要件:|Kubernetes v1.14 以降が必要です。<br>クラスター上の PodSecurityPolicy リソース (古い PSP モデル) はありません。<br>Windows ノードはサポートされていません。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||


## <a name="set-up-your-workload-protection"></a>ワークロード保護を設定する

Azure Security Center には、**Kubernetes 用の Azure Policy アドオン** がインストールされている場合に使用できる推奨事項のバンドルが含まれています。

### <a name="step-1-deploy-the-add-on"></a>手順 1:アドオンをデプロイする

推奨設定を構成するために、**Kubernetes 用の Azure Policy アドオン** をインストールします。 

- 「[Log Analytics エージェントと拡張機能の自動プロビジョニングを有効にする](security-center-enable-data-collection.md#auto-provision-mma)」で説明されているように、このアドオンは自動的にデプロイできます。 アドオンの自動プロビジョニングが "オン" に設定されている場合は、既存のクラスターおよび今後作成されるクラスターすべて (アドオンのインストール要件を満たすもの) で、拡張機能が既定で有効になります。

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Security Center の自動プロビジョニング ツールを使用して Kubernetes 用の Policy アドオンをインストールする":::

- アドオンを手動でデプロイするには:

    1. 推奨事項ページで、"**Kubernetes 用の Azure Policy アドオンをクラスターにインストールして有効にする必要があります**" という推奨事項を検索します。 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="推奨事項 **Kubernetes 用の Azure Policy アドオンをクラスターにインストールして有効にする必要があります**":::

        > [!TIP]
        > この推奨事項は 5 つの異なるセキュリティ コントロールに含まれていますが、次の手順でどれを選択してもかまいません。

    1. いずれかのセキュリティ コントロールから、推奨事項を選択して、アドオンをインストールできるリソースを確認します。
    1. 関連するクラスターを選択し、 **[修復]** を選択します。

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="**Kubernetes 用の Azure Policy アドオンをクラスターにインストールして有効にする必要があります** という推奨事項の詳細ページ":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>手順 2:13 個の推奨事項のバンドルを表示および構成する

1. アドオンのインストールが完了してから約 30 分後に、Security Center は、関連する各セキュリティ コントロールに、次の推奨事項についてのクラスターの正常性状態を表示します。

    > [!TIP]
    > 一部の推奨事項にはパラメーターが含まれており、それらを効果的に使用するために、Azure Policy を使用してカスタマイズする必要があります。 たとえば、**コンテナー イメージは信頼されたレジストリからのみデプロイする必要があります** という推奨事項のメリットを得るには、信頼されたレジストリを定義する必要があります。
    > 
    > 構成を必要とする推奨事項に必要なパラメーターを入力していない場合、ワークロードは異常と表示されます。

    | 推奨事項の名前                                                         | セキュリティ コントロール                         | 構成が必要 |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | コンテナーの CPU とメモリの制限を強制する必要がある                          | DDoS 攻撃からのアプリケーションの保護 | No                     |
    | 特権コンテナーの使用を避ける                                     | アクセスおよびアクセス許可の管理            | No                     |
    | コンテナーで不変 (読み取り専用) のルート ファイル システムを適用する必要がある     | アクセスおよびアクセス許可の管理            | No                     |
    | 特権エスカレーションを含むコンテナーは避ける必要がある                       | アクセスおよびアクセス許可の管理            | No                     |
    | コンテナーをルート ユーザーとして実行しない                           | アクセスおよびアクセス許可の管理            | No                     |
    | 機密性の高いホストの名前空間を共有するコンテナーは避ける必要がある              | アクセスおよびアクセス許可の管理            | No                     |
    | コンテナーで最小限の特権を持つ Linux 機能を適用する必要がある       | アクセスおよびアクセス許可の管理            | **はい**                |
    | ポッド HostPath ボリューム マウントの使用は既知のリストに制限する必要がある    | アクセスおよびアクセス許可の管理            | **はい**                |
    | コンテナーは許可されたポートでのみリッスンする必要がある                              | 承認されていないネットワーク アクセスの制限     | **はい**                |
    | サービスは許可されたポートでのみリッスンする必要がある                                | 承認されていないネットワーク アクセスの制限     | **はい**                |
    | ホスト ネットワークとポートの使用を制限する必要がある                     | 承認されていないネットワーク アクセスの制限     | **はい**                |
    | コンテナーの AppArmor プロファイルのオーバーライドまたは無効化を制限する必要がある | セキュリティ構成の修復        | **はい**                |
    | コンテナー イメージは信頼されたレジストリからのみデプロイする必要がある            | 脆弱性の修復                | **はい**                |
    |||


1. カスタマイズする必要があるパラメーターが含まれた推奨事項では、次のようにパラメーターを設定します。

    1. Security Center のメニューから、 **[セキュリティ ポリシー]** を選択します。
    1. 関連するサブスクリプションを選択します。
    1. **[Security Center の既定のポリシー]** セクションで、 **[有効なポリシーの表示]** を選択します。
    1. "ASC の既定値" を選択します。
    1. **[パラメーター]** タブを開き、必要に応じて値を変更します。
    1. **[確認と保存]** を選択します。
    1. **[保存]** を選択します。


1. 推奨事項を適用するには、 

    1. 推奨事項の詳細ページを開き、 **[拒否]** を選択します。

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Azure Policy パラメーターの [拒否] オプション":::

        これにより、スコープを設定したペインが開きます。 

    1. スコープを設定したら、 **[Change to deny]\(拒否への変更\)** を選択します。

1. クラスターに適用される推奨事項を確認するには、次の手順に従います。

    1. Security Center の [資産インベントリ](asset-inventory.md) ページを開き、リソースの種類のフィルターを使用して **Kubernetes サービス** を選択します。

    1. 調査するクラスターを選択し、クラスターで利用可能な推奨事項を確認します。 

1. ワークロード保護セットから推奨事項を表示すると、影響を受けるポッド ("Kubernetes コンポーネント") の数がクラスターの横に表示されます。 特定のポッドの一覧を表示するには、クラスターを選択し、 **[アクションの実行]** を選択します。

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="K8s の推奨事項の影響を受けるポッドの表示"::: 

1. 適用をテストするには、次の 2 つの Kubernetes デプロイを使用します。

    - 1 つは、ワークロード保護の推奨事項のバンドルに準拠した正常なデプロイ用です。
    - もう 1 つは、推奨事項の "*いずれか*" に準拠していない異常なデプロイ用です。

    サンプル .yaml ファイルをそのままデプロイするか、独自のワークロードを修復するための参照として使用します (手順 VIII)。  


## <a name="healthy-deployment-example-yaml-file"></a>正常なデプロイのサンプル.yaml ファイル

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>異常なデプロイのサンプル.yaml ファイル

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>次の手順

この記事では、Kubernetes ワークロード保護を構成する方法について説明しました。 

その他の関連資料については、次のページを参照してください。 

- [コンピューティングに関する Security Center の推奨事項](recommendations-reference.md#recs-compute)
- [AKS クラスター レベルのアラート](alerts-reference.md#alerts-akscluster)
- [コンテナー ホスト レベルのアラート](alerts-reference.md#alerts-containerhost)