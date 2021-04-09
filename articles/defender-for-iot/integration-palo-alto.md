---
title: Palo Alto の統合
description: Defender for IoT は、その継続的な ICS 脅威監視プラットフォームが Palo Alto の次世代ファイアウォールと統合され、重大な脅威をより迅速かつ効率的にブロックできるようになりました。
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783933"
---
# <a name="about-the-palo-alto-integration"></a>Palo Alto の統合について

Defender for IoT は、その継続的な ICS 脅威監視プラットフォームが Palo Alto の次世代ファイアウォールと統合され、重大な脅威をより迅速かつ効率的にブロックできるようになりました。

利用できる統合の種類は次のとおりです。

- 自動ブロック オプション:Defender for IoT と Palo Alto の直接統合

- ブロックに関する推奨事項を中央管理システムに送信する:Defender for IoT と Panorama の統合

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>指定された Palo Alto ファイアウォールによる即時ブロックを構成する

マルウェア関連のアラートなどの重大なケースでは、自動ブロックを有効にすることができます。 これを行うには、特定の Palo Alto ファイアウォールにブロック コマンドを直接送信する Defender for IoT の転送ルールを構成します。

Defender for IoT では、重大な脅威を識別するときに、感染したソースをブロックするオプションを含むアラートを送信します。 アラートの詳細で **[ソースのブロック]** を選択すると、指定された Palo Alto ファイアウォールにブロック コマンドを送信する転送ルールがアクティブ化されます。

構成するには、次の手順に従います。

1. 左側のウィンドウで、 **[転送]** を選択します。

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="アラートの転送画面。":::

1. **[転送ルールの作成]** を選択し ます。

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="転送ルールの作成":::

1. Palo Alto NGFW への転送ルールを構成するには、次の手順に従います。  
 
   - 標準のルール パラメーターを定義し、 **[アクション]** ドロップダウン ボックスから **[Palo Alto NGFW に送信]** を選択します。
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="転送ルールを編集する。":::

1. [アクション] ウィンドウで、次のパラメーターを設定します。

   - **[Host]\(ホスト\)** : NGFW サーバーの IP アドレスを入力します。
   - **ポート**:NGFW サーバーのポートを入力します。
   - **[ユーザー名]** : NGFW サーバーのユーザー名を入力します。
   - **パスワード**:NGFW サーバーのパスワードを入力します。
   - **構成する**:次のオプションを設定して、Palo Alto ファイアウォールによる疑わしいソースのブロックを許可します。
     - **[無効な関数コードをブロックする]** :プロトコル違反 - ICS プロトコル仕様に違反する無効なフィールド値 (潜在的な悪用)。
     - **[不正な PLC プログラミング/ファームウェア更新のブロック]** :不正な PLC 変更。
     - **[承認されていない PLC 停止をブロックする]** :PLC 停止 (ダウンタイム)。
     - **[マルウェア関連のアラートをブロックする]** :産業用マルウェアの実行のブロック (TRITON、NotPetya など)。 **[自動ブロック]** のオプションを選択できます。 その場合、ブロックは直ちに自動的に実行されます。
     - **[承認されていないスキャンをブロックする]** :不正なスキャン (潜在的な偵察)。
     
1. **[Submit]\(送信\)** をクリックします。

疑わしいソースをブロックするには、次の手順に従います。 

1. **[アラート]** ペインで、Palo Alto 統合に関連するアラートを選択します。 **[アラートの詳細]** ダイアログ ボックスが表示されます。
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="アラートの詳細":::

1. 疑わしいソースを自動的にブロックするには、 **[ソースのブロック]** を選択します。 **[確認してください]** ダイアログ ボックスが表示されます。

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="[確認してください] 画面でブロックを確認する。":::

1. **[確認してください]** ダイアログ ボックスで、 **[OK]** を選択します。 疑わしいソースが Palo Alto ファイアウォールによってブロックされます。

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Palo Alto Panorama へのブロック ポリシーの送信

Defender for IoT と Palo Alto Networks には、Palo Alto Networks の NMS である Panorama で自動的に新しいポリシーを作成する既製の統合が存在します。 この統合では、Panorama 管理者による確認が必要であり、自動ブロックは使用できません。

