---
title: 再起動する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL で再起動操作を実行する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90930815"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーを再起動する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

この記事では、フレキシブル サーバーの再起動を実行するための詳細な手順について説明します。 この操作は、データベース サーバーの再起動を必要とする静的パラメーターの変更を適用するために役立ちます。 この手順は、ゾーン冗長の高可用性が構成されているサーバーでも同じです。 

> [!IMPORTANT]
> 高可用性が構成されている場合は、プライマリ サーバーとスタンバイ サーバーの両方が同時に再起動されます。

## <a name="pre-requisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   フレキシブル サーバーが用意されている必要があります。

## <a name="restart-your-flexible-server"></a>フレキシブル サーバーを再起動する

フレキシブル サーバーを再起動するには、次の手順に従います。

1.  [Azure portal](https://portal.azure.com/) で、開始するフレキシブル サーバーを選択します。

2.  左側のパネルから **[概要]** をクリックし、 **[再起動]** をクリックします。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="再起動の選択":::

3.  ポップアップ確認メッセージが表示されます。

4.  続行する場合は、 **[はい]** をクリックします。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="再起動の確認":::
 
6.  再起動操作が開始されたことを示す通知が表示されます。

## <a name="next-steps"></a>次のステップ

-   [ビジネス継続性](./concepts-business-continuity.md)について確認する
-   [ゾーン冗長による高可用性](./concepts-high-availability.md)について確認する
