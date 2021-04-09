---
title: Azure Application Gateway の HTTP 設定の構成
description: この記事では、Azure Application Gateway の HTTP 設定を構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652660"
---
# <a name="application-gateway-http-settings-configuration"></a>Application Gateway の HTTP 設定の構成

アプリケーション ゲートウェイは、ここで指定した構成を使用してトラフィックをバックエンド サーバーにルーティングします。 HTTP 設定を作成したら、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。

## <a name="cookie-based-affinity"></a>Cookie ベースのアフィニティ

Azure Application Gateway では、ユーザー セッションを維持するために、ゲートウェイで管理される Cookie を使用します。 ユーザーが最初の要求を Application Gateway に送信すると、セッションの詳細を含むハッシュ値を使用して、応答にアフィニティ Cookie が設定されます。これにより、このアフィニティ Cookie を伝送する後続の要求は、持続性を維持するために同じバックエンド サーバーにルーティングされるようになります。 

この機能は、ユーザー セッションを同じサーバー上に維持する必要があり、ユーザー セッションのセッション状態がサーバー上にローカル保存される場合に便利です。 アプリケーションが Cookie ベースのアフィニティを処理できない場合は、この機能を使用できません。 使用するには、クライアントが Cookie をサポートしている必要があります。

[Chromium ブラウザー](https://www.chromium.org/Home) [v80 の更新](https://chromiumdash.appspot.com/schedule)では、[SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) 属性のない HTTP Cookie を SameSite=Lax として扱うことが必須になりました。 CORS (クロス オリジン リソース共有) 要求のケースで、Cookie をサードパーティのコンテキストで送信する必要がある場合は、*SameSite=None; Secure* 属性を使用する必要があり、HTTPS 経由でのみ送信する必要があります。 それ以外の場合、HTTP のみのシナリオでは、ブラウザーはサードパーティのコンテキストで Cookie を送信しません。 Chrome のこの更新の目的は、セキュリティを強化し、クロスサイト リクエスト フォージェリ (CSRF) 攻撃を回避することです。 

この変更をサポートするために、2020 年 2 月 17 日以降、Application Gateway (すべての SKU タイプ) は、既存の *ApplicationGatewayAffinity* Cookie に加えて、*ApplicationGatewayAffinityCORS* と呼ばれる別の Cookie を挿入します。 *ApplicationGatewayAffinityCORS* Cookie には、クロス オリジン要求に対してもスティッキー セッションが維持されるように、2 つの属性が追加されています ( *"SameSite = None; Secure"* )。

既定のアフィニティ Cookie 名は *ApplicationGatewayAffinity* であり、変更することができます。 カスタムのアフィニティ Cookie 名を使用している場合、サフィックスとして CORS を付加した状態で、追加の Cookie が追加されます。 たとえば、*CustomCookieNameCORS* です。

> [!NOTE]
> 属性 *SameSite=None* が設定されている場合は、Cookie に *Secure* フラグも含まれていて、HTTPS 経由で送信される必要があります。  CORS 経由のセッション アフィニティが必要な場合は、ワークロードを HTTPS に移行する必要があります。 Application Gateway での TLS オフロードとエンドツーエンド TLS のドキュメントについては、[概要](ssl-overview.md)に関するページ、[Azure portal を使用して TLS ターミネーションでアプリケーション ゲートウェイを構成する](create-ssl-portal.md)方法に関するページ、「[ポータルで Application Gateway を使用してエンド ツー エンド TLS を構成する](end-to-end-ssl-portal.md)」を参照してください。

## <a name="connection-draining"></a>接続のドレイン

接続のドレインを使用すると、計画的なサービスの更新中にバックエンド プール メンバーを正常に削除することができます。 HTTP の設定で接続のドレインを有効にすることで、この設定をバックエンド プールのすべてのメンバーに適用できます。 これで、バックエンド プールの登録を解除するすべてのインスタンスが既存の接続を維持して、構成可能なタイムアウトに対して進行中の要求を処理し、新しい要求や接続を受信しないことが保証されます。 これに対する唯一の例外は、ゲートウェイによって管理されるセッション アフィニティのために登録を解除するインスタンス宛ての要求です。これらは、登録を解除するインスタンスによって引き続き転送されます。 接続のドレインは、バックエンド プールから明示的に削除されるバックエンド インスタンスに適用されます。

## <a name="protocol"></a>Protocol

Application Gateway では、要求のバックエンド サーバーへのルーティングに対して HTTP と HTTPS の両方がサポートされます。 HTTP を選択した場合、バックエンド サーバーへのトラフィックは暗号化されません。 暗号化されていない通信を許容できない場合は、HTTPS を選択してください。

リスナーで HTTPS と組み合わせたこの設定は、[エンド ツー エンド TLS](ssl-overview.md) をサポートします。 これによって、機密データを暗号化して安全にバックエンドに送信することができます。 エンド ツー エンド TLS が有効になったバックエンド プール内の各バックエンド サーバーは、セキュリティで保護された通信を許可するように証明書で構成する必要があります。

## <a name="port"></a>Port

この設定では、バックエンド サーバーがアプリケーション ゲートウェイからのトラフィックをリッスンするポートを指定します。 1 から 65535 までの範囲のポートを構成できます。

## <a name="request-timeout"></a>要求タイムアウト

この設定は、アプリケーション ゲートウェイがバックエンド サーバーからの応答の受信を待機する秒数です。

## <a name="override-back-end-path"></a>バックエンド パスのオーバーライド

この設定を使用すると、要求がバックエンドに転送されるときに使用できる、オプションのカスタム転送パスを構成できます。 **[バックエンド パスのオーバーライド]** フィールドに指定したカスタム パスと一致する受信パスの部分が、転送先パスにコピーされます。 次の表は、この機能のしくみを示しています。

- HTTP 設定が基本の要求ルーティング規則に関連付けられている場合:

  | 元の要求  | バックエンド パスのオーバーライド | バックエンドに転送される要求 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- HTTP 設定がパスベースの要求ルーティング規則に関連付けられている場合:

  | 元の要求           | パスの規則       | バックエンド パスのオーバーライド | バックエンドに転送される要求 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

## <a name="use-for-app-service"></a>[App Service 用に使用します]

これは、Azure App Service バックエンドに必要な 2 つの設定を選択する UI 専用ショートカットです。 これにより、**バックエンド アドレスからホスト名を選択** できます。また、新しいカスタム プローブがまだない場合は作成されます。 (詳細については、この記事の「[バックエンド アドレスからホスト名を選択する](#pick-host-name-from-back-end-address)」設定のセクションを参照してください。)新しいプローブが作成されます。プローブのヘッダーはバックエンド メンバーのアドレスから選択されます。

## <a name="use-custom-probe"></a>[カスタム プローブの使用]

この設定で、[カスタム プローブ](application-gateway-probe-overview.md#custom-health-probe)が HTTP 設定と関連付けられます。 カスタム プローブを 1 つだけ HTTP 設定と関連付けることができます。 カスタム プローブを明示的に関連付けないと、バックエンドの正常性を監視するために[既定のプローブ](application-gateway-probe-overview.md#default-health-probe-settings)が使用されます。 バック エンドの正常性監視をきめ細かく制御するには、カスタム プローブを作成することをお勧めします。

> [!NOTE]
> 対応する HTTP 設定が明示的にリスナーに関連付けられていない限り、カスタム プローブはバックエンド プールの正常性を監視しません。

## <a name="pick-host-name-from-back-end-address"></a>バックエンド アドレスからホスト名を選択する

この機能によって、要求の *host* ヘッダーが、バックエンド プールのホスト名に動的に設定されます。 これには IP アドレスまたは FQDN が使用されます。

この機能が役立つのは、バックエンドのドメイン名がアプリケーション ゲートウェイの DNS 名と異なっており、バックエンドが特定のホスト ヘッダーを利用して正しいエンドポイントに解決される場合です。

たとえば、バックエンドとしてのマルチテナント サービスのケースなどです。 アプリ サービスは、単一の IP アドレスを持つ共有領域を使用するマルチテナント サービスです。 そのため、アプリ サービスにアクセスするには、カスタム ドメイン設定で構成されているホスト名を使用する必要があります。

既定ではカスタム ドメイン名は *example.azurewebsites.net* です。 アプリケーション ゲートウェイを使用して、App Service に明示的に登録されていないホスト名またはアプリケーション ゲートウェイの FQDN によって App Service にアクセスするには、元の要求のホスト名をオーバーライドして、アプリ サービスのホスト名にする必要があります。 これを行うために、 **[バックエンド アドレスからホスト名を選択します]** 設定を有効にします。

既存のカスタム DNS 名がアプリ サービスにマップされているカスタム ドメインの場合、この設定を有効にする必要はありません。

> [!NOTE]
> この設定は、専用のデプロイである App Service Environment には必要ありません。

## <a name="host-name-override"></a>[ホスト名の上書き]

この機能によって、アプリケーション ゲートウェイの着信要求の *host* ヘッダーが、指定するホスト名に置き換えられます。

たとえば、*www.contoso.com* が **[ホスト名]** 設定に指定されている場合、元の要求 *`https://appgw.eastus.cloudapp.azure.com/path1` は、バックエンド サーバーに転送されるときに、*`https://www.contoso.com/path1` に変更されます。

## <a name="next-steps"></a>次の手順

- [バックエンド プールについて](configuration-overview.md#back-end-pool)