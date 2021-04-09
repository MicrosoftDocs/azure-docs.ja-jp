---
title: Azure Functions での HTTP 出力バインド
description: Azure Functions で HTTP 応答を返す方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "84697444"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions での HTTP 出力バインド

HTTP 要求送信者に応答するには、HTTP 出力バインドを使用します。 このバインディングには、HTTP トリガーが必要です。このバインディングを使用すると、トリガーの要求に関連付けられている応答をカスタマイズできます。

HTTP によってトリガーされる関数の既定の戻り値は次のとおりです。

- Functions 2.x 以降は、本文が空の `HTTP 204 No Content`
- Functions 1.x は、本文が空の `HTTP 200 OK`

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。 C# クラス ライブラリには、*function.json* のこれらのプロパティに対応する属性プロパティはありません。

|プロパティ  |説明  |
|---------|---------|
| **type** |`http` に設定する必要があります。 |
| **direction** | `out` に設定する必要があります。 |
| **name** | 応答の関数コードで使用される変数名、または戻り値を使うことを示す `$return`。 |

## <a name="usage"></a>使用法

HTTP 応答を送信するには、言語標準の応答パターンを使います。 C# またはで C# スクリプトでは、関数の戻り値の型を `IActionResult` または `Task<IActionResult>` にします。 C# の場合、戻り値の属性は必要ありません。

応答の例のため、[トリガー例](./functions-bindings-http-webhook-trigger.md#example)を参照してください。

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインドに使用可能なグローバル構成設定について説明します。 次の host.json ファイルの例には、このバインドのバージョン 2.x 以降の設定のみが含まれています。 バージョン 2.x 以降でのグローバル構成設定の詳細については、「[Azure Functions の host.json のリファレンス](functions-host-json.md)」を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md#http)」を参照してください。

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
| customHeaders|なし|HTTP 応答でカスタム ヘッダーを設定できます。 前の例では、コンテンツ タイプのスニッフィングを避けるために、`X-Content-Type-Options` ヘッダーを応答に追加しています。 |
|dynamicThrottlesEnabled|true<sup>\*</sup>|この設定を有効にすると、要求処理パイプラインが、`connections/threads/processes/memory/cpu/etc` のようなシステム パフォーマンス カウンターを定期的にチェックし、それらのカウンターのいずれかが組み込みの上限しきい値 (80%) を超えている場合は、カウンターが正常なレベルに戻るまで、要求は `429 "Too Busy"` 応答で拒否されます。<br/><sup>\*</sup>従量課金プランの既定値は、`true` です。 専用プランの既定値は、`false` です。|
|hsts|無効|`isEnabled` が `true` に設定されている場合、[`HstsOptions` クラス](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)で定義されているように、[.NET Core の HTTP Strict Transport Security (HSTS) 動作](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts)が適用されます。 上の例では、[`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) プロパティも 10 日間に設定されています。 `hsts` のサポートされているプロパティは次のとおりです。 <table><tr><th>プロパティ</th><th>説明</th></tr><tr><td>excludedHosts</td><td>HSTS ヘッダーが追加されていないホスト名の文字列配列。</td></tr><tr><td>includeSubDomains</td><td>Strict-Transport-Security ヘッダーの includeSubDomain パラメーターが有効になっているかどうかを示すブール値。</td></tr><tr><td>maxAge</td><td>Strict-Transport-Security ヘッダーの最長有効期間パラメーターを定義する文字列。</td></tr><tr><td>preload</td><td>Strict-Transport-Security ヘッダーの事前読み込みパラメーターが有効になっているかどうかを示すブール値。</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|並列で実行される HTTP 関数の最大数。 この値によりコンカレンシーを制御でき、リソース使用率の管理に役立ちます。 たとえば、多くのシステム リソース (メモリ、CPU、ソケット) を消費する HTTP 関数があった場合、コンカレンシーが高すぎると問題が発生します。 または、サードパーティのサービスに対して要求を送信する関数があり、その呼び出し速度を制限する必要がある場合です。 このような場合は、調整を適用することができます。 <br/><sup>*</sup>従量課金プランでの既定値は 100 です。 専用プランでの既定値は無制限です (`-1`)。|
|maxOutstandingRequests|200<sup>\*</sup>|特定の時点で保持される未処理の要求の最大数。 この制限には、キューに格納され、まだ実行が開始されていない要求と、処理中の実行が含まれます。 この制限を超える受信要求は、429 "Too Busy" 応答で拒否されます。 これにより、呼び出し元は時間ベースの再試行戦略を採用でき、要求の最大待機時間の制御にも役立ちます。 この設定は、スクリプト ホストの実行パス内で発生するキューのみを制御します。 ASP.NET 要求キューなどの他のキューは有効なままで、この設定の影響を受けません。 <br/><sup>\*</sup>従量課金プランでの既定値は 200 です。 専用プランでの既定値は無制限です (`-1`)。|
|routePrefix|api|すべてのルートに適用されるルート プレフィックス。 既定のプレフィックスを削除するには、空の文字列を使用します。 |

## <a name="next-steps"></a>次のステップ

- [HTTP 要求から関数を実行する](./functions-bindings-http-webhook-trigger.md)