---
title: Azure Site Recovery サービスを使用してディザスター リカバリー用のセカンダリ Azure リージョンにレプリケート済みの Azure VM をフェールオーバーして再保護する
description: Azure Site Recovery サービスを使用して、ディザスター リカバリー用のセカンダリ Azure リージョンにレプリケート済みの Azure VM をフェールオーバーする方法とそれを再保護する方法について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 96e3c0b761a9ed4c5f84d8ece1ba504bd5aacf6f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797569"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>リージョン間での Azure VM のフェールオーバーと再保護

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのコンピューターと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックの管理と調整を行うことでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、Azure VM をセカンダリ Azure リージョンにフェールオーバーする方法について説明します。 フェールオーバーした後で、VM を再保護します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure VM をフェールオーバーする
> * プライマリ リージョンにレプリケートされるように、セカンダリ Azure VM を再保護する

> [!NOTE]
> このチュートリアルでは、既定の設定と最小限のカスタマイズによる最も簡単な手順を紹介しています。 さらに複雑なシナリオについては、Azure VM の攻略ガイドの記事を参照してください。

## <a name="prerequisites"></a>前提条件

- [ディザスター リカバリー訓練](azure-to-azure-tutorial-dr-drill.md)を実施して、すべてが正しく動作していることを確認します。
- テスト フェールオーバーを実行する前に、VM のプロパティを確認します。 VM は [Azure の要件](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)に準拠している必要があります。

## <a name="run-a-failover-to-the-secondary-region"></a>セカンダリ リージョンへのフェールオーバーを実行する

1. **[レプリケートされたアイテム]** で、フェールオーバーする VM を選択し、**[フェールオーバー]** を選択します

   ![フェールオーバー](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. **[フェールオーバー]** で、フェールオーバー先の**復旧ポイント**を選択します。 次のいずれかのオプションを使うことができます。

   * **最新** (既定値): Site Recovery サービスのすべてのデータを処理し、最短の RPO (回復ポイントの目標) を提供します
   * **最後に処理があった時点**:仮想マシンを、Site Recovery サービスによって処理された最新の復旧ポイントに戻します
   * **カスタム**:特定の復旧ポイントにフェールオーバーします。 このオプションは、テスト フェールオーバーを実行するときに役立ちます。

3. Site Recovery でフェールオーバーをトリガーする前にそのソース VM をシャットダウンする場合は、**[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 Site Recovery では、フェールオーバー後にソースがクリーンアップされません。

4. **[ジョブ]** ページで、フェールオーバーの進行状況を確認します。

5. フェールオーバーの実行後、仮想マシンにログインして検証します。 仮想マシンの復旧ポイントを変更する場合は、**[復旧ポイントの変更]** を選択してください。

6. 正常な状態に仮想マシンがフェールオーバーされたら、フェールオーバーを**コミット**することができます。
   コミットすると、サービスで使用可能なすべての復旧ポイントが削除されます。 以降、復旧ポイントを変更することはできません。

## <a name="reprotect-the-secondary-vm"></a>セカンダリ VM を再保護する

VM のフェールオーバー後、プライマリ リージョンにレプリケートされるように、VM を再保護する必要があります。

1. VM が**フェールオーバー コミット済み**状態であること、プライマリ リージョンが使用でき、そこに新しいリソースを作成したり、アクセスしたりできることを確認します。
2. **[コンテナー]** > **[レプリケートされたアイテム]** で、フェールオーバーされた VM を右クリックし、**[再保護]** を選択します。

   ![右クリックして再保護する](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. 保護の方向として "セカンダリ リージョンからプライマリ リージョン" が既に選択されていることを確認します。
3. **リソース グループ、ネットワーク、ストレージ、および可用性セット**の情報を確認します。 新規としてマークされているリソースが、再保護操作の一環として作成されます。
4. **[OK]** をクリックすると、再保護ジョブがトリガーされます。 このジョブにより、最新のデータでターゲット サイトがシード処理されます。 その後、差分がプライマリ リージョンにレプリケートされます。 これで、VM は保護された状態になります。

## <a name="next-steps"></a>次の手順
- 再保護の後、利用できるようになった時点でプライマリ リージョンにフェールバックする[方法を確認](azure-to-azure-tutorial-failback.md)します。
- 再保護フローの[詳細](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)について学習します。
