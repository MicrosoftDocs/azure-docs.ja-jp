---
title: Purview Studio の使用
description: この概念に関する記事では、Azure Purview Studio の使用方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: ca25bbb72ff853f819f3e8ce4e0092ddb762b156
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183813"
---
# <a name="use-purview-studio"></a>Purview Studio の使用

この記事では、Azure Purview のいくつかの主な機能の概要について説明します。

## <a name="prerequisites"></a>前提条件

* Azure Purview アカウントが Azure portal で既に作成されており、ユーザーが Purview Studio にアクセスするためのアクセス許可を持っていること。

## <a name="launch-purview-account"></a>Purview アカウントの起動

* Purview アカウントを起動するには、Azure portal の [Purview アカウント] に移動し、起動するアカウントを選択して、そのアカウントを起動します。

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Azure Purview アカウント カタログの起動を選択するスクリーンショット。":::

* Purview アカウントを起動する別の方法として、`https://web.purview.azure.com` にアクセスし、 **[Azure Active Directory]** とアカウント名を選択してアカウントを起動することもできします。

## <a name="home-page"></a>ホーム ページ

**ホーム** は、Azure Purview クライアントの開始ページです。

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="ホームページのスクリーンショット。":::

次の一覧は、**ホーム ページ** の主な機能をまとめたものです。 一覧にある各番号は、前のスクリーンショットで強調表示されている番号に対応しています。

1. カタログのフレンドリ名。 カタログ名は、 **[管理センター]**  >  **[アカウント情報]** で設定できます。

2. カタログ分析には、以下の数が表示されます。
    - ユーザー、グループ、アプリケーション
    - データ ソース
    - アセット
    - 用語集の用語

3. 検索ボックスを使用すると、データ カタログ全体でデータ資産を検索できます。

4. クイック アクセス ボタンを使用すると、アプリケーションのよく使用される機能にアクセスできます。 表示されるボタンは、ユーザー アカウントに割り当てられているロールによって異なります。

    - "*データ キュレーター*" の場合、それらのボタンは **[ナレッジ センター]** 、 **[資産の参照]** 、 **[用語集の管理]** 、 **[分析情報の表示]** です。
    - "*データ閲覧者*" の場合、主要なボタンは **[ナレッジ センター]** 、 **[資産の参照]** 、 **[用語集の表示]** 、 **[分析情報の表示]** です。
    - "*データ ソース管理者*"  +  "*データ キュレーター*" の場合、主要なボタンは **[ナレッジ センター]** 、 **[データ ソースの登録]** 、 **[資産の参照]** 、 **[用語集の管理]** です。
    - "*データ ソース管理者*"  +  "*データ閲覧者*" の場合、主要なボタンは **[ナレッジ センター]** 、 **[データ ソースの登録]** 、 **[資産の参照]** 、 **[用語集の表示]** です。

5. 左側のナビゲーション バーは、アプリケーションのメイン ページを見つける場合に役立ちます。 表示されるボタンは、ユーザー アカウントに割り当てられているロールによって異なります。

    - "*データ キュレーター*" の場合、それらのボタンは **[ホーム]** 、 **[用語集]** 、 **[分析情報]** 、 **[管理センター]** です。
    - "*データ閲覧者*" の場合、それらのボタンは **[ホーム]** 、 **[用語集]** 、 **[分析情報]** 、 **[管理センター]** です。
    - "*データ ソース管理者*"  +  "*データ キュレーターまたは閲覧者*" の場合、それらのボタンは **[ホーム]** 、 **[ソース]** 、 **[用語集]** 、 **[分析情報]** 、 **[管理センター]** です。
  
6. **[最近アクセス]** タブには、最近アクセスしたデータ資産の一覧が表示されます。 資産へのアクセス方法について詳しくは、[Data Catalog の検索](how-to-search-catalog.md)および[資産の種類での参照](how-to-browse-catalog.md#browse-experience)に関する記事をご覧ください。  **[マイ アイテム]** タブは、ログオンしているユーザーが所有するデータ資産の一覧です。
7. **[役に立つリンク]** には、リージョンの状態、ドキュメント、価格、概要、Purview の状態へのリンクが含まれています
8. 上部のナビゲーション バーには、リリース ノートや更新プログラム、Purview アカウントの変更、通知、ヘルプ、フィードバックの各セクションに関する情報が含まれています。

## <a name="knowledge-center"></a>ナレッジ センター

ナレッジ センターは、Purview に関連するすべてのビデオとチュートリアルを検索できる場所です。

## <a name="guided-tours"></a>ガイド ツアー

Azure Purview Studio の各 UX には、ページの概要を示すガイド ツアーが用意されています。 ガイド ツアーを開始するには、上部のバーにある **[ヘルプ]** を選択して、 **[ガイド ツアー]** を選択します。

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="ガイド ツアーのスクリーンショット。":::

> [!Important]
   > データソース管理者ロール自体には、Purview Studio へのアクセス権はありません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [セキュリティ プリンシパルを追加する](tutorial-scan-data.md)
