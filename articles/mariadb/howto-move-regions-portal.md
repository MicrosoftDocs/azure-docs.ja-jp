---
title: Azure リージョンの移動 - Azure portal - Azure Database for MariaDB
description: 読み取りレプリカと Azure portal を使用して、Azure Database for MariaDB サーバーをリージョン間で移動します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: f4ce34bc1a1af7b2c0ee57a3297415bd9d033517
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664938"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MariaDB サーバーを別のリージョンに移動する

既存の Azure Database for MariaDB サーバーをリージョン間で移動する場合、さまざまなシナリオがあります。 たとえば、ディザスター リカバリー計画の一環として、運用サーバーを別のリージョンに移動する必要がある場合があります。

別のリージョンへの移動を完了するには、Azure Database for MariaDB の[リージョン間の読み取りレプリカ](concepts-read-replicas.md#cross-region-replication)を使用できます。 これを行うには、まずターゲット リージョンに読み取りレプリカを作成します。 次に、読み取りレプリカ サーバーへのレプリケーションを停止すると、読み取りと書き込みの両方のトラフィックを受け入れるスタンドアロン サーバーになります。 

> [!NOTE]
> この記事では、サーバーを別のリージョンに移動することに重点を置いています。 サーバーを別のリソース グループまたはサブスクリプションに移動する場合は、[移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)に関する記事を参照してください。 

## <a name="prerequisites"></a>前提条件

- 読み取りレプリカ機能は、汎用またはメモリ最適化の価格レベルの Azure Database for MariaDB サーバーにのみ使用可能です。 ソース サーバーがこれらの価格レベルのいずれであるかを確認します。

- 移動元の Azure リージョンに Azure Database for MariaDB ソース サーバーが存在することを確認します。

## <a name="prepare-to-move"></a>移動の準備をする

Azure portal を使用してターゲット リージョンにリージョン間読み取りレプリカ サーバーを作成するには、次の手順を実行してください。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. ソース サーバーとして使用する既存の Azure Database for MariaDB サーバーを選択します。 この操作で、 **[概要]** ページが開きます。
1. **[設定]** で、メニューから **[レプリケーション]** を選択します。
1. **[レプリカの追加]** を選択します。
1. レプリカ サーバーの名前を入力します。
1. レプリカ サーバーの場所を選択します。 既定の場所は、ソース サーバーの場所と同じです。 レプリカをデプロイするターゲットの場所が選択されていることを確認します。
1. **[OK]** を選択して、レプリカの作成を確認します。 レプリカの作成中に、ソース サーバーからレプリカにデータがコピーされます。 ソース サーバーのサイズに比例して、作成時間が数分になる場合があります。

>[!NOTE]
> 作成されたレプリカでは、ソース サーバーの VNet サービス エンドポイントは継承されません。 これらの規則は、レプリカに対して個別に設定する必要があります。

## <a name="move"></a>詳細ビュー

> [!IMPORTANT]
> スタンドアロン サーバーをもう一度レプリカにすることはできません。
> 読み取りレプリカでレプリケーションを停止する前に、レプリカに必要なすべてのデータがあることを確認してください。

レプリカ サーバーへのレプリケーションを停止すると、スタンドアロン サーバーになります。 Azure portal からレプリカへのレプリケーションを停止するには、次の手順を実行してください。

1. レプリカが作成されたら、Azure Database for MariaDB ソース サーバーを見つけて選択します。 
1. **[設定]** で、メニューから **[レプリケーション]** を選択します。
1. レプリカ サーバーを選択します。
1. **[レプリケーションを停止する]** を選択します。
1. **[OK]** をクリックして停止したいレプリケーションを確認します。

## <a name="clean-up-source-server"></a>ソース サーバーのクリーンアップ

ソース Azure Database for MariaDB サーバーを削除することもできます。 そのためには、次の手順を実行してください。

1. レプリカが作成されたら、Azure Database for MariaDB ソース サーバーを見つけて選択します。
1. **[概要]** ウィンドウで **[削除]** を選択します。
1. ソース サーバーの名前を入力して、削除することを確認します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure portal を使用して Azure Database for MariaDB サーバーをリージョン間で移動してから、不要なソース リソースをクリーンアップしました。 

- [レプリカの読み取り](concepts-read-replicas.md) の詳細を確認する
- [Azure portal での読み取りレプリカの管理](howto-read-replicas-portal.md)について詳細を確認する
- [ビジネス継続性](concepts-business-continuity.md)オプションについて確認します