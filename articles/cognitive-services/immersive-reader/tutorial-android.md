---
title: チュートリアル:Android コード サンプルを使用してイマーシブ リーダーを起動する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、イマーシブ リーダーを起動するサンプル Android アプリケーションを構成して実行します。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516437"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>チュートリアル:Android Java コード サンプルを使用してイマーシブ リーダーを起動する

[概要](./overview.md)に関するページでは、イマーシブ リーダーの機能とそのしくみ (どのようにして言語学習者、新しい読者、および学習方法の異なる学生が読解力向上のために実証済みの手法を実装するか) について説明しました。 このチュートリアルでは、イマーシブ リーダーを起動する Android アプリケーションの作成方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サンプル プロジェクトを使用して Android 用のアプリを構成して実行します。
> * アクセス トークンを取得します。
> * イマーシブ リーダーを起動してサンプル コンテンツを表示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](./how-to-create-immersive-reader.md)に従ってください。 環境のプロパティを構成する際に、ここで作成した値のいくつかが必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* [Git](https://git-scm.com/).
* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Android Studio](https://developer.android.com/studio)。

## <a name="configure-authentication-credentials"></a>認証資格情報の構成

1. Android Studio を起動して、**immersive-reader-sdk/js/samples/quickstart-java-android** ディレクトリ (Java) または **immersive-reader-sdk/js/samples/quickstart-kotlin** ディレクトリ (Kotlin) からプロジェクトを開きます。

1. **/assets** フォルダー内に **env** という名前のファイルを作成します。 次の名前と値を追加し、適切な値を指定します。 このファイルには公開してはならないシークレットが含まれているため、ソース管理にコミットしないでください。
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

AVD マネージャーからデバイス エミュレーターを選択して、プロジェクトを実行します。

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk) と[イマーシブ リーダー SDK リファレンス](./reference.md)を確認します。
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) でコード サンプルを見る。