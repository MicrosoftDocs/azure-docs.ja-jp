---
title: 一般的なエラー - Azure IoT Edge | Microsoft Docs
description: この記事では、IoT Edge ソリューションをデプロイするときに発生する一般的な問題を解決する方法について説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: cc6d7491d9c38f1ddf4aba2adecad4aaee3c344b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489564"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge での一般的な問題と解決

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

この記事では、IoT Edge ソリューションのデプロイ時に遭遇する可能性のある一般的な問題の解決手順を紹介しています。 IoT Edge デバイスからログやエラーを見つける方法については、[IoT Edge デバイスのトラブルシューティング](troubleshoot.md)に関するページを参照してください。

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>約 1 分後に IoT Edge エージェントが停止する

**監視された動作:**

edgeAgent モジュールが起動し、約 1 分間正常に実行された後、停止します。 ログには、IoT Edge エージェントが AMQP 経由で IoT Hub に接続を試みてから、WebSocket 経由の AMQP を使って接続を試みていることが示されています。 それが失敗すると、IoT Edge エージェントは終了します。

edgeAgent ログの例:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**根本原因:**

ホスト ネットワーク上のネットワーク構成では、IoT Edge エージェントはネットワークに到達できません。 エージェントは、最初に AMQP (ポート 5671) で接続を試みます。 接続が失敗した場合は、WebSocket (ポート 443) が試されます。

IoT Edge ランタイムは、各モジュールが通信するネットワークをセットアップします。 Linux では、このネットワークはブリッジ ネットワークです。 Windows では、NAT を使います。 この問題は、NAT ネットワークを使う Windows コンテナーを使っている Windows デバイスで、より多く見られます。

**解決策:**

このブリッジ/NAT ネットワークに割り当てられている IP アドレスにインターネットへのルートが存在することを確認します。 ホストでの VPN 構成が IoT Edge ネットワークをオーバーライドしている場合があります。

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge エージェントがモジュールのイメージにアクセスできない (403)

**監視された動作:**

コンテナーの実行が失敗し、edgeAgent ログに 403 エラーが表示されます。

**根本原因:**

IoT Edge エージェントには、モジュールのイメージにアクセスするためのアクセス許可がありません。

**解決策:**

デプロイ マニフェストで、レジストリの資格情報が正しく指定されていることを確認します。

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge エージェント モジュールで "空の構成ファイル" がレポートされ、デバイスでモジュールが開始しない

**監視された動作:**

デバイスで、デプロイにおいて定義されているモジュールの開始に問題があります。 edgeAgent のみが実行されますが、継続的に "空の構成ファイル..." が報告されます。

**根本原因:**

既定では、IoT Edge は独自の分離されたコンテナー ネットワークでモジュールを開始します。 デバイスに、このプライベート ネットワーク内での DNS 名の解決に関する問題がある可能性があります。

**解決策:**

**オプション 1: コンテナー エンジンの設定で DNS サーバーを設定します**

コンテナー エンジンの設定で環境に対して DNS サーバーを指定すると、そのエンジンによって開始されるすべてのコンテナー モジュールに適用されます。 `daemon.json` という名前のファイルを作成し、使用する DNS サーバーを指定します。 次に例を示します。

```json
{
    "dns": ["1.1.1.1"]
}
```

上の例では、パブリックにアクセスできる DNS サービスに DNS サーバーが設定されます。 Edge デバイスがその環境からこの IP アドレスにアクセスできない場合は、アクセス可能な DNS サーバーのアドレスに置き換えます。

プラットフォームの適切な場所に `daemon.json` を置きます。

| プラットフォーム | 場所 |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows コンテナーを使用した Windows ホスト | `C:\ProgramData\iotedge-moby\config` |

その場所に `daemon.json` ファイルが既にある場合は、それに対する **dns** キーを追加してファイルを保存します。

コンテナー エンジンを再起動して更新を有効にします。

| プラットフォーム | コマンド |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (管理者用 PowerShell) | `Restart-Service iotedge-moby -Force` |

**オプション 2: モジュールごとに IoT Edge のデプロイで DNS サーバーを設定します**

IoT Edge のデプロイで各モジュールの *createOptions* に DNS サーバーを設定できます。 次に例を示します。

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

この構成は、*edgeAgent* および *edgeHub* モジュールにも忘れずに設定してください。

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge ハブが起動に失敗する

**監視された動作:**

edgeHub モジュールが起動に失敗します。 次のいずれかのエラーに似たメッセージがログに表示される場合があります。

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

または

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**根本原因:**

edgeHub モジュールがバインドしようとしているポートには、ホスト マシン上の他のプロセスが既にバインドしています。 ポート 443、5671、8883 は、ゲートウェイのシナリオで使用するためためのポートとして、IoT Edge ハブによってマップされます。 そのいずれかのポートが別のプロセスによって既にバインドされていると、モジュールは起動に失敗します。

