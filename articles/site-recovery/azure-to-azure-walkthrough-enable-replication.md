---
title: "Azure Site Recovery を使用して別の Azure リージョンへの Azure VM のレプリケーションを有効にする | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して、Azure VM の別の Azure リージョンへのレプリケーションを有効にするために必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.openlocfilehash: 8691ff9dbdb79ef445482b355b00859072d539e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-enable-replication-for-azure-vms"></a>手順 5: Azure VM のレプリケーションを有効にする


[Recovery Services のコンテナー](azure-to-azure-walkthrough-vault.md)を設定した後に、この記事に従って、[Azure Site Recovery](site-recovery-overview.md) を使って別の Azure リージョンに対して仮想マシン (VM) のレプリケーションを有効にします。 レプリケーションを有効にするには、ソースとターゲットの設定を行って、レプリケーション ポリシーを検証し、レプリケートする対象の VM を選択します。

- 記事の手順を完了すると、Azure VM がセカンダリの Azure リージョンにレプリケートされます。
- この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。

>[!NOTE]
>
> Azure VM レプリケーションは、現在プレビューの段階です。


## <a name="select-the-source"></a>ソースを選択する

1. [Recovery Services コンテナー] で、[コンテナー名] > **[+ レプリケート]** の順にクリックします。
2. **[ソース]** で **[Azure - プレビュー]** を選択します。
2. **[ソースの場所]** で、現在 VM が実行されているソースの Azure リージョンを選択します。
3. VM の **Azure 仮想マシンのデプロイ モデル**として、**[Resource Manager]** または **[クラシック]** を選択します。
4. Resource Manager VM の場合は**ソース リソース グループ**を、クラシック VM の場合は**クラウド サービス**を選択します。
5. **[OK]** をクリックして設定を保存します。

    ![ソースを構成する](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>VM を選択する

Site Recovery は、サブスクリプションとリソース グループ/クラウド サービスに関連付けられている VM の一覧を取得します。

1. **[仮想マシン]** で、レプリケートする VM を選択します。
2. **[OK]**をクリックします。

    ![VM を選択する](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>設定を構成する

Site Recovery では、ターゲット リージョン (ソース リージョン設定に基づく) の既定の設定と次のレプリケーション ポリシーをプロビジョニングします。

   - **ターゲットの場所**: ディザスター リカバリーに使用するターゲット リージョン。 ターゲットの場所が Site Recovery コンテナーの場所と一致していることをお勧めします。
   - **ターゲット リソース グループ** : フェールオーバー後に、ターゲット リージョン のAzure VM が属するリソース グループ。 既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しいリソース グループを作成します。
   - **ターゲット仮想ネットワーク** : フェールオーバー後に、ターゲット リージョン のAzure VM が配置されるネットワーク。 既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しい仮想ネットワーク (およびサブネット) を作成します。 このネットワークは、ソース ネットワークにマップされます。 ソースとターゲットの場所で同じ IP アドレスを保持する必要がある場合、VM のフェールオーバー後に特定の IP アドレスを割り当てできることに注意してください。
   - **ストレージ アカウントのキャッシュ**: Site Recovery では、ソース リージョンにストレージ アカウントを使用します。 ソース VM での変更は、ターゲットの場所にレプリケートする前に、このアカウントに送信されます。
   - **ターゲット ストレージ アカウント** : 既定では、Site Recovery は、ソース VM のストレージ アカウントをミラーするために、ターゲット リージョンに新しいストレージ アカウントを作成します。
   -  **ターゲットの可用性セット:** 既定では、Site Recovery は "asr" サフィックスを付けて新しい可用性セットをターゲット リージョンに作成します。
   - **レプリケーション ポリシー名** : ポリシーの名前。
   - **復旧ポイントのリテンション期間** : 既定では、Site Recovery は 24 時間の復旧ポイントを保持します。 1 ～ 72 時間の値を構成できます。
   - **アプリ整合性スナップショットの頻度**: 既定では、Site Recovery はアプリ整合性スナップショットを 4 時間ごとに取得します。 1 ～ 12 時間の任意の値を構成できます。 データは継続的にレプリケートされます。
    - クラッシュ整合性復旧ポイントは、作成されたときに、一貫性のあるデータの書き込み順序を維持します。 データの不整合を発生させずにクラッシュから復旧するようにアプリが設計されている場合、通常はこの種類の復旧ポイントで十分です。
    - クラッシュ整合性復旧ポイントは、数分ごとに生成されます。 これらの復旧ポイントを使用して VM をフェールオーバーして復旧すると、分単位の順番に従って Recovery Point Objective (RPO) が提供されます。
    - アプリ整合性復旧ポイントは (書き込み順序の整合性も同様)、実行中のアプリがすべての操作を完了して、バッファーをディスクへフラッシュしている (アプリケーションの休止) ことを確認します。 SQL Server、Oracle、および Exchange などのデータベース アプリには、これらの復旧ポイントを使用することをお勧めします。

    ![設定を構成する](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>設定を変更する

ターゲットおよびレプリケーションのポリシー設定を変更する場合は、次の操作を行います。

1. ターゲット設定を表示または変更するには、**[設定]** をクリックします。
2. 既定のターゲット設定を上書きするには、**[カスタマイズ]** をクリックします。 ターゲット リソース グループ、仮想ネットワーク、可用性セット、およびターゲット ストレージ アカウントを指定できます。 VM がソース リージョンにあるセットの一部である場合、可用性セットのみを追加できます。

    ![設定を構成する](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. 復旧ポイントとアプリ整合性スナップショットのレプリケーション設定を上書きするには、**[レプリケーション ポリシー]** の横にある **[カスタマイズ]** をクリックします。

    ![設定を構成する](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. ターゲット リソースのプロビジョニングを開始するには、**[Create target resources]\(ターゲット リソースを作成\)** をクリックします。 プロビジョニングには 1 分程度かかります。 プロビジョニング中はブレードを閉じないでください。閉じた場合、最初からやり直す必要があります。




## <a name="enable-replication"></a>Enable replication

1. **[設定]** で、**[レプリケーションを有効にする]** をクリックします。 これにより、選択した VM の初期レプリケーションが有効になります。 初期レプリケーションの状態の更新には、少し時間がかかる場合があります。 **[更新]** をクリックして、最新の状態を取得します。

2. **保護の有効化**ジョブの進行状況は、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で追跡できます。

3. **[設定]** > **[レプリケートされたアイテム]** では、VM の状態と初期レプリケーションの進行状況を確認できます。 VM の設定をドリルダウンするには、その VM をクリックします。



## <a name="next-steps"></a>次のステップ

「[手順 6: テスト フェールオーバーを実行する](azure-to-azure-walkthrough-test-failover.md)」に進む
