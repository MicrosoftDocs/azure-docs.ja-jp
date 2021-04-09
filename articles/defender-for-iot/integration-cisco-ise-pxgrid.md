---
title: Cisco ISE pxGrid の統合について
description: Defender for IoT と Cisco ISE pxGrid の機能の橋渡しをすることにより、セキュリティ チームには、エンタープライズ エコシステムについてのかつてないデバイス可視性が提供されます。
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784086"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Cisco ISE pxGrid の統合について

Defender for IoT によって提供されるのは、重要な国内インフラストラクチャの防御に関して実績のある Blue Team の専門家によって構築された ICS と IoT の唯一のサイバーセキュリティ プラットフォームであり、特許を取得した ICS 対応の脅威分析と機械学習を備えた唯一のプラットフォームです。 Defender for IoT によって、次のものが提供されます。

- ICS デバイス、脆弱性、既知の脅威、およびゼロデイ脅威に関する瞬時の分析情報。

- OT プロトコル、デバイス、アプリケーション、それらの動作に関する、ICS 対応の深く組み込まれた知識。

- 独自の分析により、標的型 ICS 攻撃の最も可能性の高い経路を予測する自動 ICS 脅威モデル化テクノロジ。

## <a name="powerful-device-visibility-and-control"></a>強力なデバイスの可視性と制御

Defender for IoT と Cisco ISE pxGrid の統合により、OT の状況に関する新しいレベルの一元的な可視性、監視、制御が提供されます。

これらの橋渡しされたプラットフォームを使用することで、これまでは到達できなかった ICS や IIoT のデバイスに対する、自動的なデバイスの可視化と保護が可能になります。

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>ICS および IIoT デバイスの可視性: 包括的で深い

特許を有する Defender for IoT テクノロジを使用すると、エンタープライズ データに関して、ICS および IIoT デバイスの包括的で深い検出とインベントリ管理が保証されます。

デバイスの種類、製造元、開いているポート、シリアル番号、ファームウェアのリビジョン、IP アドレス、MAC アドレス データなどが、リアルタイムで更新されます。 Defender for IoT を使用すると、重要なエンタープライズ データ ソースと統合することで、このベースラインから可視性、検出、分析をさらに強化できます。 たとえば、CMDB、DNS、ファイアウォール、Web API などがあります。

さらに、Defender for IoT プラットフォームにより、パッシブな監視とオプションの選択的プローブ手法が組み合わされて、産業や重要なインフラストラクチャの組織におけるデバイスの最も正確で詳細なインベントリが提供されます。

### <a name="bridged-capabilities"></a>橋渡しされる機能

これらの機能と Cisco ISE pxGrid の橋渡しをすることで、セキュリティ チームには、エンタープライズ エコシステムについてのかつてないデバイス可視性が提供されます。

Cisco ISE pxGrid とのシームレスで堅牢な統合により、OT デバイスが検出されなかったり、デバイス情報が欠落したりすることがなくなります。

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="エンドポイント カテゴリ OUI のサンプル。":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="システムでのサンプル エンドポイント":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="システム内で見つかった属性のスクリーンショット。":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>ユース ケースのカバレッジ: Defender for IoT の属性に基づく ISE ポリシー

Defender for IoT のディープ ラーニングに基づく強力な ISE ポリシーを使用して、ICS と IoT のユース ケースの要件を処理します。

ポリシーを使用すると、セキュリティ サイクルを閉じ、ネットワークのコンプライアンスをリアルタイムで実現できます。

たとえば、お客様は、Defender for IoT の ICS と IoT の属性を使用して、次のようなユース ケースを処理するポリシーを作成できます。

- 許可されている属性 (たとえば、Rockwell Automation PLC の特定のファームウェア バージョン) に基づいて、既知の承認されたデバイスが機密ゾーンに入るのを許可する承認ポリシーを作成します。

- 非 OT ゾーンで ICS デバイスが検出されたら、セキュリティ チームに通知します。

- 古くなった、または準拠していないベンダーのマシンを修復します。

- 必要に応じて、デバイスを検疫してブロックします。

- 既知の脆弱性 (CVE) があるファームウェアが実行されている PLC または RTU についてのレポートを生成します。

### <a name="about-this-article"></a>この記事の内容

この記事では、Defender for IoT によって OT 属性が Cisco ISE に確実に挿入されるように、pxGrid と Defender for IoT プラットフォームを設定する方法について説明します。

### <a name="getting-more-information"></a>詳細情報の入手先

Cisco ISE pxGrid の統合要件の詳細については、<https://www.cisco.com/c/en/us/products/security/pxgrid.html> を参照してください

## <a name="integration-system-requirements"></a>統合システムの要件

この記事では、統合システムの要件について説明します。

Defender for IoT の要件

- Defender for IoT バージョン 2.5

Cisco の要件

- pxGrid バージョン 2.0

- Cisco ISE バージョン 2.4

ネットワークの要件

- Defender for IoT アプライアンスが Cisco ISE 管理インターフェイスにアクセスできることを確認します。

