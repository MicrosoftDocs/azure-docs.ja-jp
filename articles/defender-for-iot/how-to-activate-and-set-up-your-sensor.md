---
title: センサーをアクティブ化してセットアップする
description: この記事では、センサー コンソールにサインインしてアクティブ化する方法について説明します。
ms.date: 1/12/2021
ms.topic: how-to
ms.openlocfilehash: 2fdfa0dd7048bf39ae5b53a729aef578054b30ac
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779190"
---
# <a name="activate-and-set-up-your-sensor"></a>センサーをアクティブ化してセットアップする

この記事では、センサーをアクティブ化して初期セットアップを実行する方法について説明します。

アクティブ化は、管理者ユーザーが初めてサインインするときと、アクティブ化の管理が必要になったときに行います。 セットアップによって、検出とアラートが最適に行われるようにセンサーが構成されます。

セキュリティ アナリストと読み取り専用ユーザーは、センサーをアクティブ化したり、新しいパスワードを生成したりすることはできません。

## <a name="sign-in-and-activation-for-administrator-users"></a>管理者ユーザーのサインインとアクティブ化

管理者は初回サインイン時に、センサーのオンボード中にダウンロードされたアクティブ化とパスワード回復のファイルにアクセスできることを確認する必要があります。 できない場合は、Azure Defender for IoT ポータルでこれらのファイルを生成するために、Azure セキュリティ管理者、サブスクリプション共同作成者、またはサブスクリプション所有者のアクセス許可が必要です。

### <a name="first-time-sign-in-and-activation-checklist"></a>初回サインインとアクティブ化のチェックリスト

センサー コンソールにサインインする前に、管理者ユーザーは次にアクセスできる必要があります。

- センサーの IP アドレス (インストール時に定義したもの)。

- センサーのユーザーのサインイン資格情報。 センサーの ISO をダウンロードした場合は、インストール中に受け取った既定の資格情報を使用します。 アクティブ化後に新しい "*管理者*" ユーザーを作成することをお勧めします。

- 初期パスワード。 Arrow から構成済みセンサーを購入した場合は、初めてサインインするときにパスワードを生成する必要があります。

- このセンサーに関連付けられているアクティブ化ファイル。 このファイルは、Defender for IoT ポータルへのセンサーのオンボード中に生成されてダウンロードされたものです。

- 会社に必要な SSL/TLS の CA 署名証明書。

### <a name="about-activation-files"></a>アクティブ化ファイルについて

センサーは、特定の管理モードで Azure Defender for IoT にオンボードされました。

| モードの種類 | [説明] |
|--|--|
| **クラウド接続モード** | センサーによって検出された情報はセンサー コンソールに表示されます。 また、アラート情報は IoT ハブを通じて配信され、他の Azure サービス (Azure Sentinel など) と共有できます。 |
| **ローカル接続モード** | センサーによって検出された情報はセンサー コンソールに表示されます。 また、センサーがオンプレミスの管理コンソールに接続されている場合は、検出情報が共有されます。 |

ローカル接続またはクラウド接続された、このセンサー用のアクティブ化ファイルはオンボード中に生成されてダウンロードされたものです。 アクティブ化ファイルには、センサーの管理モードに関する説明が含まれています。 "*デプロイする各センサーに、一意のアクティブ化ファイルをアップロードする必要があります。* "  初めてサインインするときは、このセンサー用の関連アクティブ化ファイルをアップロードする必要があります。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender for IoT ポータル、センサーをオンボードする。":::

### <a name="about-certificates"></a>証明書について

センサーのインストール後、ローカルの自己署名証明書が生成され、センサー コンソールにアクセスするために使用されます。 管理者がコンソールに初めてサインインすると、SSL/TLS 証明書をオンボードするように求めるメッセージがそのユーザーに表示されます。

次の 2 つのレベルのセキュリティを使用できます。

- CA 署名証明書をアップロードして、組織で要求されている特定の証明書と暗号化の要件を満たします。
- 管理コンソールと接続されたセンサー間で検証を行えるようにします。 検証は、証明書失効リストと証明書の有効期限に照らして評価されます。 "*検証が失敗すると、管理コンソールとセンサー間の通信が停止され、検証エラーがコンソールに表示されます。* " このオプションは、インストール後に既定で有効になります。  

コンソールでは、次の証明書の種類がサポートされています。

- プライベートおよびエンタープライズ キー インフラストラクチャ (プライベート PKI)

- 公開キー基盤 (パブリック PKI)

- アプライアンスのローカルで生成された (ローカルで自己署名された) もの 

  > [!IMPORTANT]
  > 既定の自己署名証明書は使用しないことをお勧めします。 この証明書はセキュリティで保護されていないため、テスト環境でのみ使用してください。 証明書の所有者を検証できないため、システムのセキュリティを維持できません。 実稼働ネットワークには、このオプションを使用しないでください。

