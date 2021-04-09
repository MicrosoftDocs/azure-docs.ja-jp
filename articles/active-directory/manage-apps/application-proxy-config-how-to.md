---
title: アプリケーション プロキシ アプリケーションを構成する方法 | Microsoft Docs
description: いくつかの簡単な手順でアプリケーション プロキシ アプリケーションを構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f1488986dc38bba89a5b9a109a444b5f0d81f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255205"
---
# <a name="how-to-configure-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションを構成する方法

この記事では、Azure AD 内にアプリケーション プロキシ アプリケーションを構成して、オンプレミスのアプリケーションをクラウドに公開する方法を説明しています。

## <a name="recommended-documents"></a>推奨されるドキュメント

管理ポータルにおけるアプリケーション プロキシ アプリケーションの初期構成と作成については、「[Azure AD アプリケーション プロキシを使用したアプリケーションの発行](application-proxy-add-on-premises-application.md)」の手順に従ってください。

コネクタの構成の詳細については、[Azure Portal でアプリケーション プロキシを有効にする方法](application-proxy-add-on-premises-application.md)に関するページを参照してください。

証明書のアップロードとカスタム ドメインの使用については、「[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)」を参照してください。

## <a name="create-the-applicationsetting-the-urls"></a>アプリケーションの作成/URL の設定

「[Azure AD アプリケーション プロキシを使用したアプリケーションの発行](application-proxy-add-on-premises-application.md)」の手順に従ってアプリケーションを作成しているときにエラーが発生した場合、アプリケーションの修復方法に関する情報や推奨事項については、そのエラーの詳細を見て確認してください。 ほとんどのエラー メッセージには、推奨される解決方法が記載されています。 一般的なエラーを避けるために、以下の点を確認してください。

- アプリケーション プロキシ アプリケーションする権限を持った管理者であること。
- 内部 URL が一意であること。
- 外部 URL が一意であること。
- URL が http または https から始まり、"/" で終わっていること。
- URL がドメイン名であって IP アドレスではないこと。

アプリケーションを作成する際、エラー メッセージは右上隅に表示されます。 通知アイコンを選択してエラー メッセージを表示することもできます。

![Azure portal の通知プロンプトの場所を示します](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>コネクタ/コネクタ グループの構成

コネクタやコネクタ グループについての警告が表示されてアプリケーションを構成できない場合、コネクタの詳しいダウンロード方法については、アプリケーション プロキシを有効にする手順を参照してください。 コネクタの詳細については、[コネクタのドキュメント](application-proxy-connectors.md)を参照してください。

コネクタがアクティブになっていない場合、コネクタがサービスに到達できていないことを意味します。 多くの場合、必要なポートの一部が開放されていないことが原因で発生します。 必要なポートの一覧を表示するには、アプリケーション プロキシの有効化に関するドキュメントの前提条件セクションを参照してください。

## <a name="upload-certificates-for-custom-domains"></a>カスタム ドメイン用の証明書のアップロード

カスタム ドメインを使用すると、外部 URL のドメインを指定することができます。 カスタム ドメインを使用するには、そのドメインの証明書をアップロードする必要があります。 カスタム ドメインと証明書の使用については、「[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)」を参照してください。

証明書のアップロード中に問題が発生した場合は、関連するエラー メッセージをポータルで探し、証明書の問題についての詳しい情報がないかどうかを調べてください。 証明書に関してよく発生する問題の例を次に示します。

- 証明書の有効期限切れ
- 証明書が自己署名されている
- 証明書の秘密キーがない

証明書をアップロードする際、エラー メッセージは右上隅に表示されます。 通知アイコンを選択してエラー メッセージを表示することもできます。

## <a name="next-steps"></a>次のステップ

[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md)
