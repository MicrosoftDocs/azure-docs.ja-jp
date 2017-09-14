# 概要
## [Azure と IoT](iot-hub-what-is-azure-iot.md)
## [Azure IoT Hub とは](iot-hub-what-is-iot-hub.md)
## [デバイス管理の概要](iot-hub-device-management-overview.md)

# [作業の開始](iot-hub-get-started.md)

## デバイスのセットアップ
### [PC でデバイスをシミュレートする](iot-hub-get-started-simulated.md)
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.JS](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### [オンライン シミュレーターを使用する](iot-hub-raspberry-pi-web-simulator-get-started.md)

### [物理デバイスを使用する](iot-hub-get-started-physical.md)
#### [Python での Raspberry Pi](iot-hub-raspberry-pi-kit-python-get-started.md)
#### [Node.js での Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [C での Raspberry Pi](iot-hub-raspberry-pi-kit-c-get-started.md)

#### [Arduino での MXChip IoT DevKit](iot-hub-arduino-iot-devkit-az3166-get-started.md)

#### [Node.js での Intel Edison](iot-hub-intel-edison-kit-node-get-started.md)
#### [C での Intel Edison](iot-hub-intel-edison-kit-c-get-started.md)

#### [Arduino IDE での Adafruit Feather HUZZAH ESP8266](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [Arduino IDE での Sparkfun ESP8266 Thing Dev](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [Arduino IDE での Adafruit Feather M0](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

#### IoT Gateway Starter Kit の使用
##### [ゲートウェイとしての Intel NUC のセットアップ](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
##### [IoT Hub へのゲートウェイの接続](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
##### [ゲートウェイを使用したデータ変換](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

## IoT の拡張シナリオ
### [iothub-explorer を使用したクラウド デバイス メッセージングの管理](iot-hub-explorer-cloud-device-messaging.md)
### [Azure データ ストレージへの IoT Hub メッセージの保存](iot-hub-store-data-in-azure-table-storage.md)
### [Power BI でのデータ視覚化](iot-hub-live-data-visualization-in-power-bi.md)
### [Web Apps でのデータ視覚化](iot-hub-live-data-visualization-in-web-apps.md)
### [Azure Machine Learning を使用した天気予報](iot-hub-weather-forecast-machine-learning.md)
### [iothub-explorer を使用したデバイス管理](iot-hub-device-management-iothub-explorer.md)
### [Logic Apps を使用したリモートの監視と通知](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

# 方法
## プラン
### [IoT Hub と Event Hubs の比較](iot-hub-compare-event-hubs.md)
### [ソリューションをスケール調整する](iot-hub-scaling.md)
### [高可用性と障害復旧](iot-hub-ha-dr.md)
### [その他のプロトコルのサポート](iot-hub-protocol-gateway.md)
## [開発](iot-hub-how-to.md)
### [開発者ガイド](iot-hub-devguide.md)
#### [デバイスからクラウドの機能ガイド](iot-hub-devguide-d2c-guidance.md)
#### [クラウドからデバイスの機能ガイド](iot-hub-devguide-c2d-guidance.md)
#### [メッセージを送受信する](iot-hub-devguide-messaging.md)
##### [デバイスからクラウドへのメッセージを IoT Hub に送信する](iot-hub-devguide-messages-d2c.md)
##### [デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る](iot-hub-devguide-messages-read-builtin.md)
##### [デバイスからクラウドへのメッセージにカスタム エンドポイントとルーティング ルールを使用する](iot-hub-devguide-messages-read-custom.md)
##### [クラウドからデバイスへのメッセージを IoT Hub から送信する](iot-hub-devguide-messages-c2d.md)
##### [IoT Hub メッセージを作成し、読み取る](iot-hub-devguide-messages-construct.md)
##### [通信プロトコルの選択](iot-hub-devguide-protocols.md)
#### [デバイスからのファイルをアップロードする](iot-hub-devguide-file-upload.md)
#### [デバイス ID を管理する](iot-hub-devguide-identity-registry.md)
#### [IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)
#### [デバイス ツインを理解する](iot-hub-devguide-device-twins.md)
#### [デバイスでダイレクト メソッドを呼び出す](iot-hub-devguide-direct-methods.md)
#### [複数デバイスでのジョブをスケジュール設定する](iot-hub-devguide-jobs.md)
#### [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)
#### [クエリ言語](iot-hub-devguide-query-language.md)
#### [クォータとスロットル](iot-hub-devguide-quotas-throttling.md)
#### [価格の例](iot-hub-devguide-pricing.md)
#### [デバイス SDK とサービス SDK](iot-hub-devguide-sdks.md)
#### [MQTT サポート](iot-hub-mqtt-support.md)
#### [用語集](iot-hub-devguide-glossary.md)
### [C 用 Azure IoT device SDK の使用](iot-hub-device-sdk-c-intro.md)
#### [IoTHubClient の使用](iot-hub-device-sdk-c-iothubclient.md)
#### [シリアライザーの使用](iot-hub-device-sdk-c-serializer.md)
### デバイスからクラウドへのメッセージの処理
#### [.NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
### C2D メッセージの送信
#### [.NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.JS](iot-hub-node-node-c2d.md)
### [デバイスからファイルをアップロードする](iot-hub-csharp-csharp-file-upload.md)
### デバイス ツインを使ってみる
#### [Node.js バックエンド/Node.js デバイス](iot-hub-node-node-twin-getstarted.md)
#### [.NET バックエンド/Node.js デバイス](iot-hub-csharp-node-twin-getstarted.md)
#### [.NET バックエンド/.NET デバイス](iot-hub-csharp-csharp-twin-getstarted.md)
#### [Java バックエンド/Java デバイス](iot-hub-java-java-twin-getstarted.md)
### ダイレクト メソッドの使用
#### [Node.js バックエンド/Node.js デバイス](iot-hub-node-node-direct-methods.md)
#### [.NET バックエンド/Node.js デバイス](iot-hub-csharp-node-direct-methods.md)
#### [.NET バックエンド/.NET デバイス](iot-hub-csharp-csharp-direct-methods.md)
#### [Java バックエンド/Java デバイス](iot-hub-java-java-direct-methods.md)
### デバイス管理の開始
#### [Node.js バックエンド/Node.js デバイス](iot-hub-node-node-device-management-get-started.md)
#### [.NET バックエンド/Node.js デバイス](iot-hub-csharp-node-device-management-get-started.md)
#### [Java バックエンド/Java デバイス](iot-hub-java-java-device-management-getstarted.md)
### ツインのプロパティの使用方法
#### [Node.js バックエンド/Node.js デバイス](iot-hub-node-node-twin-how-to-configure.md)
#### [.NET バックエンド/Node.js デバイス](iot-hub-csharp-node-twin-how-to-configure.md)
#### [.NET バックエンド/.NET デバイス](iot-hub-csharp-csharp-twin-how-to-configure.md)
### デバイス ジョブを使用したデバイスのファームウェアの更新
#### [Node バックエンド/Node デバイス](iot-hub-node-node-firmware-update.md)
#### [.NET バックエンド/Node.js デバイス](iot-hub-csharp-node-firmware-update.md)
### ジョブのスケジュールとブロードキャスト
#### [Node.js バックエンド/Node.js デバイス](iot-hub-node-node-schedule-jobs.md)
#### [.NET バックエンド/Node.js デバイス](iot-hub-csharp-node-schedule-jobs.md)
#### [Java](iot-hub-java-java-schedule-jobs.md)
## 管理
### IoT Hub の作成 
#### [ポータルの使用](iot-hub-create-through-portal.md)
#### [PowerShell の使用](iot-hub-create-using-powershell.md)
#### [CLI 2.0 の使用](iot-hub-create-using-cli.md)
#### [CLI の使用](iot-hub-create-using-cli-nodejs.md)
#### [REST API の使用](iot-hub-rm-rest.md)
#### [PowerShell からテンプレートを使用する](iot-hub-rm-template-powershell.md)
#### [.NET からテンプレートを使用する](iot-hub-rm-template.md)
### ファイルのアップロードを構成する
#### [ポータルの使用](iot-hub-configure-file-upload.md)
#### [PowerShell の使用](iot-hub-configure-file-upload-powershell.md)
#### [CLI 2.0 の使用](iot-hub-configure-file-upload-cli.md)
### [IoT デバイスの一括管理](iot-hub-bulk-identity-mgmt.md)
### [使用状況のメトリック](iot-hub-metrics.md)
### [操作の監視](iot-hub-operations-monitoring.md)
### [IP フィルター処理を構成する](iot-hub-ip-filtering.md)
## セキュリティ保護
### [徹底的なセキュリティ](iot-hub-security-ground-up.md)
### [セキュリティのベスト プラクティス](iot-hub-security-best-practices.md)
### [セキュリティのアーキテクチャ](iot-hub-security-architecture.md)
### [IoT デプロイのセキュリティ保護](iot-hub-security-deployment.md)
## Azure IoT Edge
### [概要](iot-hub-iot-edge-overview.md)
### 作業開始
#### [Linux](iot-hub-linux-iot-edge-get-started.md)
#### [Windows](iot-hub-windows-iot-edge-get-started.md)
### デバイスのシミュレーション
#### [Linux](iot-hub-linux-iot-edge-simulated-device.md)
#### [Windows](iot-hub-windows-iot-edge-simulated-device.md)
### [実在のデバイスの使用](iot-hub-iot-edge-physical-device.md)
### モジュールを作成する
#### [Java](iot-hub-iot-edge-create-module-java.md)
#### [.NET Framework](https://github.com/Azure-Samples/iot-edge-samples#how-to-run-the-net-module-sample-windows-10)
#### [.NET Standard](iot-hub-iot-edge-create-module-dotnet-core.md)
#### [Node.JS](iot-hub-iot-edge-create-module-js.md)
### 構築
#### [.NET Framework](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample)
#### [.NET Core モジュール](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_module_sample)
#### [.NET Core マネージ ゲートウェイ](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_managed_gateway)
#### [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample)
#### [Node.JS](https://github.com/Azure/iot-edge/tree/master/samples/nodejs_simple_sample)
#### [モジュールを動的に追加する](https://github.com/Azure/iot-edge/tree/master/samples/dynamically_add_module_sample)
#### [プロセス外プロキシ モジュール](https://github.com/Azure/iot-edge/tree/master/samples/proxy_sample)
#### [ネイティブ モジュール ホスト](https://github.com/Azure/iot-edge/tree/master/samples/native_module_host_sample)

# リファレンス
## [コード サンプル](https://azure.microsoft.com/en-us/resources/samples/?service=iot-hub)
## [Azure CLI](/cli/azure/iot)
## [.NET (サービス)](/dotnet/api/microsoft.azure.devices)
## [.NET (デバイス)](/dotnet/api/microsoft.azure.devices.client)
## [Java (サービス)](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java (デバイス)](/java/api/com.microsoft.azure.sdk.iot.device)
## [Node.js SDK](http://azure.github.io/azure-iot-sdk-node/)
## [C デバイス SDK](https://azure.github.io/azure-iot-sdk-c/index.html)
## [Azure IoT Edge](http://azure.github.io/iot-edge/)
## [REST (リソース プロバイダー)](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST (デバイス ID)](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST (デバイス ツイン)](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST (デバイス メッセージング)](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST (ジョブ)](https://docs.microsoft.com/rest/api/iothub/jobapi)

# 関連項目
## [Azure IoT Suite](https://azure.microsoft.com/documentation/suites/iot-suite/)
## [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)
## [Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/)
## [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

# リソース
## [IoT デバイス カタログ向けの Azure 認定](https://catalog.azureiotsuite.com/)
## [Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot/)
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=internet-of-things)
## [DeviceExplorer ツール](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
## [iothub-diagnostics ツール](https://github.com/Azure/iothub-diagnostics)
## [iothub-explorer ツール](https://github.com/Azure/iothub-explorer)
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/iot-hub/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azureiothub)
## [料金](https://azure.microsoft.com/pricing/details/iot-hub/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=iot-hub)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-iot-hub)
## [技術的なケース スタディ](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=iot-hub)
