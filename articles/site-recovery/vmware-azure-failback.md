---
title: VMware VM と物理サーバーを Azure から Azure Site Recovery によってフェールバックする
description: VMware VM と物理サーバーの Azure へのディザスター リカバリー時に、Azure にフェールオーバーした後で、オンプレミス サイトにフェールバックする方法について説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004196"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>VMware VM をオンプレミスサイトにフェールバックする

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、オンプレミス VM を Azure に [フェールオーバー](site-recovery-failover.md) した後、Azure VM をオンプレミスサイトにフェールバックする方法について説明します。 オンプレミスへのフェールバック後、オンプレミスの VM が Azure へのレプリケートを開始できるように、レプリケーションを有効にします。

## <a name="before-you-start"></a>開始する前に

1. [VMware フェールバック](failover-failback-overview.md#vmwarephysical-reprotectionfailback) について説明します。 
2. [フェールバックの準備をする](vmware-azure-prepare-failback.md) の手順を確認し、完了していること、および必要なすべてのコンポーネントがデプロイされていることを確認します。 コンポーネントには、Azure のプロセスサーバー、オンプレミスのマスター ターゲット サーバー、およびフェールバック用の VPN サイト間接続 (または ExpressRoute プライベート ピアリング) などがあります。
3. 再保護とフェールバックの[要件](vmware-azure-reprotect.md#before-you-begin) が完了していること、および Azure VM の [再保護を有効にする](vmware-azure-reprotect.md#enable-reprotection) がオンになっていることを確認してください。これにより、Azure からオンプレミスサイトにレプリケートされます。 フェールバックするには、VM がレプリケートされた状態である必要があります。




## <a name="run-a-failover-to-fail-back"></a>フェールオーバーを実行して、Azure からフェールバックする

1. Azure VM が再保護され、オンプレミス サイトにレプリケートされていることを確認します。
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




## <a name="reprotect-from-on-premises-to-azure"></a>オンプレミスから Azure への再保護

フェールバックをコミットすると、Azure VM が削除されます。 VM はオンプレミスサイトに戻りますが、保護されていません。 Azure への VM レプリケートを再開するには、次の手順を実行します:

1. [コンテナー] > **[レプリケートされた項目]** で、[フェールバックされたVM ] を選択して、**再保護** を選択します。
2. Azure にデータを送信するために使用されるプロセスサーバーを指定します。
3. **[OK]** を選択すると、再保護ジョブが開始されます。

> [!NOTE]
> オンプレミスの VM を起動した後、エージェントが構成サーバーに再登録されるまでに最大 15 分かかります。 この間は再保護が失敗し、エージェントがインストールされていないというエラー メッセージが返されます。 これが発生した場合は、数分待ってから再保護してください。

## <a name="next-steps"></a>次のステップ

再保護ジョブが完了すると、オンプレミス VM が Azure にレプリケートされます。 必要に応じて、Azure への[別のフェールオーバーを実行](site-recovery-failover.md)できます。

