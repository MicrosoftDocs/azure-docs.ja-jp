---
title: Azure のアプリケーション ゲートウェイのトラブルシューティング - ILB ASE | Microsoft Docs
description: Azure 内の App Service Environment で内部ロード バランサーを使用してアプリケーション ゲートウェイをトラブルシューティングする方法について説明します。
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84628528"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>App Service Environment で内部ロード バランサーを使用するアプリケーション ゲートウェイの許可リストにバックエンド サーバー証明書が登録されない

この記事でトラブルシューティングを行う問題: Azure 内でエンド ツー エンド TLS を使用するとき、内部ロード バランサー (ILB) と App Service Environment (ASE) を一緒にバックエンドで使用してアプリケーション ゲートウェイを作成すると、証明書が許可リストに登録されません。

## <a name="symptoms"></a>現象

バック エンドで ILB を ASE と一緒に使用してアプリケーション ゲートウェイを作成すると、バックエンド サーバーが異常になることがあります。 この問題が発生するのは、アプリケーション ゲートウェイの認証証明書が、バックエンド サーバー上の構成済み証明書と一致しない場合です。 例として次のシナリオをご覧ください。

**アプリケーション ゲートウェイ構成:**

- **リスナー:** マルチサイト
- **Port:** 443
- **ホスト名:** test.appgwtestase.com
- **SSL 証明書:** CN=test.appgwtestase.com
- **バックエンド プール:** IP アドレスまたは FQDN
- **IP アドレス:** 10.1.5.11
- **HTTP 設定:** HTTPS
- **ポート:** 443
- **カスタム プローブ:** ホスト名 – test.appgwtestase.com
- **認証証明書:** test.appgwtestase.com の .cer
- **バックエンドの正常性:** 異常 – バックエンド サーバー証明書が Application Gateway の許可リストに登録されていません。

**ASE 構成:**

- **ILB IP:** 10.1.5.11
- **ドメイン名:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL バインド:** SNI SSL – CN=test.appgwtestase.com

アプリケーション ゲートウェイにアクセスすると、バックエンド サーバーが異常であるため次のエラー メッセージを受け取ります。

**[502 - Web server received an invalid response while acting as a gateway or proxy server.]\(502 - Web サーバーがゲートウェイまたはプロキシ サーバーとして動作しているときに、無効な応答を受信しました。\)**

## <a name="solution"></a>解決策

HTTPS Web サイトにアクセスするときにホスト名を使用しないと、SNI が無効になっている場合、バックエンド サーバーによって既定 Web サイトに対する構成済みの証明書が返されます。 ILB ASE の場合、既定の証明書は ILB 証明書に基づきます。 ILB の構成済み証明書がない場合、証明書は ASE App 証明書に基づきます。

完全修飾ドメイン名 (FQDN) を使用して ILB にアクセスすると、バックエンド サーバーは HTTP 設定にアップロードされた正しい証明書を返します。 これに該当しない場合は、次のオプションを検討してください。

- アプリケーション ゲートウェイのバックエンド プール内で、ILB の IP アドレスを指す FQDN を使用します。 このオプションが機能するのは、プライベート DNS ゾーンまたはカスタム DNS を構成している場合のみです。 それ以外の場合は、パブリック DNS の "A"レコードを作成する必要があります。

- ILB 上のアップロードされた証明書、または HTTP 設定内の既定証明書 (ILB 証明書) を使用します。 アプリケーション ゲートウェイは、プローブのために ILB の IP にアクセスするときに証明書を取得します。

- すべての Web サイトで証明書が共通になるように、ILB とバックエンド サーバーでワイルドカード証明書を使用します。 ただし、このソリューションはサブドメインの場合のみ可能で、各 Web サイトに必要なホスト名が異なる場合は可能ではありません。

- ILB の IP アドレスを使用する場合は、アプリケーション ゲートウェイの **[App Service 用に使用します]** オプションをクリアします。

オーバーヘッドを減らすため、HTTP 設定に ILB 証明書をアップロードしてプローブ パスが機能するようにします。 (この手順は許可リスト登録のためだけです。 TLS 通信では使用されません。)お使いのブラウザーから HTTPS の IP アドレスを使用して ILB にアクセスし、TLS/SSL 証明書を Base-64 エンコーディングされた CER 形式でエクスポートし、それぞれの HTTP 設定に証明書をアップロードすることで、ILB 証明書を取得できます。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
