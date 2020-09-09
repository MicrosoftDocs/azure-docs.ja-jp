---
title: 概念 - ID とアクセス
description: Azure VMware Solution の ID とアクセスの概念について説明します
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9d5b7b4c7c2e0d55cffc99a3f371494f40320a16
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750592"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Azure VMware Solution の ID の概念

vCenter サーバーと NSX-T Manager は、プライベート クラウドのデプロイ時にプロビジョニングされます。 vCenter は仮想マシンのワークロードを管理するために使用し、NSX-T Manager はプライベート クラウドのソフトウェア定義ネットワークを拡張するために使用します。

アクセスと ID 管理では、vCenter の場合は CloudAdmin グループ特権が、NSX-T Manager の場合は制限付き管理者権限が使用されます。 このポリシーにより、プライベート クラウド プラットフォームを自動的にアップグレードできるようになります。 これにより、最新の機能と修正プログラムが定期的に配信されます。 プライベート クラウドのアップグレードの詳細については、[プライベート クラウドのアップグレードの概念に関する記事][concepts-upgrades]を参照してください。

## <a name="vcenter-access-and-identity"></a>vCenter のアクセスと ID

vCenter の特権は、CloudAdmin グループを通じて提供されます。 このグループは、vCenter でローカルに管理することも、vCenter LDAP シングル サインオンと Azure Active Directory の統合によって管理することもできます。 プライベート クラウドを展開した後、この統合を有効にする機能が提供されます。

次の表に、CloudAdmin および CloudGlobalAdmin の特権を示します。

|  特権セット           | CloudAdmin | CloudGlobalAdmin | 解説 |
| :---                     |    :---:   |       :---:      |   :--:  |
|  アラーム                  | CloudAdmin ユーザーには、Compute-ResourcePool と VM のアラームに関するすべてのアラーム特権があります。     |          --        |  -- |
|  自動展開             |  --  |        --        |  Microsoft がホスト管理を行います。  |
|  証明書            |  --  |        --       |  Microsoft が証明書の管理を行います。  |
|  コンテンツ ライブラリ         | CloudAdmin ユーザーには、コンテンツ ライブラリ内のファイルを作成および使用する特権があります。    |         SSO で有効になります。         |  Microsoft がコンテンツ ライブラリ内のファイルを ESXi ホストに配布します。  |
|  データセンター              |  --  |        --          |  Microsoft がデータセンターのすべての操作を行います。  |
|  データストア               | Datastore.AllocateSpace、Datastore.Browse、Datastore.Config、Datastore.DeleteFile、Datastore.FileManagement、Datastore.UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX エージェント マネージャー       |  --  |         --       |  Microsoft がすべての操作を行います。  |
|  Folder                  |  CloudAdmin ユーザーには、すべてのフォルダー特権があります。     |  --  |  --  |
|  グローバル                  |  Global.CancelTask、Global.GlobalTag、Global.Health、Global.LogEvent、Global.ManageCustomFields、Global.ServiceManagers、Global.SetCustomField、Global.SystemTag         |                  |    |
|  Host                    |  Host.Hbr.HbrManagement      |        --          |  Microsoft が他のすべてのホスト操作を行います。  |
|  InventoryService        |  InventoryService.Tagging      |        --          |  --  |
|  ネットワーク                 |  Network.Assign    |                  |  Microsoft が他のすべてのネットワーク操作を行います。  |
|  アクセス許可             |  --  |        --       |  Microsoft がすべてのアクセス許可操作を行います。  |
|  プロファイル駆動型ストレージ  |  --  |        --       |  Microsoft がすべてのプロファイル操作を行います。  |
|  リソース                |  CloudAdmin ユーザーには、すべてのリソース特権があります。        |      --       | --   |
|  スケジュールされたタスク          |  CloudAdmin ユーザーには、すべての ScheduleTask 特権があります。   |   --   | -- |
|  セッション                |  Sessions.GlobalMessage、Sessions.ValidateSession      |   --   |  Microsoft が他のすべてのセッション操作を行います。  |
|  ストレージ ビュー           |  StorageViews.View   |        --          |  Microsoft が他のすべてのストレージ ビュー操作 (サービスの構成) を行います。  |
|  タスク                   |  --  |  --   |  Microsoft が、タスクを管理する拡張機能を管理します。  |
|  vApp                    |  CloudAdmin ユーザーには、すべての vApp 特権があります。  |  --  |  --  |
|  仮想マシン         |  CloudAdmin ユーザーには、すべての VirtualMachine 特権があります。  |  --  |  --  |
|  vService                |  CloudAdmin ユーザーには、すべての vService 特権があります。  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T Manager のアクセスと ID

"管理者" アカウントを使用して、NSX-T Manager にアクセスします。 このアカウントには完全な特権があり、T1 ルーター、論理スイッチ、およびすべてのサービスを作成および管理できます。 NSX-T の完全な特権により、NSX-T T0 ルーターへのアクセスも提供されます。 T0 ルーターを変更すると、ネットワーク パフォーマンスが低下したり、プライベート クラウドへのアクセスが失われたりする可能性があります。 サポート要件を満たすには、Azure portal でサポート リクエストを開いて、NSX-T T0 ルーターへの変更を要求する必要があります。
  
## <a name="next-steps"></a>次のステップ

次のステップでは、[プライベート クラウドのアップグレードの概念][concepts-upgrades]について説明します。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md