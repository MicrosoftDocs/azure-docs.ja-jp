---
title: Azure IoT Edge のトラブルシューティング | Microsoft Docs
description: この記事を使用して、コンポーネントの状態およびログの取得など、Azure IoT Edge 用の標準的な診断スキルについて学習します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7b3b8078a03ef0e891306f056c604545cde71459
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489459"
---
# <a name="troubleshoot-your-iot-edge-device"></a>IoT Edge デバイスのトラブルシューティング

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

お使いの環境で Azure IoT Edge の実行中に問題が発生した場合は、この記事を参考にしてトラブルシューティングと診断を行ってください。

## <a name="run-the-check-command"></a>"check" コマンドを実行する

IoT Edge のトラブルシューティング時の最初のステップは、`check` コマンドを使用することです。このコマンドは、一般的な問題の構成の収集と接続テストを実行します。 `check` コマンドは、[リリース 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) 以降で使用できます。

>[!NOTE]
>トラブルシューティング ツールでは、IoT Edge デバイスがプロキシ サーバーの背後にある場合、接続チェックを実行できません。

`check` コマンドは次のように実行できます。または、`--help` フラグを追加してオプションの完全なリストを表示できます。

Linux の場合:

```bash
sudo iotedge check
```

Windows の場合:

```powershell
iotedge check
```

トラブルシューティング ツールでは、次の 3 つのカテゴリに分類される多くのチェックが実行されます。

* "*構成検査*" では、構成ファイルおよびコンテナー エンジンの問題を含め、IoT Edge デバイスからクラウドへの接続を妨げるおそれのある問題の詳細を調べます。
* "*接続検査*" では、IoT Edge ランタイムがホスト デバイス上のポートにアクセス可能であること、およびすべての IoT Edge コンポーネントが IoT Hub にアクセス可能であることが確認されます。 IoT Edge デバイスがプロキシの背後にある場合、この一連の検査でてエラーが返されます。
* "*製品の準備完了検査*" では、デバイス証明機関 (CA) の証明書の状態やモジュール ログ ファイルの構成など、推奨される運用上のベスト プラクティスが検査されます。

