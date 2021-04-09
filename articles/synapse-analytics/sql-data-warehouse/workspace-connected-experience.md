---
title: 専用 SQL プール (以前の SQL DW) 上で Synapse ワークスペース機能を有効にする
description: このドキュメントでは、お客様がワークスペース内のご自分の既存の SQL DW スタンドアロン インスタンスにアクセスしてそれを使用するにはどうすればよいかについて説明します。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694616"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>既存の専用 SQL プール (以前の SQL DW) 上で Synapse ワークスペース機能を有効にする

SQL データ ウェアハウスのすべてのお客様が、自動化、接続、ツールに影響を与えることなく、Synapse Studio とワークスペースを介して、既存の専用 SQL プール (以前の SQL DW) インスタンスにアクセスし、それを使用できるようになりました。 この記事では、Azure Synapse Analytics の既存のお客様がご自分の既存の分析ソリューションを構築して拡張するにはどうすればよいかを説明します。これは、Synapse ワークスペースと Studio を介して提供されるようになった新しい豊富な機能を活用して実現します。   

## <a name="experience"></a>エクスペリエンス
 
Synapse ワークスペースが一般提供になったことから、DW ポータルの [概要] セクションに提供される新機能を使用することで、既存の専用 SQL プール (以前の SQL DW) インスタンス用に Synapse ワークスペースを作成できるようになりました。 この新機能を使用すれば、既存のデータ ウェアハウス インスタンスをホストしている論理サーバーを、新しい Synapse ワークスペースに接続できるようになります。 この接続により、そのサーバー上でホストされているすべてのデータ ウェアハウスが、ワークスペースおよび Studio から確実にアクセスできるようになると共に、Synapse パートナー サービス (サーバーレス SQL プール、Apache Spark プール、および ADF) と組み合わせて使用できるようになります。 プロビジョニング手順が完了し、新しく作成されたワークスペースへの接続が確立されるとすぐに、リソースへのアクセスおよび使用を開始できます。  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="接続された Synapse ワークスペース":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Synapse ワークスペースおよび Studio の機能を使用して専用 SQL プール (以前の SQL DW) にアクセスして使用する
 
Synapse ワークスペース機能が有効にされた状態で専用 SQL DW (以前の SQL DW) を使用する場合は、次の情報が適用されます。 
- **SQL 機能** Synapse ワークスペース機能が有効にされた後も、すべての SQL 機能は論理 SQL サーバーと共に残ります。 そのワークスペースが有効にされた後も、SQL リソース プロバイダーを介してサーバーにアクセスすることができます。 すべての管理機能はワークスペースを介して開始でき、その操作は、ご利用の SQL プールをホストしている論理 SQL Server 上で実行されます。 ワークスペースが有効になっている場合に、既存の自動化、ツール、または接続が壊れたり、中断されたりすることはありません。  
- **リソースの移動**  Synapse ワークスペース機能が有効になっているサーバー上でリソースの移動を開始すると、サーバーとワークスペースの間のリンクが中断され、ワークスペースから既存の専用 SQL プール (以前の SQL DW) インスタンスにアクセスできなくなります。 接続が確実に保持されるようにするには、両方のリソースを同じサブスクリプションおよびリソース グループ内に残すことをお勧めします。 
- **監視** ワークスペース対応の専用 SQL プール (以前の SQL DW) 内で Synapse Studio を介して送信される SQL 要求は、監視ハブで確認することができます。 その他のすべての監視アクティビティについては、Azure portal の専用 SQL プール (以前の SQL DW) の監視をご利用ください。 
- **セキュリティ** と **アクセスの制御** 前述のようにご利用の SQL サーバーと専用 SQL プール (以前の SQL DW) のインスタンス用の管理機能はすべて、論理 SQL サーバー上に残されたままになります。 これらの機能には、ファイアウォール規則の管理、サーバーの Azure AD 管理者の設定、専用 SQL プール (以前の SQL DW) 内のデータに対するすべてのアクセス制御などが含まれます。 専用 SQL プール (以前の SQL DW) にアクセスでき、かつ Synapse ワークスペースを介してそれを使用できるようにするには、次の手順を行う必要があります。 ワークスペース ロールのメンバーシップによって、専用 SQL プール (以前の SQL DW) インスタンス内のデータに対するアクセス許可がユーザーに付与されることはありません。 通常の [SQL 認証](sql-data-warehouse-authentication.md) ポリシーに従って、ユーザーが論理サーバー上の専用 SQL プール (以前の SQL DW) インスタンスに確実にアクセスできるようにします。 専用 SQL プール (以前の SQL DW) ホスト サーバーに管理対象 ID が既に割り当てられている場合、このマネージド ID の名前は、ワークスペース パートナー サービス (ADF パイプラインなど) をサポートするために自動的に作成されるワークスペース マネージド ID の名前と同じになります。  接続されているシナリオでは、同じ名前の 2 つのマネージド ID が存在できます。 これらのマネージド ID は、それぞれの Azure AD オブジェクト ID によって区別できます。オブジェクト ID を使用して SQL ユーザーを作成する機能は近日対応予定です。

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > 接続されたワークスペース Synapse Studio には、Azure 内でユーザーに割り当てられているアクセス許可に基づいて専用プールの名前が表示されます。 ユーザーが SQL プールに対するアクセス許可を持っていない場合、プールに属するオブジェクトへのアクセスはできなくなります。 

- **ネットワーク セキュリティ** 既存の専用 SQL プール (以前の SQL DW) 上で有効にした Synapse ワークスペースで、データ侵入保護が有効になっている場合。 ワークスペースから論理 SQL サーバーへのマネージド プライベート エンドポイント接続を作成します。 サーバーとワークスペース間の通信を許可するには、プライベート エンドポイント接続要求を承認します。
- **データ ハブ** 内の **Studio** SQL プール ワークスペース対応の専用 SQL プール (以前の SQL DW) は、データ ハブ内のツールヒントを介して識別できます。 
- **新しい専用 SQL プール (以前の SQL DW) の作成** ワークスペース機能が有効にされた後で、Synapse ワークスペースと Studio を使用して新しい専用 SQL プールを作成できます。新しいプールのプロビジョニングは、論理 SQL サーバー上で実行されます。 プロビジョニングが完了すると、ポータルと Studio に新しいリソースが表示されます。      

## <a name="next-steps"></a>次の手順
既存の専用 SQL プール (以前の SQL DW) 上で [Synapse ワークスペース機能](workspace-connected-create.md)を有効にする
