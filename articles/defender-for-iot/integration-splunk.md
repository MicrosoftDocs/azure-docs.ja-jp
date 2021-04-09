---
title: Splunk の統合について
description: OT ネットワークのセキュリティと回復性への可視性がないことに対処するため、Defender for IoT により、Defender for IoT、IIoT、および Splunk 用 ICS 脅威監視アプリケーションが開発されました。これは、IT と OT セキュリティに対する統一されたアプローチを可能にする、Defender for IoT と Splunk の間のネイティブ統合です。
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785922"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Defender for IoT と Splunk 用 ICS 脅威監視アプリケーション

Defender for IoT により、IIoT、ICS、SCADA のリスクが軽減されます。それには、エージェント、ルール、署名、専門的なスキル、または以前の環境の知識に依存することなく、1 イメージ時間未満で、ICS デバイス、脆弱性、脅威についての即座の分析情報を提供する、特許取得済みの ICS 対応自己学習エンジンが使用されます。

OT ネットワークのセキュリティと回復性への可視性がないことに対処するため、Defender for IoT により、Defender for IoT、IIoT、および Splunk 用 ICS 脅威監視アプリケーションが開発されました。これは、IT と OT セキュリティに対する統一されたアプローチを可能にする、Defender for IoT と Splunk の間のネイティブ統合です。

> [!Note]
> CyberX について記載があった場合、これは Azure Defender for IoT を指しています。

## <a name="about-the-splunk-application"></a>Splunk アプリケーションについて

このアプリケーションにより、SOC アナリストに対して、産業環境に展開された特殊な OT プロトコルと IIoT デバイスに関する多次元の可視性と、疑わしい動作や異常な動作を迅速に検出するための ICS 対応の行動分析が提供されます。 また、このアプリケーションを使用すると、1 つの企業 SOC 内から、IT と OT 両方のインシデントに対応できます。 これは、スマート コンピューターやリアルタイム インテリジェンスなどの新しい IIoT イニシアティブをサポートするために IT と OT が継続的に収束される重要な進化です。

Splunk アプリケーションは、ローカル環境にインストールすることも、クラウドで実行することもできます。 Defender for IoT との統合により、両方のデプロイがサポートされます。

## <a name="about-the-integration"></a>統合について

ネイティブ アプリケーションを介した Defender for IoT と Splunk の統合により、ユーザーは次のことを実現できます。

- 産業および重要インフラストラクチャの組織がサイバー脅威を検出、調査、対処するのに必要な時間を短縮します。

- OT リスクについてのリアルタイムのインテリジェンスを取得します。

- Defender for IoT のアラートと Splunk Enterprise Security 脅威インテリジェンス リポジトリを関連付けます。

- 1 つのウィンドウから監視して対応します。

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Splunk ツールのメイン ページ。":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Splunk のアラート ページ。":::

このアプリケーションを使用することで、Splunk の管理者は、Defender for IoT によって送信された OT アラートを分析し、次のような詳細など、OT のセキュリティ デプロイ全体を監視できます。

- 5 つの分析エンジンのうち、アラートを検出したもの。

- アラートを生成したプロトコル。

- アラートを生成した Defender for IoT センサー。

- アラートの重大度。

- 通信の送信元と送信先。

## <a name="requirements"></a>必要条件

### <a name="version-requirements"></a>バージョンの要件

以下のバージョンが必要です。

- Defender for IoT バージョン 2.4 以降。

- Splunkbase バージョン 11 以降。

- Splunk Enterprise バージョン 7.2 以降。
  
## <a name="download-the-application"></a>アプリケーションのダウンロード

