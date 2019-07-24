---
title: 送信 IP アドレスの変更に備える - Azure App Service
description: 送信 IP アドレスの変更が予定されている場合に、変更後もアプリが動作し続けるようにする方法を説明します。
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ac62217af096653d61a79ff29ae352c8e950f8af
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719304"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>送信 IP アドレスの変更に備える方法

お使いの Azure App Service アプリの送信 IP アドレスが変更されるという通知を受け取った場合は、この記事の手順に従います。

## <a name="determine-if-you-have-to-do-anything"></a>何もする必要がないかどうかを判断する

* オプション 1:App Service アプリが、IP フィルター、明示的な対象リスト、またはルーティングやファイアウォールなどの送信トラフィックの特別な処理を使用していない場合は、何のアクションも必要ありません。

* オプション 2:アプリが送信 IP アドレスの特別な処理 (下の例を参照) を使用している場合は、既存の送信 IP アドレスが存在するすべての場所に新しい送信 IP アドレスを追加します。 既存の IP アドレスの置き換えはしないでください。 新しい送信 IP アドレスは、次のセクションの手順に従って確認できます。

  たとえば、送信 IP アドレスがアプリの外部のファイアウォールに明示的に含まれている場合や、外部の支払いサービスでアプリの送信 IP アドレスを含んだ許可リストが使用されている場合があります。 お使いの送信アドレスがアプリの外部の場所にあるリスト内で構成されている場合は、それを変更する必要があります。

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Azure portal で送信 IP アドレスを見つける

新しい送信 IP アドレスは、有効になる前にポータルに表示されます。 Azure で新しいアドレスの使用が開始されると、古いアドレスは使用されなくなります。 一度に 1 つのセットのみが使用されます。そこで、切り替え時に停止が発生するのを防ぐため、対象リストのエントリには古い IP アドレスと新しい IP アドレスの両方を含める必要があります。 

1.  [Azure Portal](https://portal.azure.com)を開きます。

2.  左側のナビゲーション メニューで、**[App Services]** を選択します。

3.  一覧から App Service アプリを選択します。

1.  アプリが関数アプリの場合、「[Function app outbound IP addresses](../azure-functions/ip-addresses.md#find-outbound-ip-addresses)」(関数アプリの送信 IP アドレス) を参照してください。

4.  **[設定]** ヘッダーの下で、左側のナビゲーションにある **[プロパティ]** をクリックし、**[送信 IP アドレス]** というラベルの付いたセクションを見つけます。

5. IP アドレスをコピーして、フィルターや許可リストなどの送信トラフィックの特別な処理に追加します。 リスト内の既存の IP アドレスは削除しないでください。

## <a name="next-steps"></a>次の手順

この記事では、Azure によって開始された IP アドレスの変更に備える方法について説明しました。 Azure App Service での IP アドレスの詳細については、「[Azure App Service における受信 IP アドレスと送信 IP アドレス](overview-inbound-outbound-ips.md)」を参照してください。
