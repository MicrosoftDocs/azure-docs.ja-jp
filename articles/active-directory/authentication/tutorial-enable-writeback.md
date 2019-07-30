---
title: Azure AD のパスワード ライトバックの有効化
description: このチュートリアルでは、パスワード ライトバックを Azure AD Connect の一部として有効にして、クラウドで開始されたパスワード変更をオンプレミス AD に反映することができます。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1efb67df6c31a3b03fdc45fffc0564fb09e39faf
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853032"
---
# <a name="tutorial-enabling-password-writeback"></a>チュートリアル:パスワード ライトバックを有効にする

このチュートリアルでは、ハイブリッド環境でパスワード ライトバックを有効にします。 パスワード ライトバックは、Azure Active Directory (Azure AD) のパスワードの変更を、オンプレミスの Active Directory Domain Services (AD DS) 環境に同期するために使用されます。 パスワード ライトバックは Azure AD Connect の一部として有効になり、パスワード変更を Azure AD から既存のオンプレミス ディレクトリに送信するための安全なメカニズムを提供します。 パスワード ライトバックの内部動作の詳細については、[パスワード ライトバックの概要](concept-sspr-writeback.md)に関する記事を参照してください。

> [!div class="checklist"]
> * Azure AD Connect でパスワード ライトバック オプションを有効にする
> * セルフサービスによるパスワードのリセット (SSPR) でパスワード ライトバック オプションを有効にする

## <a name="prerequisites"></a>前提条件

* 少なくとも試用版ライセンスが割り当てられている、動作している Azure AD テナントへのアクセス。
* Azure AD テナントで全体管理者特権を持つアカウント。
* 現在のバージョンの [Azure AD Connect](../hybrid/how-to-connect-install-express.md) を実行するように構成されている既存のサーバー。
* 前のセルフサービスによるパスワードのリセット (SSPR) のチュートリアルが完了していること。

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Azure AD Connect でパスワード ライトバック オプションを有効にする

パスワード ライトバックを有効にするには、まず、Azure AD Connect をインストールしたサーバーの機能を有効にする必要があります。

1. パスワード ライトバックを構成し、有効化するには、Azure AD Connect サーバーにサインインして **Azure AD Connect** 構成ウィザードを開始します。
2. **[ようこそ]** ページで **[構成]** を選びます。
3. **[追加のタスク]** ページで **[同期オプションのカスタマイズ]** を選んで、 **[次へ]** を選びます。
4. **[Azure AD に接続]** ページで全体管理者の資格情報を入力し、 **[次へ]** を選びます。
5. **[Connect ディレクトリ]** ページおよび **[ドメイン/OU のフィルタリング]** ページで、 **[次へ]** を選びます。
6. **[オプション機能]** ページで **[パスワード ライトバック]** の横にあるチェック ボックスをオンにし、 **[次へ]** を選びます。
7. **[構成の準備完了]** ページで **[構成]** を選び、処理が完了するまで待ちます。
8. 構成の完了が表示されたら、 **[終了]** を選びます。

## <a name="enable-password-writeback-option-in-sspr"></a>SSPR でパスワード ライトバック オプションを有効にする

Azure AD Connect でパスワード ライトバック機能を有効にすることは、作業の半分に過ぎません。 SSPR がパスワード ライトバックを使用できるようにすると、ループが完了し、パスワードを変更またはリセットしたユーザーが、そのパスワードをオンプレミスにも設定されるようにすることができます。

1. 全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** に移動し、 **[パスワード リセット]** をクリックしてから、 **[オンプレミスの統合]** を選択します。
3. **[オンプレミス ディレクトリにパスワードをライトバックしますか?]** オプションを **[はい]** に設定します。
4. **[パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可する]** オプションを **[はい]** に設定します。
5. **[保存]**

## <a name="next-steps"></a>次の手順

このチュートリアルでは、セルフサービスによるパスワードのリセットのパスワード ライトバックを有効にしました。 パイロットにソリューションをロールアウトする前に、Azure portal ウィンドウを開いたままにして、次のチュートリアルに進み、セルフサービスによるパスワードのリセットに関連する追加の設定を構成してください。

> [!div class="nextstepaction"]
> [サインイン時にリスクを評価する](tutorial-risk-based-sspr-mfa.md)