この統合は次のインシデントを対象としています。

- **不正な PLC 変更:** デバイスのラダー ロジックまたはファームウェアの更新。 これは、正当なアクティビティを表すことも、デバイスを侵害しようとする試みを表すこともあります。 この侵害は、リモート アクセス型トロイの木馬 (RAT) などの悪意のあるコードの挿入や、回転するタービンなどの物理プロセスを危険な方法で作動させるパラメーターによって発生します。

- **プロトコル違反:** プロトコル仕様に違反するパケット構造またはフィールド値。 これは、誤って構成されたアプリを表すことも、デバイスを侵害しようとする悪意のある試みを表すこともあります。 たとえば、ターゲット デバイスでバッファー オーバーフロー状態を発生させたりします。

- **PLC 停止:** デバイスの機能を停止させるコマンド。これにより、PLC によって制御されている物理プロセスが危険にさらされます。

- **ICS ネットワークで検出された産業用マルウェア:** 自身のネイティブ プロトコルを使用して ICS デバイスを操作するマルウェア (TRITON や Industroyer など)。 Defender for IoT では、Conficker、WannaCry、NotPetya など、ICS および SCADA 環境に水平移動した IT マルウェアも検出します。

- **スキャン型マルウェア:** 攻撃の前段階でシステム構成に関するデータを収集する偵察ツール。 たとえば、トロイの木馬 Havex では、産業用ネットワークをスキャンして、Windows ベースの SCADA システムが ICS デバイスと通信するときに使用する標準プロトコルである OPC を使用しているデバイスを検索します。

## <a name="the-process"></a>プロセス

事前構成済みのユース ケースが Defender for IoT によって検出されると、アラートに **[ソースのブロック]** ボタンが追加されます。 その後、**CyberX** ユーザーが **[ソースのブロック]** ボタンを選択すると、定義済みの転送ルールが Defender for IoT によって送信され、Panorama 上にポリシーが作成されます。

このポリシーは、Panorama 管理者がネットワーク内の関連する NGFW にポリシーをプッシュする場合にのみ適用されます。

IT ネットワークでは、動的 IP アドレスが使用されている場合があります。 そのため、それらのサブネットでは、IP アドレスではなく FQDN (DNS 名) に基づいてポリシーを作成する必要があります。 Defender for IoT では、構成された時間間隔で逆引き参照を実行し、動的 IP アドレスを持つデバイスを FQDN (DNS 名) と照合します。

さらに、Defender for IoT では、関連する Panorama ユーザーにメールを送信し、Defender for IoT によって作成された新しいポリシーが承認を待機していることを通知します。 次の図は、Defender for IoT と Panorama の統合アーキテクチャを示しています。

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="CyberX-Panorama 統合アーキテクチャ":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Defender for IoT の構成で Panorama ブロック ポリシーを作成する

### <a name="to-configure-dns-lookup"></a>DNS 参照を構成するには

1. 左側のウィンドウで、 **[システム設定]** を選択します。

1. **[システム設定]** ウィンドウで、 **[DNS 設定]** :::image type="icon" source="media/integration-paloalto/settings.png"::: を選択します。

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="DNS 設定を構成する。":::

1. **[DNS 設定の編集]** ダイアログ ボックスで、次のパラメーターを設定します。

   - **状態**: DNS リゾルバーの状態。

   - **[DNS サーバー アドレス]** ネットワーク DNS サーバーの IP アドレスまたは FQDN を入力します。
   - **[DNS サーバー ポート]** :DNS サーバーの照会に使用されるポートを入力します。
   - **サブネット**:動的 IP アドレスのサブネット範囲を設定します。 Defender for IoT によって DNS サーバーの IP アドレスの逆引き参照が実行され、現在の FQDN 名と照合される範囲です。
   - **[逆引き参照のスケジュール]** :スケジュールのオプションを次のように定義します。
     - [特定時刻]:日単位で逆引き参照を実行するタイミングを指定します。
     - [一定間隔 (時間単位)]:逆引き参照を実行する頻度を設定します。
   - **[ラベル数]** :ネットワーク IP アドレスをデバイスの FQDN に自動的に解決するように Defender for IoT に指示します。 <br />DNS の FQDN 解決を構成するには、表示するドメイン ラベルの数を追加します。 最大 30 文字が左から右に表示されます。
