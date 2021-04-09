---
title: Defender for IoT の CLI コマンドを操作する
description: この記事では、センサーおよびオンプレミスの管理コンソール用の Defender for IoT CLI コマンドについて説明します。
ms.date: 12/12/2020
ms.topic: article
ms.openlocfilehash: 9cd3f4325db2bc45ddcd6cc011dd4993e385a43c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778680"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Defender for IoT の CLI コマンドを操作する

この記事では、センサーおよびオンプレミスの管理コンソール用の CLI コマンドについて説明します。 コマンドには、次のユーザーがアクセスできます。

- 管理者
- CyberX 
- サポート

CLI での作業を開始するには、ターミナルを使用して接続します。 たとえば、ターミナル名 `Putty` と `Support` ユーザーです。 

## <a name="create-local-alert-exclusion-rules"></a>ローカルのアラートの除外ルールを作成する

次のコマンドを CLI に入力して、ローカルのアラートの除外ルールを作成できます。

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

アラートの除外ルールでは、次の属性を使用できます。

| 属性 | 説明 |
|--|--|
| [-h] | コマンドのヘルプ情報を出力します。 |
| -n NAME | 作成されるルールの名前。 |
| [-ts TIMES] | ルールがアクティブになる期間。 これは、次のように指定する必要があります。<br />`xx:yy-xx:yy`<br />間にコンマを使用すると、複数の期間を定義することができます。 (例: `xx:yy-xx:yy, xx:yy-xx:yy`)。 |
| [-dir DIRECTION] | ルールが適用される方向。 これは、次のように指定する必要があります。<br />`both | src | dst` |
| [-dev DEVICES] | ルールによって除外されるデバイスの IP アドレスとアドレスの種類。次のように指定します。<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTS] | ルールによって除外されるアラートの名前。<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>ローカルのアラートの除外ルールを追加する

次のコマンドを CLI に入力して、ローカルのアラートの除外ルールを追加できます。

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

ここで使用される属性は、「ローカル アラートの除外ルールを作成する」セクションで説明されている属性と同じです。 使用方法の違いは、ここでは、既存のルールに属性が適用されることです。

## <a name="show-local-alert-exclusion-rules"></a>ローカルのアラートの除外ルールを表示する

次のコマンドを入力して、既存の除外ルールの一覧を表示します。

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>ローカルのアラートの除外ルールを削除する

次のコマンドを入力して、既存のアラートの除外ルールを削除できます。

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

アラートの除外ルールでは、次の属性を使用できます。

| 属性 | 説明|
| --------- | ---------------------------------- |
| -n NAME | 削除するルールの名前。 |

## <a name="sync-time-from-the-ntp-server"></a>NTP サーバーからの時刻の同期

指定の NTP サーバーからの時刻同期を有効または無効にすることができます。

### <a name="enable-ntp-sync"></a>NTP 同期を有効にする

次のコマンドを入力して、指定した NTP サーバーから定期的に時刻を取得します。

```azurecli-interactive
ntp enable IP
```

コマンド内で定義できる属性は、NTP サーバーの IP アドレスです。

### <a name="disable-ntp-sync"></a>NTP 同期を無効にする

次のコマンドを入力して、指定した NTP サーバーとの時刻同期を無効にします。

```azurecli-interactive
ntp disable IP
```

コマンド内で定義できる属性は、NTP サーバーの IP アドレスです。

## <a name="network-configuration"></a>ネットワーク構成

次の表では、Azure Defender for IoT のネットワーク オプションを構成するために使用できるコマンドについて説明します。

