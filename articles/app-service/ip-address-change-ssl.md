---
title: SSL IP アドレスの変更に備える
description: SSL IP アドレスの変更が予定されている場合に、変更後もアプリが動作し続けるようにする方法を説明します。
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020961"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>SSL IP アドレスの変更に備える方法

お使いの Azure App Service アプリの SSL IP アドレスが変更されるという通知を受け取った場合は、この記事の手順に従って既存の SSL IP アドレスを解放し、新しい SSL IP アドレスを割り当てます。

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>SSL IP アドレスを解放し、新しい SSL IP アドレスを割り当てる

1.  [Azure Portal](https://portal.azure.com)を開きます。

2.  左側のナビゲーション メニューで、 **[App Services]** を選択します。

3.  一覧から App Service アプリを選択します。

4.  **[設定]** ヘッダーの下で、左側のナビゲーションにある **[SSL 設定]** をクリックします。

1. [TLS/SSL バインド] セクションで、ホスト名レコードを選択します。 表示されたエディターで、 **[SSL の種類]** ドロップダウン メニューから **[SNI SSL]** を選択し、 **[バインドの追加]** をクリックします。 操作成功のメッセージが表示されたら、既存の IP アドレスは解放されています。

6.  **[SSL バインド]** セクションで、再度、同じホスト名レコードと証明書を選択します。 表示されたエディターで、今度は **[SSL の種類]** ドロップダウン メニューから **[IP ベースの SSL]** を選択し、 **[バインドの追加]** をクリックします。 操作成功のメッセージが表示されたら、新しい IP アドレスは取得されています。

7.  ドメイン登録ポータル (サードパーティの DNS プロバイダーまたは Azure DNS) で A レコード (IP アドレスを直接ポイントしている DNS レコード) が構成されている場合は、既存の IP アドレスを新しく生成された IP アドレスで置き換えます。 新しい IP アドレスは、次のセクションの手順に従って確認できます。

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Azure portal で新しい SSL IP アドレスを見つける

1.  数分待ってから、[Azure portal](https://portal.azure.com) を開きます。

2.  左側のナビゲーション メニューで、 **[App Services]** を選択します。

3.  一覧から App Service アプリを選択します。

4.  **[設定]** ヘッダーの下で、左側のナビゲーションにある **[プロパティ]** をクリックし、 **[仮想 IP アドレス]** というラベルのセクションを見つけます。

5. この IP アドレスをコピーし、ドメインのレコードや IP メカニズムを再構成します。

## <a name="next-steps"></a>次のステップ

この記事では、Azure によって開始された IP アドレスの変更に備える方法について説明しました。 Azure App Service での IP アドレスの詳細については、「[Azure App Service における受信 IP アドレスと送信 IP アドレス](overview-inbound-outbound-ips.md)」を参照してください。
