---
title: SQL Managed Instance の削除後のサブネットの削除
description: Azure SQL Managed Instance の削除後に Azure 仮想ネットワークを削除する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99093686"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance の削除後にサブネットを削除する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、サブネットに存在する最後の Azure SQL Managed Instance を削除した後で、そのサブネットを手動で削除する方法についてのガイドラインを紹介します。

SQL Managed Instance は、[仮想クラスター](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)にデプロイされます。 各仮想クラスターは、サブネットに関連付けられています。 同じサブネット内に SQL Managed Instance をすばやく作成できるように、仮想クラスターは、最後のインスタンスが削除されてから 12 時間存続するように設計されています。 空の仮想クラスターを維持しても料金はかかりません。 この期間中は、仮想クラスターに関連付けられたサブネットを削除できません。

12 時間を待たずに仮想クラスターとそのサブネットをすぐに削除したい場合、手動で削除できます。 Azure portal または仮想クラスター API を使用して、仮想クラスターを手動で削除します。

> [!IMPORTANT]
> - 仮想クラスターを正常に削除するためには、その仮想クラスターに SQL Managed Instance が存在していないことが必要です。 
> - 仮想クラスターの削除は、約 1.5 時間続く実行時間の長い操作です (最も新しい仮想クラスターの削除時間については、[SQL Managed Instance の管理操作](./sql-managed-instance-paas-overview.md#management-operations)に関するページを参照してください)。 このプロセスが完了するまで、仮想クラスターはポータル上に引き続き表示されます。

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Azure portal から仮想クラスターを削除する

Azure portal を使用して仮想クラスターを削除するには、仮想クラスターのリソースを検索します。

![検索ボックスを強調表示した Azure portal のスクリーンショット](./media/virtual-cluster-delete/virtual-clusters-search.png)

削除する仮想クラスターが見つかったら、このリソースを選択して **[削除]** を選択します。 仮想クラスターを削除してよいか確認するメッセージが表示されます。

![削除オプションが強調された、Azure portal の仮想クラスター ダッシュボードのスクリーンショット](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure portal の通知によって、仮想クラスターの削除要求が正常に送信されたことの確認が表示されます。 削除操作自体には約 1.5 時間かかり、その間、仮想クラスターはポータル上に引き続き表示されます。 プロセスが完了すると、仮想クラスターは表示されなくなり、それに関連付けられていたサブネットは解放されて再利用できるようになります。

> [!TIP]
> SQL Managed Instance が仮想クラスターに表示されておらず、仮想クラスターを削除できない場合、継続的なインスタンスのデプロイが進行中でないことを確認してください。 これには、開始後にキャンセルされたがまだ進行中であるデプロイが含まれます。 これは、これらの操作によって仮想クラスターがまだ使用されていて、削除されないようロックされているためです。 インスタンスがデプロイされたリソース グループの **[デプロイ]** タブを確認すると、進行中のデプロイが示されます。 この場合、デプロイの完了を待ち、SQL Managed Instance を削除してから、仮想クラスターを削除します。

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>API を使用して仮想クラスターを削除する

API を使用して仮想クラスターを削除するには、[仮想クラスターの削除メソッド](/rest/api/sql/virtualclusters/delete)に関するページに記載されている URI パラメーターを使用します。

## <a name="next-steps"></a>次のステップ

- 概要については、「[Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」を参照してください。
- [SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)について確認します。
- [SQL Managed Instance 用に既存の仮想ネットワークを変更する](vnet-existing-add-subnet.md)方法を確認します。
- 仮想ネットワークを作成し、Azure SQL Managed Instance を作成し、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[Azure SQL Managed Instance の作成 (ポータル) ](instance-create-quickstart.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](custom-dns-configure.md)に関するページを参照してください。