- 企業内のすべての Defender for IoT アプライアンスに CLI でアクセスできることを確認します。

> [!NOTE]
> MAC アドレスを持つデバイスのみが、Cisco ISE pxGrid と同期されます。

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid の設定

この記事では、次の方法について説明します。

- pxGrid との通信を設定する

- エンドポイント デバイスのトピックをサブスクライブする

- 証明書の生成

- pxGrid の設定を定義する

## <a name="set-up-communication-with-pxgrid"></a>pxGrid との通信を設定する

この記事では、pxGrid との通信を設定する方法について説明します。

通信を設定するには:

1. Cisco ISE にサインインします。

1. **[Administration]\(管理\)** > **[System]\(システム\)** を選択し、 **[Deployment]\(展開\)** を選択します。

1. 必要なノードを選択します。 [General Settings]\(全般設定\) タブで、 **[pxGrid]** チェック ボックスをオンにします。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="[pxGrid] チェック ボックスがオンになっていることを確認する。":::

1. **[Profiling Configuration]\(プロファイル構成\)** タブを選択します。

1. **[pxGrid]** チェック ボックスをオンにします。 説明を追加します。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="追加の説明のスクリーンショット":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>エンドポイント デバイスのトピックをサブスクライブする

ISE pxGrid ノードでエンドポイント デバイスのトピックがサブスクライブされていることを確認します。 **[Administration]\(管理\)** > **[pxGrid Services]\(pxGrid サービス\)** > **[Web Clients]\(Web クライアント\)** に移動します。 そこで、ISE によりエンドポイント デバイスのトピックがサブスクライブされていることを確認できます。

## <a name="generate-certificates"></a>証明書の生成

この記事では、証明書を生成する方法について説明します。

生成するには:

1. **[Administration]\(管理\)**  >  **[pxGrid Services]\(pxGrid サービス\)** を選択してから、 **[Certificates]\(証明書\)** を選択します。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="証明書を生成するには、[Certificates]\(証明書\) タブを選択する。":::

1. **[I Want To]\(行うこと\)** フィールドで、 **[Generate a single certificate (without a certificate signing request)]\(1 つの証明書を生成する (証明書署名要求なし)\)** を選択します。

1. 残りのフィールドに入力し、 **[Create]\(作成\)** を選択します。

1. クライアント証明書キーとサーバー証明書を作成した後、それらを Java キーストア形式に変換します。 これらを、ネットワーク内の各 Defender for IoT センサーにコピーする必要があります。

## <a name="define-pxgrid-settings"></a>pxGrid の設定を定義する

設定を定義するには:

1. **[Administration]\(管理\)**  >  **[pxGrid Services]\(pxGrid サービス\)** を選択してから、 **[Settings]\(設定\)** を選択します。

1. **[Automatically approve new certificate-based accounts]\(新しい証明書ベースのアカウントを自動的に承認する\)** と **[Allow password-based account creation]\(パスワードベースのアカウントの作成を許可する\)** を有効にします。

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="両方のチェック ボックスがオンになっていることを確認する。":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Defender for IoT アプライアンスを設定する

この記事では、pxGrid と通信するように Defender for IoT アプライアンスを設定する方法について説明します。 Cisco ISE にデータを挿入するすべての Defender for IoT アプライアンスで、構成を行う必要があります。

アプライアンスを設定するには:

1. センサーの CLI にサインインします。

1. センサーで前に作成された `trust.jks` をコピーします。 それらを `/var/cyberx/properties/` にコピーします。

1. `/var/cyberx/properties/pxgrid.properties`を編集します:

    1. キーと信頼を追加してから、ファイル名とパスワードを保存します。

    2. pxGrid インスタンスのホスト名を追加します。

    3. `Enabled=true`

1. アプライアンスを再起動します。

1. データの挿入が行われる社内のアプライアンスごとに、これらの手順を繰り返します。

## <a name="view-and-manage-detections-in-cisco-ise"></a>Cisco ISE での検出を表示して管理する

1. エンドポイントの検出は、ISE のコンテキストの **[Visibility]\(表示\)**  >  **[Endpoints]\(エンドポイント\)** タブに表示されます。

1. **[Policy]\(ポリシー\)**  >  **[Profiling]\(プロファイリング\)**  >  **[Add]\(追加\)**  >  **[Rules]\(ルール\)**  >  **[+ Condition]\(+ 条件\)**  >  **[Create New Condition]\(新しい条件の作成\)** を選択します。

1. **[Attribute]\(属性\)** を選択し、IOT デバイス ディクショナリを使用して、挿入された属性に基づいてプロファイリング ルールを作成します。 次の属性が挿入されます。

    - デバイスの種類

    - ハードウェアのリビジョン

    - IP アドレス

    - MAC アドレス

    - 名前

    - Product ID

    - プロトコル

    - シリアル番号

    - ソフトウェアのリビジョン

    - ベンダー

MAC アドレスを持つデバイスのみが同期されます。

## <a name="troubleshooting-and-logs"></a>トラブルシューティングとログ

ログは次の場所にあります。

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>次の手順

[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)方法を学習します。
