---
title: Kubernetes にセルフホステッド ゲートウェイをデプロイする | Microsoft Docs
description: Azure API Management のセルフホステッド ゲートウェイ コンポーネントを Kubernetes にデプロイする方法について説明します
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89500132"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Kubernetes にセルフホステッド ゲートウェイをデプロイする

この記事では、Azure API Management のセルフホステッド ゲートウェイ コンポーネントを Kubernetes クラスターにデプロイする手順について説明します。

## <a name="prerequisites"></a>前提条件

- 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
- Kubernetes クラスターを作成します。
   > [!TIP]
   > [単一ノード クラスター](https://kubernetes.io/docs/setup/#learning-environment)は、開発と評価の目的に適しています。 運用環境のワークロードには、オンプレミスまたはクラウドで [Kubernetes 認定](https://kubernetes.io/partners/#conformance)のマルチノード クラスターを使用します。
- [API Management インスタンスでセルフホステッド ゲートウェイ リソースをプロビジョニングします](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-to-kubernetes"></a>Kubernetes へのデプロイ

1. **[Deployment and infrastructure]\(デプロイとインフラストラクチャ\)** で **[ゲートウェイ]** を選択します。
2. デプロイするセルフホステッド ゲートウェイ リソースを選択します。
3. **[Deployment]/(デプロイ/)** を選択します。
4. **[トークン]** テキスト ボックスのアクセス トークンは、既定の **[有効期限]** 値と **[秘密鍵]** 値に基づいて自動生成されました。 必要に応じて、いずれかまたは両方のコントロールで値を選択し、新しいトークンを生成します。
5. **[デプロイ スクリプト]** の下にある **[Kubernetes]** タブを選択します。
6. **\<gateway-name\>.yml** ファイル リンクを選択し、YAML ファイルをダウンロードします。
7. **[デプロイ]** テキスト ボックスの右下隅にある **[コピー]** アイコンを選択し、`kubectl` コマンドをクリップボードに保存します。
8. コマンドをターミナル (またはコマンド) ウィンドウに貼り付けます。 最初のコマンドでは、手順 4 で生成されたアクセス トークンを含めた Kubernetes シークレットを作成します。 2 番目のコマンドでは、手順 6 でダウンロードした構成ファイルを Kubernetes クラスターに適用し、ファイルが現在のディレクトリにあることを求めます。
9. コマンドを実行して[既定の名前空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)に必要な Kubernetes オブジェクトを作成し、Microsoft Container Registry からダウンロードされた[コンテナー イメージ](https://aka.ms/apim/sputnik/dhub)からセルフホステッド ゲートウェイ ポッドを起動します。
10. 次のコマンドを実行し、デプロイが成功したかどうかを確認します。 すべてのオブジェクトが作成され、ポッドが初期化されるまでに少し時間がかかる場合があることにご注意ください。
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. サービスが正常に作成されたかどうかを確認するには、次のコマンドを実行します。 サービスの IP とポートが異なることに注意してください。
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Azure portal に戻り、 **[概要]** を選択します。
13. **[状態]** に緑のチェック マークが表示されていることを確認し、そのマークの後ろのノード数が YAML ファイルに指定されているレプリカ数に一致することを確認します。 この状態は、デプロイされたセルフホステッド ゲートウェイ ポッドが API Management サービスと正常に通信しており、"ハートビート" が通常であることを意味します。

    ![ゲートウェイの状態](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> ランダムに選択されたポッドのログを表示するには、<code>kubectl logs deployment/<gateway-name></code> コマンドを実行します (複数存在する場合)。
> 特定のポッドまたはコンテナーのログを表示する方法など、コマンド オプションの完全なセットに対して <code>kubectl logs -h</code> を実行します。

## <a name="production-deployment-considerations"></a>運用環境のデプロイに関する考慮事項

### <a name="access-token"></a>アクセス トークン
有効なアクセス トークンがない場合、セルフホステッド ゲートウェイで、関連付けられている API Management サービスのエンドポイントから構成データにアクセスしてダウンロードすることができません。 アクセス トークンは最大で 30 日間有効です。 有効期限が切れる前に再生成し、手動か自動化により、新しいトークンでクラスターを構成する必要があります。

トークンの更新を自動化する場合、この管理 API [操作](/rest/api/apimanagement/2019-12-01/gateway/generatetoken)を使用して新しいトークンを生成します。 Kubernetes シークレットの管理の詳細については、[Kubernetes Web サイト](https://kubernetes.io/docs/concepts/configuration/secret)を参照してください。

### <a name="namespace"></a>名前空間
Kubernetes [名前空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)を使用すると、1 つのクラスターを複数のチーム、プロジェクト、またはアプリケーションに分割する際に役立ちます。 名前空間からはリソースと名前の範囲が与えられます。 リソース クォータとアクセス制御ポリシーに関連付けることができます。

Azure portal からは、**既定** の名前空間でセルフホステッド ゲートウェイ リソースを作成するコマンドが与えられます。 この名前空間は自動的に作成され、あらゆるクラスターに存在します。削除できません。
運用環境の別の名前空間にセルフホステッド ゲートウェイを[作成およびデプロイ](https://kubernetesbyexample.com/ns/)することを検討してください。

### <a name="number-of-replicas"></a>レプリカの数
運用環境に適したレプリカの最小数は 2 です。

既定では、セルフホステッド ゲートウェイは、**RollingUpdate** デプロイ [戦略](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)を使用してデプロイされます。 既定値を確認し、特に使用するレプリカの数が多い場合は、[maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) および [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) フィールドに明示的に設定することを検討してください。

### <a name="container-resources"></a>コンテナー リソース
既定では、Azure portal で提供される YAML ファイルには、コンテナー リソース要求が指定されていません。

コンテナーごとの CPU およびメモリ リソースの量と、特定のワークロードをサポートするために必要なレプリカの数を確実に予測して推奨することは不可能です。 それには次のようなさまざまな要因があります。

- クラスターが実行されている特定のハードウェア。
- 仮想化の存在と種類。
- 同時クライアント接続の数と割合。
- 要求レート。
- 構成されたポリシーの種類と数。
- ペイロードのサイズと、ペイロードがバッファーされるのか、ストリーミングされるのか。
- バックエンド サービスの待機時間。

リソース要求を 2 コアと 2 GiB に設定することから始めることをお勧めします。 ロード テストを実行し、結果に基づいてスケールアップ、スケールダウン、スケールアウト、スケールインします。

### <a name="container-image-tag"></a>コンテナー イメージ タグ
Azure portal に用意されている YAML ファイルでは、**最新の** タグが使用されます。 このタグでは常に、最新版のセルフホステッド ゲートウェイ コンテナー イメージが参照されます。

新しいバージョンへの意図しないアップグレードを避けるために、運用環境で特定のバージョン タグを使用することを検討してください。

利用できるタグの全一覧は[こちら](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list)からダウンロードできます。

### <a name="dns-policy"></a>DNS ポリシー
DNS 名前解決は、Azure 内の依存関係に接続してバックエンド サービスに API 呼び出しをディスパッチする、セルフホステッド ゲートウェイの機能において重要な役割を果たします。

Azure portal に用意されている YAML ファイルからは、既定の [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) ポリシーが適用されます。 このポリシーによって、クラスター DNS で解決されない名前解決要求は、ノードから継承される上流 DNS サーバーに転送されます。

Kubernetes での名前解決の詳細については、[Kubernetes Web サイト](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service)を参照してください。 自分のセットアップに合わせて [DNS ポリシー](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)または [DNS 構成](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config)をカスタマイズすることを検討してください。

### <a name="external-traffic-policy"></a>外部トラフィック ポリシー
Azure portal で提供されている YAML ファイルでは、[[サービス]](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) オブジェクトの `externalTrafficPolicy` フィールドが `Local` に設定されます。 これにより、([要求コンテキスト](api-management-policy-expressions.md#ContextVariables)でアクセスできる) 呼び出し元の IP アドレスが保持され、ノード間の負荷分散が無効になるため、発生するネットワーク ホップが排除されます。 この設定では、1 ノードあたりのゲートウェイ ポッド数が等しくないデプロイでトラフィックが非対称に分散される可能性があることに注意してください。

### <a name="custom-domain-names-and-ssl-certificates"></a>カスタム ドメイン名と SSL 証明書

API Management エンドポイントにカスタム ドメイン名を使用する場合、とりわけ、管理エンドポイントのためにカスタム ドメイン名を使用する場合は、 **\<gateway-name\>.yaml** ファイルの `config.service.endpoint` の値を更新して、既定のドメイン名をカスタム ドメイン名に置き換える必要が生じる場合があります。 Kubernetes クラスター内のセルフホステッド ゲートウェイのポッドから管理エンドポイントにアクセスできることを確認します。

このシナリオでは、管理エンドポイントによって使用される SSL 証明書が、既知の CA 証明書によって署名されていない場合は、その CA 証明書がセルフホステッド ゲートウェイのポッドによって信頼されていることを確認する必要があります。

### <a name="configuration-backup"></a>構成のバックアップ
Azure の接続が一時的に停止した場合のセルフホステッド ゲートウェイの動作については、「[セルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md#connectivity-to-azure)」を参照してください。

セルフホステッド ゲートウェイ コンテナーのローカル ストレージ ボリュームを構成して、ダウンロードされた最新の構成のバックアップ コピーを保持できるようにします。 接続がダウンしている場合、ストレージ ボリュームでは再起動時、バックアップ コピーを利用できます。 ボリューム マウント パスは <code>/apim/config</code> である必要があります。 例は [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml) でご覧ください。
Kubernetes でのストレージの詳細については、[Kubernetes Web サイト](https://kubernetes.io/docs/concepts/storage/volumes/)を参照してください。

### <a name="local-logs-and-metrics"></a>ローカル ログとメトリック
セルフホスト ゲートウェイでは、関連する API Management サービスの構成設定ごとに、テレメトリが [Azure Monitor](api-management-howto-use-azure-monitor.md) と [Azure Application Insights](api-management-howto-app-insights.md) に送信されます。
[Azure への接続](self-hosted-gateway-overview.md#connectivity-to-azure)が一時的に失われた場合、Azure へのテレメトリのフローは中断され、停止中のデータが失われます。
API トラフィックを監視し、Azure の接続が停止している間のテレメトリの損失を防ぐ機能を確保するために、[ローカル監視の設定](how-to-configure-local-metrics-logs.md)を検討してください。

## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[セルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関するページを参照してください。
