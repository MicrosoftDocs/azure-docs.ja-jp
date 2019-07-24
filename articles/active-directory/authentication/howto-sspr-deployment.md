---
title: セルフサービスによるパスワードのリセットのデプロイ ガイド - Azure Active Directory
description: Azure AD のセルフ サービスによるパスワードのリセットを正常に展開するためのヒント
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb9333845a78204ea1c6e56e17b3b6f9e5513c9c
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370687"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>セルフサービスによるパスワードのリセットを適切にロールアウトする方法

Azure Active Directory (Azure AD) のセルフサービスによるパスワード リセット (SSPR) 機能が円滑にロールアウトされるように、ほとんどのお客様は次の手順を実行します。

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. 組織の一部でパイロット ロールアウトを完了します。
   * パイロットの方法については、「[チュートリアル: Azure AD のセルフサービスによるパスワードのリセットのパイロット展開を完了する](tutorial-sspr-pilot.md)」を参照してください。
1. ヘルプデスクを教育します。
   * ユーザーをどのように支援しますか。
   * SSPR の使用をユーザーに強制して、ヘルプデスクがユーザーを支援しないようにしますか。
   * 登録とリセットのための URL を提供しましたか。
      * 登録: https://aka.ms/ssprsetup
      * リセット: https://aka.ms/sspr
1. ユーザーを教育します。
   * このドキュメントの以下のセクションでは、通信のサンプル、パスワード ポータル、登録の強制、認証データの設定について説明します。
   * Azure Active Directory の製品グループは、組織がビジネス ケースを作成し、セルフサービスのパスワード リセットのデプロイを計画できるように、このサイトのドキュメントと並行して使用できる[ステップバイステップのデプロイ プラン](https://aka.ms/SSPRDeploymentPlan)を作成しました。
1. セルフサービスによるパスワードのリセットを組織全体で有効にします。
   * 準備ができたら、**[セルフ サービスによるパスワードのリセットが有効]** スイッチを **[すべて]** に設定することで、すべてのユーザーのパスワードのリセットを有効にします。

## <a name="sample-communication"></a>通信のサンプル

多くのお客様は、ユーザーに SSPR の使用を促す最も簡単な方法は、電子メール キャンペーンで簡単な使用手順を配布することです。 [ロールアウトに役立つテンプレートとして使用できる、簡単なメールやその他の資料を作成しました](https://www.microsoft.com/download/details.aspx?id=56768)。

* **準備中**: ユーザーに必要な作業を通知するために、ロールアウトの数週間前または数日前に使用する電子メール テンプレートです。
* **利用開始**: ユーザーに認証データの登録と確認を促すために、プログラム開始当日に使用する電子メール テンプレートです。 ユーザーは、今登録すると、SSPR が必要になったときに利用できます。
* **サインアップ リマインダー**: ユーザーに認証データの登録と確認を行うよう念を押すために、デプロイの数日から数週間後に使用する電子メール テンプレートです。
* **SSPR ポスター**: カスタマイズして、ロールアウト前後の数日および数週間に組織の周辺で掲示できるポスターです。
* **SSPR テーブル テント**: ユーザーによる登録の完了を促すためにランチ ルーム、会議室、またはデスクに配置できるテーブル カードです。
* **SSPR ステッカー**: カスタマイズして印刷できるステッカーのテンプレートです。SSPR にアクセスする方法を忘れないよう、ノート PC、モニター、キーボード、または携帯電話に貼ることができます。

![ユーザーへのロールアウト用 SSPR 電子メールのサンプル][Email]

## <a name="create-your-own-password-portal"></a>独自のパスワードのポータルを作成する

多くのお客様は、Web ページをホストし、ルート DNS エントリ (https://passwords.contoso.com など) を作成することを選択しています。 このページには、以下の情報へのリンクを設定します。

* [Azure AD のパスワード リセットのポータル - https://aka.ms/sspr](https://aka.ms/sspr)
* [Azure AD のパスワード リセットの登録ポータル - https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Azure AD のパスワード変更ポータル - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* その他の組織固有の情報

ユーザーがこのサービスの使用を必要とするときにアクセスできる、覚えやすいブランド化された URL を、送信する任意の電子メールまたは広告に含めることができます。 組織のニーズに応じて使用およびカスタマイズできる[パスワードのリセットのサンプル ページ](https://github.com/ajamess/password-reset-page)をご用意しました。

## <a name="use-enforced-registration"></a>強制登録を使用する

ユーザーにパスワードのリセットを登録してほしい場合は、ユーザーが Azure AD を通じてサインインしたときに登録を要求できます。 このオプションを有効にするには、ディレクトリの **[パスワードのリセット]** ウィンドウで **[登録]** タブの **[サインイン時にユーザーに登録を求めますか]** オプションを有効にします。

管理者は、特定の期間の経過後、ユーザーに再登録を要求することができます。 **[ユーザーが認証情報を再確認するように求められるまでの日数]** オプションを 0 ～ 730 日に設定できます。

このオプションを有効にすると、ユーザーがサインインしたときに、管理者がユーザーに認証情報を確認するよう求めていることを通知するメッセージが表示されます。

## <a name="populate-authentication-data"></a>認証データの設定

[ユーザーのために認証データを設定する](howto-sspr-authenticationdata.md)ことを検討する必要があります。 そうすれば、ユーザーは、パスワードのリセットに登録しなくても SSPR を使用できます。 ユーザーは、定義されているパスワードのリセット ポリシーに準拠した認証データを指定していれば、自分のパスワードをリセットできます。

## <a name="disable-self-service-password-reset"></a>セルフサービスのパスワード リセットの無効化

組織がセルフサービスによるパスワード リセットを無効にしたい場合、単純なプロセスで実行できます。 Azure AD テナントを開いて **[パスワードのリセット]** > **[プロパティ]** の順に移動し、**[セルフ サービスによるパスワードのリセットが有効]** の **[なし]** を選択します。 ユーザーの登録されている認証方法は引き続き維持されて、将来も使われます。

## <a name="next-steps"></a>次の手順

* [パスワードのリセットまたは変更](../user-help/active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](../user-help/active-directory-passwords-reset-register.md)
* [Azure Multi-Factor Authentication と Azure AD のセルフサービスによるパスワードのリセットで集中型登録を有効にする](concept-registration-mfa-sspr-converged.md)
* [ライセンスに関する質問](concept-sspr-licensing.md)
* [SSPR が使用するデータと、ユーザー用に事前設定が必要なデータ](howto-sspr-authenticationdata.md)
* [SSPR のポリシー オプション](concept-sspr-policy.md)
* [パスワード ライトバックの概要とその必要性](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [不具合が発生していると思われるSSPR のトラブルシューティング方法](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "こうしたメール テンプレートを組織の要件に合わせてカスタマイズする"
