---
title: "\"ここからアクセスすることはできません\" というエラーに対する修正 - Azure AD"
description: "\"ここからアクセスすることはできません\" というエラー メッセージを受け取る理由に対して可能性のある修正を探します。"
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88799419"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>"ここからアクセスすることはできません" というエラー メッセージに対して可能性のある修正方法

組織内部の Web アプリやサービスにアクセスするとき、「**ここからアクセスすることはできません**」というエラー メッセージが出力されることがあります。 このメッセージは、デバイスが組織のリソースにアクセスできないようにするポリシーが組織で設定されていることを意味します。 最終的にはヘルプデスクに連絡してこの問題を解決してもらうことになりますが、まず最初に試すことができるいくつかの操作を以下に示します。

## <a name="make-sure-youre-using-a-supported-browser"></a>サポートされているブラウザーを使用しているかどうかを確認する
受け取った「**ここからアクセスすることはできません**」メッセージに、サポートされていないブラウザーから組織のサイトにアクセスしようとしていることが示されている場合、実行中のブラウザーを確認してください。

![ブラウザーのサポートに関連するエラー メッセージ](media/user-help-device-remediation/browser-version.png)

この問題を解決するには、オペレーティング システムに基づいて、サポートされているブラウザーをインストールして実行する必要があります。 Windows 10 を使用している場合、サポートされているブラウザーには、Microsoft Edge、Internet Explorer、および Google Chrome が含まれます。 別のオペレーティング システムを使用している場合は、[サポートされているブラウザー](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)から完全な一覧を確認できます。

## <a name="make-sure-youre-using-a-supported-operating-system"></a>サポートされているオペレーティング システムを使用しているかどうかを確認する
サポートされているオペレーティング システムのバージョンを実行しているかどうかを確認します。これには以下のバージョンが含まれます。

- **Windows クライアント。** Windows 7 以降。

- **Windows Server。** Windows Server 2008 R2 以降。

- **MacOS。** macOS X 以降

- **Android および iOS。** Android および iOS モバイル オペレーティング システムの最新バージョン

この問題を解決するには、サポートされているオペレーティング システムをインストールして実行する必要があります。

## <a name="make-sure-your-device-is-joined-to-your-network"></a>デバイスがネットワークに参加しているかどうかを確認する
受け取った「**ここからアクセスすることはできません**」メッセージに、デバイスが組織のアクセス ポリシーに準拠していないことが示されている場合、デバイスが組織のネットワークに参加していることを確認します。

![ネットワークに参加しているかどうかに関連するエラー メッセージ](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>デバイスがネットワークに参加しているかどうかを確認する
1. 職場または学校アカウントを使用して、Windows にサインインします。 たとえば、「 alain@contoso.com 」のように入力します。

2. 仮想プライベート ネットワーク (VPN) または DirectAccess を使用して組織のネットワークに接続します。

3. 接続後、**Windows ロゴ キーを押しながら L キーを押して** デバイスをロックします。

4. 職場または学校アカウントを使用してデバイスのロックを解除し、問題のあるアプリまたはサービスにもう一度アクセスしてみます。

    「**ここからアクセスすることはできません**」エラー メッセージが再び表示される場合、**[詳細]** リンクを選択し、詳細情報をヘルプデスクに連絡してください。

### <a name="to-join-your-device-to-your-network"></a>デバイスをネットワークに参加させる
デバイスが組織のネットワークに参加していない場合は、次の 2 つのいずれかの操作を行います。

- **職場のデバイスを参加させる。** 制限されている可能性があるリソースにアクセスできるように、職場所有の Windows 10 デバイスを組織のネットワークに参加させます。 詳細情報および手順については、「[職場のデバイスを組織のネットワークに参加させる](user-help-join-device-on-network.md)」を参照してください。

- **個人所有の職場のデバイスを登録する。** 個人デバイス (通常は電話またはタブレット) を組織のネットワークに登録します。 デバイスが登録されたら、そのデバイスは組織の制限されたリソースにアクセスできるようになります。 詳細情報および手順については、「[個人デバイスを組織のネットワークに登録する](user-help-register-device-on-network.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [MyApps ポータルとは](./my-apps-portal-end-user-access.md)

- [パスワードではなく携帯電話でサインインする](user-help-auth-app-sign-in.md)