**解決策:**

この問題は、次の 2 つの方法で解決できます。

IoT Edge デバイスがゲートウェイ デバイスとして機能している場合、443、5671、8883 のいずれかのポートを使用しているプロセスを見つけて停止する必要があります。 ポート 443 のエラーは通常、他のプロセスが Web サーバーであることを意味します。

IoT Edge デバイスをゲートウェイとして使用する必要がなければ、ポートのバインドを edgeHub のモジュール作成オプションから削除することができます。 作成オプションは Azure portal で変更できるほか、deployment.json ファイルで直接変更することもできます。

Azure Portal で次の操作を行います。

1. お使いの IoT ハブに移動し、 **[IoT Edge]** を選択します。

2. 更新する IoT Edge デバイスを選択します。

3. **[Set Modules] \(モジュールの設定)** を選択します。

4. **[ランタイムの設定]** を選択します。

5. **Edge Hub** モジュールの設定で、 **[作成オプション]** テキスト ボックスの内容をすべて削除します。

6. 変更を保存してデプロイを作成します。

deployment.json ファイルで次の操作を行います。

1. IoT Edge デバイスに適用した deployment.json ファイルを開きます。

2. edgeAgent の desired プロパティ セクションで、`edgeHub` 設定を見つけます。

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. `createOptions` 行とその前の `image` 行の末尾にあるコンマを削除します。

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. ファイルを保存し、再度 IoT Edge デバイスに適用します。

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge セキュリティ デーモンが無効なホスト名で失敗する

**監視された動作:**

[IoT Edge Security Manager のログを確認](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)しようとするとエラーが発生し、次のメッセージが出力されます。

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**根本原因:**

IoT Edge ランタイムは、64 文字未満のホスト名のみをサポートできます。 通常、物理マシンに長いホスト名は付いていませんが、これは仮想マシンではより一般的な問題です。 特に、Azure でホストされる Windows 仮想マシンのために自動生成されるホスト名は長くなる傾向があります。

**解決策:**

このエラーが発生したときは、仮想マシンの DNS 名を構成し、setup コマンドでその DNS 名をホスト名として設定することで、エラーを解決できます。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Azure Portal で、目的の仮想マシンの概要ページに移動します。
2. DNS 名の下の **[構成]** を選択します。 仮想マシンに既に構成済みの DNS 名がある場合は、新しいものを構成する必要はありません。

   ![仮想マシンの DNS 名を構成する](./media/troubleshoot/configure-dns.png)

3. **[DNS 名ラベル]** に値を指定し、 **[保存]** を選択します。
4. 新しい DNS 名をコピーします。これは、 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com** の形式である必要があります。
5. 仮想マシン内で、次のコマンドを使用して、実際の DNS 名によって IoT Edge ランタイムを設定します。

   * Linux の場合:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Windows の場合:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Azure Portal で、目的の仮想マシンの概要ページに移動します。

2. DNS 名の下の **[構成]** を選択します。 仮想マシンに既に構成済みの DNS 名がある場合は、新しいものを構成する必要はありません。

   ![仮想マシンの DNS 名を構成する](./media/troubleshoot/configure-dns.png)

3. **[DNS 名ラベル]** に値を指定し、 **[保存]** を選択します。

4. 新しい DNS 名をコピーします。これは、 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com** の形式である必要があります。

5. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

6. `hostname` の値を DNS 名に置き換えます。

7. ファイルを保存して閉じ、IoT Edge に変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Windows で IoT Edge デーモン ログを取得できません

**監視された動作:**

Windows で `Get-WinEvent` の使用時に EventLogException が表示されます。

**根本原因:**

`Get-WinEvent` PowerShell コマンドは、存在しているレジストリ エントリを利用して、特定の `ProviderName` でログを見つけます。

**解決策:**

IoT Edge デーモンにレジストリ エントリを設定します。 次の内容の **iotedge.reg** ファイルを作成し、ダブルクリックするか `reg import iotedge.reg` コマンドを使用して Windows レジストリにインポートします。

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>小型のデバイスでの安定性の問題

**監視された動作:**

Raspberry Pi のようなリソースに制約があるデバイスを、特にゲートウェイとして使用した場合、安定性の問題が発生する可能性があります。 IoT Edge ハブ モジュールでメモリ不足例外が発生したり、ダウンストリームのデバイスが接続に失敗したり、数時間後にデバイスからテレメトリ メッセージを送信できなくなったりするなどの症状が発生します。

**根本原因:**

IoT Edge ハブは IoT Edge ランタイムの一部であり、既定でパフォーマンスに対して最適化され、メモリの大部分を割り当てようとします。 この最適化は、制約のある Edge デバイスには適していないため、安定性の問題が発生する可能性があります。

**解決策:**