*CyberX ICS Threat Monitoring for Splunk Application* を [Splunkbase](https://splunkbase.splunk.com/app/4313/) からダウンロードします。

## <a name="splunk-permission-requirements"></a>Splunk のアクセス許可要件

Splunk で次のアクセス許可が必要です。

- *admin* ユーザー ロールのアクセス許可を持つすべてのユーザー。

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Defender for IoT のアラートを Splunk に送信する

Defender for IoT のアラートからは、次のような広範なセキュリティ イベントに関する情報が提供されます。

- 学習されたベースライン ネットワーク アクティビティからの逸脱。

- マルウェアの検出。

- 疑わしい操作の変更に基づく検出。

- ネットワークの異常。

- プロトコル仕様からのプロトコルの逸脱。

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="検出画面。":::

Splunk サーバーにアラートを送信するように、Defender for IoT を構成できます。アラート情報はそこで、Splunk Enterprise のダッシュボードに表示されます。

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="すべてのアラートとその詳細を表示する。":::

次のアラート情報が、Splunk サーバーに送信されます。

- アラートの日付と時刻。

- イベントを検出した Defender for IoT エンジン: Protocol Violation、Policy Violation、Malware、Anomaly、Operational のいずれかのエンジン。

- アラートのタイトル。

- アラートのメッセージ。

- アラートの重大度: Warning、Minor、Major、Critical のいずれか。

- 送信元デバイスの名前。

- 送信元デバイスの IP アドレス。

- 送信先デバイスの名前。

- 送信先デバイスの IP アドレス。

- Defender for IoT プラットフォームの IP アドレス (ホスト)。

- Defender for IoT プラットフォーム アプライアンスの名前 (送信元の種類)。

出力のサンプルを次に示します。

| Time | Event |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Defender for IoT プラットフォーム アラート**: A device was stopped by a PLC Command (デバイスが PLC コマンドによって停止されました)<br /><br />**[種類]** :Operational Violation (操作違反) <br /><br />**[重大度]** :メジャー <br /><br />**送信元名**: my_device1 <br /><br />**送信元 IP**: 192.168.110.131 <br /><br />**送信先名**: my_device2<br /><br /> **送信先 IP**: 10.140.33.238 <br /><br />**メッセージ**: A network device was stopped using a Stop PLC command. (ネットワーク デバイスは、Stop PLC コマンドを使用して停止されました。) This device will not operate until a Start command is sent. (このデバイスは、Start コマンドが送信されるまで動作しません。) 192.168.110.131 was stopped by 10.140.33.238 (a Siemens S7 device), using a PLC Stop command. (192.168.110.131 は、PLC Stop コマンドを使用して、10.140.33.238 (Siemens S7 デバイス) によって停止されました。)<br /><br />**[Host]\(ホスト\)** : 192.168.90.43 <br /><br />**送信元の種類**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>アラート転送ルールを定義する

Defender for IoT の "*転送ルール*" を使用して、アラート情報を Splunk サーバーに送信します。

以下に基づいてアラート ルールをカスタマイズするオプションを使用できます。

- 特定のプロトコルの検出。

- エラーの重大度。

- イベントを検出した Defender for IoT エンジン。

転送ルールを作成するには:

1. センサーまたはオンプレミス管理コンソールの左側のウィンドウで、 **[転送]** を選択します。

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="青い [Create Forwarding Rules]\(転送ルールの作成\) ボタンを選択する。":::

1. **[Create Forwarding Rules]\(転送ルールの作成\)** を選択します。 **[Create Forwarding Rule]\(転送ルールの作成\)** ウィンドウで、ルールのパラメーターを定義します。

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="転送ルールのルールを作成する。":::

    | パラメーター | 説明 |
    |--|--|
    | **名前** | 転送ルールの名前。 |
    | **重大度の選択** | 転送するインシデントの最小セキュリティ レベル。 たとえば、マイナーを選択すると、マイナー アラートおよびこの重大度レベルよりも上のすべてのアラートが転送されます。 |
    | **プロトコル** | 既定では、すべてのプロトコルが選択されます。 特定のプロトコルを選択するには、 **[Specific]\(特定\)** を選択し、このルールを適用するプロトコルを選択します。 |
    | **エンジン** | 既定では、すべてのセキュリティ エンジンが含まれます。 このルールを適用する特定のセキュリティ エンジンを選択するには、 **[Specific]\(特定\)** を選択して、エンジンを選択します。 |
    | **システム通知** | センサーのオンラインまたはオフラインの状態を転送します。 このオプションは、Central Manager にログインしている場合にのみ使用できます。 |

1. Splunk に資産情報を送信するよう Defender for IoT に指示するには、 **[Action]\(アクション\)** を選択してから、 **[Send to Splunk Server]\(Splunk サーバーに送信\)** を選択します。

1. 次の Splunk パラメーターを入力します。

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="この画面で入力する必要がある Splunk パラメーター。":::

    | パラメーター | 説明 |
    |--|--|
    | **Host** | Splunk サーバーのアドレス |
    | **[ポート]** | 8089 |
    | **ユーザー名** | Splunk サーバーのユーザー名 |
    | **パスワード** | Splunk サーバーのパスワード |

1. **[送信]** を選択します

## <a name="next-steps"></a>次の手順

[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)方法を学習します。
