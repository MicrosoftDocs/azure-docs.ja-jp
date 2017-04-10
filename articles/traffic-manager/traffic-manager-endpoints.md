---
title: "Azure Traffic Manager でのエンドポイントの管理 | Microsoft Docs"
description: "この記事では、Azure Traffic Manager からエンドポイントの追加、削除、有効化、無効化する方法について説明します。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 038270d1-28ba-4078-9c5d-37fc5d683be6
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 22729a7164b8998e92147e418ce96ff9b09896b9

---

# <a name="add-disable-enable-or-delete-endpoints"></a>エンドポイントの追加と削除、有効化と無効化

Azure App Service の Web Apps 機能では、データセンター内の Web サイト向けに、Web サイトのモードに関係なく、フェールオーバーとラウンド ロビンによるトラフィック ルーティング機能があらかじめ用意されています。 Azure Traffic Manager を使用すると、異なるデータセンター内の Web サイトやクラウド サービスに対して、フェールオーバーとラウンド ロビンによるトラフィックルーティング方法を指定できます。 この機能の実現に必要な最初の手順は、Traffic Manager にクラウド サービスまたは Web サイトのエンドポイントを追加することです。

> [!NOTE]
> Azure クラシック ポータルを使用して、外部の場所や Traffic Manager プロファイルをエンドポイントとして追加することはできません。 REST API [Create Definition](http://go.microsoft.com/fwlink/p/?LinkId=400772) または [Windows PowerShell Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774) を使用する必要があります。

Traffic Manager プロファイルを構成する個々のエンドポイントを無効にすることもできます。 エンドポイントには、クラウド サービスと Web サイトの両方が含まれます。 無効にしたエンドポイントはその後もプロファイルの一部として残りますが、プロファイルは、エンドポイントが含まれていないかのように機能します。 この操作は、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントを一時的に削除するときに役立ちます。 エンドポイントが稼働状態になって再度動作すると、有効にすることができます。

> [!NOTE]
> エンドポイントの無効化は、Azure でのデプロイメント状態とは関係ありません。 正常なエンドポイントは、Traffic Manager で無効になっていても、稼働状態のままでトラフィックを受信できます。 また、あるプロファイルでエンドポイントを無効化しても、そのエンドポイントの他のプロファイルでの状態には影響しません。

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>クラウド サービスまたは Web サイトのエンドポイントを追加するには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
2. ページの上部にある **[エンドポイント]** をクリックして、既に構成に含まれているエンドポイントを表示します。
3. ページの下部にある **[追加]** をクリックして、**[サービス エンドポイントの追加]** ページにアクセスします。 既定では、このページの **[サービス エンドポイント]**の下にクラウド サービスの一覧が表示されます。
4. クラウド サービスの場合は、このプロファイルでエンドポイントとして有効にするクラウド サービスを一覧から選択します。 クラウド サービスの名前をクリアすると、エンドポイントの一覧から削除されます。
5. Web サイトの場合は、**[サービスの種類]** ドロップダウン リストをクリックし、**[Web アプリ]** を選択します。
6. このプロファイルでエンドポイントとして追加する Web サイトを一覧から選択します。 Web サイトの名前をクリアすると、エンドポイントの一覧から削除されます。 選択できる Web サイトは、各 Azure データセンター (リージョンとも呼ばれます) につき 1 つだけです。 複数の Web サイトをホストしているデータセンター内の Web サイトを選択する場合、1 つの Web サイトを選択すると、そのデータセンター内の他の Web サイトは選択できなくなります。 また、一覧には標準 Web サイトのみが表示されます。
7. このプロファイルのエンドポイントを選択したら、右下にあるチェックマークをクリックして変更を保存します。

> [!NOTE]
> *フェールオーバー* トラフィック ルーティング方法を使用している場合は、エンドポイントを追加または削除した後で、[構成] ページの [フェールオーバー優先度リスト] を調整して、構成に必要なフェールオーバー順序を反映させてください。 詳細については、「 [フェールオーバーによるトラフィック ルーティング方法の構成](traffic-manager-configure-failover-routing-method.md)」をご覧ください。

## <a name="to-disable-an-endpoint"></a>エンドポイントを無効にするには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
2. ページの上部にある、 **[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。
3. 無効にするエンドポイントをクリックしてから、ページの下部にある **[無効化]** をクリックします。
4. Traffic Manager ドメイン名用に構成された DNS Time To Live (TTL) に基づいて、トラフィックはエンドポイントに送信されなくなります。 TTL は、Traffic Manager プロファイルの [構成] ページから変更できます。

## <a name="to-enable-an-endpoint"></a>エンドポイントを有効にするには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
2. ページの上部にある、 **[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。
3. 有効にするエンドポイントをクリックしてから、ページの下部にある **[有効化]** をクリックします。
4. プロファイルに基づいて、サービスへのトラフィックの送信が再開されます。

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>クラウド サービスまたは Web サイトのエンドポイントを削除するには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
2. ページの上部にある **[エンドポイント]** をクリックして、既に構成に含まれているエンドポイントを表示します。
3. [エンドポイント] ページで、プロファイルから削除するエンドポイントの名前をクリックします。
4. ページの下部にある **[削除]**をクリックします。

> [!NOTE]
> Azure クラシック ポータルを使用して、外部の場所や Traffic Manager プロファイルをエンドポイントとして削除することはできません。 Windows PowerShell を使用する必要があります。 詳細については、 [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [フェールオーバーのルーティング方法の構成](traffic-manager-configure-failover-routing-method.md)
* [ラウンド ロビンによるトラフィック ルーティング方法の構成](traffic-manager-configure-round-robin-routing-method.md)
* [パフォーマンスによるトラフィック ルーティング方法の構成](traffic-manager-configure-performance-routing-method.md)
* [Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)
* [Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO5-->


