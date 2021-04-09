---
title: ポータルを使用してエンド ツー エンドの TLS 暗号化を構成する
titleSuffix: Azure Application Gateway
description: Azure portal を使用して、エンド ツー エンド TLS 暗号化によりアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6e2d1828acefacb03cc2f42193b8cd8897578b6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397503"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>ポータルで Application Gateway を使用してエンド ツー エンド TLS を構成する

この記事では、Azure portal を使用して Application Gateway v1 SKU でエンド ツー エンド Transport Layer Security (TLS) 暗号化 (以前の Secure Sockets Layer (SSL) 暗号化) を構成する方法について説明します。

> [!NOTE]
> Application Gateway v2 SKU では、エンド ツー エンド構成を有効化するために、信頼されたルート証明書が必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

アプリケーション ゲートウェイでエンドツーエンドの TLS を構成するには、ゲートウェイ用の証明書が必要です。 バックエンド サーバーにも証明書が必要です。 ゲートウェイ証明書は、TLS プロトコルの仕様に準拠して対称キーを得る目的で使用されます。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 

エンド ツー エンド TLS 暗号化の場合、適切なバックエンド サーバーがアプリケーション ゲートウェイで許可されている必要があります。 このアクセスを許可するには、アプリケーション ゲートウェイにバックエンド サーバーの公開証明書 (認証証明書 (v1) または信頼されたルート証明書 (v2) とも呼ばれます) をアップロードします。 証明書を追加することにより、アプリケーション ゲートウェイが既知のバックエンド インスタンスのみと通信することが保証されます。 この構成によりエンド ツー エンド通信のセキュリティがさらに強化されます。

詳細については、[Application Gateway での TLS ターミネーションとエンド ツー エンド TLS の概要](./ssl-overview.md)に関する記事を参照してください。

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>エンド ツー エンド TLS を使用して新しいアプリケーション ゲートウェイを作成する

エンド ツー エンド TLS 暗号化を使用して新しいアプリケーション ゲートウェイを作成するには、新しいアプリケーション ゲートウェイの作成中に、まず TLS 終了を有効化する必要があります。 このアクションにより、クライアントとアプリケーション ゲートウェイの間の通信で TLS 暗号化が有効になります。 次に、HTTP 設定でバックエンド サーバーの証明書を安全な受信者リストに配置する必要があります。 この構成により、アプリケーション ゲートウェイとバックエンド サーバー間の通信に TLS 暗号化が有効になります。 これにより、エンドツーエンドの TLS 暗号化が実現されます。

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>新しいアプリケーション ゲートウェイの作成中に TLS 終了を有効化する

詳細については、「[新しいアプリケーション ゲートウェイの作成中に TLS 終了を有効化する](./create-ssl-portal.md)」を参照してください。

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>バックエンド サーバーの認証証明書またはルート証明書を追加する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[HTTP 設定]** を選択します。 アプリケーション ゲートウェイを作成したときに、既定の HTTP 設定 **appGatewayBackendHttpSettings** が Azure によって自動的に作成されました。 

3. **[appGatewayBackendHttpSettings]** を選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **バックエンド認証証明書または信頼されたルート証明書** のウィンドウが表示されます。

5. **[新規作成]** を選択します。

6. **[名前]** フィールドに適切な名前を入力します。

7. **[CER 証明書のアップロード]** ボックスで、証明書ファイルを選択します。

   Standard および WAF (v1) アプリケーション ゲートウェイの場合は、.cer 形式のバックエンド サーバー証明書の公開キーをアップロードする必要があります。

   ![証明書を追加する](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 および WAF_v2 アプリケーション ゲートウェイの場合は、.cer 形式のバックエンド サーバー証明書のルート証明書をアップロードする必要があります。 バックエンド証明書が既知の証明機関 (CA) によって発行されている場合は、**[既知の CA 証明書を使用する]** チェック ボックスを選択することができます。証明書をアップロードする必要はありません。

   ![信頼されたルート証明書を追加する](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![ルート証明書](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. **[保存]** を選択します。

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイでエンド ツー エンド TLS を有効化する

エンド ツー エンド TLS 暗号化を使用して既存のアプリケーション ゲートウェイを構成するには、リスナーでまず TLS 終了を有効化する必要があります。 このアクションにより、クライアントとアプリケーション ゲートウェイの間の通信で TLS 暗号化が有効になります。 次に、HTTP 設定でバックエンド サーバー用のこれらの証明書を、安全な受信者リストに配置します。 この構成により、アプリケーション ゲートウェイとバックエンド サーバー間の通信に TLS 暗号化が有効になります。 これにより、エンドツーエンドの TLS 暗号化が実現されます。

TLS 終了を有効化するために、HTTPS プロトコルおよび証明書を含むリスナーを使用する必要があります。 これらの条件を満たす既存のリスナーを使用するか、新しいリスナーを作成することができます。 前者を選択した場合は、次に示す「既存のアプリケーション ゲートウェイで TLS 終了を有効化する」セクションを無視して、「バックエンド サーバーの認証証明書または信頼されたルート証明書を追加する」に直接進むことができます。

後者のオプションを選択した場合は、次の手順を実行します。
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイで TLS 終了を有効化する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[リスナー]** を選択します。

3. 要件に応じて、**基本** リスナーまたは **マルチサイト** リスナーを選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **[証明書]** ウィンドウが表示されます。

5. クライアントとアプリケーション ゲートウェイの間で TLS 終了に使用する PFX 証明書をアップロードします。

   > [!NOTE]
   > テスト目的で、自己署名証明書を使用してもかまいません。 ただし、これらは管理が難しく、完全にセキュリティで保護されていないため、運用環境のワークロードには推奨されません。 詳細については、「[自己署名証明書の作成](./create-ssl-portal.md#create-a-self-signed-certificate)」を参照してください。

6. 要件に応じて、**リスナー** に必要なその他の設定を追加します。

7. **[OK]** を選択して保存します。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>バックエンド サーバーの認証証明書または信頼されたルート証明書を追加する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[HTTP 設定]** を選択します。 既存のバックエンド HTTP 設定で証明書を安全な受信者リストに載せるか、新しい HTTP 設定を作成することができます。 (次の手順で、既定の HTTP 設定の証明書 (**appGatewayBackendHttpSettings**) が安全な受信者リストに追加されます。)

3. **[appGatewayBackendHttpSettings]** を選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **バックエンド認証証明書または信頼されたルート証明書** のウィンドウが表示されます。 

5. **[新規作成]** を選択します。

6. **[名前]** フィールドに適切な名前を入力します。

7. **[CER 証明書のアップロード]** ボックスで、証明書ファイルを選択します。

   Standard および WAF (v1) アプリケーション ゲートウェイの場合は、.cer 形式のバックエンド サーバー証明書の公開キーをアップロードする必要があります。

   ![証明書を追加する](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 および WAF_v2 アプリケーション ゲートウェイの場合は、.cer 形式のバックエンド サーバー証明書のルート証明書をアップロードする必要があります。 バックエンド証明書が CA によって発行されている場合は、**[既知の CA 証明書を使用する]** チェック ボックスを選択することができます。証明書をアップロードする必要はありません。

   ![信頼されたルート証明書を追加する](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)