---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: f4ba467b6d80c9ccafba0a91c1f04152b92cf869
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125237"
---
1. Mac で [Azure portal] にアクセスします。 **[すべてのサービス]** > **[App Services]** の順にクリックして、先ほど作成したバックエンドをクリックします。 モバイル アプリの設定で、優先する言語を選択します。

   - Objective-C &ndash; **[クイック スタート]** > **[iOS (Objective-C)]**
   - Swift &ndash; **[クイック スタート]** > **[iOS (Swift)]**

     **[3. クライアント アプリケーションの構成]** で、**[ダウンロード]** をクリックします。 これにより、バックエンドに接続するよう事前に構成された Xcode プロジェクト一式がダウンロードされます。 Xcode を使用してプロジェクトを開きます。

1. **[Run]** をクリックしてプロジェクトをビルドし、iOS シミュレーターでアプリを起動します。

1. アプリケーションでプラス アイコン (**+**) をクリックし、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、保存ボタンをクリックします。 これにより、事前にデプロイした Azure バックエンドに POST 要求が送信されます。 バックエンドは要求から取得したデータを TodoItem SQL テーブルに挿入し、新しく格納されたアイテムに関する情報をモバイル アプリに返します。 モバイル アプリでは、このデータが一覧に表示されます。

   ![iOS で実行されているクイック スタート アプリ](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
