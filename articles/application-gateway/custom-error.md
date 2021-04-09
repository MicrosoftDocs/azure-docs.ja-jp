---
title: Azure Application Gateway のカスタム エラー ページを作成する
description: この記事では、Application Gateway のカスタム エラー ページを作成する方法について示します。 カスタム エラー ページでは、独自のブランディングとレイアウトを使用することができます。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 355caeb54f09797ae719f21401ceebb7d53d745a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592722"
---
# <a name="create-application-gateway-custom-error-pages"></a>Application Gateway のカスタム エラー ページを作成する

Application Gateway では、既定のエラー ページを表示する代わりに、カスタム エラー ページを作成することができます。 カスタム エラー ページでは、独自のブランディングとレイアウトを使用することができます。

たとえば、Web アプリが到達可能でない場合は、独自のメンテナンス ページを定義できます。 また、悪意のある要求が Web アプリに送信される場合は、未承認のアクセス ページを作成することができます。

次の 2 つのシナリオのカスタム エラー ページがサポートされています。

- **メンテナンス ページ** - 502 無効なゲートウェイ ページの代わりに、このカスタム エラー ページを送信します。 これは、Application Gateway にトラフィックをルーティングするバックエンドがないときに表示されます。 たとえば、予定メンテナンスがある場合、または予期しない問題がバックエンド プール アクセスに影響する場合です。
- **未承認のアクセス ページ** - 403 未承認のアクセス ページの代わりに、このカスタム エラー ページを送信します。 これは、Application Gateway WAF が悪意のあるトラフィックを検出し、そのトラフィックをブロックするときに表示されます。

バックエンド サーバーでエラーが発生した場合、未変更のまま呼び出し元に渡されます。 カスタム エラー ページは表示されません。 要求がバックエンドに到達できない場合に、Application Gateway はカスタム エラー ページを表示できます。

グローバル レベルとリスナー レベルで、カスタム エラー ページを定義できます。

- **グローバル レベル** - エラー ページは、その Application Gateway に展開されているすべての Web アプリのトラフィックに適用されます。
- **リスナー レベル** - エラー ページは、そのリスナーで受信されるトラフィックに適用されます。
- **両方** - リスナー レベルで定義されているカスタム エラー ページは、グローバル レベルで設定されているページよりも優先されます。

カスタム エラー ページを作成するには、以下が必要です。

- HTTP 応答状態コード。
- エラー ページの対応する保存先。 
- その場所にパブリックでアクセスできる Azure ストレージ BLOB。
- *.htm または *.html 拡張子タイプ。 

エラー ページのサイズは、1 MB 未満である必要があります。 エラー ページにリンクされているイメージがある場合は、パブリックにアクセスできる絶対 URL か、カスタム エラー ページにインライン base64 でエンコードされたイメージである必要があります。 同じ BLOB の場所に配置されたイメージへの相対リンクは現在サポートされていません。 

エラー ページを指定すると、Application Gateway はストレージ BLOB の場所からそのページをダウンロードし、ローカルの Application Gateway のキャッシュに保存します。 その後、エラー ページは Application Gateway から直接提供されます。 既存のカスタム エラー ページを変更するには、Application Gateway 構成内の別の BLOB の場所を指すようにする必要があります。 Application Gateway は、BLOB の場所を定期的にチェックして新しいバージョンをフェッチすることはありません。

## <a name="portal-configuration"></a>ポータル構成

1. ポータルの Application Gateway に移動し、Application Gateway を選択します。

    ![スクリーンショットはアプリケーション ゲートウェイの [概要] ページを示しています。](media/custom-error/ag-overview.png)
2. **[リスナー]** をクリックして、エラー ページを指定する特定のリスナーに移動します。

    ![Application Gateway のリスナー](media/custom-error/ag-listener.png)
3. リスナー レベルで 403 WAF エラー用のカスタム エラー ページまたは 502 メンテナンス ページを構成します。

    > [!NOTE]
    > Azure portal からのグローバル レベル カスタム エラー ページの作成は、現在サポートされていません。

4. 特定のエラー状態コード用の公的にアクセス可能な BLOB URL を指定し、 **[保存]** をクリックします。 これで、カスタム エラー ページで Application Gateway が構成されます。

   ![Application Gateway のエラー コード](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell 構成

Azure PowerShell を使用して、カスタム エラー ページを構成できます。 たとえば、グローバル カスタム エラー ページは次のようになります。

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

また、リスナー レベルのエラー ページは次のようになります。

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

詳しくは、「[Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror)」および「[Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Application Gateway 診断の詳細については、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」を参照してください。
