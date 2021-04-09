---
title: Azure Application Gateway 要求ルーティング規則の構成
description: この記事では、Azure Application Gateway 要求ルーティング規則を構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397537"
---
# <a name="application-gateway-request-routing-rules"></a>Application Gateway 要求ルーティング規則

Azure portal を使用してアプリケーション ゲートウェイを作成するときに、既定の規則 (*rule1*) を作成します。 この規則によって、既定のリスナー (*appGatewayHttpListener*) が既定のバックエンド プール (*appGatewayBackendPool*) と既定のバックエンド HTTP 設定 (*appGatewayBackendHttpSettings*) にバインドされます。 ゲートウェイを作成した後で、既定の規則の設定の編集や新しい規則の作成を行うことができます。

## <a name="rule-type"></a>規則の種類

新しい規則を作成するときは、["*基本*" または "*パス ベース*"](./application-gateway-components.md#request-routing-rules) を選択します。

- 関連付けられたリスナー (例: *blog <i></i>.contoso.com/\*)* に対するすべての要求を 1 つのバックエンド プールに転送する場合は、基本を選択します。
- 特定の URL パスからの要求を特定のバックエンド プールにルーティングする場合は、パス ベースを選択します。 パスのパターンは URL のパスのみに適用され、クエリ パラメーターには適用されません。

### <a name="order-of-processing-rules"></a>規則を処理する順序

v1 SKU と v2 SKU の場合、着信要求のパターン マッチングは、パス ベースの規則の URL パス マップにパスが一覧された順序で処理されます。 要求がパス マップ内の複数のパスのパターンと一致する場合、一覧の順序が最初のパスが一致します。 また、要求はそのパスに関連付けられたバック エンドに転送されます。

## <a name="associated-listener"></a>関連付けられたリスナー

リスナーに関連付けられた "*要求のルーティング規則*" が評価されて、要求のルーティング先のバックエンド プールを決定できるように、リスナーを規則に関連付けます。

## <a name="associated-back-end-pool"></a>関連付けられたバックエンド プール

リスナーが受信する要求を処理するバックエンド ターゲットを含むバックエンド プールを規則に関連付けます。

 - 基本規則の場合、1 つのバックエンド プールのみが許可されます。 関連付けられているリスナー上のすべての要求は、そのバックエンド プールに転送されます。

 - パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド プールを追加します。 入力した URL パスと一致する要求が、対応するバックエンド プールに転送されます。 また、既定のバックエンド プールを追加します。 規則内のどの URL パスとも一致しない要求は、そのプールに転送されます。

## <a name="associated-back-end-http-setting"></a>関連付けられたバックエンド HTTP 設定

各規則のバックエンド HTTP 設定を追加します。 要求は、この設定に指定されるポート番号、プロトコル、および他の情報を使用して、アプリケーション ゲートウェイからバックエンド ターゲットにルーティングされます。

基本規則の場合、1 つのバックエンド HTTP 設定のみが許可されます。 関連付けられているリスナー上のすべての要求は、この HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。

パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド HTTP 設定を追加します。 この設定の URL パスと一致する要求は、各 URL パスに対応する HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。 また、既定の HTTP 設定を追加します。 この規則のどの URL パスとも一致しない要求は、既定の HTTP 設定を使用して既定のバックエンド プールに転送されます。

## <a name="redirection-setting"></a>リダイレクト設定

基本規則でリダイレクトが構成されている場合、関連付けられているリスナーのすべての要求はターゲットにリダイレクトされます。 これは "*グローバル*" なリダイレクトです。 パスベースの規則に対してリダイレクトが構成されている場合は、特定のサイト領域内の要求のみがリダイレクトされます。 たとえば、 */cart/\** で示されるショッピング カート領域です。 これが *パスベース* のリダイレクトです。

リダイレクトの詳細については、「[Application Gateway のリダイレクトの概要](redirect-overview.md)」を参照してください。

### <a name="redirection-type"></a>リダイレクトの種類

必要なリダイレクトの種類を選択します。*Permanent(301)* 、*Temporary(307)* 、*Found(302)* 、または *See other(303)* があります。

### <a name="redirection-target"></a>リダイレクト ターゲット

リダイレクトのターゲットとして別のリスナーまたは外部サイトを選択します。

#### <a name="listener"></a>リスナー

ゲートウェイ上のあるリスナーから別のリスナーにトラフィックをリダイレクトするには、リダイレクト ターゲットとしてリスナーを選択します。 この設定は、HTTP から HTTPS へのリダイレクトを有効にする場合に必要です。 着信 HTTP 要求を確認するソース リスナーから、着信 HTTPS 要求を確認する宛先リスナーにトラフィックがリダイレクトされます。 元の要求のクエリ文字列とパスを、リダイレクト ターゲットに転送される要求に含めることもできます。

![Application Gateway コンポーネントのダイアログ ボックス](./media/configuration-overview/configure-redirection.png)

HTTP から HTTPS へのリダイレクトの詳細については、以下を参照してください。
- [Azure portal を使用して HTTP から HTTPS にリダイレクトする](redirect-http-to-https-portal.md)
- [PowerShell を使用して HTTP から HTTPS にリダイレクトする](redirect-http-to-https-powershell.md)
- [Azure CLI を使用して HTTP から HTTPS にリダイレクトする](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>外部サイト

この規則に関連付けられたリスナーに対するトラフィックを外部サイトにリダイレクトするときに、外部サイトを選択します。 元の要求のクエリ文字列を、リダイレクト ターゲットに転送される要求に含めることができます。 元の要求内のパスを外部サイトに転送することはできません。

リダイレクトの詳細については、以下を参照してください。
- [PowerShell を使用して外部サイトにトラフィックをリダイレクトする](redirect-external-site-powershell.md)
- [CLI を使用して外部サイトにトラフィックをリダイレクトする](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>HTTP ヘッダーと URL を書き換える

書き換え規則を使用すると、要求および応答パケットがアプリケーション ゲートウェイを通じてクライアントとバックエンド プールの間を移動する際に、HTTP(S) 要求と応答ヘッダーや URL パスとクエリ文字列パラメーターを追加、削除、または更新できます。

ヘッダーおよび URL パラメーターは、静的な値に設定するか、その他のヘッダーやサーバー変数に設定できます。 クライアント IP アドレスを抽出する、バックエンドに関する機密情報を削除する、セキュリティを追加するなど、重要なユース ケースで役立ちます。
詳細については、次を参照してください。

 - [HTTP ヘッダーと URL の書き換えの概要](rewrite-http-headers-url.md)
 - [HTTP ヘッダーの書き換えの構成](rewrite-http-headers-portal.md)
 - [URL の書き換えの構成](rewrite-url-portal.md)

## <a name="next-steps"></a>次の手順

- [HTTP 設定について学習する](configuration-http-settings.md)