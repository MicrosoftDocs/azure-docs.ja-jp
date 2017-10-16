---
title: "Azure VM を別の Azure リージョンにレプリケートする (プレビュー)"
description: "このクイック スタートでは、ある Azure リージョンから別のリージョンに Azure VM をレプリケートするのに必要な手順を紹介します。"
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 614193fd61f976c61ef0de8be14c7bc55d7c28da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-an-azure-vm-to-another-azure-region-preview"></a>Azure VM を別の Azure リージョンにレプリケートする (プレビュー)

[Azure Site Recovery](site-recovery-overview.md) サービスは、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、復旧など、オンプレミスのマシンと Azure 仮想マシン (VM) のディザスター リカバリーを管理し、調整します。

このクイック スタートでは、Azure VM を別の Azure リージョンにレプリケートする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

Azure Portal (http://portal.azure.com) にログインします。

## <a name="enable-replication-for-the-azure-vm"></a>Azure VM のレプリケーションの有効化

1. Azure Portal で **[仮想マシン]** をクリックし、レプリケートする VM を選択します。

2. **[設定]** で、**[ディザスター リカバリー (プレビュー)]** をクリックします。
3. **[Configure disaster recovery]\(ディザスター リカバリーを構成する\)** > **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。
4. このクイック スタートでは、その他は既定の設定をそのまま使用します。
5. **[レプリケーションを有効にする]** をクリックします。 これにより VM レプリケーションを有効にするジョブが開始されます。

    ![レプリケーションの有効化](media/azure-to-azure-quickstart/enable-replication1.png)



## <a name="verify-settings"></a>設定の確認

レプリケーション ジョブの完了後、レプリケーションの状態を確認し、レプリケーションの設定を変更して、デプロイをテストできます。

1. VM のメニューで、**[ディザスター リカバリー (プレビュー)]** をクリックします。
2. レプリケーションの正常性、作成された復旧ポイント、およびマップ上のソース リージョンとターゲット リージョンを確認できます。

   ![レプリケーションの状態](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

プライマリ リージョンの VM のレプリケーションは、そのレプリケーションを無効にすると停止されます。

- ソース レプリケーションの設定は自動的にクリーンアップされます。
- VM の Site Recovery の課金も停止します。

レプリケーションを停止するには、次の手順に従います。

1. VM を選択します。
2. **[ディザスター リカバリー (プレビュー)]** で、**[詳細]** をクリックします。
3. **[レプリケーションの無効化]** をクリックします。

   ![レプリケーションを無効にする](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、1 つの VM をセカンダリ リージョンにレプリケートしました。

> [!div class="nextstepaction"]
> [Azure VM のディザスター リカバリーを構成する](azure-to-azure-tutorial-enable-replication.md)
