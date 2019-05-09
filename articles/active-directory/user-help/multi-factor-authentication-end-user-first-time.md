---
title: 2 段階認証の設定 - Azure Active Directory |Microsoft Docs
description: 会社が Azure Multi-factor Authentication を構成している場合は、2 段階認証のサインアップを求められます。 設定方法について説明します。
services: active-directory
keywords: azure ディレクトリの使用方法, クラウドの active directory, active directory のチュートリアル
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0749e1acba9f13f9e0e95dfc66f9cf818c752cff
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165622"
---
# <a name="set-up-my-account-for-two-step-verification"></a>アカウントへの 2 段階認証の設定
2 段階認証とは、他のユーザーの侵入を困難にすることでアカウントの保護に役立つ追加のセキュリティ手順です。 あなたがこの記事を読んでいるということは、おそらく会社や学校の管理者から Multi-Factor Authentication に関するメールを受け取ったと考えられます。 または、サインインしようとして、追加のセキュリティ確認を設定するように求めるメッセージが表示されたのかもしれません。 その場合は、**自動登録プロセスが完了するまでサインインできません**。

この記事では、**会社または学校のアカウント**の設定について説明します。 2 段階認証を個人の Microsoft アカウントで有効にする場合は、「[2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)」をご覧ください。

## <a name="set-up-your-account"></a>アカウントの設定

社内のサポートから 2 段階認証の使用を開始するように要求される場合、"**お客様の管理者が、このアカウントに追加のセキュリティ検査を設定することを必須としています**" という画面が表示されます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time/first.png)

開始するには、**[今すぐセットアップ]** を選択します。

サインインした際にこのような画面が表示されない場合は、「[2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)」の手順に従って、認証オプションを管理できる設定ページを探してください。

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>サインインの認証方法の決定

登録プロセスで最初の質問は、連絡方法です。 テーブルのオプションを参照し、リンクから各メソッドのセットアップの手順に進みます。

