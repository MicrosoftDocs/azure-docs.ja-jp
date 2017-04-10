---
title: "Azure Traffic Manager プロファイルの管理 | Microsoft Docs"
description: "この記事では、Azure Traffic Manager プロファイルを作成、無効化、有効化、削除する方法、およびプロファイルの履歴を表示する方法について説明します。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 7e7de7dc1eca6903403afef03fdd6afb98ff16c9

---

# <a name="manage-an-azure-traffic-manager-profile"></a>Azure Traffic Manager プロファイルの管理

Traffic Manager プロファイルでは、クラウド サービスまたは Web サイト エンドポイントへのトラフィックの分散を制御するためにトラフィック ルーティング方法が使用されます。 この記事では、これらのプロファイルの作成と管理の方法について説明します。

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>簡易作成による Traffic Manager プロファイルの作成

Azure クラシック ポータルで簡易作成を使用して、Traffic Manager プロファイルをすばやく作成できます。 簡易作成では、基本的な構成設定でプロファイルを作成できます。 ただし、簡易作成を使用して、一連のエンドポイント (クラウド サービスと Web サイト)、フェールオーバーによるトラフィックルーティング方法のフェールオーバー順序、または監視設定などを設定することはできません。 プロファイルの作成後、Azure クラシック ポータルでこれらの設定を構成できます。 Traffic Manager では、1 プロファイル当たり、最大 200 のエンドポイントをサポートしますが、 ほとんどの使用シナリオに必要なエンドポイントは少数にすぎません。

### <a name="to-create-a-traffic-manager-profile"></a>Traffic Manager プロファイルを作成するには

