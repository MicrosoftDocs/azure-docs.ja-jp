---
title: インクルード ファイル
description: インクルード ファイル
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 0ce9575f078058c821ffffe1b9fe45eed5a4ad94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724177"
---
* [[プラットフォームの構成]](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#configure-platform-settings) 設定の手順 4 で適切なプラットフォームを選択したら、ユーザー インターフェイスの右側のサイド パネルで **[リダイレクト URI]** と **[アクセス トークン]** を構成します。

    * **[リダイレクト URI]** は、認証要求で指定されたアドレスと一致する必要があります。

        * ローカル開発環境でホストされているアプリでは、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。 **[パブリック クライアント]** は必ず **[はい]** に設定してください。
        * Azure App Service でホストされているシングルページ アプリでは、 **[Web]** を選択します。

    * **[ログアウト URL]** が適切かどうかを確認します。

    * **[アクセス トークン]** または **[ID トークン]** をオンにすることによって、暗黙的な許可のフローを有効にします。

    [![リダイレクト URI を作成する](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    **[構成]** 、 **[保存]** の順にクリックします。

* お使いの Azure Active Directory アプリを Azure Time Series Insights に関連付けます。 **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[所属する組織で使用している API]** を選択します。

    [![API をお使いの Azure Active Directory アプリに関連付ける](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   検索バーに「`Azure Time Series Insights`」と入力した後、`Azure Time Series Insights` を選択します。

* 次に、お使いのアプリで必要な API のアクセス許可の種類を指定します。 既定では、 **[委任されたアクセス許可]** が強調表示されます。 アクセス許可の種類を選択した後、 **[アクセス許可の追加]** を選択します。

    [![お使いのアプリで必要な API のアクセス許可の種類を指定する](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* アプリケーションから環境の API をそのまま呼び出す場合は、[資格情報を追加します](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#add-credentials)。 資格情報により、アプリケーションはそれ自体として認証され、実行時にユーザーによる操作は必要ありません。