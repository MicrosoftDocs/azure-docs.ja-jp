---
title: Azure Diagnostics で Cloud Services アプリケーションのフローをトレースする | Microsoft Docs
description: トレース メッセージを Azure アプリケーションに追加することにより、デバッグ、パフォーマンス測定、監視、トラフィック分析などを容易に行えるようになります。
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: gwallace
ms.openlocfilehash: e3e34ff9b5ce1c3a7b45468d22faddddf0c9a913
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359151"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Azure Diagnostics で Cloud Services アプリケーションのフローをトレースする
トレースは、アプリケーションの稼働中にアプリケーションの実行を監視する手段です。 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace)、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug)、および [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) の各クラスを使用すると、エラーとアプリケーションの実行に関する情報をログ、テキスト ファイル、またはその他のデバイスに記録して、後で分析することができます。 トレースの詳細については、「 [アプリケーションのトレースとインストルメント](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications)」を参照してください。

## <a name="use-trace-statements-and-trace-switches"></a>トレース ステートメントとトレース スイッチを使用する
Cloud Services アプリケーションにトレース機能を実装するには、 [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) をアプリケーションの構成に追加し、アプリケーション コード内で System.Diagnostics.Trace または System.Diagnostics.Debug を呼び出します。 worker ロールでは構成ファイル *app.config* を使用し、Web ロールでは構成ファイル *web.config* を使用します。 Visual Studio テンプレートを使用して、ホストされるサービスを新規に作成する場合は、Azure Diagnostics が自動的にプロジェクトに追加され、さらに、追加したロール用の構成ファイルに DiagnosticMonitorTraceListener が追加されます。

トレース ステートメントを配置する方法の詳細については、「[How to: Add Trace Statements to Application Code (トレース ステートメントをアプリケーション コードに追加する方法)](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)」を参照してください。

[トレース スイッチ](/dotnet/framework/debug-trace-profile/trace-switches) をコード内に配置すると、トレースを行うかどうか、対象範囲をどうするかを制御することができます。 これにより、運用環境でアプリケーションの状態を監視できます。 このことは、複数のコンピューターで実行される複数のコンポーネントを使用するビジネス アプリケーションでは特に重要です。 詳細については、[方法:トレース スイッチを構成する方法](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches)に関するページを参照してください。

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Azure アプリケーションでトレース リスナーを構成する
Trace、Debug、TraceSource では、送信されるメッセージを収集および記録するように "リスナー" をセットアップする必要があります。 リスナーでは、トレース メッセージを収集、格納、およびルーティングします。 リスナーは、トレース出力を、ログ、ウィンドウ、テキスト ファイルなど、適切なターゲットに転送します。 Azure Diagnostics では、 [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) クラスが使用されます。

次の手順を完了する前に、Azure 診断モニターを初期化する必要があります。 そのためには、「 [Microsoft Azure での診断の有効化](cloud-services-dotnet-diagnostics.md)」を参照してください。

Visual Studio で提供されるテンプレートを使用すると、リスナーの構成が自動的に追加されるので注意してください。

### <a name="add-a-trace-listener"></a>トレース リスナーを追加する
1. ロールに応じて web.config または app.config ファイルを開きます。
2. 次のコードをファイルに追加します。 Version 属性を変更して、参照するアセンブリのバージョン番号を使用します。 Azure SDK の更新プログラムがある場合を除き、アセンブリのバージョンは Azure SDK のリリースごとに必ずしも変更されるわけではありません。
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Microsoft.WindowsAzure.Diagnostics アセンブリへのプロジェクト参照があることを確認してください。 参照先の Microsoft.WindowsAzure.Diagnostics アセンブリのバージョンと一致するように上記の xml のバージョン番号を更新します。
   > 
   > 
3. 構成ファイルを保存します。

リスナーの詳細については、「 [トレース リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)」を参照してください。

リスナーを追加する手順を完了すると、コードにトレース ステートメントを追加できます。

### <a name="to-add-trace-statement-to-your-code"></a>コードにトレース ステートメントを追加するには
1. アプリケーション用のソース ファイルを開きます。 たとえば、worker ロールまたは Web ロール用の \<RoleName>.cs ファイルを開きます。
2. 次の using ステートメントを追加します (まだ追加されていない場合)。
    ```
        using System.Diagnostics;
    ```
3. Trace ステートメントを追加し、アプリケーションの状態に関する情報をキャプチャします。 Trace ステートメントの出力は、さまざまな方法で書式設定できます。 詳細については、[方法:Add Trace Statements to Application Code (トレース ステートメントをアプリケーション コードに追加する方法)](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)」を参照してください。
4. ソース ファイルを保存します。

