---
title: Azure Application Gateway の使用による Azure App Service などのマルチテナント バックエンドの概要
description: このページでは、Application Gateway によるマルチテナント バックエンドのサポートの概要について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 66e4a578e3f443f4cbc3f6e5467cf9a86adf05fe
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297044"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>App Service などのマルチテナント バックエンドに対する Application Gateway のサポート

Web サーバー内がマルチテナント アーキテクチャ設計の場合、同じ Web サーバー インスタンス上で複数の Web サイトが実行されます。 ホスト名は、ホストされる各種アプリケーションを区別するために使用されます。 既定では、クライアントからの受信 HTTP ホスト ヘッダーは、変更されることなくそのままバックエンドに送信されます。 これは、NIC、仮想マシン スケール セット、パブリック IP アドレス、内部 IP アドレス、FQDN などのバックエンド プール メンバーではうまく機能します。これらのメンバーは、特定のホスト ヘッダーや SNI 拡張機能に頼らなくても、正しいエンドポイントに解決されるからです。 しかし、Azure App Service の Web アプリや Azure API Management のように、本質的にはマルチテナントであり、特定のホスト ヘッダーや SNI 拡張機能に頼って正しいエンドポイントに解決されるサービスも多数あります。 通常、アプリケーションの DNS 名 (アプリケーション ゲートウェイに関連付けられた DNS 名になります) は、バックエンド サービスのドメイン名とは異なります。 したがって、アプリケーション ゲートウェイで受信された元の要求内のホスト ヘッダーは、バックエンド サービスのホスト名と同じではありません。 このため、アプリケーション ゲートウェイからバックエンドへの要求内のホスト ヘッダーが、バックエンド サービスのホスト名に変更されない場合、マルチテナント バックエンドでは、要求を正しいエンドポイントに解決できません。 

アプリケーション ゲートウェイには、バックエンドのホスト名に基づいて、要求内の HTTP ホスト ヘッダーをオーバーライドする機能があります。 この機能により、Azure App Service の Web アプリや API Management などのマルチテナント バックエンドがサポートされます。 この機能は、Standard SKU および WAF SKU の v1 と v2 の両方で利用できます。 

![ホストのオーバーライド](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> これは、Azure App Service Environment (ASE) には適用されません。ASE は、マルチテナントのリソースである Azure App Service とは異なり、専用のリソースであるからです。

## <a name="override-host-header-in-the-request"></a>要求内のホスト ヘッダーのオーバーライド

ホスト オーバーライドを指定する機能は、[HTTP 設定](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)内で定義されます。また、この機能はルールの作成中にバックエンド プールに適用できます。 マルチテナント バックエンドのホスト ヘッダーと SNI 拡張機能をオーバーライドする方法として、以下の 2 つがサポートされています。

- HTTP 設定内で明示的に入力された固定値をホスト名として設定する機能。 この機能では、特定の HTTP 設定が適用されたバックエンド プールへのすべてのトラフィックについて、ホスト ヘッダーがこの値にオーバーライドされます。 エンド ツー エンド SSL を使用している場合、オーバーライドされたこのホスト名は SNI 拡張機能で使用されます。 この機能を使用すれば、顧客の受信ホスト ヘッダーと異なるホスト ヘッダーがバックエンド プール ファームによって想定されるシナリオに対応できます。

- バックエンド プール メンバーの IP または FQDN からホスト名を取得する機能。 HTTP 設定では、個々のバックエンド プール メンバーからホスト名を取得するオプションが構成されている場合、バックエンド プール メンバーの FQDN からホスト名を動的に選択するオプションも設定できます。 エンド ツー エンド SSL を使用している場合、このホスト名は FQDN から取得され、SNI 拡張機能で使用されます。 この機能を使用すれば、バックエンド プールに 2 つ以上のマルチテナント PaaS サービス (Azure Web Apps など) を使用でき、各メンバーへの要求のホスト ヘッダーに FQDN から取得されたホスト名を含めるシナリオに対応できます。 このシナリオを実装する場合は、HTTP 設定内で [[バックエンド アドレスからホスト名を選択します]](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) というスイッチを使用します。これにより、元の要求内のホスト ヘッダーが動的にオーバーライドされ、バックエンド プール内に示されているホスト ヘッダーになります。  たとえば、要求が当該バックエンド サーバーに送信される場合、バックエンド プールの FQDN に "contoso11.azurewebsites.net" と "contoso22.azurewebsites.net" が含まれていれば、元の要求のホスト ヘッダー (contoso.com) は、contoso11.azurewebsites.net または contoso22.azurewebsites.net にオーバーライドされます。 

  ![Web アプリのシナリオ](./media/application-gateway-web-app-overview/scenario.png)

顧客はこの機能を使用して、HTTP 設定とカスタム プローブのオプションを適切な構成に指定します。 さらに、この設定は、ルールを使用してリスナーとバックエンド プールに関連付けられます。

## <a name="special-considerations"></a>特別な考慮事項

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>マルチテナント サービスでの SSL 終了およびエンド ツー エンド SSL

マルチテナント サービスでは、SSL 終了とエンド ツー エンド SSL 暗号化の両方がサポートされます。 アプリケーション ゲートウェイで SSL 終了を行う場合、SSL 証明書を引き続きアプリケーション ゲートウェイのリスナーに追加する必要があります。 ただし、エンド ツー エンド SSL の場合、信頼されている Azure サービス (Azure App Service の Web アプリなど) では、アプリケーション ゲートウェイ内でバックエンドをホワイト リスト登録する必要はありません。 したがって、どの認証証明書も追加する必要はありません。 

![エンド ツー エンド SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

上の画像で App Service がバックエンドとして選択されている場合は、認証証明書を追加する必要はないことに注意してください。

### <a name="health-probe"></a>正常性プローブ

**HTTP 設定**内でホスト ヘッダーをオーバーライドすると、要求とそのルーティングにのみ影響が及びます。 正常性プローブの動作には影響が及びません。 エンド ツー エンドの機能が動作するには、正しい構成を反映するようプローブと HTTP 設定の両方を変更する必要があります。 プローブ構成内でホスト ヘッダーを指定できるほか、カスタム プローブでは、現在構成されている HTTP 設定からホスト ヘッダーを取得する機能もサポートされています。 この構成は、プローブ構成の `PickHostNameFromBackendHttpSettings` パラメーターを使用して指定できます。

### <a name="redirection-to-app-services-url-scenario"></a>App Service の URL にリダイレクトされるシナリオ

App Service からの応答内のホスト名によって、エンドユーザーのブラウザーが、Application Gateway に関連付けられているドメインではなく、*.azurewebsites.net ホスト名にリダイレクトされる場合があります。 この問題が発生するのは、以下の場合です。

- App Service 上でリダイレクトが構成されている。 要求の末尾にスラッシュを追加するだけでリダイレクトが実行される場合があります。
- Azure AD 認証が構成されており、これが原因でリダイレクトが実行される。

このような問題を解決するには、[App Service の URL にリダイレクトされる問題のトラブルシューティング](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

マルチテナント アプリケーション (Azure App Service の Web アプリなど) と共にアプリケーション ゲートウェイをバックエンド プール メンバーとして設定する方法については、[Application Gateway を使用する App Service の Web アプリ の構成](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)に関するページを参照してください。
