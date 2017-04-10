---
title: "Azure 診断で Cloud Services アプリケーションのフローをトレースする | Microsoft Docs"
description: "トレース メッセージを Azure アプリケーションに追加することにより、デバッグ、パフォーマンス測定、監視、トラフィック分析などを容易に行えるようになります。"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 35b4a4270846c54a1ca760e803ef7adba60cf03b


---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Azure 診断で Cloud Services アプリケーションのフローをトレースする
トレースは、アプリケーションの稼働中にアプリケーションの実行を監視する手段です。 [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)、[System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx)、および [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) の各クラスを使用すると、エラーとアプリケーションの実行に関する情報をログ、テキスト ファイル、またはその他のデバイスに記録して、後で分析することができます。 トレースの詳細については、「 [アプリケーションのトレースとインストルメント](https://msdn.microsoft.com/library/zs6s4h68.aspx)」を参照してください。

## <a name="use-trace-statements-and-trace-switches"></a>トレース ステートメントとトレース スイッチを使用する
Cloud Services アプリケーションにトレース機能を実装するには、 [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) をアプリケーションの構成に追加し、アプリケーション コード内で System.Diagnostics.Trace または System.Diagnostics.Debug を呼び出します。 worker ロールでは構成ファイル *app.config* を使用し、Web ロールでは構成ファイル *web.config* を使用します。 Visual Studio テンプレートを使用して、ホストされるサービスを新規に作成する場合は、Azure 診断が自動的にプロジェクトに追加され、さらに、追加したロール用の構成ファイルに DiagnosticMonitorTraceListener が追加されます。

トレース ステートメントを配置する方法の詳細については、「 [トレース ステートメントをアプリケーション コードに追加する方法](https://msdn.microsoft.com/library/zd83saa2.aspx)」を参照してください。

[トレース スイッチ](https://msdn.microsoft.com/library/3at424ac.aspx) をコード内に配置すると、トレースを行うかどうか、対象範囲をどうするかを制御することができます。 これにより、運用環境でアプリケーションの状態を監視できます。 このことは、複数のコンピューターで実行される複数のコンポーネントを使用するビジネス アプリケーションでは特に重要です。 詳細については、「 [トレース スイッチの構成方法](https://msdn.microsoft.com/library/t06xyy08.aspx)」を参照してください。

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Azure アプリケーションでトレース リスナーを構成する
Trace、Debug、TraceSource では、送信されるメッセージを収集および記録するように "リスナー" をセットアップする必要があります。 リスナーでは、トレース メッセージを収集、格納、およびルーティングします。 リスナーは、トレース出力を、ログ、ウィンドウ、テキスト ファイルなど、適切なターゲットに転送します。 Azure 診断では、 [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) クラスが使用されます。

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

リスナーの詳細については、「 [トレース リスナー](https://msdn.microsoft.com/library/4y5y10s7.aspx)」を参照してください。

リスナーを追加する手順を完了すると、コードにトレース ステートメントを追加できます。

### <a name="to-add-trace-statement-to-your-code"></a>コードにトレース ステートメントを追加するには
1. アプリケーション用のソース ファイルを開きます。 たとえば、worker ロールまたは Web ロール用の <RoleName>.cs ファイルを開きます。
2. 次の using ステートメントを追加します (まだ追加されていない場合)。
    ```
        using System.Diagnostics;
    ```
3. Trace ステートメントを追加し、アプリケーションの状態に関する情報をキャプチャします。 Trace ステートメントの出力は、さまざまな方法で書式設定できます。 詳細については、「 [トレース ステートメントをアプリケーション コードに追加する方法](https://msdn.microsoft.com/library/zd83saa2.aspx)」を参照してください。
4. ソース ファイルを保存します。




<!--HONumber=Nov16_HO3-->


