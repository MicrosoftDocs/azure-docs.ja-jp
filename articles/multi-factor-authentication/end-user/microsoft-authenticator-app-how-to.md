---
title: "携帯電話向けの Microsoft Authenticator アプリ | Microsoft Docs"
description: "Azure Authenticatior の最新バージョンにアップグレードする方法について説明します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/25/2016
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6872b216ab2bfd5823b65b1bb70d503c7628ee13
ms.openlocfilehash: 26d9f04f9d3019ab5fcad48ef1e9821a4dd6a724
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリの概要
Microsoft Authenticator アプリは、Azure アカウント (bsimon@contoso.onmicrosoft.com など)、オンプレミス職場アカウント (bsimon@contoso.com など)、または Microsoft アカウント (bsimon@outlook.com など) でセキュリティ レベルを追加で提供します。

アプリは次のいずれかの方法で機能します。

* **通知**。 スマートフォンまたはタブレットに通知をプッシュして、アカウントへの不正アクセスを防止したり、不正なトランザクションを停止させることができます。 通知を確認し、適切である場合は **[認証]**を選択するだけです。 適切でない場合は、 **[拒否]**を選択します。 拒否通知について詳しくは、「Multi-Factor Authentication における [認証を拒否して不正を通報] 機能の使用法」をご覧ください。
* **パスワードと検証コード**。 アプリをソフトウェア トークンとして使用して、OAuth 検証コードを生成できます。 ユーザー名とパスワードを入力したら、アプリから提供されたコードをサインイン画面に入力します。 検証コードにより、2 番目の形式の認証が行われます。

Azure Authenticator アプリは Microsoft Authenticator アプリに置き換えられています。  Azure Authenticator アプリは引き続き機能しますが、新しい Microsoft Authenticator アプリに移行することにした場合は、この記事が役に立ちます。  

## <a name="install-the-app"></a>アプリのインストール
Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

## <a name="add-accounts-to-the-app"></a>アプリへのアカウントの追加
Microsoft Authenticator アプリに追加する各アカウントについて、次のいずれかの手順を実行します。

### <a name="add-a-personal-microsoft-account-to-the-app"></a>個人用の Microsoft アカウントをアプリに追加する

個人用の Microsoft アカウント (Outlook.com、Xbox、Skype などへのサインインに使用するアカウント) の場合、Microsoft Authenticator アプリでアカウントにサインインするだけです。

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>QR コード スキャナーを使用して職場または学校アカウントをアプリに追加する
1. セキュリティ確認の設定画面に移動します。  この画面の表示方法の詳細については、 [セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)に関するページを参照してください。
2. **[Azure Authenticator アプリ]** の横のチェック ボックスをオンにし、**[構成]** を選択します。

    ![セキュリティ確認の設定画面の [構成] ボタン](./media/authenticator-app-how-to/azureauthe.png)

    QR コードを記載した画面が表示されます。

    ![QR コードを提供する画面](./media/authenticator-app-how-to/barcode2.png)
3. Microsoft Authenticator アプリを開きます。 **[アカウント]** 画面で、**+** を選択し、職場または学校のアカウントの追加を指定します。
4. カメラを使って QR コードをスキャンし、 **[完了]** を選択して QR コードの画面を閉じます。

    カメラが適切に動作しない場合は、[QR コードと URL を手動で入力](#add-an-account-to-the-app-manually)できます。

5. アプリにアカウント名とその下に&6; 桁のコードが表示されたら完了です。 

    ![[アカウント] 画面](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>アプリにアカウントを手動で追加する
1. セキュリティ確認の設定画面に移動します。  この画面の表示方法の詳細については、 [セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。
2. **[構成]**をクリックします。

    ![セキュリティ確認の設定画面の [構成] ボタン](./media/authenticator-app-how-to/azureauthe.png)

    QR コードを記載した画面が表示されます。  コードと URL を書き留めます。

    ![QR コードと URL を提供する画面](./media/authenticator-app-how-to/barcode2.png)
3. Microsoft Authenticator アプリを開きます。 **[アカウント]** 画面で、**+** を選択し、職場または学校のアカウントの追加を指定します。

4. スキャナーで、 **[enter code manually (コードを手動で入力)]**を選択します。

    ![QR コードをスキャンする画面](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. アプリの適切なボックスにコードと URL を入力し、**[完了]** を選択します。

    ![コードと URL を入力する画面](./media/authenticator-app-how-to/manual.png)

6. アプリにアカウント名とその下に&6; 桁のコードが表示されたら完了です。

    ![[アカウント] 画面](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-touch-id"></a>Touch ID を使用してアカウントをアプリに追加する
iOS の Microsoft Authenticator アプリでは、Touch ID がサポートされています。  Azure Multi-Factor Authentication により、組織はデバイスに PIN を要求することができます。 Touch ID では、iOS ユーザーは PIN を入力する必要がありません。 代わりに、指紋をスキャンして、 **[承認]**を選択します。

Microsoft Authenticator による Touch ID の設定はシンプルです。 PIN を使用して通常の検証チャレンジを完了するだけです。 デバイスで Touch ID がサポートされている場合は、Microsoft Authenticator によりそのアカウントに Touch ID が自動的に設定されます。

![Touch ID セットアップの検証](./media/authenticator-app-how-to/touchid1.png)

それ以降、サインインの確認が必要になった場合は、受信したプッシュ通知を選択し、PIN を入力する代わりに指紋をスキャンします。

![プッシュ通知](./media/authenticator-app-how-to/touchid2.png)


