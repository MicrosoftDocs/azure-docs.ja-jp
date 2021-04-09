---
title: Forescout の統合について
description: Azure Defender for IoT と Forescout プラットフォームを統合することで、IoT と OT のランドスケープで、一元的な可視性、監視、制御を新たなレベルで実現することができます。
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784069"
---
# <a name="about-the-forescout-integration"></a>Forescout の統合について

Azure Defender for IoT は、重要な国家インフラを守る実績を持つ Blue Team のエキスパートによって構築された ICS および IoT サイバーセキュリティ プラットフォームを提供します。 Defender for IoT は、特許を取得した ICS 対応の脅威分析と機械学習を搭載した唯一のプラットフォームです。 Defender for IoT は、以下を提供します。

- 属性に関するさまざまな詳細情報を含む、デバイス ランドスケープの ICS に関する分析情報を即座に提供。
- ICS に対応した、OT プロトコル、デバイス、アプリケーション、およびそれらの動作に関する組み込まれた深い知識。
- 脆弱性、既知の脅威、およびゼロデイ脅威に対する即座の分析情報。
- 独自の分析により、標的型 ICS 攻撃の最も可能性の高い経路を予測する自動 ICS 脅威モデル化テクノロジ。

Defender for IoT と Forescout プラットフォームを統合することで、IoT と OT のランドスケープで、一元的な可視性、監視、制御を新たなレベルで実現することができます。

このようにブリッジされたプラットフォームを使用することで、これまで到達できなかった ICS デバイスやサイロ化されたワークフローに対する、自動でのデバイスの可視化と管理が可能になります。

この統合により、SOC アナリストは、産業環境に展開されている OT プロトコルを、複数レベルで可視化することができます。 詳細は、ファームウェア、デバイスの種類、オペレーティング システム、独自の Azure Defender for IoT テクノロジに基づくリスク分析スコアなどの項目について確認できます。

> [!Note]
> CyberX について記載があった場合、これは Azure Defender for IoT を指しています。
## <a name="devices"></a>デバイス

### <a name="device-visibility-and-management"></a>デバイスの可視性と管理

デバイスのインベントリは、Defender for IoT プラットフォームによって検出された重要な属性によって強化されています。 これにより、次のことが可能になります。

- 1 つのウィンドウで OT デバイス ランドスケープを包括的かつ継続的に可視化。
- OT リスクについてのリアルタイムのインテリジェンスを取得。

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="デバイス インベントリ":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="デバイスの詳細":::

### <a name="device-control"></a>デバイス制御

Forescout の統合により、産業および重要インフラストラクチャの組織がサイバー脅威を検出、調査、対処するのに必要な時間を短縮することができます。

- Forescout ポリシー アクションをトリガーすることによって、Azure Defender for IoT OT デバイス インテリジェンスを使用してセキュリティ サイクルを閉じます。 たとえば、特定のプロトコルが検出された場合、またはファームウェアの詳細が変更された場合に、SOC 管理者にアラートの電子メールを自動的に送信できます。

- 監視、インシデント管理、デバイス制御を監視する他の *Forescout eyeExtended* モジュールと Defender for IoT の情報を関連付けることができます。

## <a name="system-requirements"></a>システム要件

- Azure Defender for IoT バージョン 2.4 以降
- Forescout バージョン 8.0 以降
- Azure Defender for IoT プラットフォーム用の *Forescout eyeExtend* モジュールのライセンス。

### <a name="getting-more-forescout-information"></a>Forescout に関する情報の取得

