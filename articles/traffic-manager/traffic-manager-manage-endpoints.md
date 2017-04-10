---
title: "Azure Traffic Manager でのエンドポイントの管理 | Microsoft Docs"
description: "この記事では、Azure Traffic Manager からエンドポイントの追加、削除、有効化、無効化する方法について説明します。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 52f6d4f3e68e5eb120ee499827cc8549b8e547fd

---

# <a name="add-disable-enable-or-delete-endpoints"></a>エンドポイントの追加、削除、有効化、無効化

Azure App Service の Web Apps 機能では、データセンター内の Web サイト向けに、Web サイトのモードに関係なく、フェールオーバーとラウンド ロビンによるトラフィック ルーティング機能があらかじめ用意されています。 Azure Traffic Manager を使用すると、異なるデータセンター内の Web サイトやクラウド サービスに対して、フェールオーバーとラウンド ロビンによるトラフィックルーティング方法を指定できます。 この機能の実現に必要な最初の手順は、Traffic Manager にクラウド サービスまたは Web サイトのエンドポイントを追加することです。

> [!NOTE]
> この記事では、クラシック ポータルの使用方法について説明します。 Azure クラシック ポータルでは、クラウド サービスと Web アプリのエンドポイントとしての作成と割り当てのみがサポートされています。 新しい [Azure Portal](https://portal.azure.com) がお勧めのインターフェイスです。

Traffic Manager プロファイルを構成する個々のエンドポイントを無効にすることもできます。 無効にしたエンドポイントはその後もプロファイルの一部として残りますが、プロファイルは、エンドポイントが含まれていないかのように機能します。 この操作は、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントを一時的に削除するときに役立ちます。 エンドポイントが稼働状態になって再度動作すると、有効にすることができます。

> [!NOTE]
> エンドポイントの無効化は、Azure でのデプロイメント状態とは関係ありません。 正常なエンドポイントは、Traffic Manager で無効になっていても、稼働状態のままでトラフィックを受信できます。 また、あるプロファイルでエンドポイントを無効化しても、そのエンドポイントの他のプロファイルでの状態には影響しません。

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>クラウド サービスまたは Web サイトのエンドポイントを追加するには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけます。 設定ページを開くには、プロファイル名の右側にある矢印をクリックします。
2. ページの上部にある **[エンドポイント]** をクリックして、既に構成に含まれているエンドポイントを表示します。
3. ページの下部にある **[追加]** をクリックして、**[サービス エンドポイントの追加]** ページにアクセスします。 既定では、このページの **[サービス エンドポイント]**の下にクラウド サービスの一覧が表示されます。
4. クラウド サービスの場合は、このプロファイルでエンドポイントとして追加するクラウド サービスを一覧から選択します。 クラウド サービスの名前をクリアすると、エンドポイントの一覧から削除されます。
5. Web サイトの場合は、**[サービスの種類]** ドロップダウン リストをクリックし、**[Web アプリ]** を選択します。
6. このプロファイルでエンドポイントとして追加する Web サイトを一覧から選択します。 Web サイトの名前をクリアすると、エンドポイントの一覧から削除されます。 選択できる Web サイトは、Azure データセンター (リージョンとも呼ばれます) ごとに 1 つだけです。 最初の Web サイトを選択すると、同じデータセンター内の他の Web サイトは選択できなくなります。 また、一覧には標準 Web サイトのみが表示されます。
7. このプロファイルのエンドポイントを選択したら、右下にあるチェックマークをクリックして変更を保存します。

> [!NOTE]
> *[フェールオーバー]* トラフィック ルーティング方法でプロファイルにエンドポイントを追加したり、プロファイルからエンドポイントを削除したりすると、フェールオーバー優先度リストの順番が適切ではなくなる可能性があります。 フェールオーバー優先度リストの順番は、[構成] ページで調整できます。 詳細については、「 [フェールオーバーによるトラフィック ルーティング方法の構成](traffic-manager-configure-failover-routing-method.md)」をご覧ください。

## <a name="to-disable-an-endpoint"></a>エンドポイントを無効にするには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけます。 設定ページを開くには、プロファイル名の右側にある矢印をクリックします。
2. ページの上部にある、 **[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。
3. 無効にするエンドポイントをクリックしてから、ページの下部にある **[無効化]** をクリックします。
4. クライアントは、有効期間 (TTL) 中はエンドポイントにトラフィックを送信し続けます。 TTL は、Traffic Manager プロファイルの [構成] ページで変更できます。

## <a name="to-enable-an-endpoint"></a>エンドポイントを有効にするには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけます。 設定ページを開くには、プロファイル名の右側にある矢印をクリックします。
2. ページの上部にある、 **[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。
3. 有効にするエンドポイントをクリックしてから、ページの下部にある **[有効化]** をクリックします。
4. クライアントは、プロファイルで指定されているとおりに、有効になっているエンドポイントにリダイレクトされます。

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>クラウド サービスまたは Web サイトのエンドポイントを削除するには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけます。 設定ページを開くには、プロファイル名の右側にある矢印をクリックします。
2. ページの上部にある **[エンドポイント]** をクリックして、既に構成に含まれているエンドポイントを表示します。
3. [エンドポイント] ページで、プロファイルから削除するエンドポイントの名前をクリックします。
4. ページの下部にある **[削除]**をクリックします。

## <a name="next-steps"></a>次のステップ

* [Traffic Manager プロファイルの管理](traffic-manager-manage-profiles.md)
* [ルーティング方法の構成](traffic-manager-configure-routing-method.md)
* [Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)
* [Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)
* [Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO5-->


