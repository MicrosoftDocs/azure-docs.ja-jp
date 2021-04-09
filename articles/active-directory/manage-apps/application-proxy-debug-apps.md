---
title: アプリケーション プロキシ アプリケーションのデバッグ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを使用して、問題をデバッグします。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2066030b04feae4e50fee3442a663209801d5875
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253393"
---
# <a name="debug-application-proxy-application-issues"></a>アプリケーション プロキシ アプリケーションの問題をデバッグする 

この記事は、Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを使用して、問題をトラブルシューティングするのに役立ちます。 オンプレミスの Web アプリケーションへのリモート アクセスにアプリケーション プロキシ サービスを使用していて、アプリケーションへの接続に問題がある場合は、このフローチャートを使用してアプリケーションの問題をデバッグします。 

## <a name="before-you-begin"></a>開始する前に

アプリケーション プロキシの問題をトラブルシューティングするときは、コネクタから始めることをお勧めします。 まず、「[アプリケーション プロキシ コネクタの問題をデバッグする](application-proxy-debug-connectors.md)」のトラブルシューティング フローに従って、アプリケーション プロキシ コネクタが正しく構成されていることを確認します。 問題がまだある場合は、この記事に戻って、アプリケーションのトラブルシューティングを行います。  

アプリケーション プロキシの詳細については、次を参照してください。

- [アプリケーション プロキシからのオンプレミス アプリケーションへのリモート アクセス](application-proxy.md)
- [アプリケーション プロキシ コネクタ](application-proxy-connectors.md)
- [コネクタのインストールと登録](application-proxy-add-on-premises-application.md)
- [アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>アプリケーションの問題のフローチャート

このフローチャートでは、いくつかのアプリケーションへの接続の一般的な問題をデバッグする手順について説明します。 各手順の詳細については、フローチャートの下の表を参照してください。

![アプリケーションをデバッグする手順を示すフローチャート](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| 手順 | アクション | 説明 |
|---------|---------|---------|
|1 | ブラウザーを開き、アプリにアクセスして、資格情報を入力する | ご自分の資格情報を使用してアプリへのサインインを試し、任意のユーザーに関連するエラー ([この企業アプリケーションにアクセスできない](application-proxy-sign-in-bad-gateway-timeout-error.md)など) を確認します。 |
|2 | アプリへのユーザーの割り当てを確認する | ユーザー アカウントに、企業ネットワーク内からアプリにアクセスするためのアクセス許可があることを確認してから、「[アプリケーションをテストする](application-proxy-add-on-premises-application.md#test-the-application)」の手順に従って、アプリへのサインインをテストします。 サインインに関する問題が解決しない場合は、[サインイン エラーのトラブルシューティング方法](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)に関するページを参照してください。  |
|3 | ブラウザーを開き、アプリへのアクセスを試みる | エラーがすぐに表示される場合は、アプリケーション プロキシが正しく構成されていることを確認します。 特定のエラー メッセージンに関する詳細は、「[アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)」を参照してください。  |
|4 | カスタム ドメインのセットアップを確認するか、エラーをトラブルシューティングする | ページに何も表示されない場合は、[カスタム ドメインの使用](application-proxy-configure-custom-domain.md)を確認して、カスタム ドメインが正しく構成されていることを確認します。<br></br>ページで読み込みが行われず、エラー メッセージが表示される場合は、「[アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)」を参照して、エラーのトラブルシューティングを行います。 <br></br>エラー メッセージが表示されるまでに 20 秒以上かかる場合は、接続に問題がある可能性があります。 「[アプリケーション プロキシ コネクタのデバッグ](application-proxy-debug-connectors.md)」のトラブルシューティング記事に移動します。  |
|5 | 問題が解決しない場合は、コネクタのデバッグに移動する | プロキシとコネクタ間またはコネクタとバックエンド間に接続の問題がある可能性があります。 「[アプリケーション プロキシ コネクタのデバッグ](application-proxy-debug-connectors.md)」のトラブルシューティング記事に移動します。 |
|6 | すべてのリソースを発行し、ブラウザーの開発者ツールを確認し、リンクを修正する | 発行するパスに、アプリケーションに必要なすべての画像、スクリプト、スタイル シートが含まれていることを確認します。 詳細については、「[オンプレミス アプリを Azure AD に追加する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)」を参照してください。 <br></br>ブラウザーの開発者ツール (Internet Explorer または Microsoft Edge の F12 ツール) を使用して、[アプリケーション ページが正しく表示されない](application-proxy-page-appearance-broken-problem.md)の説明に従って、発行に関する問題を確認します。 <br></br>[ページ上のリンクが機能しない](application-proxy-page-links-broken-problem.md)で、壊れたリンクを解決するためのオプションを確認します。 |
|7 | ネットワーク待ち時間を確認する | ページの読み込みが遅い場合、[待機時間を削減するための考慮事項](application-proxy-network-topology.md#considerations-for-reducing-latency)でネットワーク待機時間を最小限に抑える方法について学習します。 | 
|8 | その他のトラブルシューティング ヘルプを確認する | 問題が解決しない場合は、[アプリケーション プロキシのトラブルシューティング ドキュメント](application-proxy-troubleshoot.md)でその他のトラブルシューティングの記事を検索します。 |

## <a name="next-steps"></a>次のステップ


* [コネクタ グループを使用して別のネットワークや場所にアプリケーションを発行する](application-proxy-connector-groups.md)
* [既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)
* [アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)
* [Azure AD アプリケーション プロキシ コネクタをサイレント インストールする方法](application-proxy-register-connector-powershell.md)
