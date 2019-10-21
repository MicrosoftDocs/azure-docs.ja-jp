---
title: Objective-C での MSAL に関する問題のトラブルシューティング | Microsoft ID プラットフォーム
description: MSAL.Objective-C ライブラリでの SSL 証明書の使用に関するさまざまな問題の処理について学習します。
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76892686beec8ea18d56166519353fb5a2495124
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269341"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>方法:iOS と macOS 用の MSAL に関する SSL の問題のトラブルシューティング

この記事では、[iOS と macOS 用の Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) を使用しているときに発生する可能性のある問題のトラブルシューティングに役立つ情報を提供します。

## <a name="network-issues"></a>ネットワークの問題

**エラー -1200**: "SSL エラーが発生し、サーバーへのセキュリティで保護された接続を確立できません。"

このエラーは、接続がセキュリティで保護されていないことを意味します。 これは、証明書が無効なときに発生します。 SSL チェックに失敗しているサーバーなど、詳細については、エラー オブジェクトの `userInfo` ディクショナリにある `NSURLErrorFailingURLErrorKey` を参照してください。

このエラーは、Apple のネットワーク ライブラリからのものです。 NSURL エラー コードの完全なリストは、macOS および iOS SDK の NSURLError.h にあります。 このエラーの詳細については、「[URL 読み込みシステムのエラー コード](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)」を参照してください。

## <a name="certificate-issues"></a>証明書の問題

無効な証明書を提供する URL で認証フローの一部として使用する予定のサーバーに接続する場合は、問題を診断するために、[Qualys SSL Labs アナライザー](https://www.ssllabs.com/ssltest/analyze.html)などの SSL 検証サービスを使用して URL をテストするから始めます。 さまざまなシナリオとブラウザーに対してサーバーをテストし、多くの既知の脆弱性を確認します。

既定では、Apple の新しい [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 機能で、SSL 証明書を使用するアプリにより厳格なセキュリティ ポリシーを適用します。 一部のオペレーティング システムと Web ブラウザーでは、これらのポリシーの一部が既定で適用されるようになりました。 セキュリティ上の理由から、ATS を無効にしないことをお勧めします。

SHA-1 ハッシュを使用する証明書には、既知の脆弱性があります。 最新の Web ブラウザーでは、SHA-1 ハッシュを使用した証明書は許可されません。

## <a name="captive-portals"></a>キャプティブ ポータル

キャプティブ ポータルは、最初に Wi-Fi ネットワークにアクセスしたときに、そのネットワークへのアクセス権が付与されていない場合に、ユーザーに Web ページを提示します。 ユーザーがポータルの要件を満たすまで、インターネット トラフィックが傍受されます。 ユーザーがネットワーク リソースに接続できないことが原因のネットワーク エラーは、ユーザーがポータル経由で接続するまで見込まれます。

## <a name="next-steps"></a>次の手順

[キャプティブ ポータル](https://en.wikipedia.org/wiki/Captive_portal)と Apple の新しい [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 機能について学習します。
