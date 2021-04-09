---
title: Linux で Azure Service Fabric アプリをデバッグする
description: ローカルの Linux 開発用コンピューターで Service Fabric サービスを監視し、診断する方法について説明します。
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 523cb0d1a8e8f322c1936f1fe52a954399b2acc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88999769"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>ローカル Linux コンピューター開発のセットアップでサービスを監視して診断する


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

監視、検出、診断、トラブルシューティングにより、ユーザー エクスペリエンスの中断を最小限に抑えてサービスを続行できます。 監視と診断は、実際のデプロイされた運用環境で重要です。 サービスの開発中に類似するモデルを採用することにより、運用環境に移行するときに診断のパイプラインが動作します。 Service Fabric により、サービスの開発者は、1 台のコンピューターのローカルの開発のセットアップと実際の運用クラスターのセットアップの両方でシームレスに操作できる診断を簡単に実装できます。


## <a name="debugging-service-fabric-java-applications"></a>Service Fabric の Java アプリケーションのデバッグ

Java アプリケーションの場合は、 [複数のログ記録フレームワーク](https://en.wikipedia.org/wiki/Java_logging_framework) を利用できます。 `java.util.logging` は JRE の既定のオプションであるため、 [GitHub のコード例](https://github.com/Azure-Samples/service-fabric-java-getting-started)にも使用されています。 以降では、 `java.util.logging` フレームワークを構成する方法について説明します。

java.util.logging を使用すると、アプリケーションのログを、メモリ、出力ストリーム、コンソール、ファイル、またはソケットにリダイレクトできます。 これらのそれぞれのオプションに対して、フレームワークで既定のハンドラーが既に提供されています。 `app.properties` ファイルを作成すると、すべてのログをローカル ファイルにリダイレクトするようにアプリケーションのファイル ハンドラーを構成できます。

次のコード スニペットに構成例を示します。

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

`app.properties` ファイルが指しているフォルダーが存在する必要があります。 `app.properties` ファイルを作成した後は、エントリ ポイントのスクリプトである `<applicationfolder>/<servicePkg>/Code/` フォルダー内の `entrypoint.sh` を修正して、`java.util.logging.config.file` プロパティを `app.properties` ファイルに設定する必要があります。 エントリは、次のスニペットのようになります。

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


この構成を使用すると、 `/tmp/servicefabric/logs/`にログが輪番方式で収集されます。 このケースでは、ログ ファイルの名前は mysfapp%u.%g.log になります。
* **%u** は同時 Java プロセス間の競合を解決する一意の番号です。
* **%g** はログのローテーションを区別する世代番号です。

ハンドラーが明示的に構成されていない場合、既定でコンソール ハンドラーが登録されます。 /var/log/syslog の syslog のログを表示できます。

詳細については、 [GitHub のコード例](https://github.com/Azure-Samples/service-fabric-java-getting-started)を参照してください。


## <a name="debugging-service-fabric-c-applications"></a>Service Fabric の C# アプリケーションのデバッグ


Linux で CoreCLR アプリケーションをトレースするときには、複数のフレームワークを使用できます。 詳細については、[ロギング用の .NET 拡張機能](https://github.com/dotnet/extensions/tree/master/src/Logging)に関するページをご覧ください。  EventSource は、C# 開発者にとってわかりやすいので、この資料では、Linux での CoreCLR サンプルでのトレースに EventSource を使用します。

最初の手順では、メモリ、出力ストリーム、またはコンソール ファイルにログを書き込むことができるように、System.Diagnostics.Tracing を含めます。  EventSource を使用したログ記録の場合は、project.json に、次のプロジェクトを追加します。

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

カスタム EventListener を使用して、サービス イベントをリッスンし、それらを適切にトレース ファイルにリダイレクトすることができます。 次のコード スニペットは、EventSource とカスタム EventListener を使用したログ記録の実装例を示しています。


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


上記のスニペットは、`/tmp/MyServiceLog.txt` 内のファイルにログを出力します。 このファイル名は、適切に更新する必要があります。 ログをコンソールにリダイレクトする場合は、カスタマイズされた EventListener クラスで次のスニペットを使用します。

```csharp
public static TextWriter Out = Console.Out;
```

「[C# Samples](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)」(C# サンプル) のサンプルでは、EventSource とカスタム EventListener を使用してイベントをファイルにログ記録しています。



## <a name="next-steps"></a>次のステップ
アプリケーションに追加した同じトレース コードで Azure のクラスター上のアプリケーションの診断を行うこともできます。 ツールの各オプションや、その設定方法について説明した記事を参照してください。
* [Azure Diagnostics でログを収集する方法](./service-fabric-diagnostics-event-aggregation-lad.md)
