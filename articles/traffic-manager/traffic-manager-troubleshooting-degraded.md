---
title: Azure Traffic Manager での機能低下状態のトラブルシューティング
description: Traffic Manager プロファイルが機能低下状態と示されるときのトラブルシューティング方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: duau
ms.openlocfilehash: b76eab5771d724e4f0ec56b7d5acd5cf5f91edc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183457"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Azure Traffic Manager での機能低下状態のトラブルシューティング

この記事では、機能低下状態になっている Azure Traffic Manager プロファイルをトラブルシューティングする方法について説明します。 Azure Traffic Manager の機能低下状態をトラブルシューティングする最初の手順として、ログを有効にします。  詳細については、[リソース ログの有効化](./traffic-manager-diagnostic-logs.md)に関するページを参照してください。 このシナリオでは、.cloudapp.net ホステッド サービスの一部を参照するように Traffic Manager プロファイルを構成しているとします。 Traffic Manager の正常性が **機能低下** 状態の場合は､1 つまたは複数のエンドポイントのステータスも **機能低下** している可能性があります｡

![エンドポイントの機能低下状態](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Traffic Manager の正常性が **Inactive** 状態の場合は､両方のエンドポイントが **Disabled** されている可能性があります｡

![Traffic Manager の Inactive 状態](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Traffic Manager のプローブについて

* Traffic Manager は、プローブがプローブ パスから HTTP 200 応答を受け取った場合にのみエンドポイントがオンラインになると見なします。 アプリケーションから他の HTTP 応答コードが返された場合は、その応答コードを Traffic Manager プロファイルの [[状態コードの範囲が必要です]](./traffic-manager-monitoring.md#configure-endpoint-monitoring) に追加する必要があります。
* 30x リダイレクト応答は、Traffic Manager プロファイルの [[状態コードの範囲が必要です]](./traffic-manager-monitoring.md#configure-endpoint-monitoring) でこれを有効な応答コードとして指定しない限り、エラーとして扱われます。 Traffic Manager は、リダイレクト ターゲットをプローブしません。
* HTTPs プローブの場合、証明書エラーは無視されます。
* 200 が返される限り、プローブ パスの実際の内容は関係ありません。 "/favicon.ico" などの静的コンテンツの URL へのプローブは、よくある手法です。 ASP ページなどの動的なコンテンツは、アプリケーションが正常な場合であっても、常に 200 を返すとは限りません。
* ベスト プラクティスとしては、サイトが稼動しているかどうかを判断するに足る十分なロジックを持っているパスに、プローブ パスを設定します。 前の例では、パスを "/favicon.ico" に設定することで、w3wp.exe が応答するかどうかのテストのみを実行しています。 このプローブでは、Web アプリケーションが正常であることが示されない可能性があります。 お勧めなのは、サイトの正常性を判断するロジックが組み込まれた "/Probe.aspx" などへのパスを設定する方法です。 たとえば、CPU 使用率のパフォーマンス カウンターを使用したり、失敗した要求の数を測定したりできます。 また、データベースのリソースやセッション状態にアクセスを試みて、Web アプリケーションが動作していることを確認することもできます。
* プロファイル内のすべてのエンドポイントが機能低下している場合、Traffic Manager はすべてのエンドポイントを正常として扱い、トラフィックをすべてのエンドポイントにルーティングします。 この動作により、プローブ メカニズムの問題が発生しても、サービスは完全には停止しなくなります。

## <a name="troubleshooting"></a>トラブルシューティング

プローブのエラーをトラブルシューティングするには、プローブ URL からの HTTP 状態コード戻り値を表示するツールが必要になります。 未加工の HTTP 応答を表示するツールは多数あります。

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

また、Internet Explorer の F12 デバッグ ツールの [ネットワーク] タブを使用して、HTTP 応答を表示することもできます。

この例では、弊社のプローブ URL (http:\//watestsdp2008r2.cloudapp.net:80/Probe) からの応答の表示を期待します。 次の PowerShell の例で、問題を示します。

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

出力例:

```output
StatusCode StatusDescription
---------- -----------------
        301 Moved Permanently
```

リダイレクト応答を受け取ったことがわかります。 既に説明したように、200 以外の StatusCode はすべて障害と見なされます。 Traffic Manager で、エンドポイントの状態が "オフライン" に変更されます。 問題を解決するには、Web サイトの構成をチェックして、適切な StatusCode がプローブ パスから返されることを確認します。 200 を返すパスを指すように、Traffic Manager のプローブを再構成します。

プローブで HTTPS プロトコルを使用している場合は、証明書のチェックを無効にして、テストの際の SSL/TLS エラーを回避することが必要になる場合があります。 次の PowerShell ステートメントは、現在の PowerShell セッションの証明書の検証を無効にします。

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>次の手順

[Traffic Manager のトラフィック ルーティング方法について](traffic-manager-routing-methods.md)

[Traffic Manager について](traffic-manager-overview.md)

[Cloud Services](/previous-versions/azure/jj155995(v=azure.100))

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Traffic Manager の操作 (REST API リファレンス)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager コマンドレット][1]

[1]: /powershell/module/az.trafficmanager