Forescout プラットフォームの詳細については、[Forescout リソース センター](https://www.forescout.com/company/resources/#resource_filter_group)を参照してください。

## <a name="system-setup"></a>システムの設定

この記事では、Defender for IoT プラットフォームと Forescout プラットフォーム間の通信を設定する方法について説明します。

### <a name="set-up-the-defender-for-iot-platform"></a>Defender for IoT プラットフォームの設定

Defender for IoT から Forescout への通信を確保するには、Defender for IoT でアクセス トークンを生成します。

アクセス トークンを使用することで、Defender for IoT によって検出されたデータに外部システムからアクセスし、外部 REST API を使用して SSL 接続経由でそのデータに対してアクションを実行できるようになります。 アクセス トークンは、Azure Defender for IoT REST API にアクセスするために生成することができます。

トークンを生成するには:

1. Forescout からクエリされる Defender for IoT センサーにサインインします。

1. **[システム設定]** を選択し **[全般]** セクションから **[アクセス トークン]** を選択します。 **[アクセス トークン]** ダイアログ ボックスが開きます。
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="アクセス トークン":::
1. **[アクセス トークン]** ダイアログ ボックスから **[新しいトークンの生成]** を選択します。
1. **[新しいアクセス トークン]** ダイアログ ボックスに、トークンの説明を入力します。
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="新しいアクセス トークン":::
1. **[次へ]** を選択します。 ダイアログ ボックスにトークンが表示されます。 :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="トークンの表示":::
   > [!NOTE]
   > *トークンは安全な場所に記録します。これは、Forescout プラットフォームを構成するときに必要になります。*
1. **[完了]** を選択します。

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="トークンの追加を完了する":::

### <a name="set-up-the-forescout-platform"></a>Forescout プラットフォームを設定する

Defender for IoT センサーと通信するよう Forescout プラットフォームを設定することができます。

設定するには:

1. Forescout プラットフォームに "*CyberX 用 Forescout eyeExtend モジュール*" をインストールします。

1. CounterACT コンソールにサインインし、 **[ツール]** メニューから **[オプション]** を選択します。 **[オプション]** ダイアログ ボックスが開きます。

1. **[モジュール]** フォルダーに移動して選択します。

1. **[モジュール]** ペインで、 **[CyberX Platform]\(CyberX プラットフォーム\)** を選択します。 Defender for IoT platform ペインが開きます。

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Azure Defender for IoT モジュールの設定":::

   次の情報を入力します。

   - **サーバー アドレス** - Forescout アプライアンスによってクエリされる、Defender for IoT センサーの IP アドレスを入力します。
   - **アクセス トークン** - Forescout アプライアンスに接続する Defender for IoT センサー用に生成されたアクセス トークンを入力します。 トークンを生成するには、「[Defender for IoT プラットフォームの設定](#set-up-the-defender-for-iot-platform)」を参照してください。

1. **[適用]** を選択します。

Forescout プラットフォームで別のセンサーと通信する場合は、次の手順を実行します。

1. 該当する Defender for IoT センサーに新しいアクセス トークンを作成します。

1. **[Forescout Modules]\(Forescout モジュール\)**  >  **[CyberX Platform]\(CyberX プラットフォーム\)** ダイアログ ボックスで、次の手順を実行します。

   - 表示されている情報を削除します。
   
   - 新しいセンサーの IP アドレスと新しいアクセス トークンの情報を入力します。

### <a name="verify-communication"></a>通信を検証する

Defender for IoT と Forescout を構成した後、Defender for IoT でセンサーの [アクセス トークン] ダイアログ ボックスを開きます。

このトークンの **[Used]\(使用日\)** フィールドに **N/A** と表示されている場合、センサーと Forescout アプライアンス間の接続が機能していません。

**[Used]\(使用日\)** は、このトークンを持つ外部呼び出しが最後に受信された日時を示します。

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="トークンが受信されたことを確認する":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Forescout で Defender for IoT の検出を表示する

デバイスの属性を表示するには、次の手順を実行します。

1. Forescout プラットフォームにサインインし、 **[Asset Inventory]\(アセット インベントリ\)** に移動します。

1. **[CyberX Platform]\(CyberX プラットフォーム\)** に移動します。 Defender for IoT によって検出された OT デバイスの、以下のデバイス属性が表示されます。

   | Item | [説明] |
   |--|--|
   | Authorized by Azure Defender for IoT (Azure Defender for IoT によって承認済み) | ネットワークの学習期間中に、Defender for IoT によってネットワーク上で検出されたデバイス。 |
   | ファームウェア | デバイスのファームウェアの詳細。 たとえば、モデルやバージョンの詳細などです。 |
   | 名前 | デバイスの名前。 |
   | オペレーティング システム | デバイスのオペレーティング システム。 |
   | Type | デバイスの種類。 たとえば、PLC、Historian (ヒストリアン)、Engineering Station (エンジニアリング ステーション) などです。 |
   | ベンダー | デバイスのベンダー。 たとえば、Rockwell Automation などです。 |
   | リスク レベル | Defender for IoT によって計算されたリスク レベル。 |
   | プロトコル | デバイスによって生成されたトラフィックで検出されたプロトコル。 |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="ファームウェアの属性を表示します。":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="ベンダーの属性を表示します。":::

### <a name="viewing-more-details"></a>詳細を表示する

Defender for IoT によって指示されたデバイスのその他のデバイス情報を表示します。 たとえば、Forescout のコンプライアンスとポリシー情報などです。

これを行うには、 **[Device Inventory Hosts]\(デバイス インベントリ ホスト\)** セクションからデバイスを右クリックします。 その他の情報が表示されている [Host Details]\(ホストの詳細\) ダイアログ ボックスが開きます。

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="ホストの詳細":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Forescout で Azure Defender for IoT ポリシーを作成する

Forescout ポリシーを使用して、Defender for IoT によって検出されたデバイスの制御と管理を自動化することができます。 たとえば、次のように入力します。

- 特定のファームウェア バージョンが検出されたときに SOC 管理者に自動的に電子メールを送信します。

- 特定の Defender for IoT 検出デバイスを Forescout グループに追加して、他の SIEM 統合などのインシデントおよびセキュリティ ワークフローでさらに処理できるようにします。

条件プロパティを使用して、Defender for IoT で Forescout カスタム ポリシーを作成します。

Defender for IoT プロパティにアクセスするには、次の手順を実行します。

1. **[Policy Conditions]\(ポリシー条件\)** ダイアログ ボックスから、 **[Properties Tree]\(プロパティ ツリー\)** に移動します。

1. **[Properties Tree]\(プロパティ ツリー\)** の **[CyberX Platform]\(CyberX プラットフォーム\)** フォルダーを展開します。 次の Defender for IoT のプロパティを使用できます。

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Properties":::

## <a name="next-steps"></a>次の手順

[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)方法を学習します。
