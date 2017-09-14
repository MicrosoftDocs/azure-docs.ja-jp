---
title: "Azure のロールベースのアクセス制を使用したバックアップの管理 | Microsoft Docs"
description: "ロールベースのアクセス制御を使用して、Recovery Services コンテナーのバックアップ管理操作へのアクセスを管理します。"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: d0b6eb8eea8971eb8f80c6623f9a41a3692241b3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---

# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>ロール ベースのアクセス制御を使用した Azure Backup の回復ポイントの管理
Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。

> [!IMPORTANT]
> Azure Backup で提供されるロールが実行できるの、Azure Portal または Recovery Services コンテナーの PowerShell コマンドレットで実行できるアクションに制限されています。 これらのロールが、Azure Backup エージェント クライアント UI または System Center Data Protection Manager UI または Azure Backup Server UI で実行されるアクションを使用することはできません。

Azure Backup では、バックアップの管理操作を制御する 3 つの組み込みロールが提供されます。 [Azure RBAC の組み込みのロール](../active-directory/role-based-access-built-in-roles.md)について説明します。

* [バックアップ共同作成者](../active-directory/role-based-access-built-in-roles.md#backup-contributor) - このロールは、Recovery Services コンテナーの作成と他のロールへの権限付与を除き、バックアップの作成と管理のすべての権限を持ちます。 このロールは、すべてのバックアップ管理操作を実行できる、バックアップ管理の管理者と考えてください。
* [バックアップ オペレーター](../active-directory/role-based-access-built-in-roles.md#backup-operator) - このロールは、バックアップの削除とバックアップ ポリシーの管理を除き、共同作成者が行うすべての操作の権限を持ちます。 このロールは共同作成者と同等ですが、データの削除によるバックアップの停止やオンプレミス リソースの登録解除など、削減する操作は実行できません。
* [バックアップ リーダー](../active-directory/role-based-access-built-in-roles.md#backup-reader) - このロールは、すべてのバックアップ管理操作を見る権限を持ちます。 このロールは監視役と考えてください。

制御を強化するために独自のロールを定義する場合は、Azure RBAC で [カスタム ロールを作成](../active-directory/role-based-access-control-custom-roles.md)する方法を参照してください。



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>バックアップ管理アクションへの組み込みバックアップ ロールのマッピング
次の表に、バックアップ管理アクションと、その操作を実行するために必要な最小限の RBAC ロールを示します。

| 管理操作 | 必要最小限 RBAC ロール |
| --- | --- |
| Recovery Services コンテナーの作成 | コンテナーのリソース グループの共同作成者 |
| Azure VM のバックアップの有効化 | コンテナーのバックアップ オペレーター、VM の仮想マシン共同作成者 |
| VM のオンデマンド バックアップ | バックアップ オペレーター |
| VM の復元 | VM および Vnet をデプロイするバックアップ オペレーター、リソース グループの共同作成者 |
| VM バックアップからのディスクや個々のファイルの復元 | バックアップ オペレーター |
| Azure VM バックアップのバックアップ ポリシーの作成 | バックアップの共同作成者 |
| Azure VM バックアップのバックアップ ポリシーの変更 | バックアップの共同作成者 |
| Azure VM バックアップのバックアップ ポリシーの削除 | バックアップの共同作成者 |
| VM バックアップでのバックアップの停止 (データを保持またはデータを削除) | バックアップの共同作成者 |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録 | バックアップ オペレーター |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録解除 | バックアップの共同作成者 |

## <a name="next-steps"></a>次のステップ
* [ロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md): Azure Portal での RBAC の基本について説明します。
* 次の要素を使用したアクセス管理方法の詳細
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [ロールベースのアクセス制御のトラブルシューティング](../active-directory/role-based-access-control-troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。

