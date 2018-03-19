---
title: "SSL オフロード用の証明書の更新 - Azure Application Gateway - PowerShell | Microsoft Docs"
description: "このページでは、Azure リソース マネージャーを使用して、Application Gateway の SSL オフロードで使用する証明書を更新する方法について説明します。"
documentationcenter: na
services: application-gateway
author: ShuheiUda
manager: na
editor: na
ms.assetid: na
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/29/2017
ms.author: shuda
translationtype: Human Translation
ms.sourcegitcommit: na
ms.openlocfilehash: na
ms.lasthandoff: 06/29/2017


---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Azure リソース マネージャーを使用した Application Gateway の SSL オフロード用証明書の更新

Azure Application Gateway では、Secure Sockets Layer (SSL) セッションを解除するように構成し、Web サーバーに代わってゲートウェイで CPU 負荷のかかる SSL 解除処理を代行させることができます。 

SSL オフロードを使用するには、Azure Application Gateway に証明書をアップロードする必要があります。また、アップロードした証明書に設定された有効期限が切れる前に、新たに証明書を発行して更新を行うことが必要です。

## <a name="before-you-begin"></a>開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. 既存の Application Gateway で SSL オフロードの機能が既に構成されている必要があります。未構成の場合は、[SSL オフロード用の Application Gateway の構成](application-gateway-ssl-arm.md)の手順に沿って構成できます。
3. Application Gateway にアップロードする証明書は PFX 形式のファイル (PKCS #12 とも呼ばれます) として用意する必要があります。

## <a name="update-ssl-certificate-for-application-gateway"></a>アプリケーション ゲートウェイが SSL オフロードに使用する証明書を更新します

### <a name="step-1"></a>手順 1

Azure アカウントにログインします。

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>手順 2.

アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>手順 3.

使用する Azure サブスクリプションを選択します。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>手順 4.

既存の Application Gateway の構成情報を取得します。

```powershell
$appgw = Get-AzureRmApplicationGateway -Name "appgwtest" -ResourceGroupName "appgw-rg"
```
### <a name="step-5"></a>手順 5.

既存の証明書名と同一の名称で、更新する証明書ファイルを設定します。

```powershell
$appgw = Set-AzureRmApplicationGatewaySslCertificate -Name "existing certificate name" -ApplicationGateway $appgw -CertificateFile "<full path for certificate file>" -Password "<password>"
```

### <a name="step-6"></a>手順 6.

`Set-AzureRmApplicationGateway` コマンドを使用して、新しい構成を保存します。

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```


## <a name="next-steps"></a>次のステップ

Azure Application Gateway で SSL オフロードを構成する方法を学習するには、 [SSL オフロードの構成](application-gateway-ssl-portal.md)
