---
title: "Traffic Manager エンドポイントの無効化または有効化 | Microsoft Docs"
description: "この記事では、Traffic Manager プロファイル エンドポイントの無効化または有効化について説明します。"
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 9b2264ce-be06-43b2-a00b-5c724e5d71fd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3c8d9f5995ac51b128e46691962b5f6a220ab7d


---
<!-- repub for nofollow -->

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Traffic Manager エンドポイントの無効化または有効化
Traffic Manager プロファイルを構成する個々のエンドポイントを無効にすることもできます。 エンドポイントには、クラウド サービスと Web サイトの両方が含まれます。 無効にしたエンドポイントはその後もプロファイルの一部として残りますが、プロファイルは、エンドポイントが含まれていないかのように機能します。 この操作は、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントを一時的に削除するときに役立ちます。 エンドポイントが再度稼働状態になったら、有効にできます。

> [!NOTE]
> **エンドポイントの無効化は、Azure でのデプロイメント状態とは関係ありません。正常なエンドポイントは、Traffic Manager で無効になっていても、稼働状態のままでトラフィックを受信できます。また、あるプロファイルでエンドポイントを無効化しても、そのエンドポイントの他のプロファイルでの状態には影響しません。**
> 
> 

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

## <a name="next-steps"></a>次のステップ
[Traffic Manager  - プロファイルの無効化、有効化、または削除](disable-enable-or-delete-a-profile.md)

[Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)

[Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)




<!--HONumber=Nov16_HO3-->


