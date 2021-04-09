---
title: 中国の Android での Microsoft Authenticator の利用と制限事項 | Microsoft Docs
description: Microsoft Authenticator アプリを設定および使用する方法について説明します - 中国での使用
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/27/2021
ms.author: curtand
ms.openlocfilehash: d499417414e560bdaa3fcd0a12255946aca413d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054841"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>中国のパブリック クラウドでの Android の Microsoft Authenticator

Android 用の Microsoft Authenticator アプリは、中国でダウンロードが可能です。 中国では Google Play ストアが利用できないため、アプリを他の中国のアプリ マーケットプレースからダウンロードする必要があります。 Android 用の Microsoft Authenticator アプリは、現在、中国の次のストアでご利用いただけます。

- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

アプリの最新のビルドは Google Play ストアにありますが、他のすべてのアプリ ストアのアプリをできるだけ早く更新しています。 アプリ ストアにはカスタム Android アプリケーション パッケージ (APK) がデプロイされていないため、アプリは次のいずれかの場所からシームレスに更新できます。

- ダウンロード元のストア
- Google Play ストア (ユーザーが領域を越える場合)

## <a name="limitations"></a>制限事項

Android 用の Microsoft Authenticator アプリでは、Google の Firebase Cloud Messaging システムと Google Play 開発者サービスを使用してプッシュ通知を受信します。 中国ではどちらのサービスも利用できないため、アプリの機能にはいくつかの制限があります。

- プッシュ通知を使用して Authenticator アプリを多要素認証 (MFA) 方式として登録することはできません。

- [電話によるサインイン](../authentication/howto-authentication-sms-signin.md)を設定できません。 これは、プッシュ通知を使用してアプリを MFA 方式として設定する必要がありますが、現在は利用できません。

ユーザーが以前にアプリを使用して電話によるサインインまたは多要素認証を設定できている場合は、アプリで通知要求の手動チェックを実行し、それを本人確認に使用できます。

## <a name="multi-factor-authentication-workaround"></a>多要素認証の回避策

ユーザーは、多要素認証でプッシュ通知を使用する代わりに、デバイスで身元を確認するために MFA に使用できる[確認コードを受け取るように Authenticator アプリを設定する](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes)ことができます。 これらの確認コードは 30 秒間有効であり、それらを使用するには、管理者は、時間ベースのワンタイム パスワード (TOTP) 確認コードを使用してテナントが確認を実行できるようにする必要があります。

## <a name="availability"></a>可用性

Microsoft Authenticator の機能 | 中国での利用可能性
------------------------------- | ---------------------
プッシュ通知を使用した MFA の登録 | いいえ
既存の MFA アカウントでのプッシュ通知を使用した ID 確認 | いいえ
既存の MFA アカウントでの通知の手動チェック | はい
TOTP/確認コードのみを使用した MFA の登録/認証 | はい
電話によるサインインの登録 | いいえ
プッシュ通知を使用した既存の電話によるサインイン | いいえ
認証要求に対して手動チェックを実行することによる、既存の電話によるサインインの確認 | はい
個人用 Microsoft アカウント用の認証アプリのサポート | いいえ

## <a name="next-steps"></a>次のステップ

- [Microsoft Authenticator アプリをダウンロードしてインストールします](user-help-auth-app-download-install.md)
