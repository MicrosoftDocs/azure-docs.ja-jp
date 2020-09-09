---
title: チュートリアル:Swift iOS コード サンプルを使用してイマーシブ リーダーを起動する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、イマーシブ リーダーを起動するサンプル Swift アプリケーションを構成して実行します。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516369"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>チュートリアル:Swift iOS コード サンプルを使用してイマーシブ リーダーを起動する

[概要](./overview.md)に関するページでは、イマーシブ リーダーの機能とそのしくみ (どのようにして言語学習者、新しい読者、および学習方法の異なる学生が読解力向上のために実証済みの手法を実装するか) について説明しました。 このチュートリアルでは、イマーシブ リーダーを起動する iOS アプリケーションの作成方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サンプル プロジェクトを使用して iOS 用の Swift アプリを構成して実行します。
> * アクセス トークンを取得します。
> * イマーシブ リーダーを起動してサンプル コンテンツを表示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](./how-to-create-immersive-reader.md)に従ってください。 環境のプロパティを構成する際に、ここで作成した値のいくつかが必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
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

* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk) と[イマーシブ リーダー SDK リファレンス](./reference.md)を確認します。
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) でコード サンプルを見る。