|名前|コマンド|説明|
|-----------|-------|-----------|
|ping|`ping IP`| Defender for IoT プラットフォームの外部にあるアドレスに ping を実行します。|
|Blink|`network blink`| インターフェイスのライトを点滅させて接続を見つけます。 |
|ネットワークの再構成 |`network edit-settings`| ネットワーク構成パラメーターの変更を有効にします。 |
|ネットワーク設定の表示 |`network list`|ネットワーク アダプター パラメーターを表示します。 |
|ネットワーク構成の検証 |`network validate` |出力ネットワーク設定を表示します。 <br /> <br />例: <br /> <br />Current Network Settings: <br /> interface: eth0 <br /> ip:10.100.100.1 <br />subnet:255.255.255.0 <br />default gateway:10.100.100.254 <br />dns:10.100.100.254 <br />monitor interfaces: eth1|
|証明書のインポート |`certificate import FILE` |HTTPS 証明書をインポートします。 \*.crt ファイルへの完全なパスを指定する必要があります。 |
|日付の表示 |`date` |ホスト上の現在の日付を GMT 形式で返します。 |

## <a name="filter-network-configurations"></a>ネットワーク構成のフィルター

`network capture-filter` コマンドを使用すると、管理者は分析する必要のないネットワーク トラフィックを排除できます。 包含リストまたは除外リストを使用してトラフィックをフィルター処理できます。

```azurecli-interactive
network capture-filter
```

コマンドを入力すると、次の質問が表示されます。

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

`Y` を選択すると nano ファイルが開き、次の構文に従ってデバイス、チャネル、ポート、およびサブセットを追加できます。

| 属性 | 説明 |
|--|--|
| 1.1.1.1 | このデバイスのすべてのトラフィックを含めます。 |
| 1.1.1.1,2.2.2.2 | このチャネルのすべてのトラフィックを含めます。 |
| 1.1.1,2.2.2 | このサブネットのすべてのトラフィックを含めます。 |

改行することで引数を区切ります。

デバイス、チャネル、またはサブネットを含めると、センサーは、通常は処理されないポートやトラフィックを含む、その引数で有効なすべてのトラフィックを処理します。

その後、次の質問が表示されます。

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

`Y` を選択すると nano ファイルが開き、次の構文に従ってデバイス、チャネル、ポート、およびサブセットを追加できます。

| 属性 | 説明 |
|--|--|
| 1.1.1.1 | このデバイスのすべてのトラフィックを除外します。 |
| 1.1.1.1,2.2.2.2 | このチャネルのすべてのトラフィック、つまり 2 つのデバイス間のすべてのトラフィックを除外します。 |
| 1.1.1.1,2.2.2.2,443 | ポート別にこのチャネルのすべてのトラフィックを除外します。 |
| 1.1.1 | このサブネットのすべてのトラフィックを除外します。 |
| 1.1.1,2.2.2 | サブネット間のすべてのトラフィックを除外します。 |

改行することで引数を区切ります。

デバイス、チャネル、またはサブネットを除外すると、センサーはその引数で有効なすべてのトラフィックを除外します。

### <a name="ports"></a>Port

すべてのトラフィックに対して UDP および TCP ポートを含めるか除外します。

>`502`: 単一のポート

>`502,443`: 両方のポート

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>コンポーネント

次の質問が表示されます。

>`In which component do you wish to apply this capture filter?`

オプションには、 `all`、`dissector`、`collector`、`statistics-collector`、`rpc-parser`、または `smb-parser`があります。

ほとんどのユースケースでは、`all` を選択します。

### <a name="custom-base-capture-filter"></a>カスタムのベース キャプチャ フィルター

ベース キャプチャ フィルターは、コンポーネントのベースラインです。 たとえば、このフィルターによってコンポーネントで使用できるポートが決まります。

以下のオプションのすべてで `Y` を選択します。 変更が設定されると、すべてのフィルターがベースラインに追加されます。 変更を加えると、既存のベースラインが上書きされます。

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

1\.1.1 などのサブネットを除外する場合:

- `internal` では、そのサブネットのみが除外されます。

- `all-connected` では、そのサブネットと、そのサブネットとの間のすべてのトラフィックが除外されます。

`internal` を選択することをお勧めします。

> [!NOTE]
> 選択内容は、ツール内のすべてのフィルターに使用され、セッションに依存しません。 つまり、`internal` を選択できないフィルターも、 `all-connected` を選択できないフィルターもあります。

### <a name="comments"></a>説明

 ```/var/cyberx/properties/cybershark.properties``` でフィルターを表示できます。

