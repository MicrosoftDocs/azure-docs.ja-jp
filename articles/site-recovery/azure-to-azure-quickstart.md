---
title: Azure Site Recovery でセカンダリ リージョンへの Azure VM ディザスター リカバリーを設定する
description: Azure Site Recovery サービスを使用して、別の Azure リージョンへの Azure VM 用のディザスター リカバリーをすばやく設定します。
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135708"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>クイック スタート:Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定

[Azure Site Recovery](site-recovery-overview.md) サービスは、計画された停止や計画外の停止の際にビジネス アプリをオンライン状態に保つことで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、フェールバックなど、オンプレミスのマシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理し、調整します。

このクイックスタートでは、セカンダリ Azure リージョンにレプリケートすることによって、Azure VM のディザスター リカバリーを設定する方法について説明します。 一般に、レプリケーションを有効にするには、既定の設定が使用されます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションと VM が必要です。

- Azure アカウントとアクティブなサブスクリプションをお持ちでない場合は、[無料でアカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。
- 1 GB 以上の RAM を搭載した VM をお勧めします。 VM を作成する方法の[詳細](../virtual-machines/windows/quick-create-portal.md)をご覧ください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-replication-for-the-azure-vm"></a>Azure VM のレプリケーションの有効化

次の手順では、セカンダリ ロケーションへの VM のレプリケーションを有効にします。

1. Azure portal の **[ホーム]**  >  **[Virtual Machines]** メニューで、レプリケートする VM を選択します。
1. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
1. **[基本]**  >  **[ターゲット リージョン]** からターゲット リージョンを選択します。
1. レプリケーションの設定を表示するために、 **[レプリケーションの確認と開始]** を選択します。 既定の設定を変更する必要がある場合は、 **[Advanced settings]\(詳細設定\)** を選択します。
1. VM のレプリケーションを有効にするジョブを開始するには、 **[レプリケーションの開始]** を選択します。

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="レプリケーションを有効にする。":::

## <a name="verify-settings"></a>設定の確認

レプリケーション ジョブの完了後、レプリケーションの状態を確認し、レプリケーションの設定を変更して、デプロイをテストできます。

1. Azure portal のメニューで **[Virtual Machines]** を選択し、レプリケートする VM を選択します。
1. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
1. レプリケーションの詳細を表示するには、 **[概要]** から **[Essentials]\(基本\)** を選択します。 **[正常性と状態]** 、 **[フェールオーバーの準備]** 、 **[インフラストラクチャ ビュー]** のマップに詳しい情報が表示されます。

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="レプリケーションの状態。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライマリ リージョンの VM のレプリケーションを停止するには、レプリケーションを無効にする必要があります。

- ソース レプリケーションの設定は自動的にクリーンアップされます。
- レプリケーションの過程で VM にインストールされた Site Recovery 拡張機能は削除されません。
- VM の Site Recovery の課金は停止します。

レプリケーションを無効にするには、次の手順に従います。

1. Azure portal のメニューで **[Virtual Machines]** を選択し、レプリケートする VM を選択します。
1. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
1. **[概要]** から **[レプリケーションの無効化]** を選択します。
1. Site Recovery 拡張機能をアンインストールするには、VM の **[設定]**  >  **[拡張機能]** に移動します。

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="レプリケーションを無効にする。":::

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、1 つの VM をセカンダリ リージョンにレプリケートしました。 次に、複数の Azure VM のレプリケーションを設定します。

> [!div class="nextstepaction"]
> [Azure VM のディザスター リカバリーを設定する](azure-to-azure-tutorial-enable-replication.md)
