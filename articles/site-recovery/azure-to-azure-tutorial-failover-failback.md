---
title: "Azure Site Recovery でレプリケート済み Azure VM をセカンダリ Azure リージョンにフェールオーバーおよびフェールバックする (プレビュー)"
description: "Azure Site Recovery で Azure VM レプリケーションをセカンダリ Azure リージョンにフェールオーバーおよびフェールバックする方法について説明します"
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 6a66528bfeb7249add9f589830293760c238e7db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Azure リージョン間で Azure VM をフェールオーバーおよびフェールバックする (プレビュー)

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミス マシンと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、フェールバックを管理し、調整することでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、1 つの Azure VM をセカンダリ Azure リージョンにフェールオーバーする方法について説明します。 フェールオーバー後、利用できるようになった時点でプライマリ リージョンにフェールバックします。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure VM をフェールオーバーする
> * プライマリ リージョンにレプリケートされるように、セカンダリ Azure VM を再保護する
> * セカンダリ VM をフェールバックする
> * プライマリ VM をセカンダリ リージョンに再保護する

## <a name="prerequisites"></a>前提条件

- [ディザスター リカバリー訓練](azure-to-azure-tutorial-dr-drill.md)を実施して、すべてが正しく動作していることを確認します。
- テスト フェールオーバーを実行する前に、VM のプロパティを確認します。 VM は [Azure の要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠している必要があります。

## <a name="run-a-failover-to-the-secondary-region"></a>セカンダリ リージョンへのフェールオーバーを実行する

1. **[レプリケートされたアイテム]** で、フェールオーバーする VM を選択し、**[フェールオーバー]** を選択します

   ![フェールオーバー](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. **[フェールオーバー]** で、フェールオーバー先の**復旧ポイント**を選択します。 次のいずれかのオプションを使うことができます。

   * **最新**(既定値): このオプションは、Site Recovery サービスのすべてのデータを処理し、最も低い目標復旧時点 (RPO) を提供します。
   * **最後に処理があった時点**: このオプションは、仮想マシンを、Site Recovery サービスによって処理された最新の復旧ポイントに戻します。
   * **カスタム**: 特定の復旧ポイントにフェールオーバーする場合は、このオプションを使用します。 このオプションは、テスト フェールオーバーを実行するときに役立ちます。

3. Site Recovery でフェールオーバーを開始する前にそのソース仮想マシンをシャットダウンする場合は、**[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。

4. **[ジョブ]** ページで、フェールオーバーの進行状況を確認します。

5. フェールオーバーの実行後、仮想マシンにログインして検証します。 仮想マシンの復旧ポイントを変更する場合は、**[復旧ポイントの変更]** を選択してください。

6. 正常な状態に仮想マシンがフェールオーバーされたら、フェールオーバーを**コミット**することができます。
   コミットすると、サービスで使用可能なすべての復旧ポイントが削除されます。 以降、**[復旧ポイントの変更]** オプションは使用できなくなります。

## <a name="reprotect-the-secondary-vm"></a>セカンダリ VM を再保護する

VM のフェールオーバー後、プライマリ リージョンにレプリケートされるように、VM を再保護する必要があります。

1. VM が**フェールオーバー コミット済み**状態であること、プライマリ リージョンが使用でき、そこに新しいリソースを作成したり、アクセスしたりできることを確認します。
2. **[コンテナー]** > **[レプリケートされたアイテム]** で、フェールオーバーされた VM を右クリックし、**[再保護]** を選択します。

   ![右クリックして再保護する](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. 保護の方向として "セカンダリ リージョンからプライマリ リージョン" が既に選択されています。
3. **リソース グループ、ネットワーク、ストレージ、および可用性セット**の情報を確認します。 マーク (新規) されているリソースが、再保護操作の一環として作成されます。
4. **[OK]** をクリックすると、再保護ジョブがトリガーされます。 このジョブにより、最新のデータでターゲット サイトがシード処理されます。 その後、差分がプライマリ リージョンにレプリケートされます。 これで、VM は保護された状態になります。

## <a name="fail-back-to-the-primary-region"></a>プライマリ リージョンにフェールバックする

VM が再保護されたら、必要に応じてプライマリ リージョンにフェールバックできます。 これを実行するには、[フェールオーバー](#run-a-failover)の手順に従ってください。
