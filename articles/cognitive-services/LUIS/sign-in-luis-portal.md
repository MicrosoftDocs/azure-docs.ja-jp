---
title: LUIS ポータルにサインインする
description: 新しいユーザーが LUIS ポータルにサインインする場合、現在のユーザー アカウントに基づいてサインイン エクスペリエンスが若干異なるものになります。
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706777"
---
# <a name="sign-in-to-luis-portal"></a>LUIS ポータルにサインインする

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

この記事では、LUIS ポータルの概要とオーサリング リソースの作成について説明します。 この記事の手順を完了すると、LUIS アプリを作成して発行できるようになります。

## <a name="access-the-portal"></a>ポータルへのアクセス


1. LUIS を使ってみるには、[LUIS ポータル](https://www.luis.ai)にアクセスします。 まだサブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com//free/cognitive-services/)してからポータルに戻るように求められます。
2. ページを最新の情報に更新します。新規作成したサブスクリプションが更新結果に反映されます
3. ドロップダウン リストからサブスクリプションを選択します

    > [!div class="mx-imgBorder"]
    > ![サブスクリプションの選択](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. サブスクリプションが別のテナントに存在する場合、既存のウィンドウからテナントを切り替えることはできません。 テナントを切り替えるには、このウィンドウを閉じて、上部バーの右端にあるイニシャルの含まれたアバターを選択してください。 上部にある **[別のオーサリング リソースの選択]** をクリックして、このウィンドウを再び開きます。

    > [!div class="mx-imgBorder"]
    > ![ディレクトリの切り替え](./media/migrate-authoring-key/switch-directories.png)

5. サブスクリプションに関連付けられている既存の LUIS オーサリング リソースがある場合は、ドロップダウン リストから選択します。 このオーサリング リソースの下で作成したすべてのアプリケーションが表示されます。
6. 表示されない場合は、このモーダルの下部にある **[新しいオーサリング リソースの作成]** をクリックします。
7.  新しい作成リソースを作成するときは、次の情報を指定します。

    > [!div class="mx-imgBorder"]
    > ![新しいリソースの作成](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **[テナント名]** - Azure サブスクリプションが関連付けられているテナント。 既存のウィンドウからテナントを切り替えることはできません。 テナントを切り替えるには、このウィンドウを閉じて、画面の右上隅にあるイニシャルの含まれたアバターを選択してください。 上部にある **[別のオーサリング リソースの選択]** を選択して、このウィンドウを再び開きます。
    * **[Azure リソース グループ名]** - サブスクリプションで選択したカスタム リソース グループの名前。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。 現在サブスクリプション内にリソース グループがない場合、LUIS ポータルでそれを作成することはできません。 [Azure portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) にアクセスしてそれを作成してから、LUIS にアクセスしてサインイン プロセスを続行します。
    * **[Azure リソース名]** - 選択したカスタム名。オーサリング トランザクションの URL の一部として使用されます。 リソース名には英数字と `-` のみを含めることができ、先頭または末尾に `-` を使用することはできません。 名前に他の記号が含まれている場合は、リソースの作成に失敗します。
    * **[場所]** - LUIS で現在サポートされている次の [3 つのオーサリング場所](./luis-reference-regions.md)のいずれかでアプリケーションを作成することを選択します:米国西部、西ヨーロッパ、オーストラリア東部
    * **[価格レベル]** - 既定では、推奨されているように F0 authoring 価格レベルが選択されています。 セキュリティをさらに強化する必要がある場合は、Azure portal から[カスタマー マネージド キー](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding)を作成します。
8. これで、LUIS に正常にサインインしました。 アプリケーションの作成を開始できます。

## <a name="troubleshooting"></a>トラブルシューティング

* 新しいリソースを作成するときは、リソース名に英数字と "-" のみが含まれていること、および先頭または末尾に "-" を使用できないことを確認してください。 アクティブなインスタンスがある場合、ファイルは正しく実行されません。
* [Azure リソース作成するための、サブスクリプションに対する適切なアクセス許可](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)があることを確認します。 適切なアクセス許可がない場合は、ご使用のサブスクリプションの管理者に連絡して、十分なアクセス許可を付与してもらってください。

## <a name="next-steps"></a>次の手順

* [新しいアプリの開始](luis-how-to-start-new-app.md)方法を学習する