### <a name="sign-in-and-activate-the-sensor"></a>サインインしてセンサーをアクティブ化する

サインインしてアクティブ化するには、次のようにします。

1. インストール中に定義された IP を使用して、ブラウザーからセンサー コンソールにアクセスします。 サインインのダイアログ ボックスが開きます。

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender for IoT センサー。":::

1. センサーのインストール中に定義された資格情報を入力するか、 **[パスワードの回復]** オプションを選択します。 Arrow から構成済みセンサーを購入した場合は、まずパスワードを生成します。 パスワードの回復の詳細については、[初回サインインでのパスワード エラーの調査](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in)に関する記事を参照してください。

1. サインインすると、 **[Activation]\(アクティブ化\)** ダイアログ ボックスが開きます。 **[アップロード]** を選択し、センサーのオンボード中にダウンロードしたアクティブ化ファイルにアクセスします。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="[アップロード] を選択して、アクティブ化ファイルにアクセスする。":::

1. アクティブ化の前にセンサーのネットワーク構成を変更する場合は、 **[Sensor Network Configuration]\(センサーのネットワーク構成\)** リンクを選択します。 「[アクティブ化の前にセンサーのネットワーク構成を更新する](#update-sensor-network-configuration-before-activation)」を参照してください。

1. 使用条件に同意します。

1. **[アクティブ化]** を選びます。 SSL/TLS 証明書のダイアログ ボックスが開きます。

1. 証明書名を定義します。
1. CRT およびキーのファイルをアップロードします。
1. パスフレーズを入力し、必要に応じて PEM ファイルをアップロードします。
1. **[次へ]** を選択します。 検証画面が開きます。 既定で、管理コンソールと接続されたセンサー間の検証が有効になっています。
1. 検証を無効にするには、 **[システム全体の検証を有効にする]** トグルをオフにします。 検証を有効にすることをお勧めします。
1. **[保存]** を選択します。  

CA 署名証明書のアップロード後に、画面を更新する必要がある場合があります。

新しい証明書のアップロード、サポートされている証明書パラメーター、CLI 証明書コマンドの操作の詳細については、[個々のセンサーの管理](how-to-manage-individual-sensors.md)に関する記事を参照してください。

#### <a name="update-sensor-network-configuration-before-activation"></a>アクティブ化の前にセンサーのネットワーク構成を更新する  

センサーのネットワーク構成パラメーターは、ソフトウェアのインストール時または構成済みセンサーを購入したときに定義されています。 定義されているパラメーターは、次のとおりです。

- IP アドレス
- DNS
- デフォルト ゲートウェイ
- サブネット マスク
- ホスト名

センサーをアクティブ化する前に、この情報を更新する必要がある場合があります。 たとえば、Arrow によって定義された構成済みパラメーターを変更する必要がある場合があります。 また、センサーをアクティブ化する前に、プロキシ設定を定義することもできます。

センサーのネットワーク構成パラメーターを更新するには、次のようにします。

1. **[Activation]\(アクティブ化\)** ダイアログ ボックスの **[Sensor Network Configuration]\(センサーのネットワーク構成\)** リンクを選択します。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="センサーのネットワーク構成。":::

2. インストール時に定義したパラメーターが表示されます。 プロキシを定義するオプションも使用できます。 必要に応じて任意の設定を更新し、 **[Save]\(保存\)** を選択します。

### <a name="subsequent-sign-ins"></a>以降のサインイン

初回のアクティブ化後は、サインイン後にアクティブ化ファイルが要求されずに Azure Defender for IoT センサー コンソールが開きます。 必要なのはサインイン資格情報のみです。

サインインすると、Azure Defender for IoT コンソールが開きます。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender for IoT コンソール。":::

## <a name="initial-setup-and-learning-for-administrators"></a>初期セットアップと学習 (管理者向け)

初めてサインインすると、Azure Defender for IoT センサーによってネットワークの監視が自動的に開始されます。 ネットワーク デバイスがデバイス マップとデバイス インベントリのセクションに表示されます。 Azure Defender for IoT によって、ネットワークで発生するすべてのセキュリティと運用上のインシデントの検出とアラートが開始されます。 その後、検出された情報に基づいてレポートとクエリを作成できます。

最初、このアクティビティは学習モードで実行されます。これは、ネットワークの通常のアクティビティを学習するようセンサーに指示するものです。 たとえば、ネットワークで検出されたデバイス、ネットワークで検出されたプロトコル、特定のデバイス間で発生するファイル転送がセンサーによって学習されます。 このアクティビティがネットワークのベースライン アクティビティになります。

### <a name="review-and-update-basic-system-settings"></a>基本システム設定を確認および更新する

センサーのシステム設定を確認して、検出とアラートが最適に行われるようにセンサーが構成されていることを確認します。

センサーのシステム設定を定義します。 例:

- ICS (または IoT) と分離されたサブネットを定義します。

- サイト固有のプロトコルのポート別名を定義します。

- 使用中の VLAN と名前を定義します。

- DHCP が使用されている場合は、正当な DHCP 範囲を定義します。

- Active Directory およびメール サーバーとの統合を定義します。

### <a name="disable-learning-mode"></a>学習モードを無効にする

システム設定を調整した後は、システム検出でネットワーク アクティビティが正確に反映されるようになるまで、Azure Defender for IoT センサーを学習モードで実行できます。

学習モードは、ネットワークのサイズと複雑さに応じて、約 2 週間から 6 週間実行する必要があります。 学習モードを無効にすると、ベースライン アクティビティと異なるアクティビティによってアラートがトリガーされます。

学習モードを無効にするには、次のようにします。

- **[システム設定]** を選択し、 **[Learning]\(学習\)** オプションをオフにします。

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>セキュリティ アナリストおよび読み取り専用ユーザーの初回サインイン

サインインする前に、次があることを確認してください。

- センサーの IP アドレス。
- 管理者によって提供されたサインイン資格情報。

## <a name="console-tools-overview"></a>コンソール ツール: 概要

サイドメニューからコンソール ツールにアクセスします。

**ナビゲーション** 

| ウィンドウ | アイコン | 説明 |
| -----------|--|--|
| ダッシュボード | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | ネットワークのセキュリティの状態に関する直感的なスナップショットを表示します。 |
| デバイス マップ | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | マップ内のネットワーク デバイス、デバイス接続、およびデバイス プロパティを表示します。 さまざまなズーム、強調表示、およびフィルターのオプションを使用して、ネットワークを表示できます。 |
| デバイスのインベントリ | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | デバイス インベントリには、センサーによって検出されたデバイス属性の一覧が表示されます。 次のオプションを使用できます。 <br /> - テーブルのフィールドに従って情報を並び変えるかフィルター処理し、フィルター処理された情報を表示します。 <br /> - CSV ファイルに情報をエクスポートします。 <br /> - Windows レジストリの詳細をインポートします。|
| 警告 | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | ポリシー違反が発生した場合、ベースライン動作からの逸脱が発生した場合、またはネットワーク内で各種の疑わしいアクティビティが検出された場合にアラートを表示します。 |
| Reports | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | データ マイニング クエリに基づくレポートを表示します。 |

**分析**

| ウィンドウ| アイコン | 説明 |
|---|---|---|
| イベント タイムライン | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | アラート、ネットワーク イベント (情報)、ユーザーの操作 (ユーザーのサインインやユーザーの削除など) に関する情報が含まれたタイムラインを表示します。|

**ナビゲーション**

| ウィンドウ | アイコン | 説明 |
|---|---|---|
| データ マイニング | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | さまざまなレイヤーにおけるネットワーク デバイスに関する包括的で詳細な情報を生成します。 |
| 調査 | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | さまざまなウィジェットでの傾向と統計を表示します。 |
| リスク評価 | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | **[Vulnerabilities]\(脆弱性\)** ウィンドウを表示します。 |

**管理者**

| ウィンドウ | アイコン | 説明 |
|---|---|---|
| Users | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | さまざまなアクセス レベルでユーザーとロールを定義します。 |
| 転送 | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Defender for IoT と統合しているパートナーおよび内部ソース (Azure Sentinel など) へのアラート情報を、電子メール アドレスや Webhook サーバーなどに転送します。 <br /> 詳細については、「[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)」を参照してください。 |
| システムの設定 | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | システム設定を構成します。 たとえば、DHCP 設定を定義したり、メール サーバーの詳細を指定したり、ポートのエイリアスを作成したりします。 |
| 設定のインポート | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | **[設定のインポート]** ウィンドウを表示します。 デバイスの情報は手動で変更できます。<br /> 詳細については、「[デバイス情報をインポートする](how-to-import-device-information.md)」を参照してください。 |

**サポート**

| ウィンドウ| アイコン | 説明 |
|----|---|---|
| サポート | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | ヘルプが必要な場合は、[Microsoft サポート](https://support.microsoft.com/)にご連絡ください。 |

## <a name="see-also"></a>関連項目

[センサーをオンボードする](getting-started.md#onboard-a-sensor)

[センサーのアクティブ化ファイルを管理する](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[監視するトラフィックを制御する](how-to-control-what-traffic-is-monitored.md)
