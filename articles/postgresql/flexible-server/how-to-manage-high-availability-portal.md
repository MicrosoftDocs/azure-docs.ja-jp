---
title: ゾーン冗長の高可用性を管理する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーでのゾーン冗長の高可用性を有効または無効にする方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90930827"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>フレキシブル サーバーでゾーン冗長の高可用性を管理する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

この記事では、フレキシブル サーバーでゾーン冗長の高可用性構成を有効または無効にする方法について説明します。

高可用性機能では、別々のゾーンに物理的に分けられたプライマリ レプリカとスタンバイ レプリカをプロビジョニングします。 詳細については、[高可用性の概念に関するドキュメント](./concepts-high-availability.md)を参照してください。 フレキシブル サーバーの作成時、または作成後に、高可用性を有効にすることを選択できます。 このページでは、高可用性を有効または無効にする方法に関するガイドラインを示します。 この操作では、VNET 構成、ファイアウォール設定、バックアップ保有期間などの他の設定は変更されません。 同様に、高可用性の有効化と無効化はオンライン操作であり、アプリケーションの接続と操作には影響しません。

## <a name="pre-requisites"></a>前提条件

ゾーン冗長の高可用性は、複数のゾーンがサポートされているリージョンでのみ使用できます。 

## <a name="enable-high-availability-during-server-creation"></a>サーバーの作成時に高可用性を有効にする

このセクションでは、特に HA に関連するフィールドの詳細について説明します。 次の手順に従って、柔軟なサーバーの作成中に高可用性を展開できます。

1.  [Azure portal](https://portal.azure.com/) で、[フレキシブル サーバー] を選択し、[作成] をクリックします。  **[サブスクリプション]** 、 **[リソース グループ]** 、 **[サーバー名]** 、 **[リージョン]** 、およびその他のフィールドなどの詳細の入力方法の詳細については、サーバー作成に関するハウツー ドキュメントをご覧ください。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="サブスクリプションとリージョンを表示する":::

2.  **可用性ゾーン** を確認します。 これは、待機時間を短縮するために、データベースと同じ可用性ゾーンにアプリケーションを併置する場合に便利です。 フレキシブル サーバーを任意の可用性ゾーンに展開する場合は、 **[優先設定なし]** を選択します。
    ![AZ の選択]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="可用性ゾーンの選択":::  

3.  [可用性] オプションで **[ゾーン冗長の高可用性]** のチェックボックスをオンにします。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="高可用性のチェックボックス":::

4.  デフォルトのコンピューティングとストレージを変更する場合は、 **[サーバーの構成]** をクリックします。
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="サーバーの構成 - コンピューティング + ストレージ":::  

5.  [高可用性] オプションをオンにすると、バースト可能レベルは選択できなくなります。 **[汎用]** または **[メモリ最適化]** のどちらかのコンピューティング レベルを選択できます。 次に、ドロップダウン リストから選択した **コンピューティング サイズ** を選択できます。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="コンピューティング レベルの選択":::  


6.  スライディング バーを使用して GiB 単位で **ストレージ サイズ** を選択し、7 日から 35 日の範囲で **バックアップ保持期間** を選択します。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="ストレージ バックアップ"::: 

7. **[保存]** をクリックします。 

## <a name="enable-high-availability-post-server-creation"></a>サーバーの作成後に高可用性を有効にする

次の手順に従って、既存のフレキシブル サーバーの高可用性を有効にします。

1.  [Azure portal](https://portal.azure.com/) で、既存の PostgreSQL フレキシブル サーバーを選択します。

2.  [フレキシブル サーバー] ページで、左側のパネルから **[高可用性]** をクリックして、[高可用性] ページを開きます。
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="左側のパネルの選択"::: 

3.  **[ゾーン冗長の高可用性]** チェックボックスをオンにして、このオプションを **有効** にし、 **[保存]** をクリックして変更内容を保存します。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="高可用性を有効にする"::: 

4.  高可用性を有効にすることにより、追加のサーバーと記憶域のデプロイによってコストが増大することを示す確認ダイアログが表示されます。

5.  **[Enable HA] (高可用性を有効にする)** ボタンをクリックして、高可用性を有効にします。

6.  高可用性のデプロイが進行中であることを示す通知が表示されます。

## <a name="disable-high-availability"></a>高可用性を無効にする

次の手順に従って、既にゾーン冗長で構成されているフレキシブル サーバーの高可用性を無効にします。

1.  [Azure portal](https://portal.azure.com/) で、既存の Azure Database for PostgreSQL - フレキシブル サーバーを選択します。

2.  フレキシブル サーバー ページで、前面パネルから **[高可用性]** をクリックして、[高可用性] ページを開きます。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="左側のパネルの選択"::: 

3.  **[ゾーン冗長の高可用性]** チェックボックスをオンにして、オプションを **無効** にします。 次に、 **[保存]** をクリックして変更内容を保存します。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="高可用性を無効にする"::: 

4.  高可用性を無効にすることを確認できる確認ダイアログが表示されます。

5.  **[Disable HA]\(高可用性を無効にする\)** ボタンをクリックして、高可用性を無効にします。

6.  高可用性のデプロイの使用停止が進行中であることを示す通知が表示されます。

## <a name="next-steps"></a>次のステップ

-   [ビジネス継続性](./concepts-business-continuity.md)について確認する
-   [ゾーン冗長による高可用性](./concepts-high-availability.md)について確認する