- **statistics-collector**:  ```/var/cyberx/properties/net.stats.collector.properties``` の  `bpf_filter property`

- **dissector**:  ```/var/cyberx/properties/cybershark.properties``` の `override.capture_filter` プロパティ

- **rpc-parser**:  ```/var/cyberx/properties/rpc-parser.properties``` の `override.capture_filter` プロパティ

- **smb-parser**:  ```/var/cyberx/properties/smb-parser.properties``` の  `override.capture_filter`  プロパティ

- **collector**:  ```/var/cyberx/properties/collector.properties``` の `general.bpf_filter`  プロパティ

cyberx ユーザーの次のコードを入力して、既定の構成を復元できます。

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>クライアントとサーバーのホストを定義する

Defender for IoT でクライアントとサーバーのホストが自動的に検出されなかった場合は、次のコマンドを入力して、クライアントとサーバーのホストを設定します。

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

`directions` コマンドでは、次の属性を使用できます。

| 属性 | 説明 |
|--|--|
| [-h] | コマンドのヘルプ情報を出力します。 |
| [--identifier IDENTIFIER] | サーバー識別子。 |
| [--port PORT] | サーバー ポート。 |
| [--remove] | クライアントまたはサーバーのホストを一覧から削除します。 |
| [--add] | クライアントまたはサーバーのホストを一覧に追加します。 |
| [--tcp] | このホストと通信するときに TCP を使用します。 |
| [--udp] | このホストと通信するときに UDP を使用します。 |

## <a name="system-actions"></a>システム アクション
次の表では、Defender for IoT 内でさまざまなシステム アクションを実行するために使用できるコマンドについて説明します。

|名前|コード|説明|
|----|----|-----------|
|日付の表示|`date`|ホスト上の現在の日付を GMT 形式で返します。|
|ホストの再起動|`system reboot`|ホスト デバイスを再起動します。|
|ホストのシャットダウン|`system shutdown`|ホストをシャットダウンします。|
|システムのバックアップ|`system backup`|即時バックアップを開始します (スケジュールしていないバックアップ)。|
|バックアップからシステムを復元する|`system restore`|最新のバックアップから復元します。|
|バックアップ ファイルの一覧表示|`system backup-list`|利用可能なバックアップ ファイルを一覧表示します。|
|すべての Defender for IoT プラットフォーム サービスの状態を表示する|`system sanity`|すべての Defender for IoT プラットフォーム サービスの現在の状態を一覧表示することによって、システムのパフォーマンスを確認します。|
|ソフトウェア バージョンの表示|`system version`|システムで現在実行されているソフトウェアのバージョンを表示します。|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>SSL および TLS 証明書をアプライアンスにデプロイする

次のコマンドを入力して、SSL および TLS エンタープライズ証明書を CLI にインポートします。

```azurecli-interactive
cyberx-xsense-certificate-import
```
このツールを使用するには、証明書ファイルをデバイスにアップロードする必要があります。 これを行うには、WinSCP や Wget などのツールを使用します。 

このコマンドでは、次の入力フラグがサポートされています。

| フラグ | 説明 |
|--|--|
| -H | コマンドライン ヘルプの構文を表示します。 |
| --crt | 証明書ファイル (CRT 拡張子) へのパス。 |
| --key | \*.key ファイル。 キーの長さは、2048 ビット以上である必要があります。 |
| --chain | 証明書チェーン ファイルへのパス (省略可能)。 |
| --pass | 証明書の暗号化に使用されるパスフレーズ (省略可能)。 |
| --passphrase-set | 既定値は **False** (**使用しない**) です。 <br />前の証明書で指定された前のパスフレーズを使用するには、**True** に設定します (省略可能)。 | 

ツールを使用する場合:

- アプライアンスで証明書ファイルが読み取り可能であることを確認します。 

- DNS サーバーと対応する IP アドレスを使用して、(証明書に表示されているように) アプライアンス ドメインを IT で確認します。 
    
## <a name="see-also"></a>関連項目

[Defender for IoT API センサーと管理コンソール API](references-work-with-defender-for-iot-apis.md)
