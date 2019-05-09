---
title: Azure Application Gateway のカスタム エラー ページを作成する
description: この記事では、Application Gateway のカスタム エラー ページを作成する方法について示します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/14/2019
ms.author: victorh
ms.openlocfilehash: abfe33ff679bef125d9bf5b78e1790a1a4c64863
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301606"
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

    ![ag-overview](media/custom-error/ag-overview.png)
2. **[リスナー]** をクリックして、エラー ページを指定する特定のリスナーに移動します。

    ![Application Gateway のリスナー](media/custom-error/ag-listener.png)
3. リスナー レベルで 403 WAF エラー用のカスタム エラー ページまたは 502 メンテナンス ページを構成します。

    > [!NOTE]
    > Azure portal からのグローバル レベル カスタム エラー ページの作成は、現在サポートされていません。

4. 特定のエラー状態コード用の公的にアクセス可能な BLOB URL を指定し、**[保存]** をクリックします。 これで、カスタム エラー ページで Application Gateway が構成されます。

   ![Application Gateway のエラー コード](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell 構成

Azure PowerShell を使用して、カスタム エラー ページを構成できます。 たとえば、グローバル カスタム エラー ページは次のようになります。

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

また、リスナー レベルのエラー ページは次のようになります。

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

詳しくは、「[Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0)」および「[Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0)」をご覧ください。

## <a name="next-steps"></a>次の手順

Application Gateway 診断の詳細については、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」を参照してください。