---
title: Web アプリ チュートリアル - クライアント アプリケーションのセットアップ
description: このチュートリアルでは、Web アプリケーションのデプロイを準備するためにパブリック アプリケーションを登録する手順について説明します
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bf357dcc9c4790632a1131278edc41c13ac810b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018914"
---
# <a name="client-application-registration"></a>クライアント アプリケーションの登録
前のチュートリアルでは、Azure API for FHIR をデプロイしてセットアップしました。 これで、Azure API for FHIR のセットアップが完了したので、パブリック クライアント アプリケーションを登録します。 詳細またはトラブルシューティングについては、[パブリック クライアント アプリの登録](register-public-azure-ad-client-app.md)に関する完全な攻略ガイドで読むことができますが、主な手順については、このチュートリアルの以下の部分で紹介しています。

1. Azure Active Directory に移動します
1. **[アプリの登録]**  -->  **[新しい登録]** の順に選択します
1. アプリケーションの名前を指定します
1. **[パブリック クライアント / ネイティブ (モバイルとデスクトップ)]** を選択し、リダイレクト URI を `https://www.getpostman.com/oauth2/callback` に設定します。

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="[アプリケーションの登録] ペインのスクリーンショット。アプリケーション名とリダイレクト URL の例が表示されています。":::

## <a name="client-application-settings"></a>クライアント アプリケーションの設定

クライアント アプリケーションを登録したら、[概要] ページからアプリケーション (クライアント) ID とテナント ID をコピーします。 この 2 つの値は、後でクライアントにアクセスするときに必要になります。

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="クライアント アプリケーションを設定するペインのスクリーンショット。アプリケーション ID とディレクトリ ID が強調表示されています。":::

### <a name="connect-with-web-app"></a>Web アプリと接続する

Azure API for FHIR と接続する [Web アプリ](tutorial-web-app-write-web-app.md)を作成し終わったら、適切な認証オプションを設定する必要もあります。 

1. 左側のメニューの **[管理]** セクションで、 **[認証]** を選択します。 

1. 新しいプラットフォーム構成を追加するには、 **[Web]** を選択します。

1. このチュートリアルの 4 番目の部分で Web アプリケーションを作成するときの準備として、リダイレクト URI をセットアップします。 これを行うには、リダイレクト URI の一覧に `https://\<WEB-APP-NAME>.azurewebsites.net` を追加します。 [Web アプリを記述する](tutorial-web-app-write-web-app.md)手順で別の名前を選択する場合は、ここに戻って更新する必要があります。

1. **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="アプリの認証設定を行うブレードのスクリーンショット。プラットフォームを追加する手順が強調表示されています。":::

## <a name="add-api-permissions"></a>API アクセス許可を追加する

これで適切な認証をセットアップできたので、API のアクセス許可を設定します。

1. **[API のアクセス許可]** を選択し、 **[アクセス許可の追加]** をクリックします。
1. **[所属する組織で使用している API]** で、Azure Healthcare API を検索します。
1. **[user_impersonation]** を選択し、 **[アクセス許可の追加]** をクリックします。

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="API のアクセス許可を追加するブレードのスクリーンショット。API のアクセス許可を追加する手順が強調表示されています。":::

## <a name="next-steps"></a>次の手順
これで、パブリック クライアント アプリケーションをセットアップできました。 次のチュートリアルでは、Postman を通じてこのアプリケーションをテストし、アクセスできるようにする手順を説明します。

>[!div class="nextstepaction"]
>[Postman でクライアント アプリケーションをテストする](tutorial-web-app-test-postman.md)