IoT Edge ハブに対して、環境変数 **OptimizeForPerformance** を **false** に設定します。 環境変数を設定するには、次の 2 つの方法があります。

Azure Portal で次の操作を行います。

IoT Hub で、IoT Edge デバイスを選択し、[デバイスの詳細] ページから **[モジュールの設定]**  >  **[ランタイムの設定]** の順に選択します。 *OptimizeForPerformance* という IoT Edge ハブ モジュール用の環境変数を、*false* に設定して作成します。

![false に設定された OptimizeForPerformance](./media/troubleshoot/optimizeforperformance-false.png)

デプロイ マニフェストで:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge モジュールが 404 エラーで edgeHub にメッセージを送信できない

**監視された動作:**

カスタム IoT Edge モジュールは、404 `Module not found` エラーで IoT Edge ハブにメッセージを送信できません。 IoT Edge デーモンによって、次のメッセージがログに出力されます。

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**根本原因:**

IoT Edge デーモンでは、セキュリティ上の理由から、edgeHub に接続するすべてのモジュールのプロセス識別が強制されます。 モジュールによって送信されているすべてのメッセージが、モジュールのメイン プロセス ID から来ていることが確認されます。 モジュールによって、最初に確立されたのと異なるプロセス ID からメッセージが送信されている場合、そのメッセージは 404 エラー メッセージで拒否されます。

**解決策:**

バージョン 1.0.7 時点では、モジュールのすべてのプロセスが接続を承認されています。 詳しくは、[1.0.7 リリース 変更ログ](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)のページをご覧ください。

1\.0.7 へのアップグレードが不可能な場合は、次の手順を完了します。 カスタム IoT Edge モジュールによる edgeHub へのメッセージの送信で、常に同じプロセス ID が使用されるようにします。 たとえば、Docker ファイル内で、`CMD` コマンドではなく `ENTRYPOINT` を使用するようにします。 `CMD` コマンドでは、モジュールに使用されるプロセス ID とメイン プログラムを実行している bash コマンドに使用されるプロセス ID とが異なりますが、`ENTRYPOINT` であれば単一のプロセス ID が使用されます。

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>デプロイに成功した IoT Edge モジュールがデバイスから消える

**監視された動作:**

IoT Edge デバイスのモジュールを設定し、モジュールが正常にデプロイされたものの、そのモジュールが数分後にはデバイスから消え、また Azure portal のデバイス情報からも消えます。 定義されていないモジュールがデバイスに表示されることもあります。

**根本原因:**

デバイスが自動デプロイの対象になった場合、自動デプロイは、個々のデバイスに対するモジュールの手動設定よりも優先されます。 Azure portal の **[モジュールの設定]** 機能または Visual Studio Code の **[Create deployment for single device]\(単一デバイスのデプロイの作成\)** 機能は、少しの間だけ有効になります。 定義したモジュールがデバイスで起動したことを確認できます。 その後は、自動デプロイの優先度が反映され、デバイスの desired のプロパティが上書きされます。

**解決策:**

使用するデプロイ メカニズムは、デバイスごとに 1 種類 (自動デプロイまたはデバイスの個別デプロイのどちらか一方) のみとしてください。 デバイスが複数の自動デプロイの対象となっている場合、適切なデプロイが特定のデバイスに適用されるよう、優先度またはターゲットの記述を変更することができます。 自動デプロイのターゲットの記述と一致しないよう、デバイス ツインを更新することもできます。

詳細については、「[1 台のデバイスまたは多数のデバイスを対象とした IoT Edge 自動展開について](module-deployment-monitoring.md)」を参照してください。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>ゲートウェイの背後にある IoT Edge が HTTP 要求を実行して edgeAgent モジュールを起動できない

**監視された動作:**

IoT Edge デーモンが、有効な構成ファイルでアクティブになっていますが、edgeAgent モジュールを起動できません。 コマンド `iotedge list` を実行すると、空のリストが返されます。 IoT Edge デーモンのログでは、`Could not perform HTTP request` が報告されます。

**根本原因:**

ゲートウェイの背後にある IoT Edge デバイスは、構成ファイルの `parent_hostname` フィールドに指定されている親 IoT Edge デバイスからモジュール イメージを取得します。 `Could not perform HTTP request` エラーは、子デバイスが HTTP 経由で親デバイスに到達できないことを意味します。

**解決方法:**

親 IoT Edge デバイスが子 IoT Edge デバイスからの受信要求を受信できることを確認します。 子デバイスから送信される要求のために、ポート 443 および 6617 のネットワーク トラフィックを開きます。

:::moniker-end

## <a name="next-steps"></a>次のステップ

IoT Edge プラットフォームのバグを発見したと思われる場合は、 改善を続けられるように[問題を報告](https://github.com/Azure/iotedge/issues)してください。

その他に質問がある場合は、[サポート リクエスト](https://portal.azure.com/#create/Microsoft.Support)を作成して対応を要請してください。
