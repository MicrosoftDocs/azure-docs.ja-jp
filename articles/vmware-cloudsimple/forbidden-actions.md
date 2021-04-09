---
title: 昇格されたアクセス中に禁止されているアクション
description: VMware Engine では、次の禁止されたアクションのいずれかが VMware Engine で検出された場合にサービスが中断されないよう、変更が元に戻されます。
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92915375"
---
# <a name="forbidden-actions-during-elevated-access"></a>昇格されたアクセス中に禁止されているアクション

昇格された期間中は、一部のアクションが禁止されています。 VMware Engine で次の禁止されたアクションのいずれかが検出されると、サービスが中断されないよう、VMware Engine によって変更が元に戻されます。

## <a name="cluster-actions"></a>クラスターのアクション

- vCenter からのクラスターの削除。
- クラスターにおける vSphere の高可用性 (HA) の変更。
- vCenter からクラスターへのホストの追加。
- vCenter からクラスターへのホストの削除。

## <a name="host-actions"></a>ホストのアクション

- ESXi ホスト上のデータストアの削除。
- ホストからの vCenter エージェントのアンインストール。
- ホスト構成の変更。
- ホスト プロファイルへの変更の適用。
- メンテナンス モードへのホストの移行。

## <a name="network-actions"></a>ネットワークのアクション

- プライベート クラウド内にある既定の分散仮想スイッチ (DVS) の削除。
- 既定の DVS からのホストの削除。
- DVS 設定のインポート。
- DVS 設定の再構成。
- DVS のアップグレード。
- Management ポートグループの削除。
- Management ポートグループの編集。

## <a name="roles-and-permissions-actions"></a>ロールとアクセス許可のアクション

- グローバル ロールの作成。
- 管理オブジェクトへのアクセス許可の変更または削除。
- 既定のロールの変更または削除。
- Cloud-Owner-Role よりも高いレベルへのロールの権限の引き上げ。

## <a name="other-actions"></a>その他のアクション

- 既定のライセンスの削除:
  - vCenter Server
  - ESXi ノード
  - NSX-T
  - HCX
- 管理リソース プールの変更または削除。
- 管理 VM の複製。


## <a name="next-steps"></a>次の手順
[CloudSimple のメンテナンスと更新](cloudsimple-maintenance-updates.md) 
