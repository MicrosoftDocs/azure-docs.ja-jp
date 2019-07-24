---
title: クイック スタート - Azure Application Gateway を使用してエンド ツー エンド SSL 暗号化を構成する - Azure portal | Microsoft Docs
description: Azure portal を使用して、エンド ツー エンド SSL 暗号化により Azure Application Gateway を作成する方法について説明します。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58229339"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>ポータルで Application Gateway を使用してエンド ツー エンド SSL を構成する

この記事では、Azure portal を使用して、Application Gateway v1 SKU でエンド ツー エンド SSL 暗号化を構成する方法を示します。  

> [!NOTE]
> Application Gateway v2 SKU では、エンド ツー エンド構成を有効化するために、信頼されたルート証明書が必要です。 信頼されたルート証明書を追加するためのポータルのサポートは、まだ提供されていません。 したがって、v2 SKU の場合は、[PowerShell を使用したエンド ツー エンド SSL の構成](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

アプリケーション ゲートウェイでエンド ツー エンド SSL を構成するには、ゲートウェイ用とバックエンド サーバー用の証明書が必要です。 ゲートウェイ証明書は、SSL プロトコルの仕様に従って対称キーを得る目的で使用されます。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 エンド ツー エンド SSL 暗号化では、アプリケーション ゲートウェイにバックエンドをホワイトリスト登録する必要があります。 これを行うには、アプリケーション ゲートウェイにバックエンド サーバーの公開証明書 (認証証明書とも呼ばれる) をアップロードします。 証明書を追加することにより、アプリケーション ゲートウェイは、既知のバックエンド インスタンスのみと通信するため、 エンド ツー エンド通信のセキュリティが強化されます。

詳細については、[SSL 終了とエンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview) に関するページを参照してください。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>エンド ツー エンド SSL を使用して新しいアプリケーション ゲートウェイを作成する

エンド ツー エンド SSL 暗号化を使用して新しいアプリケーション ゲートウェイを作成するには、新しいアプリケーション ゲートウェイの作成中に、まず SSL 終了を有効化する必要があります。 これにより、クライアントとアプリケーション ゲートウェイの間の通信で SSL 暗号化が有効になります。 その後、アプリケーション ゲートウェイとバックエンド サーバーの間の通信で SSL 暗号化を有効化するために、HTTP 設定でバックエンド サーバーの証明書をホワイトリストに登録する必要があります。これにより、エンド ツー エンド SSL 暗号化が実現されます。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>新しいアプリケーション ゲートウェイの作成中に SSL 終了を有効化する

[新しいアプリケーション ゲートウェイの作成中に SSL 終了を有効化する](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)方法については、この記事を参照してください。

### <a name="whitelist-certificates-for-backend-servers"></a>バックエンド サーバーの証明書をホワイトリストに登録する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[HTTP 設定]** を選択します。 アプリケーション ゲートウェイを作成したときに、既定の HTTP 設定 **appGatewayBackendHttpSettings** が Azure によって自動的に作成されました。 

3. **[appGatewayBackendHttpSettings]** を選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **[バックエンド認証証明書]** ウィンドウが表示されます。 

5. **[バックエンド認証証明書]** で、**[新規作成]** を選択します。

6. 適切な **[名前]** を入力します。

7. **[CER 証明書のアップロード]** ボックスを使用して、証明書をアップロードします。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > この手順で指定する証明書は、バックエンドに存在する .pfx 証明書の公開キーである必要があります。 バックエンド サーバーにインストールされている (ルート証明書ではない) 証明書を Claim, Evidence, and Reasoning (CER) 形式でエクスポートし、この手順で使用します。 この手順によって、アプリケーション ゲートウェイにバックエンドがホワイトリスト登録されます。

8. **[保存]** を選択します。

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>既存のアプリケーション ゲートウェイでエンド ツー エンド SSL を有効化する

エンド ツー エンド SSL 暗号化を使用して既存のアプリケーション ゲートウェイを構成するには、リスナーでまず SSL 終了を有効化する必要があります。 これにより、クライアントとアプリケーション ゲートウェイの間の通信で SSL 暗号化が有効になります。 その後、アプリケーション ゲートウェイとバックエンド サーバーの間の通信で SSL 暗号化を有効化するために、HTTP 設定でバックエンド サーバーの証明書をホワイトリストに登録する必要があります。これにより、エンド ツー エンド SSL 暗号化が実現されます。

SSL 終了を有効化するために、HTTPS プロトコルおよび証明書を含むリスナーを使用する必要があります。 既存のリスナーのプロトコルを変更することはできません。 したがって、HTTPS プロトコルおよび証明書を含む既存のリスナーを使用するか、または新しいリスナーを作成するかを選択できます。 前者を選択した場合は、次に示す「**既存のアプリケーション ゲートウェイで SSL 終了を有効化する**」の手順を無視し、「**バックエンド サーバーの証明書をホワイトリストに登録する**」にそのまま進むことができます。 後者を選択した場合は、次の手順に従います。 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>既存のアプリケーション ゲートウェイで SSL 終了を有効化する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[リスナー]** を選択します。

3. 要件に応じて、**基本**リスナーと**マルチサイト** リスナーのどちらかを選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **[証明書]** ウィンドウが表示されます。

5. クライアントとアプリケーション ゲートウェイの間で SSL 終了に使用する PFX 証明書をアップロードします。

   > [!NOTE]
   > テスト目的で、自己署名証明書を使用してもかまいません。 運用環境のワークロードでは、自己署名証明書を使用しないでください。 [自己署名証明書の作成](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)方法を参照してください。

6. 要件に応じて、**リスナー**に必要なその他の設定を追加します。

7. **[OK]** を選択して保存します。

### <a name="whitelist-certificates-for-backend-servers"></a>バックエンド サーバーの証明書をホワイトリストに登録する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[HTTP 設定]** を選択します。 既存のバックエンド HTTP 設定で証明書をホワイトリストに登録するか、または新しい HTTP 設定を作成することができます。 次の手順では、既定の HTTP 設定 **appGatewayBackendHttpSettings** の証明書をホワイトリストに追加します。

3. **[appGatewayBackendHttpSettings]** を選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **[バックエンド認証証明書]** ウィンドウが表示されます。 

5. **[バックエンド認証証明書]** で、**[新規作成]** を選択します。

6. 適切な **[名前]** を入力します。

7. **[CER 証明書のアップロード]** ボックスを使用して、証明書をアップロードします。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > この手順で指定する証明書は、バックエンドに存在する .pfx 証明書の公開キーである必要があります。 バックエンド サーバーにインストールされている (ルート証明書ではない) 証明書を Claim, Evidence, and Reasoning (CER) 形式でエクスポートし、この手順で使用します。 この手順によって、アプリケーション ゲートウェイにバックエンドがホワイトリスト登録されます。

8. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)
