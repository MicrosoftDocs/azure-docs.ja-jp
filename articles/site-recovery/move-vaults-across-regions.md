---
title: Azure Site Recovery コンテナーを別のリージョンに移動する
description: Recovery Services コンテナー (Azure Site Recovery) を別の Azure リージョンに移動する方法を説明します
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: a8cb5ec782b5932c13e321b2ba2d6513597fef52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87422642"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Recovery Services コンテナーと Azure Site Recovery 構成を別の Azure リージョンに移動する

既存の Azure リソースを別のリージョンに移動するシナリオには、さまざまなケースがあります。 管理容易性のため、ガバナンス上の理由、会社の合併や買収による場合などです。 Azure VM と一緒に移動されることの多い関連リソースの 1 つは、ディザスター リカバリー構成です。 

既存のディザスター リカバリー構成をリージョン間で移動する簡単な方法はありません。 これは、ターゲット リージョンがソース VM リージョンに基づいて構成されているためです。 ソース VM リージョンを変更するとなると、以前から存在していたターゲット リージョンの構成は再利用できず、リセットする必要があるのです。 この記事では、ディザスター リカバリーの設定を再構成して別のリージョンに移動するための段階的な手順を定義します。

このドキュメントの内容は、次のとおりです。

> [!div class="checklist"]
> * 移動の前提条件を確認する。
> * Azure Site Recovery によって使用されたリソースを特定する。
> * レプリケーションを無効にする。
> * リソースを削除する。
> * VM の新しいソース リージョンに基づいて Site Recovery を設定する。

> [!IMPORTANT]
> 現在、Recovery Services コンテナーとディザスター リカバリー構成を別のリージョンにそのまま移動する簡単な方法はありません。 この記事では、レプリケーションを無効にして新しいリージョンで設定するプロセスを説明します。

## <a name="prerequisites"></a>前提条件

- Azure VM を別のリージョンに移動する前に、必ずディザスター リカバリー構成を削除してください。 

  > [!NOTE]
  > Azure VM の新しいターゲット リージョンがディザスター リカバリーのターゲット リージョンと同じである場合は、既存のレプリケーション構成を使用して移動することができます。 [Azure IaaS VM を別の Azure リージョンに移動する](azure-to-azure-tutorial-migrate.md)方法に関する記事の手順に従ってください。

- 必ず十分な情報に基づいて意思決定を行い、関係者に通知してください。 VM の移動が完了するまでは VM が災害から保護されません。

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Azure Site Recovery によって使用されたリソースを特定する
この手順を実施したうえで次の手順に進むことをお勧めします。 VM がまだレプリケートされている間の方が、関連するリソースの特定が容易です。

レプリケートされている各 Azure VM について、 **[保護されたアイテム]**  >  **[レプリケートされたアイテム]**  >  **[プロパティ]** の順に移動し、以下のリソースを特定します。

- ターゲット リソース グループ
- キャッシュ ストレージ アカウント
- ターゲット ストレージ アカウント (アンマネージド ディスクベースの Azure VM の場合) 
- ターゲット ネットワーク


## <a name="disable-the-existing-disaster-recovery-configuration"></a>既存のディザスター リカバリー構成を無効にする

1. [Recovery Services コンテナー] に移動します。
2. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** でマシンを右クリックし、 **[レプリケーションの無効化]** をクリックします。
3. 移動するすべての VM に対して、この手順を繰り返します。

> [!NOTE]
> モビリティ サービスは、保護されたサーバーからアンインストールされません。 手動でアンインストールする必要があります。 再度サーバーを保護する場合は、モビリティ サービスのアンインストールを省略できます。

## <a name="delete-the-resources"></a>リソースの削除

1. [Recovery Services コンテナー] に移動します。
2. **[削除]** を選択します。
3. その他、[先ほど特定した](#identify-the-resources-that-were-used-by-azure-site-recovery)リソースをすべて削除します。
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Azure VM を新しいターゲット リージョンに移動する

実際の要件に応じた記事の手順に従って、Azure VM をターゲット リージョンに移動します。

- [Azure VM を別のリージョンに移動する](azure-to-azure-tutorial-migrate.md)
- [Azure VM を Availability Zones に移動する](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>VM の新しいソース リージョンに基づいて Site Recovery を設定する

新しいリージョンに移動した Azure VM のディザスター リカバリーを構成します。「[Azure VM のディザスター リカバリーを設定する](azure-to-azure-tutorial-enable-replication.md)」の手順に従ってください。
