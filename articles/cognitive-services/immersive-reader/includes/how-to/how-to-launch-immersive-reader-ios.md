---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620301"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](../../how-to-create-immersive-reader.md)に従ってください。  環境のプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* [macOS](https://www.apple.com/macos)。
* [Git](https://git-scm.com/).
* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)。

## <a name="configure-authentication-credentials"></a>認証資格情報の構成

1. Xcode を開いて、**immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodepro** を開きます。
1. 上部のメニューで、 **[Product]\(製品\)**  >  **[Scheme]\(スキーム\)**  >  **[Edit Scheme]\(スキームの編集\)** の順に選択します。
1. **[Run]\(実行\)** ビューで、 **[Arguments]\(引数\)** タブを選択します。
1. **[Environment Variables]\(環境変数\)** セクションで、次の名前と値を追加します。 イマーシブ リーダー リソースを作成したときに取得した値を指定します。

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

この変更には公開してはいけないシークレットが含まれているので、ソース管理にコミットしないでください。

## <a name="start-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

Xcode で、**Ctrl + R** キーを選択してプロジェクトを実行します。

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk) と[イマーシブ リーダー SDK リファレンス](../../reference.md)を確認します。
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) でコード サンプルを見る。
