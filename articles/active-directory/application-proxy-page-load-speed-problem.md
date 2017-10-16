---
title: "アプリケーション プロキシのアプリケーション読み込みに時間がかかりすぎる | Microsoft Docs"
description: "Azure AD アプリケーション プロキシによるページ読み込みパフォーマンスの問題をトラブルシューティングする"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ce462c90746e6af0dc201686557121665b82b93d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>アプリケーション プロキシのアプリケーション読み込みに時間がかかりすぎる

この記事では、Azure AD アプリケーション プロキシのアプリケーションが読み込みに時間がかかる理由と、この問題を解決するためにできることを説明します。

## <a name="overview"></a>概要
アプリケーションが動作していても待ち時間が長い場合は、ご使用のネットワーク トポロジの中にいくつかの小さな調整点があり、これらを考慮することで速度を向上できるかもしれません。 さまざまなトポロジの評価については、[ネットワークに関する考慮事項のマニュアル](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations)をご覧ください。

こうした考慮事項が役に立たない場合、残念ながらパフォーマンス チューニングに関する他の推奨事項は今のところありません。 アプリケーション プロキシのサービスがお客様に近い地域のデータ センターにまで展開されれば、待機時間の短縮に直結する可能性があります。 Azure データ センターの全一覧をご覧になるには、[待機時間テスト ページ](http://www.azurespeed.com/Azure/Latency)をご覧ください。 

アプリケーション プロキシ サービスを使用したデータ センターは、[コネクタ ポート テスト ツール](https://aadap-portcheck.connectorporttest.msappproxy.net/)で見つけることができます。 

## <a name="feedback-on-application-proxy-data-center-locations"></a>アプリケーション プロキシ データ センターの場所に関するフィードバック 
いまのところはアプリケーション プロキシが含まれていないがお客様にとって待機時間の大幅な短縮につながる Azure データ センターがあるかもしれません。 データ センターの場所は <aadapfeedback@microsoft.com> であり、お客様のフィードバックは今後の展開の参考とさせていただきます。

現在待機時間が長いテナントの待機時間短縮に役立ついくつかの追加機能に取り組んでいます。ドキュメントが公開されたら必ずお知らせいたします。

## <a name="next-steps"></a>次のステップ
[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-working-with-proxy-servers.md)
