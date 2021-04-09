---
title: アプリケーション プロキシ コネクタのデバッグ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) アプリケーション プロキシ コネクタを使用して、問題をデバッグします。
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
ms.openlocfilehash: c656bfe7f9cc8923859a99f31210cd554aa66a41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256968"
---
# <a name="debug-application-proxy-connector-issues"></a>アプリケーション プロキシ コネクタの問題をデバッグする 

この記事は、Azure Active Directory (Azure AD) アプリケーション プロキシ コネクタを使用して、問題をトラブルシューティングするのに役立ちます。 オンプレミスの Web アプリケーションへのリモート アクセスにアプリケーション プロキシ サービスを使用していて、アプリケーションへの接続に問題がある場合は、このフローチャートを使用してコネクタの問題をデバッグします。 

## <a name="before-you-begin"></a>開始する前に

この記事では、アプリケーション プロキシ コネクタをインストールしていて、問題が発生していることを前提としています。 アプリケーション プロキシの問題をトラブルシューティングするときは、このトラブルシューティングのフローから始めて、アプリケーション プロキシ コネクタが正しく構成されているかどうかを判断することをお勧めします。 アプリケーションへの接続にまだ問題がある場合は、[アプリケーション プロキシ アプリケーションの問題のデバッグ](application-proxy-debug-apps.md)のトラブルシューティングのフローに従います。  


アプリケーション プロキシとそのコネクタの使用に関する詳細は、次を参照してください。

- [アプリケーション プロキシからのオンプレミス アプリケーションへのリモート アクセス](application-proxy.md)
- [アプリケーション プロキシ コネクタ](application-proxy-connectors.md)
- [コネクタのインストールと登録](application-proxy-add-on-premises-application.md)
- [アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>コネクタの問題のフローチャート

このフローチャートでは、いくつかのコネクタの一般的な問題をデバッグする手順について説明します。 各手順の詳細については、フローチャートの下の表を参照してください。

![コネクタをデバッグする手順を示すフローチャート](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| 手順 | アクション | 説明 |
|---------|---------|---------|
|1 | アプリに割り当てられたコネクタ グループを検索する | 複数のサーバーにインストールされているコネクタがある場合があります。その場合、コネクタは[コネクタ グループに割り当てられている](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)必要があります。 コネクタ グループの詳細については、「[コネクタ グループを使用して別のネットワークや場所にアプリケーションを発行する](application-proxy-connector-groups.md)」をご覧ください。 |
|2 | コネクタをインストールして、グループを割り当てる | コネクタをインストールしていない場合は、「[コネクタのインストールと登録](application-proxy-add-on-premises-application.md#install-and-register-a-connector)」を参照してください。<br></br> コネクタをインストールできない場合、[コネクタのインストール問題](application-proxy-connector-installation-problem.md)ページを参照してください。<br></br> コネクタがグループに割り当てられていない場合は、[コネクタをグループに割り当てる](application-proxy-connector-groups.md#create-connector-groups)に関するセクションを参照してください。<br></br>アプリケーションがコネクタ グループに割り当てられていない場合は、「[コネクタ グループにアプリケーションを割り当てる](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)」を参照してください。|
|3 | コネクタ サーバーでポートのテストを実行する | [telnet](/windows-server/administration/windows-commands/telnet) またはその他のポート テスト ツールを使用して、コネクタ サーバーでポートのテストを実行して、[ポート 443 と 80 が開いている](application-proxy-add-on-premises-application.md#open-ports)かどうかを確認します。|
|4 | ドメインとポートを構成する | [ドメインとポートが正しく構成されていることを確認](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)します。コネクタが正常に機能するには、特定のポートが開いている必要があり、サーバーが URL にアクセスできる必要があります。 |
|5 | バックエンド プロキシが使用されているかどうかを確認する | コネクタがバックエンド プロキシ サーバーを使用しているか、またはそれらをバイパスしているかどうかを確認します。 詳細については、「[コネクタのプロキシの問題とサービスの接続の問題のトラブルシューティング](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)」を参照してください。 |
|6 | バックエンド プロキシを使用するようにコネクタとアップデーターを更新する | バックエンド プロキシが使用されている場合は、コネクタで同じプロキシが使用されていることを確認します。 コネクタをプロキシ サーバーと連携させるためのトラブルシューティングと構成の詳細については、「[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)」を参照してください。 |
|7 | コネクタ サーバーでアプリの内部 URL を読み込む | コネクタ サーバーでアプリの内部 URL を読み込みます。 |
|8 | 内部ネットワークの接続を確認する | このデバッグ フローで診断することができない内部ネットワーク内の接続の問題があります。 コネクタが機能するには、アプリケーションに内部的にアクセスできる必要があります。 [アプリケーション プロキシ コネクタ](application-proxy-connectors.md#under-the-hood)の説明に従って、コネクタ イベント ログを有効にして表示することができます。 |
|9 | バックエンドでタイムアウト値を延長する | アプリケーションの **[追加設定]** で、 **[バックエンド アプリケーションのタイムアウト]** 設定を **[長い]** に変更します。 「[オンプレミス アプリを Azure AD に追加する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)」を参照してください。 |
|10 | 問題が解決しない場合は、特定のフローの問題をターゲットにして、アプリと SSO デバッグのフローを確認する | [アプリケーション プロキシ アプリケーションの問題のデバッグ](application-proxy-debug-apps.md)のトラブルシューティング フローを使用します。 |

## <a name="next-steps"></a>次のステップ


* [コネクタ グループを使用して別のネットワークや場所にアプリケーションを発行する](application-proxy-connector-groups.md)
* [既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)
* [アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)
* [Azure AD アプリケーション プロキシ コネクタをサイレント インストールする方法](application-proxy-register-connector-powershell.md)