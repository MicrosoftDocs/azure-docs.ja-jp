---
title: Azure 仮想マシン スケール セットで OS イメージをアップグレードするためのメンテナンス コントロールの概要
description: メンテナンス コントロールを使用して、OS イメージの自動アップグレードが Azure 仮想マシン スケール セットにロール アウトされるタイミングを制御する方法について説明します。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532961"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>プレビュー:Azure 仮想マシン スケール セットのメンテナンス コントロール 

メンテナンス コントロールを使用して、仮想マシン スケール セットの[自動 OS イメージ アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)を管理します。

メンテナンス コントロールを使用すると、より簡単で予測可能なエクスペリエンスによって、仮想マシン スケール セット内の OS ディスクに更新プログラムを適用するタイミングを決定できます。 

メンテナンス構成が、複数のサブスクリプションやリソース グループ全体で機能するようにする。

ワークフロー全体は次のような手順になります。 
- メンテナンス構成を作成します。
- 仮想マシン スケール セットをメンテナンス構成に関連付けます。
- OS の自動アップグレードを有効にします。

> [!IMPORTANT]
> Azure 仮想マシン スケール セットで OS イメージをアップグレードするためのメンテナンス コントロールは、現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="limitations"></a>制限事項

- VM はスケール セット内に存在する必要があります。
- ユーザーは、**リソース共同作成者** のアクセス権を持っている必要があります。
- メンテナンス構成で、メンテナンス期間を 5 時間以上にする必要があります。
- メンテナンス構成で、メンテナンスの繰り返しを 'Day' に設定する必要があります


## <a name="management-options"></a>管理オプション

次のいずれかのオプションを使用して、メンテナンス構成を作成および管理できます。

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>次のステップ

メンテナンス コントロールと PowerShell を使用して Azure 仮想マシン スケール セットにメンテナンスを適用するタイミングを制御する方法を確認します。

> [!div class="nextstepaction"]
> [PowerShell を使用した仮想マシン スケール セットのメンテナンス管理](virtual-machine-scale-sets-maintenance-control-powershell.md)