1. **クラウド サービスと Web サイトを運用環境にデプロイします。** クラウド サービスの詳細については、「[クラウド サービス](http://go.microsoft.com/fwlink/p/?LinkId=314074)」を参照してください。 Web サイトの詳細については、「 [Web サイト](http://go.microsoft.com/fwlink/p/?LinkId=393327)」を参照してください。
2. **Azure クラシック ポータルにログインします**。 ポータルの左下で **[新規]** をクリックし、**[Network Services]、[Traffic Manager]**、**[簡易作成]** の順にクリックして、プロファイルの構成を開始します。
3. **DNS プレフィックスを構成します。**  Traffic manager プロファイルに一意の DNS プレフィックス名を指定します。 Traffic Manager のドメイン名のプレフィックスだけを指定することができます。
4. **サブスクリプションを選択します。**  適切な Azure サブスクリプションを選択します。 各プロファイルは、単一のサブスクリプションに関連付けられています。 サブスクリプションが 1 つのみの場合、このオプションは表示されません。
5. **トラフィック ルーティング方法を選びます。** **[traffic routing Policy (トラフィック ルーティング ポリシー)]** でトラフィック ルーティング方法を選びます。 トラフィック ルーティング方法の詳細については、「 [Traffic Manager でのトラフィック ルーティング方法について](traffic-manager-routing-methods.md)」をご覧ください。
6. **[Create (作成)] をクリックしてプロファイルを作成します。** プロファイルの構成が完了したら、Azure クラシック ポータルの [Traffic Manager] ウィンドウでプロファイルを検索できます。
7. **Azure クラシック ポータルで、エンドポイント、監視、およびその他の設定を構成します。** [簡易作成] を使用した場合は、基本的な設定のみを構成します。 エンドポイントの一覧やエンドポイントのフェールオーバーの順番など、その他の設定を構成する必要があります。

## <a name="disable-enable-or-delete-a-profile"></a>プロファイルの無効化、有効化、または削除

Traffic Manager が構成済みのエンドポイントに対するユーザーの要求を参照しないように、既存のプロファイルを無効にすることができます。 Traffic Manager プロファイルを無効にすると、プロファイルとプロファイルに含まれる情報は元のまま残ります。このため、Traffic Manager インターフェイスで編集することもできます。  プロファイルをもう一度有効にすると、参照が再開されます。 Traffic Manager プロファイルは、Azure クラシック ポータルで作成すると自動的に有効になります。 プロファイルが不要になった場合は、削除できます。

### <a name="to-disable-a-profile"></a>プロファイルを無効にするには

1. カスタム ドメイン名を使用している場合は、インターネット DNS サーバー上の CNAME レコードを変更して、Traffic Manager プロファイルをポイントしないようにします。
2. Traffic Manager プロファイルをこのように設定すると、トラフィックがエンドポイントに送信されなくなります。
3. 無効にするプロファイルを選択します。 [Traffic Manager] ページでプロファイル名の横の列をクリックして、プロファイルを強調表示します。 プロファイルの名前または名前の横の矢印をクリックすると、プロファイルの設定ページが開きます。
4. プロファイルを選択した後に、ページ下部にある **[無効化]** をクリックします。

### <a name="to-enable-a-profile"></a>プロファイルを有効にするには

1. 無効にするプロファイルを選択します。 [Traffic Manager] ページでプロファイル名の横の列をクリックして、プロファイルを強調表示します。 プロファイルの名前または名前の横の矢印をクリックすると、プロファイルの設定ページが開きます。
2. プロファイルを選択した後に、ページ下部にある **[有効化]** をクリックします。
3. カスタム ドメイン名を使用している場合は、インターネット DNS サーバー上の CNAME レコードを作成して、Traffic Manager プロファイルのドメイン名をポイントするようにします。
4. トラフィックは再度、エンドポイントに送信されるようになります。

### <a name="to-delete-a-profile"></a>プロファイルを削除するには

1. インターネット DNS サーバー上の DNS リソース レコードが、Traffic Manager プロファイルのドメイン名を指す CNAME リソース レコードを使用していないことを確認します。
2. 無効にするプロファイルを選択します。 [Traffic Manager] ページでプロファイル名の横の列をクリックして、プロファイルを強調表示します。 プロファイルの名前または名前の横の矢印をクリックすると、プロファイルの設定ページが開きます。
3. プロファイルを選択した後に、ページ下部にある **[削除]** をクリックします。

## <a name="view-traffic-manager-profile-change-history"></a>Traffic Manager プロファイルの変更履歴を表示する

Azure クラシック ポータルの [管理サービス] では、Traffic Manager プロファイルの変更履歴を表示できます。

### <a name="to-view-your-traffic-manager-change-history"></a>Traffic Manager プロファイルの変更履歴を表示するには

1. Azure クラシック ポータルの左側のウィンドウで、 **[管理サービス]**をクリックします。
2. [管理サービス] ページで、 **[操作ログ]**をクリックします。
3. [操作ログ] ページでは、Traffic Manager プロファイルの変更履歴の中から表示するものを絞り込むことができます。 フィルター処理のオプションを選択したら、チェックマークをクリックすると結果が表示されます。

   * すべてのプロファイルの変更を表示するには、サブスクリプションと時間の範囲を選んで、**[タイプ]** ショートカット メニューの **[Traffic Manager]** を選びます。
   * プロファイル名でフィルター処理するには、 **[サービス名]** フィールドにプロファイルの名前を入力するか、ショートカット メニューから選びます。
   * 個々の変更の詳細を表示するには、変更を表示する行を選択して、ページの下部にある **[詳細]** をクリックします。 **[操作の詳細]** ウィンドウでは、操作の一環として作成または更新された API オブジェクトを XML 形式で表示できます。

## <a name="next-steps"></a>次のステップ

* [エンドポイントの追加](traffic-manager-endpoints.md)
* [フェールオーバーのルーティング方法の構成](traffic-manager-configure-failover-routing-method.md)
* [ラウンド ロビンによるトラフィック ルーティング方法の構成](traffic-manager-configure-round-robin-routing-method.md)
* [パフォーマンスによるトラフィック ルーティング方法の構成](traffic-manager-configure-performance-routing-method.md)
* [会社のインターネット ドメインで Traffic Manager ドメイン名が参照されるようにする](traffic-manager-point-internet-domain.md)
* [Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO5-->


