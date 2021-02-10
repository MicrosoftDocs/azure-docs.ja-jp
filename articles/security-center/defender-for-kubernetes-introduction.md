---
title: Azure Defender for Kubernetes - 利点と機能
description: Azure Defender for Kubernetes の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a19e90a15991cdc03999bf43d5bece63325aab05
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916576"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes の概要

Azure Kubernetes Service (AKS) は、コンテナー化されたアプリケーションを開発、デプロイ、管理するための Microsoft のマネージド サービスです。

Azure Security Center と AKS を組み合わせることで、最高レベルのクラウドネイティブな Kubernetes セキュリティ オファリングが形成され、以下で概説するように、環境のセキュリティ強化、ワークロード保護、実行時保護が実現されます。

Kubernetes クラスターの脅威を検出するには、**Azure Defender for Kubernetes** を有効にします。

[Azure Defender for server](defender-for-servers-introduction.md) を有効にすると、Linux AKS ノードのホスト レベルの脅威検出を使用できます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**Azure Defender for Kubernetes** の課金については、[価格に関するページ](security-center-pricing.md)をご覧ください。|
|必要なロールとアクセス許可:|**セキュリティ管理者** はアラートを無視できます。<br>**セキュリティ閲覧者** は、結果を表示できます。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes の利点

### <a name="run-time-protection"></a>実行時保護

Security Center では、次の AKS ソースの継続的な分析によって、コンテナー化された環境に対する脅威をリアルタイムで防止し、ホスト *および* AKS クラスター レベルで検出された脅威や悪意のあるアクティビティに関するアラートを生成します。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

Security Center では、さまざまなレベルで脅威の防止が提供されます。 

- **ホスト レベル (Azure Defender for servers が提供)** - Azure Defender は、Security Center が他の VM で使用しているものと同じ Log Analytics エージェントを使用して、Web シェルの検出や既知の疑わしい IP アドレスを使用した接続などの不審なアクティビティについて、Linux AKS ノードを監視します。 エージェントは、特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行など、コンテナー固有の分析についても監視します。

    AKS ホスト レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-containerhost)をご覧ください。

    >[!IMPORTANT]
    > ホストにエージェントをインストールしなかった場合、脅威の防止によってもたらされるメリットとセキュリティ アラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

- **AKS クラスター レベル (Azure Defender for Kubernetes が提供)** - クラスター レベルでは、脅威の防止は Kubernetes の監査ログの分析に基づいています。 この **エージェントレス型** 監視を有効にするには、Azure Defender を有効にします。 このレベルのアラートを生成するために、Security Center は、AKS によって取得されたログを使用して、AKS のマネージド サービスを監視します。 このレベルのイベントの例として、公開されている Kubernetes ダッシュボード、高い特権ロールの作成、機微なマウントの作成などがあります。

    AKS クラスター レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-akscluster)に関するページを参照してください。

    >[!NOTE]
    > Azure Kubernetes Service のアクションやデプロイについてのセキュリティ アラートが Security Center から生成されるのは、サブスクリプションの設定で Kubernetes オプションが有効にされた後になります。 

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Security Center の Kubernetes 保護のしくみ

Azure Security Center、Azure Kubernetes Service、Azure Policy 間の相互作用の概要図を次に示します。

Security Center が受信して分析する項目には、以下が含まれていることがわかります。

- API サーバーからの監査ログ
- Log Analytics エージェントからの生のセキュリティ イベント
- AKS クラスターからのクラスター構成情報
- Azure Policy からのワークロード構成 (**Kubernetes 用 の Azure Policy アドオン** を使用) [Kubernetes 受付制御を使用したワークロード保護のベスト プラクティスの詳細をご覧ください](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)。

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure Security Center、Azure Kubernetes Service、Azure Policy 間の相互作用のアーキテクチャ概要" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes - よくあるご質問

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Log Analytics エージェントを使用しなくても AKS 保護を利用できますか。

前述のように、オプションの **Azure Defender for Kubernetes** プランではクラスター レベルで保護を提供し、**Azure Defender for servers** の Log Analytics エージェントはノードを保護します。 

可能なかぎり最も完全な保護を実現するためには、両方をデプロイすることをお勧めします。

ホストにエージェントをインストールしないことを選択した場合、脅威の防止の利点とセキュリティ アラートの一部だけが提供されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。


### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS を使用すると、AKS ノードにカスタム VM 拡張機能をインストールできますか。

Azure Defender で AKS ノードを監視するには、それらのノードで Log Analytics エージェントが実行されている必要があります。 

AKS は管理サービスであり、Log Analytics エージェントは Microsoft マネージド拡張機能であるため、AKS クラスターでもサポートされています。



### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>クラスターでコンテナー エージェントに対して Azure Monitor が既に実行されている場合、Log Analytics エージェントも必要ですか。

Azure Defender で AKS ノードを監視するには、それらのノードで Log Analytics エージェントが実行されている必要があります。

クラスターでコンテナー エージェントに対して Azure Monitor が既に実行されている場合は、Log Analytics エージェントもインストールできます。この 2 つのエージェントは、問題なく一緒に動作できます。

[コンテナー エージェントに対する Azure Monitor の詳細を学習](../azure-monitor/insights/container-insights-manage-agent.md)します。


## <a name="next-steps"></a>次のステップ

この記事では、Azure Defender for Kubernetes など、Security Center の Kubernetes 保護について説明しました。 

関連資料については、次の記事をご覧ください。 

- [Azure Defender を有効にする](security-center-pricing.md#enable-azure-defender)
- [SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)
- [アラートのリファレンス表](alerts-reference.md)