| 連絡方法 | 説明 |
| --- | --- |
| [モバイル アプリ](#use-a-mobile-app-as-the-contact-method) |- **確認のための通知を受け取る**。 このオプションは、ユーザーのスマート フォンまたはタブレット上の認証アプリに通知をプッシュします。 通知を確認し、適切である場合は、アプリで **[認証]** を選択するだけです。 会社または学校の場合は、認証する前に PIN を入力する必要があります。<br>- **Use verification code (確認コードを使用)**。 このモードでは、認証アプリは 30 秒ごとに更新される確認コードを生成します。 サインイン インターフェイスに最新の確認コードを入力します。<br>Microsoft Authenticator アプリは、[Android](https://go.microsoft.com/fwlink/?linkid=866594) および [iOS](https://go.microsoft.com/fwlink/?linkid=866594) で利用できます。|
| [携帯電話呼び出しまたはテキスト](#use-your-mobile-phone-as-the-contact-method) |- **音声通話**はユーザーが設定した電話番号に自動音声通話を行います。 呼び出しに応答し、電話のキーパッドの # を押して認証を行います。<br>- **テキスト メッセージ**は確認コードを含むテキスト メッセージを送信します。 テキスト内の指示に従って、テキスト メッセージに返信するか、指定された確認コードをサインイン インターフェイスに入力します。 |
| [会社電話呼び出し](#use-your-office-phone-as-the-contact-method) |ユーザーが設定した電話番号に自動音声通話を行います。 呼び出しに応答し、電話のキーパッドの # を押して認証を行います。 |

## <a name="use-a-mobile-app-as-the-contact-method"></a>連絡方法としてのモバイル アプリの使用
この方法を使用する場合は、携帯電話またはタブレットに認証アプリをインストールする必要があります。 この記事で説明する手順は、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) と [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) で利用できる Microsoft Authenticator アプリに基づいています。

>[!NOTE]
>Microsoft Authenticator アプリを使用する必要はありません。 既に別の認証子アプリを使用している場合は、それを引き続き使用できます。

1. ドロップダウン リストから **[モバイル アプリ]** を選択します。
2. **[確認のため通知を受け取る]** または **[Use verification code (確認コードを使用)]** のいずれかを選択し、**[セットアップ]** を選択します。

   ![[追加のセキュリティ確認] 画面](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. 携帯電話またはタブレットでアプリを開き、**[+]** を選択してアカウントを追加します。 (Android デバイスの場合は、3 つのドットを選択して、**[アカウントの追加]** を選択します)。
4. [職場または学校アカウント] をタップします。 携帯電話の QR コード スキャナーを開きます。 カメラが正常に動作しない場合は、会社情報を手動で入力することもできます。 詳細については、「 [手動でのアカウントの追加](#add-an-account-manually)」を参照してください。  
5. モバイル アプリを構成するために、画面に表示された QR コード画像をスキャンします。  **[完了]** を選択して、QR コードの画面を閉じます。  

   ![QR コード画面](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. 電話でアクティブ化が完了したら、 **[連絡してください]** を選択します。  これによって、通知コードまたは認証コードが電話に送信されます。 **[認証]** を選択します。  
7. 会社によっては、検証時に暗証番号 (PIN) の入力を求められる場合があります。

   ![PIN を入力するボックス](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. PIN の入力が完了したら、 **[閉じる]** を選択します。 この時点で、認証が完了します。
9. モバイル アプリにアクセスできなくなった場合に備えて、携帯電話番号を入力しておくことをお勧めします。 ドロップダウン リストからお住まいの国を指定し、国名の横のボックスに携帯電話番号を入力します。 **[次へ]** を選択します。
10. この時点で、Outlook 2010 以降や Apple デバイスのネイティブのメール アプリなどの非ブラウザー アプリのアプリ パスワードの設定が求められます。 これは、一部のアプリで 2 段階認証がサポートされていないためです。 これらのアプリを使用しない場合は、**[完了]** をクリックして残りの手順をスキップします。
11. これらのアプリを使用している場合は、指定されたアプリ パスワードをコピーし、通常のパスワードの代わりにアプリケーションに貼り付けます。 複数のアプリで同じアプリ パスワードを使用できます。 詳細については、「アプリ パスワードに関するヘルプ」をご覧ください。
12. **[Done]** をクリックします。

### <a name="add-an-account-manually"></a>手動でのアカウントの追加
手動でアカウントをモバイル アプリに追加する場合は、QR リーダーを使用する代わりに、次の手順に従ってください。

1. **[Enter account manually (アカウントの手動入力)]** ボタンを選択します。  
2. バーコードが表示されているページに記載されたコードと URL を入力します。 この情報は、モバイル アプリの**コード**と **URL** ボックスに入力されます。

    ![セットアップ](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. アクティブ化が完了したら、 **[連絡してください]** を選択します。 これによって、通知コードまたは認証コードが電話に送信されます。 **[認証]** を選択します。

## <a name="use-your-mobile-phone-as-the-contact-method"></a>連絡方法としての携帯電話の使用
1. ドロップダウン リストから **[認証用電話]** を選択します。  

    ![セットアップ](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. ドロップダウン リストからお住まいの国を選択し、携帯電話番号を入力します。
3. 携帯電話で使用する方法 (テキストまたは通話) を選択します。
4. 電話番号を確認して **[連絡してください]** を選択します。 選択したモードによって、テキストが送信されるか、呼び出されます。 画面に表示される指示に従って、**[確認]** を選択します。
5. この時点で、Outlook 2010 以降や Apple デバイスのネイティブのメール アプリなどの非ブラウザー アプリのアプリ パスワードの設定が求められます。 これは、一部のアプリで 2 段階認証がサポートされていないためです。 これらのアプリを使用しない場合は、**[完了]** をクリックして残りの手順をスキップします。
6. これらのアプリを使用している場合は、指定されたアプリ パスワードをコピーし、通常のパスワードの代わりにアプリケーションに貼り付けます。 複数のアプリで同じアプリ パスワードを使用できます。 詳細については、「アプリ パスワードに関するヘルプ」をご覧ください。
7. **[Done]** をクリックします。

## <a name="use-your-office-phone-as-the-contact-method"></a>連絡方法としての会社電話の使用
1. ドロップダウン リストから **[会社電話]** を選択します。  

    ![セットアップ](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. 電話番号ボックスに会社の連絡先情報が自動的に入力されます。 番号が間違っているか、見つからない場合は、変更するように管理者に依頼してください。
3. 電話番号を確認して **[連絡してください]** を選択すると、その番号が呼び出されます。 画面に表示される指示に従って、**[確認]** を選択します。
4. この時点で、Outlook 2010 以降や Apple デバイスのネイティブのメール アプリなどの非ブラウザー アプリのアプリ パスワードの設定が求められます。 これは、一部のアプリで 2 段階認証がサポートされていないためです。 これらのアプリを使用しない場合は、**[完了]** をクリックして残りの手順をスキップします。
5. これらのアプリを使用している場合は、指定されたアプリ パスワードをコピーし、通常のパスワードの代わりにアプリケーションに貼り付けます。 複数のアプリで同じアプリ パスワードを使用できます。 詳細については、[アプリ パスワード](multi-factor-authentication-end-user-app-passwords.md)に関するページをご覧ください。
6. **[Done]** をクリックします。

## <a name="next-steps"></a>次の手順
* 推奨されるオプションを変更し、[2 段階認証設定を管理します](multi-factor-authentication-end-user-manage-settings.md)
* 2 段階認証をサポートしていないネイティブ デバイス アプリに[アプリ パスワード](multi-factor-authentication-end-user-app-passwords.md)を設定します。
* 携帯電話サービスを利用していない場合でも、迅速で安全な認証のために [Microsoft Authenticator アプリ](user-help-auth-app-download-install.md)をご確認ください。