1. **[SAVE]\(保存\)** を選択します。
1. DNS 設定が正しいことを確認するには、 **[参照テスト]** を選択します。 このテストでは、DNS サーバーの IP アドレスと DNS サーバーのポートが正しく設定されていることが確認されます。

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Palo Alto ファイアウォールで疑わしいトラフィックをブロックするように転送ルールを構成するには

1. 左側のウィンドウで、 **[転送]** を選択します。 [転送] ウィンドウが表示されます。

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="[転送] 画面。":::

1. **[転送]** ウィンドウで、 **[転送ルールの作成]** を選択します。

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="転送ルールの作成":::

1. Palo Alto Panorama への転送ルールを構成するには、次の手順に従います。

   標準のルール パラメーターを定義し、 **[アクション]** ドロップダウン ボックスから **[Palo Alto Panorama に送信]** を選択します。 [アクションの詳細] ウィンドウが表示されます。

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="選択アクション":::

1. [アクション] ウィンドウで、次のパラメーターを設定します。

   - **[Host]\(ホスト\)** : Panorama サーバーの IP アドレスを入力します。

   - **ポート**:Panorama サーバーのポートを入力します。
   - **[ユーザー名]** : Panorama サーバーのユーザー名を入力します。
   - **パスワード**:Panorama サーバーのパスワードを入力します。
   - **[アドレスのレポート]** :ブロックの実行方法を次のように定義します。
   
     - **[IP アドレスごと]** :常に IP アドレスに基づいて Panorama 上にブロック ポリシーを作成します。
     
     - **[FQDN または IP アドレスごと]** :FQDN が存在する場合は FQDN、それ以外の場合は IP アドレスに基づいて、Panorama 上にブロック ポリシーを作成します。
     
   - **電子メール**: ポリシー通知メール用のメール アドレスを設定します
     > [!NOTE]
     > 必ず Defender for IoT でメール サーバーを構成してください。 メール アドレスが入力されていない場合は、Defender for IoT から通知メールが送信されません。
   - **[アラート検出時に DNS 参照を実行する] (チェック ボックス)** :[アドレスのレポート] で [FQDN または IP アドレスごと] オプションが設定されている場合。 このチェック ボックスは既定でオンになっています。 IP アドレスのみが設定されている場合、このオプションは無効になります。
   - **構成する**:Palo Alto Panorama による疑わしいソースのブロックを許可するには、次のオプションを設定します。
   
     - **[無効な関数コードをブロックする]** :プロトコル違反 - ICS プロトコル仕様に違反する無効なフィールド値 (潜在的な悪用)。
     
     - **[不正な PLC プログラミング/ファームウェア更新のブロック]** :不正な PLC 変更。
     
     - **[承認されていない PLC 停止をブロックする]** :PLC 停止 (ダウンタイム)。
     
     - **[マルウェア関連のアラートをブロックする]** :産業用マルウェアの実行のブロック (TRITON、NotPetya など)。 **[自動ブロック]** のオプションを選択できます。 その場合、ブロックは直ちに自動的に実行されます。
     
     - **[承認されていないスキャンをブロックする]** :不正なスキャン (潜在的な偵察)。
     
1. **[Submit]\(送信\)** をクリックします。

### <a name="to-block-the-suspicious-source"></a>疑わしいソースをブロックするには

1. **[アラート]** ペインで、Palo Alto 統合に関連するアラートを選択します。 **[アラートの詳細]** ダイアログ ボックスが表示されます。

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="アラートの詳細":::        

1. 疑わしいソースを自動的にブロックするには、 **[ソースのブロック]** を選択します。

1. **[確認してください]** ダイアログ ボックスで、 **[OK]** を選択します。

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirm":::

## <a name="next-steps"></a>次の手順

[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)方法を学習します。
