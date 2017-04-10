> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>はじめに
[IoT Hub ツインの概要][lnk-twin-tutorial]に関するページでは、"*タグ*" を使用してソリューション バックエンドからデバイス メタデータを設定する方法、"*報告されるプロパティ*" を使用してデバイス アプリからデバイスの状態を報告する方法、SQL に似た言語を使用してこの情報を照会する方法について学習しました。

このチュートリアルでは、"*必要なプロパティ*" と "*報告されるプロパティ*" を併せて使用し、デバイス アプリをリモートで構成する方法について説明します。 具体的には、デバイス ツインの報告されるプロパティと必要なプロパティを使用して、デバイス アプリケーション設定の複数手順の構成を有効にする方法のほか、ソリューション バックエンドですべてのデバイスにおけるこの操作の状態を表示できるようにする方法について説明します。 デバイス構成の役割の詳細については、[IoT Hub を使用したデバイス管理の概要][lnk-dm-overview]に関するページを参照してください。

大まかには、デバイス ツインを使用すると、ソリューション バックエンドは、特定のコマンドを送信する代わりに、管理対象デバイスに必要な構成を指定できます。 これにより、デバイスはその構成を更新する最善の方法を確立します (特定のデバイスの状態が特定のコマンドを直ちに実行できるかどうかに影響する IoT シナリオでは非常に重要)。同時に、更新プロセスの現在の状態と潜在的なエラー状態をソリューション バックエンドに継続的に報告します。 このパターンは、多数のデバイスの管理に役立ちます。これは、すべてのデバイスにおける構成プロセスの状態を、ソリューション バックエンドで完全に表示できるためです。

> [!NOTE]
> (ユーザー制御アプリからファンをオンにするなど) より対話的な方法でデバイスを制御するシナリオについては、[ダイレクト メソッド][lnk-methods]の使用を検討してください。
> 
> 

このチュートリアルでは、ソリューション バックエンドによってターゲット デバイスのテレメトリ構成が変更されます。その結果、デバイス アプリは、複数手順のプロセスに従って構成の更新 (ソフトウェア モジュールの再起動など) を適用します。このチュートリアルでは単純な遅延でこれをシミュレートします。

ソリューション バックエンドでは、次の方法でデバイス ツインの必要なプロパティに構成が格納されます。

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [!NOTE]
> 構成は複雑なオブジェクトである可能性があるため、比較しやすいように、構成には通常一意の ID (ハッシュまたは [GUID][lnk-guid]) が割り当てられます。
> 
> 

デバイス アプリは、必要なプロパティ **telemetryConfig** を報告されるプロパティに反映して、現在の構成を報告します。

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

報告される **telemetryConfig** に追加のプロパティ **status** があることに注意してください。このプロパティは、構成の更新プロセスの状態を報告するために使用されます。

望ましい構成が新たに届いたら、デバイス アプリは情報を変更して保留中の構成を報告します。

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

しばらくしてから、デバイス アプリは、上記のプロパティを更新してこの操作の失敗または成功を報告します。
すべてのデバイスにおける構成プロセスの状態は、ソリューション バックエンドでいつでも照会できることに注目してください。

このチュートリアルでは、次の操作方法について説明します。

* ソリューション バックエンドから構成の更新を受け取り、構成の更新プロセスで複数の更新を "*報告されるプロパティ*" として報告する、シミュレーション対象デバイス アプリを作成します。
* デバイスの望ましい構成を更新し、構成の更新プロセスを照会するバックエンド アプリを作成します。

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier


<!--HONumber=Feb17_HO3-->


