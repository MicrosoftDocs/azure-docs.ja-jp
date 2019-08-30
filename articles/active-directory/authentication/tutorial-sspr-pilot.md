---
title: Azure AD SSPR パイロットを有効にする
description: このチュートリアルでは、パイロット グループのユーザーに対して Azure AD のセルフサービスによるパスワードのリセットを有効にします。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 685c928a20e9bfb47365b5ffa769f1a223d2c563
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69561507"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>チュートリアル:Azure AD のセルフ サービスによるパスワードのリセットのパイロット展開を完了する

このチュートリアルでは、Azure AD のセルフサービスによるパスワードのリセット (SSPR) を組織にパイロット展開できるようにし、管理者以外のアカウントを使用してテストします。

セルフサービスによるパスワードのリセットをテストするときは、必ず管理者以外のアカウントを使用することが重要です。 管理者アカウントのパスワード リセット ポリシーは、Microsoft が管理しており、より強力な認証方法を使用することが必要とされています。 このポリシーでは、セキュリティの質問と回答を使用することができず、2 つの方法を使用してリセットを行う必要があります。

> [!div class="checklist"]
> * セルフ サービスのパスワード リセットを有効にする
> * SSPR をユーザーとしてテストする

## <a name="prerequisites"></a>前提条件

* グローバル管理者アカウント

## <a name="enable-self-service-password-reset"></a>セルフ サービスのパスワード リセットを有効にする

1. 全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を参照し、 **[パスワード リセット]** を選択します。
1. 組織内のユーザーのサブセットに対してセルフサービス パスワードを有効にし、パイロット グループから始めます。
   * **[プロパティ]** ページのオプション **[セルフ サービスによるパスワードのリセットが有効]** の下で **[Selected]\(選択\)** を選び、パイロット グループを選択します。
      * 選択した特定の Azure AD グループのメンバーのみが SSPR 機能を使用できます。 この機能を概念実証のためにデプロイする場合、ユーザー グループを定義してこの設定を使用することをお勧めします。 ここではキュリティ グループの入れ子がサポートされています。
      * 選択したグループ内のユーザーが適切にライセンスされていることを確認します。
   * **[保存]**
1. **[認証方法]** ページで、次の操作を行います。
   * **[リセットのために必要な方法の数]** を **1** に設定します。
   * 組織が**ユーザーに許可する方法**を選択します。 このチュートリアルでは、 **[メール]** 、 **[携帯電話]** 、 **[会社電話]** 、 **[モバイル アプリ通知]** 、および **[モバイル アプリ コード]** の各チェック ボックスをオンにします。
   * **[保存]**
1. **[登録]** ページで、次の操作を行います。
   * **[Require users to register when signing in]\(サインイン時にユーザーに登録を要求する\)** に対して **[はい]** を選択します。
   * **[Number of days before users are asked to reconfirm their authentication information]\(ユーザーが認証情報を再確認するように求められるまでの日数\)** を **180** に設定します。
   * **[保存]**
1. **[通知]** ページで、次の操作を行います。
   * **[Notify users on password resets]\(パスワードのリセットについてユーザーに通知する\)** オプションを **[はい]** に設定します。
   * **[Notify all admins when other admins reset their password]\(他の管理者が自分のパスワードをリセットしたときに、すべての管理者に通知する\)** を **[はい]** に設定します。
1. **[カスタマイズ]** ページで、次の操作を行います。
   * Microsoft では、 **[ヘルプデスク リンクのカスタマイズ]** を **[はい]** に設定し、ユーザーが組織から詳細な支援を受けるためのメール アドレスまたは Web ページ URL を **[Custom helpdesk email or URL]\(カスタム ヘルプデスクのメールまたは URL\)** フィールドに指定することをお勧めします。
   * このチュートリアルでは、 **[ヘルプデスク リンクのカスタマイズ]** は **[いいえ]** のままにします。

以上で、パイロット グループ内のクラウド ユーザーに対して、セルフサービスによるパスワードのリセットが構成されました。

## <a name="test-sspr-as-a-user"></a>SSPR をユーザーとしてテストする

パイロット グループのメンバーである管理者以外のテスト ユーザーを使用して、セルフサービスによるパスワードのリセットをテストします。 **管理者の役割が割り当てられているアカウントを使用する場合、認証の方法と数は、選択した内容と異なる場合があります。これは、Microsoft が管理者ポリシーを管理しているためです。**

1. 新しい InPrivate または incognito モードのブラウザー ウィンドウを開きます。
1. テスト ユーザーを使用して、セルフサービスによるパスワードのリセットを登録ポータル ([https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)) で登録します。
1. 同じテスト ユーザーを使用して、セルフサービスによるパスワードのリセット用のポータル ([https://aka.ms/sspr](https://aka.ms/sspr)) を参照し、前のステップで指定した情報を使用して、パスワードのリセットを試みます。
1. パスワードを正常にリセットできるはずです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで構成した機能をもう使用しないと判断した場合は、次の変更を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を参照し、 **[パスワード リセット]** を選択します。
1. **[プロパティ]** ページのオプション **[セルフ サービスによるパスワードのリセットが有効]** の下で、 **[なし]** を選択します。
1. **[保存]**

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure AD のセルフ サービスによるパスワードのリセットを有効にしました。 次のチュートリアルでは、オンプレミスの Active Directory Domain Services インフラストラクチャをセルフ サービスによるパスワードのリセット エクスペリエンスと統合する方法について説明します。

> [!div class="nextstepaction"]
> [SSPR オンプレミス書き戻し統合を有効にする](tutorial-enable-writeback.md)
