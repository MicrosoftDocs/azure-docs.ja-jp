---
title: Azure Traffic Manager でのエンドポイントの管理 | Microsoft Docs
description: この記事では、Azure Traffic Manager からエンドポイントの追加、削除、有効化、無効化する方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: duau
ms.openlocfilehash: 3d5a888bf132725ff64c2a128b77f0257f435585
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184256"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>エンドポイントの追加、削除、有効化、無効化

Azure App Service の Web Apps 機能では、データセンター内の Web サイト向けに、Web サイトのモードに関係なく、フェールオーバーとラウンド ロビンによるトラフィック ルーティング機能があらかじめ用意されています。 Azure Traffic Manager を使用すると、異なるデータセンター内の Web サイトやクラウド サービスに対して、フェールオーバーとラウンド ロビンによるトラフィックルーティング方法を指定できます。 この機能の実現に必要な最初の手順は、Traffic Manager にクラウド サービスまたは Web サイトのエンドポイントを追加することです。

Traffic Manager プロファイルを構成する個々のエンドポイントを無効にすることもできます。 無効にしたエンドポイントはその後もプロファイルの一部として残りますが、プロファイルは、エンドポイントが含まれていないかのように機能します。 この操作は、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントを一時的に削除するときに役立ちます。 エンドポイントが稼働状態になって再度動作すると、有効にすることができます。

> [!NOTE]
> エンドポイントの無効化は、Azure でのデプロイメント状態とは関係ありません。 正常なエンドポイントは、Traffic Manager で無効になっていても、稼働状態のままでトラフィックを受信できます。 また、あるプロファイルでエンドポイントを無効化しても、そのエンドポイントの他のプロファイルでの状態には影響しません。

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>クラウド サービスまたは App Service のエンドポイントを Traffic Manager プロファイルに追加するには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルの検索バーで、変更の対象となる **Traffic Manager プロファイル** 名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. **[Traffic Manager プロファイル]** ブレードの **[設定]** セクションで、**[エンドポイント]** をクリックします。
4. 表示された **[エンドポイント]** ブレードで、**[追加]** をクリックします。
5. **[エンドポイントの追加]** ブレードで、以下の操作を実行します。
    1. **[種類]** で、**[Azure エンドポイント]** をクリックします。
    2. このエンドポイントを識別するための **名前** を指定します。
    3. **[ターゲット リソースの種類]** ボックスの一覧から適切なリソースの種類を選択します。
    4. **[ターゲット リソース]** で、**[選択]** セレクターをクリックすると、同じサブスクリプションのリソースが **[リソース] ブレード** に一覧表示されます。 表示された **[リソース]** ブレードで、最初のエンドポイントとして追加するサービスを選択します。
    5. **[優先順位]** で **[1]** を選択します。 この結果、このエンドポイント正常な状態である場合、すべてのトラフィックがここに送信されます。
    6. **[Add as disabled (無効として追加)]** はオフのままにします。
    7. **[OK]**
6.  手順 4. と手順 5. を繰り返して次の Azure エンドポイントを追加します。 必ず **[優先順位]** の値を **[2]** に設定してこのエンドポイント追加してください。
7.  両方のエンドポイントは、追加が完了すると、**[Traffic Manager プロファイル]** ブレードに、監視ステータスが **[オンライン]** の状態で表示されます。

> [!NOTE]
> "*フェールオーバー*" トラフィック ルーティング方法でプロファイルにエンドポイントを追加したり、プロファイルからエンドポイントを削除したりすると、フェールオーバー優先度リストの順番が期待どおりではなくなる可能性があります。 フェールオーバー優先度リストの順番は、[構成] ページで調整できます。 詳細については、「 [フェールオーバーによるトラフィック ルーティング方法の構成](./traffic-manager-configure-priority-routing-method.md)」をご覧ください。

## <a name="to-disable-an-endpoint"></a>エンドポイントを無効にするには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルの検索バーで、変更の対象となる **Traffic Manager プロファイル** 名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. **[Traffic Manager プロファイル]** ブレードの **[設定]** セクションで、**[エンドポイント]** をクリックします。 
4. 無効にするエンドポイントをクリックします。
5. **[エンドポイント]** ブレードでエンドポイントの状態を **[無効]** に変更し、**[保存]** をクリックします。
6. クライアントは、有効期間 (TTL) 中はエンドポイントにトラフィックを送信し続けます。 TTL は、Traffic Manager プロファイルの [構成] ページで変更できます。

## <a name="to-enable-an-endpoint"></a>エンドポイントを有効にするには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルの検索バーで、変更の対象となる **Traffic Manager プロファイル** 名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. **[Traffic Manager プロファイル]** ブレードの **[設定]** セクションで、**[エンドポイント]** をクリックします。 
4. 有効にするエンドポイントをクリックします。
5. **[エンドポイント]** ブレードでエンドポイントの状態を **[有効]** に変更し、**[保存]** をクリックします。
6. クライアントは、有効期間 (TTL) 中はエンドポイントにトラフィックを送信し続けます。 TTL は、Traffic Manager プロファイルの [構成] ページで変更できます。

## <a name="to-delete-an-endpoint"></a>エンドポイントを削除するには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルの検索バーで、変更の対象となる **Traffic Manager プロファイル** 名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. **[Traffic Manager プロファイル]** ブレードの **[設定]** セクションで、**[エンドポイント]** をクリックします。 
4. 削除するエンドポイントをクリックします。
5. **[エンドポイント]** ブレードで、**[削除]** をクリックします。


## <a name="next-steps"></a>次のステップ

* [Traffic Manager プロファイルの管理](traffic-manager-manage-profiles.md)
* [ルーティング方法の構成](./traffic-manager-configure-priority-routing-method.md)
* [Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)
* [Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)
* [Traffic Manager の操作 (REST API リファレンス)](/previous-versions/azure/reference/hh758255(v=azure.100))