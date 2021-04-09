---
title: 仮想ネットワークでプールをプロビジョニングする
description: コンピューティング ノードがネットワーク内の他の VM (ファイル サーバーなど) と安全に通信できるように、Azure 仮想ネットワークで Batch プールを作成する方法。
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: d6e5de75164e098fc95f6c086d9f98a652dcee4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561917"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>仮想ネットワーク内に Azure Batch プールを作成する

Azure Batch プールを作成すると、指定した [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md) (VNet) のサブネットでプールをプロビジョニングできます。 この記事では、VNet で Batch プールを設定する方法について説明します。

## <a name="why-use-a-vnet"></a>VNet を使用する理由

プール内の計算ノードは、複数インスタンスのタスクの実行など、個別の VNet を必要とせずに相互に通信できます。 ただし、既定では、プール内のノードは、ライセンス サーバーやファイル サーバーなど、プールの外部にある仮想マシンとは通信できません。

計算ノードが他の仮想マシンと、あるいはオンプレミス ネットワークと安全に通信するために、Azure VNet のサブネットでプールをプロビジョニングできます。

## <a name="prerequisites"></a>前提条件

- **[認証]** : Azure VNet を使用するには、Batch クライアント API で Azure Active Directory (AD) 認証を使用する必要があります。 Azure AD の Azure Batch のサポートについては、「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」に記載されています。

- **Azure VNet**。 VNet の要件と構成については、次のセクションを参照してください。 1 つまたは複数のサブネットを持つ VNet を前もって用意するために、Azure Portal、Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、その他の方法を利用できます。
  - Azure Resource Manager ベースの VNet を作成する方法については、[仮想ネットワークの作成](../virtual-network/manage-virtual-network.md#create-a-virtual-network)に関するページを参照してください。 Resource Manager ベースの VNet は新しいデプロイで推奨され、仮想マシン構成を使用するプールでのみサポートされます。
  - クラシック VNet を作成する方法については、「[複数のサブネットを含んだ仮想ネットワーク (クラシック) を作成する](/previous-versions/azure/virtual-network/create-virtual-network-classic)」を参照してください。 クラシック VNet は、Cloud Services 構成を使用するプールでのみサポートされます。

## <a name="vnet-requirements"></a>VNet に関する要件

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Azure portal で VNet を含むプールを作成する

VNet を作成し、それにサブネットを割り当てたら、その VNet で Batch プールを作成できます。 次の手順に従って、Azure Portal でプールを作成します。 

1. Azure Portal の Batch アカウントに移動します。 このアカウントは、使用する予定の VNet を含むリソース グループと同じサブスクリプションおよびリージョン内にある必要があります。
2. 左側の **[設定]** ウィンドウで、 **[プール]** メニュー項目を選択します。
3. **[プール]** ウィンドウで、 **[追加]** を選択します。
4. **[プールの追加]** ウィンドウで、 **[イメージの種類]** ドロップダウンから、使用する予定のオプションを選択します。
5. **[Publisher]\(パブリッシャー\)、[Offer]\(プラン\)、[SKU]** で、カスタム イメージに対して適切な値を選択します。
6. **[ノード サイズ]** 、 **[ターゲットの専用ノード数]** 、 **[低優先度ノード]** など、残りの必須の設定ほか、オプションの設定も必要に応じて指定します。
7. **[仮想ネットワーク]** で、使用する予定の仮想ネットワークとサブネットを選択します。

   ![仮想ネットワークを含むプールを追加する](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>強制トンネリングのユーザー定義ルート

組織には、検査やログ記録目的で、サブネットからオンプレミスの場所にインターネット接続のトラフィックをリダイレクトする (強制的に送る) 要件がある場合があります。 さらに、VNet でサブネットの強制トンネリングを既に有効にしている場合もあります。

強制トンネリングが有効になっている VNet でプールのノードが機能することを確認するには、そのサブネットに次の[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md) (UDR) を追加する必要があります。

- Batch サービスは、タスクをスケジュールする目的でノードと通信する必要があります。 この通信を有効にするには、Batch アカウントが存在するリージョンで Batch サービスによって利用される IP アドレスごとに UDR を追加します。 Batch サービスの IP アドレスの一覧を取得するには、「[オンプレミスのサービス タグ](../virtual-network/service-tags-overview.md)」を参照してください。

- Azure Storage への送信トラフィック (具体的には、フォーム `<account>.table.core.windows.net`、`<account>.queue.core.windows.net`、`<account>.blob.core.windows.net` の URL) がオンプレミス ネットワークによってブロックされていないことを確認します。

UDR を追加するときに、関連する各 Batch の IP アドレス プレフィックスのルートを定義し、 **[次ホップの種類]** を **[インターネット]** に設定します。

![ユーザー定義のルート](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Batch サービスの IP アドレスは、時間の経過と共に変化することがあります。 IP アドレスの変更による停止を防ぐために、Batch サービスの IP アドレスを自動的に更新し、ルート テーブルで最新の状態に保つプロセスを作成します。

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- [Azure portal でユーザー定義のルートを作成する](../virtual-network/tutorial-create-route-table-portal.md)方法について説明します。
