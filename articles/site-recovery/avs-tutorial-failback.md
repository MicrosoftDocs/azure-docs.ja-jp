---
title: Azure Site Recovery を使用して Azure VMware Solution VM を Azure からフェールバックする
description: ディザスター リカバリー中に、Azure にフェールオーバーした後、Azure VMware Solution プライベート クラウドにフェールバックする方法について説明します。
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814232"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>Azure VMware Solution プライベート クラウドに VM をフェールバックする

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して Azure VMware Solution VM を Azure に[フェールオーバー](avs-tutorial-failover.md)した後、Azure VMware Solution プライベート クラウドに AzureVM をフェールバックする方法について説明します。 フェールバック後、Azure VMware Solution VM が Azure へのレプリケートを開始できるように、レプリケーションを有効にします。

## <a name="before-you-start"></a>開始する前に

1. [VMware フェールバック](failover-failback-overview.md#vmwarephysical-reprotectionfailback) について説明します。 
2. [フェールバックの準備をする](vmware-azure-prepare-failback.md) の手順を確認し、完了していること、および必要なすべてのコンポーネントがデプロイされていることを確認します。 コンポーネントには、Azure のプロセス サーバー、マスター ターゲット サーバー、フェールバック用の VPN サイト間接続 (または ExpressRoute プライベート ピアリング) などがあります。
3. 再保護とフェールバックの[要件](avs-tutorial-reprotect.md#before-you-begin)が完了していること、および Azure VM の[再保護が有効](avs-tutorial-reprotect.md#enable-reprotection)になっていることを確認します。これにより、Azure から Azure VMware Solution プライベート クラウドにレプリケートされます。 フェールバックするには、VM がレプリケートされた状態である必要があります。




## <a name="run-a-failover-to-fail-back"></a>フェールオーバーを実行して、Azure からフェールバックする

1. Azure VM が再保護され、Azure VMware Solution プライベート クラウドにレプリケートされていることを確認します。
    - フェールバックするには、VM に少なくとも 1つの復旧ポイントが必要です。
    - 復旧計画をフェールバックする場合は、プラン内のすべてのマシンに少なくとも 1つの復旧ポイントが必要です。
2. コンテナー > **[レプリケートされたアイテム]** で、VM を選択します。 右クリックで、[VM ] > **[計画されていないフェールオーバー]** を選択します。
3. **[フェールオーバーの確認]** で、フェールオーバーの方向 (Azure から) を確認します。
4. フェールオーバーに使用する復旧ポイントを選択します。
    - **最新の** 復旧ポイントを使用することをお勧めします。 アプリケーション整合性ポイントは最新の時点より古いため、一部のデータが失われます。
    - **最新の** は、クラッシュ整合性の復旧ポイントです。
    - **最新の** では、VM は使用可能な最新の時点までフェールオーバーされます。 復旧計画内にマルチ VM 整合性のためのレプリケーショングループがある場合、グループ内の各 VM は、独立した最新の時点にフェールオーバーします。
    - アプリ整合性復旧ポイントを使用する場合、各 VM は、使用可能な最新のポイントにフェールバックします。 復旧計画にレプリケーショングループがある場合、各グループは、その共通の使用可能な復旧ポイントに復元されます。
5. フェールオーバーが開始されます。 Site Recovery は、Azure VM をシャットダウンします。
6. フェールオーバーが完了したら、すべてが正常に動作していることを確認します。 Azure VM がシャットダウンされていることを確認します。 
7. すべての検証が完了したら、右クリックで [VM] >  **[コミット]** してフェールオーバープロセスを終了します。 [コミット] は、フェールオーバーされた Azure VM を削除します。 

> [!NOTE]
> Windows VM の場合、Site Recovery は、フェールオーバー時に VMware ツールを無効にします。 Windows VM のフェールバック時に、VMware ツールが再有効化されます。 




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>Azure VMware Solution から Azure に再保護する

フェールバックをコミットすると、Azure VM が削除されます。 VM は Azure VMware Solution プライベート クラウドに戻りますが、保護されていません。 Azure への VM レプリケートを再開するには、次の手順を実行します:

1. [コンテナー] > **[レプリケートされた項目]** で、[フェールバックされたVM ] を選択して、**再保護** を選択します。
2. Azure にデータを送信するために使用されるプロセスサーバーを指定します。
3. **[OK]** を選択すると、再保護ジョブが開始されます。

> [!NOTE]
> Azure VMware Solution VM の起動後、エージェントが構成サーバーに再登録されるまでに最大 15 分かかります。 この間は再保護が失敗し、エージェントがインストールされていないというエラー メッセージが返されます。 これが発生した場合は、数分待ってから再保護してください。

## <a name="next-steps"></a>次のステップ

再保護ジョブが完了すると、Azure VMware Solution VM が Azure にレプリケートされます。 必要に応じて、Azure への[別のフェールオーバーを実行](avs-tutorial-failover.md)できます。

