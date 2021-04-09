---
title: Azure VMware Solution でパブリック IP 機能を使用する方法
description: この記事では、Azure Virtual WAN でパブリック IP 機能を使用する方法について説明します。
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 640f359ed8934048bffd7f58b36e8a3eef71c2f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581382"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Azure VMware Solution でパブリック IP 機能を使用する方法

パブリック IP は、Azure VMware Solution 接続の新機能です。 これにより、Web サーバー、仮想マシン (VM)、およびパブリック ネットワーク経由でアクセス可能なホストなどのリソースが作成されます。 

パブリック インターネット アクセスは、次の 2 つの方法で有効にします。 

- アプリケーションは、HTTP/HTTPS トラフィックの Application Gateway ロード バランサーでホストおよび公開することができます。
- Azure Virtual WAN でパブリック IP 機能を使用して公開されます。

Azure VMware Solution のプライベート クラウド デプロイの一環として、パブリック IP 機能を有効にすると、オートメーションで必要なコンポーネントが作成されて有効になります。

-  Virtual WAN

-  ExpressRoute 接続を使用する Virtual WAN ハブ

-  パブリック IP を使用する Azure Firewall サービス

この記事では、Virtual WAN でパブリック IP 機能を使用する方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件

- Azure VMware Solution 環境
- Azure VMware Solution 環境で実行されている Web サーバー。
- Virtual WAN ハブのデプロイ用の重複しない新しい IP 範囲 (通常は `/24`)。

## <a name="reference-architecture"></a>参照アーキテクチャ

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="パブリック IP アーキテクチャの図" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

アーキテクチャ図は、Azure VMware Solution 環境でホストされ、RFC1918 プライベート IP アドレスを使用して構成された Web サーバーを示しています。  Web サービスは、Virtual WAN パブリック IP 機能を通じてインターネットで利用できます。  パブリック IP は通常、Azure Firewall で変換された宛先 NAT です。 DNAT 規則に従い、ファイアウォール ポリシーによって、パブリック IP アドレス要求がポートを持つプライベート アドレス (Web サーバー) に変換されます。

ユーザー要求がパブリック IP のファイアウォールに達した後、Azure Firewall の DNAT 規則を使用してプライベート IP に変換されます。 ファイアウォールによって NAT テーブルが確認され、要求がエントリと一致する場合は、Azure VMware Solution 環境の変換されたアドレスとポートにトラフィックが転送されます。

Web サーバーによって、その要求が受信され、要求された情報またはページでファイアウォールへの応答が行われます。その後、ファイアウォールによって、パブリック IP アドレスのユーザーに情報が転送されます。

## <a name="test-case"></a>テスト ケース
このシナリオでは、IIS Web サーバーをインターネットに公開します。 パブリック IP アドレスで Web サイトを公開するには、Azure VMware Solution のパブリック IP 機能を使用します。  ファイアウォールで NAT 規則を構成し、パブリック IP を使用して Azure VMware Solution リソース (Web サーバーを利用する VM) にアクセスします。

## <a name="deploy-virtual-wan"></a>Virtual WAN をデプロイする

1. Azure portal にサインインし、**Azure VMware Solution** を検索して選択します。

1. Azure VMware Solution プライベート クラウドを選択します。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Azure VMware Solution プライベート クラウドのスクリーンショット。" border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. **[管理]** で、 **[接続]** を選択します。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="[接続] セクションのスクリーンショット。" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. **[パブリック IP]** タブを選択してから、 **[構成]** を選びます。

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="パブリック IP の構成を開始する場所を示すスクリーンショット" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. 既定値をそのまま使用するか変更してから、 **[作成]** を選択します。

   - Virtual WAN リソース グループ

   - 仮想 WAN の名前

   - 仮想ハブ アドレス ブロック (重複しない新しい IP 範囲を使用)

   - パブリック IP の数 (1 から 100)

すべてのコンポーネントのデプロイが完了するまで、約 1 時間かかります。 このデプロイは、この Azure VMware Solution 環境用の今後のすべてのパブリック IP をサポートするために 1 回だけ実行する必要があります。  

