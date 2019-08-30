---
title: Microsoft Authenticator アプリに職場または学校のアカウントを追加する - Azure Active Directory | Microsoft Docs
description: 2 要素認証のために Microsoft Authenticator アプリに職場または学校のアカウントを追加する方法。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3be2ee662a061cdcb6acc58e47eda5feda3b9eee
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880793"
---
# <a name="add-your-work-or-school-account"></a>職場または学校のアカウントを追加する

組織で 2 要素認証を使用している場合は、認証方法の 1 つとして、Microsoft Authenticator アプリを使用するように職場または学校のアカウントを設定できます。

>[!Important]
>アカウントを追加する前に、Microsoft Authenticator アプリをダウンロードしてインストールする必要があります。 まだ完了していない場合は、「[アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」という記事の手順に従ってください。

## <a name="add-your-work-or-school-account"></a>職場または学校のアカウントを追加する

1. お使いのコンピューターで、[[追加のセキュリティ確認]](https://aka.ms/mfasetup) ページに移動します。

    >[!Note]
    >**[追加のセキュリティ確認]** ページが表示されない場合は、管理者がセキュリティ情報 (プレビュー) エクスペリエンスをオンにしている可能性があります。 その場合は、[[認証アプリを使用するようにセキュリティ情報を設定する]](security-info-setup-auth-app.md) セクションの指示に従います。 そうでない場合は、組織のヘルプ デスクに問い合わせる必要があります。 セキュリティ情報の詳細については、「[セキュリティ情報 (プレビュー) の概要](user-help-security-info-overview.md)」を参照してください。

2. **[Authenticator アプリ]** の横のチェック ボックスをオンにし、 **[構成]** を選択します。

    **[モバイル アプリケーションの構成]** ページが表示されます。

    ![QR コードを提供する画面](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[職場または学校アカウント]** を選択します。

    >[!Note]
    >初めて Microsoft Authenticator アプリを設定する場合は、アプリによるカメラへのアクセスを許可するか (iOS)、またはアプリによる画像の撮影とビデオの録画を許可するか (Android) を確認するメッセージが表示されることがあります。 認証アプリがカメラにアクセスして、次の手順で QR コードを撮影できるようにするには、 **[許可]** を選択する必要があります。 カメラを許可しない場合でも、認証アプリを設定できますが、手動でコード情報を追加する必要があります。 コードを手動で追加する方法の詳細については、「[アプリにアカウントを手動で追加する](user-help-auth-app-add-account-manual.md)」を参照してください。

4. お使いのデバイスのカメラを使用して、コンピューターの **[モバイル アプリケーションの構成]** 画面から QR コードをスキャンして、 **[完了]** を選択します。

    >[!Note]
    >カメラで QR コードをキャプチャできない場合は、2 要素検証のために Microsoft Authenticator アプリにご自分のアカウント情報を手動で追加できます。 これを行う方法と詳細については、[手動でのアカウントの追加](user-help-auth-app-add-account-manual.md)に関するページを参照してください。

5. デバイスに表示されたアプリの **[アカウント]** 画面を調べ、ご自分のアカウントが正しいことと 6 桁の確認コードが関連付けられていることを確認します。 セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

    ![[アカウント] 画面](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>次の手順

- アプリにアカウントを追加すると、デバイスで Authenticator アプリを使用してサインインできます。 詳細については、[アプリを使用したサインイン](user-help-auth-app-sign-in.md)に関するページを参照してください。

- iOS を実行しているデバイスの場合、アカウント資格情報と、アカウントの順序などの関連アプリ設定を、クラウドにバックアップすることもできます。 詳細については、[Microsoft Authenticator アプリを使用したバックアップと復旧](user-help-auth-app-backup-recovery.md)に関するページを参照してください。