IoT Edge チェック ツールでは、コンテナーを使用して診断が実行されます。 コンテナー イメージ `mcr.microsoft.com/azureiotedge-diagnostics:latest` は、[Microsoft Container Registry](https://github.com/microsoft/containerregistry) から入手できます。 インターネットに直接アクセスせずにデバイスのチェックを実行する必要がある場合は、デバイスにコンテナー イメージへのアクセス権が必要です。

エラーや警告が表示された場合の対処方法など、このツールが実行する各診断チェックの詳細については、[IoT Edge のトラブルシューティング チェック](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)に関するページを参照してください。

## <a name="gather-debug-information-with-support-bundle-command"></a>"support-bundle" コマンドを使用してデバッグ情報を収集する

IoT Edge デバイスからログを収集する必要がある場合、最も便利な方法は `support-bundle` コマンドを使用することです。 このコマンドを使うと、既定では、モジュールと IoT Edge Security Manager とコンテナー エンジンのログ、`iotedge check` の JSON 出力、および他の有用なデバッグ情報が収集されます。 共有しやすいように、それらが 1 つのファイルに圧縮されます。 `support-bundle` コマンドは、[リリース 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) 以降で使用できます。

ログを取得する過去の期間を指定するには、`--since` フラグを指定して `support-bundle` コマンドを実行します。 たとえば、`6h` では過去 6 時間、`6d` では過去 6 日間、`6m` では過去 6 分間のログが取得されます。 オプションの完全な一覧を表示するには、`--help` フラグを含めます。

Linux の場合:

```bash
sudo iotedge support-bundle --since 6h
```

Windows の場合:

```powershell
iotedge support-bundle --since 6h
```

また、デバイスへの[ダイレクト メソッド](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)の呼び出しを使用して、support-bundle コマンドの出力を Azure Blob Storage にアップロードすることもできます。

> [!WARNING]
> `support-bundle` コマンドからの出力には、ホスト、デバイス名とモジュール名、モジュールによってログに記録された情報などが含まれる場合があります。パブリック フォーラムで出力を共有する場合は、この点に注意してください。

## <a name="check-your-iot-edge-version"></a>IoT Edge のバージョンを確認する

古いバージョンの IoT Edge を実行している場合は、アップグレードすると問題が解決されることがあります。 `iotedge check` ツールでは、IoT Edge セキュリティ デーモンが最新バージョンであることは確認されますが、IoT Edge ハブとエージェント モジュールのバージョンは確認されません。 デバイス上のランタイム モジュールのバージョンを確認するには、`iotedge logs edgeAgent` と `iotedge logs edgeHub` のコマンドを使用します。 バージョン番号は、モジュールの起動時にログで宣言されます。

デバイスを更新する手順については、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>デバイスに IoT Edge がインストールされていることを確認する

[edgeAgent モジュール ツインの監視](./how-to-monitor-module-twins.md)によって、デバイスに IoT Edge がインストールされていることを確認できます。

最新の edgeAgent モジュール ツインを取得するには、[Azure Cloud Shell](https://shell.azure.com/) から次のコマンドを実行します。

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

このコマンドは、edgeAgent の[報告されるプロパティ](./module-edgeagent-edgehub.md)すべてを出力します。 次に、デバイスの状態を監視する便利な方法を示します。

* ランタイムの状態
* ランタイムの開始時刻
* ランタイムの最後の終了時刻
* ランタイムの再起動回数

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>IoT Edge Security Manager の状態とそのログを確認する

[IoT Edge Security Manager](iot-edge-security-manager.md) は、デバイスの起動時およびプロビジョニングでの IoT Edge システムの初期化などの操作を担当します。 IoT Edge が開始されていない場合、セキュリティ マネージャーのログに有用な情報が提供されることがあります。

Linux の場合:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

* IoT Edge Security Manager の状態を確認します。

   ```bash
   sudo systemctl status iotedge
   ```

* IoT Edge Security Manager のログを確認します。

   ```bash
   sudo journalctl -u iotedge -f
   ```

* IoT Edge Security Manager のログの詳細を確認します。

  1. IoT Edge デーモンの設定を編集します。

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. 次の行を更新します。

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=edgelet=debug
     ```

  3. IoT Edge セキュリティ デーモンを再起動します。

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```
<!--end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* IoT Edge システム サービスの状態を表示します。

   ```bash
   sudo iotedge system status
   ```

* IoT Edge システム サービスのログを表示します。

   ```bash
   sudo iotedge system logs -- -f
   ```

* デバッグレベルのログを有効にして、IoT Edge システム サービスのより詳細なログを表示します。

  1. デバッグレベルのログを有効にします。

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. デバッグ後に既定の情報レベルのログに戻ります。

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

<!-- end 1.2 -->
:::moniker-end

Windows の場合:

* IoT Edge Security Manager の状態を確認します。

   ```powershell
   Get-Service iotedge
   ```

* IoT Edge Security Manager のログを確認します。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* IoT Edge Security Manager のログの最後の 5 分だけを表示します。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* IoT Edge Security Manager のログの詳細を確認します。

  1. システムレベルの環境変数を追加します。

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. IoT Edge セキュリティ デーモンを再起動します。

     ```powershell
     Restart-Service iotedge
     ```

## <a name="check-container-logs-for-issues"></a>コンテナーのログで問題を確認する

IoT Edge セキュリティ デーモンが実行されている場合は、コンテナーのログを参照して問題を検出します。 デプロイされたコンテナーから開始して、IoT Edge ランタイムを形成しているコンテナーである edgeAgent および edgeHub を確認します。 通常、IoT Edge エージェントのログでは、各コンテナーのライフサイクルについての情報が提供されます。 IoT Edge ハブのログでは、メッセージングとルーティングについての情報が提供されます。

```cmd
iotedge logs <container name>
```

また、デバイス上のモジュールへの[ダイレクト メソッド](how-to-retrieve-iot-edge-logs.md#upload-module-logs)の呼び出しを使用して、そのモジュールのログを Azure Blob Storage にアップロードすることもできます。

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>IoT Edge ハブを通過するメッセージを表示する

<!--1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge ハブを通過するメッセージを表示し、ランタイム コンテナーから得た詳細なログから分析情報を収集できます。 これらのコンテナーで詳細ログを有効にするには、yaml 構成ファイルに`RuntimeLogLevel` を設定します。 ファイルを開くには:

Linux の場合:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows の場合:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

既定では、`agent` 要素は次の例のようになります。

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

`env: {}` を以下で置き換えます。

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML ファイルには、インデントとしてタブを含めることはできません。 代わりにスペース 2 つを使用してください。 最上位の項目の先頭に空白を入れることはできません。

ファイルを保存し、IoT Edge Security Manager を再起動します。

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge ハブを通過するメッセージを表示し、ランタイム コンテナーから得た詳細なログから分析情報を収集できます。 これらのコンテナーで詳細ログを有効にするには、配置マニフェストで環境変数 `RuntimeLogLevel` を設定します。

IoT Edge ハブを経由するメッセージを表示するには、edgeHub モジュールの環境変数 `RuntimeLogLevel` を `debug` に設定します。

edgeHub と edgeAgent の両方のモジュールにこのランタイム ログ環境変数があり、既定値は `info` に設定されています。 この環境変数は、次の値を取ることができます。

* fatal
* error
* warning
* info
* debug
* verbose

<!-- end 1.2 -->
:::moniker-end

IoT Hub デバイスと IoT デバイスの間で送信されたメッセージを確認することもできます。 [Visual Studio Code 用の Azure IoT Hub 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、これらのメッセージを表示します。 詳細については、[Azure IoT で開発するときの便利なツール](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)に関するページを参照してください。

## <a name="restart-containers"></a>コンテナーを再起動する

ログとメッセージの情報を調べた後は、コンテナーの再起動を試みることもできます。

```cmd
iotedge restart <container name>
```

IoT Edge ランタイム コンテナーを再起動する:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>ファイアウォール規則とポート構成規則を確認する

Azure IoT Edge では、サポートされている IoT Hub プロトコルを使用した、オンプレミス サーバーから Azure クラウドへの通信が許可されています。「[通信プロトコルの選択](../iot-hub/iot-hub-devguide-protocols.md)」をご覧ください。 セキュリティ強化のため、Azure IoT Edge と Azure IoT Hub の間の通信チャネルは常にアウトバウンドに構成されます。 この構成は、[サービス支援通信方式](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices)に基づいていて、悪意のあるエンティティが探る攻撃対象の領域が最小限になります。 インバウンド通信が必要なのは、Azure IoT Hub がメッセージを Azure IoT Edge デバイスにプッシュする必要がある特定のシナリオのみです。 cloud-to-device メッセージは、セキュリティで保護された TLS チャネルを使用して保護されます。また、X.509 証明書と TPM デバイス モジュールを使用してさらに保護することができます。 この通信の確立方法は、Azure IoT Edge セキュリティ マネージャーによって管理されます。[IoT Edge セキュリティ マネージャー](../iot-edge/iot-edge-security-manager.md)に関するページを参照してください。

IoT Edge は、Azure IoT Edge ランタイムとデプロイされたモジュールをセキュリティで保護するための強化された構成を提供しますが、依然として基になるマシンとネットワークの構成に依存しています。 そのため、エッジからクラウドへの安全な通信を実現するための適切なネットワーク規則およびファイアウォール規則が設定されていることを確認することが不可欠です。 Azure IoT Edge ランタイムがホストされている、基になるサーバー用にファイアウォール規則を構成するときには、以下の表をガイドラインとして使用できます。

|Protocol|Port|受信|送信|ガイダンス|
|--|--|--|--|--|
|MQTT|8883|ブロック (既定値)|ブロック (既定値)|<ul> <li>通信プロトコルとして MQTT を使用する場合は、送信 (アウトバウンド) をオープンになるように構成します。<li>MQTT での 1883 は、IoT Edge ではサポートされていません。 <li>受信 (インバウンド) 接続はブロックする必要があります。</ul>|
|AMQP|5671|ブロック (既定値)|オープン (既定値)|<ul> <li>IoT Edge の既定の通信プロトコル。 <li> Azure IoT Edge が他のサポートされているプロトコル用に構成されていない場合、または AMQP が望ましい通信プロトコルである場合は、オープンになるように構成する必要があります。<li>AMQP での 5672 は、IoT Edge ではサポートされていません。<li>Azure IoT Edge が、IoT Hub でサポートされているのとは異なるプロトコルを使用する場合は、このポートをブロックします。<li>受信 (インバウンド) 接続はブロックする必要があります。</ul></ul>|
|HTTPS|443|ブロック (既定値)|オープン (既定値)|<ul> <li>IoT Edge のプロビジョニングのために、送信 (アウトバウンド) を 443 でオープンにするように構成します。 この構成は、手動スクリプトや Azure IoT Device Provisioning Service (DPS) を使用する場合に必要です。 <li>受信 (インバウンド) 接続が以下の特定のシナリオだけでオープンになるようにする必要があります。 <ul> <li>  メソッド要求を送信することがあるリーフ デバイスを備えた透過的なゲートウェイがある場合。 この場合、ポート 443 は、IoT Hub に接続したり Azure IoT Edge を通じて IoT Hub サービスを提供したりするために外部ネットワークに対してオープンにする必要はありません。 そのため、受信規則は内部ネットワークからのオープンな受信 (インバウンド) だけに制限することができます。 <li> Client to Device (C2D) シナリオの場合。</ul><li>HTTP での 80 は、IoT Edge ではサポートされていません。<li>企業内で非 HTTP プロトコル (AMQP や MQTT など) を構成できない場合は、メッセージを WebSockets 経由で送信できます。 その場合、ポート 443 は WebSocket 通信のために使用されます。</ul>|

## <a name="next-steps"></a>次のステップ

IoT Edge プラットフォームのバグを発見したと思われる場合は、 改善を続けられるように[問題を報告](https://github.com/Azure/iotedge/issues)してください。

その他に質問がある場合は、[サポート リクエスト](https://portal.azure.com/#create/Microsoft.Support)を作成して対応を要請してください。