>[!TIP]
>**[通知]** 領域から状態を監視することができます。 

## <a name="view-and-add-public-ip-addresses"></a>パブリック IP アドレスを表示および追加する

以下の手順に従って、さらにパブリック IP アドレスを確認して追加することができます。

1. Azure portal で、**Firewall** を検索して選択します。

1. デプロイされているファイアウォールを選んでから、 **[Visit Azure Firewall Manager to configure and manage this firewall]\(Azure Firewall Manager にアクセスして、このファイアウォールを構成および管理する\)** を選択します。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="ファイアウォールを構成および管理するためのオプションを示すスクリーンショット" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **[セキュリティ保護付き仮想ハブ]** を選択し、一覧から仮想ハブを選びます。

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Firewall Manager のスクリーンショット" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. 仮想ハブ ページで、 **[パブリック IP 構成]** を選択します。さらにパブリック IP アドレスを追加するには、 **[追加]** を選択します。 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Firewall Manager でパブリック IP 構成を追加する方法のスクリーンショット" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. 必要な IP の数を指定し、 **[追加]** を選択します。

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="指定した数のパブリック IP 構成を追加するスクリーンショット" border="true":::


## <a name="create-firewall-policies"></a>ファイアウォール ポリシーを作成する

すべてのコンポーネントがデプロイされたら、追加されたリソース グループで表示することができます。 次の手順は、ファイアウォール ポリシーの追加です。

1. Azure portal で、**Firewall** を検索して選択します。

1. デプロイされているファイアウォールを選んでから、 **[Visit Azure Firewall Manager to configure and manage this firewall]\(Azure Firewall Manager にアクセスして、このファイアウォールを構成および管理する\)** を選択します。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="ファイアウォールを構成および管理するためのオプションを示すスクリーンショット" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **[Azure Firewall ポリシー]** 、 **[Azure Firewall ポリシーの作成]** の順に選択します。

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Firewall Manager でファイアウォール ポリシーを作成する方法のスクリーンショット" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. **[基本]** タブで、必要な詳細を指定し、 **[次へ: DNS 設定]** を選択します。 

1. **[DNS]** タブで、 **[無効にする]** を選択してから、 **[Next:Rules]\(次へ: 規則\)** を選択します。

1. **[規則コレクションの追加]** を選択して、下の詳細を指定し、 **[追加]** を選択してから、 **[次へ: 脅威インテリジェンス**] を選択します。

   -  名前
   -  規則コレクションの種類 - DNAT
   -  Priority
   -  規則コレクションのアクション - 許可
   -  規則の名前
   -  ソースの種類 - **IPaddress**
   -  ソース - **\***
   -  プロトコル – **TCP**
   -  宛先ポート – **80**
   -  送信先の種類 – **IP アドレス**
   -  宛先 – **パブリック IP アドレス**
   -  変換されたアドレス – **Azure VMware Solution Web サーバーのプライベート IP アドレス**
   -  変換されたポート - **Azure VMware Solution Web サーバーのポート**

1. 既定値のままにして、 **[次へ: ハブ]** を選択します。

1. **[Associate virtual hub]\(仮想ハブの関連付け\)** を選択します。

1. 一覧からハブを選び、 **[追加]** を選択します。

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="セキュリティ保護付き仮想ハブに変換されるように選択したハブを示すスクリーンショット。" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. **タグ** を選択します。 

1. (省略可能) 名前と値のペアを作成して、リソースを分類します。 

1. **Next:確認および作成** を選択してから、**作成** を選択します。

## <a name="limitations"></a>制限事項

SDDC ごとに 100 個のパブリック IP を使用できます。

## <a name="next-steps"></a>次の手順

Azure VMware Solution でパブリック IP 機能を使用する方法について理解したので、次の事項の学習に進むことができます。

- [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md) でパブリック IP アドレスを使用する。
- [Azure VMware Solution への IPSec トンネルを作成する](create-ipsec-tunnel.md)。
