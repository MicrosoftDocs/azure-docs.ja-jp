---
title: Defender for IoT についてよく寄せられる質問
description: Azure Defender for IoT の機能とサービスに関してよく寄せられる質問に対する回答を確認します。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778595"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Azure Defender for IoT についてよく寄せられる質問

この記事では、Defender for IoT についてのよく寄せられる質問 (FAQ) の一覧を示します。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>IoT セキュリティのための Azure の一意の価値提案とは何ですか。

Defender for IoT により、企業は IoT ソリューション全体に既存のサイバー セキュリティ ビューを拡張することができます。 Azure はビジネス ソリューションのエンド ツー エンドのビューを提供します。これによりビジネス関連のアクションや意思決定を、エンタープライズ セキュリティの状態や収集されたデータに基づいて行うことができるようになります。 Azure IoT、Azure IoT Edge、および Azure Security Center を結合して使用するセキュリティにより、必要とするセキュリティでソリューションを作成できます。

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>自分の組織では、標準ではない工業プロトコルを使用しています。 これらはサポートされていますか。 

Azure Defender for IoT は、包括的なプロトコル サポートを提供しています。 埋め込みプロトコルのサポートに加えて、独自およびカスタムのプロトコル、または標準から逸脱したプロトコルを実行する IoT および OT のデバイスをセキュリティで保護することができます。 開発者は、Horizon Open Development Environment (ODE) SDK を使用して、定義されたプロトコルに基づいてネットワーク トラフィックをデコードする dissector プラグインを作成できます。 トラフィックは、完全な監視、アラート、レポートを提供するためにサービスによって分析されます。 Horizon は次のことに使用します。
- 新しいバージョンにアップグレードせずに、可視性と制御を拡張する。
- 外部プラグインとしてサイト上で開発することで、機密情報をセキュリティで保護する。 
- アラート、イベント、プロトコルのパラメーターのテキストをローカライズする。

プラグインとしてプロトコルを開発するためのこの独自のソリューションには、新しいプロトコルをサポートするための専用の開発者チームやバージョン リリースは必要ありません。 開発者、パートナー、顧客は、Horizon を使用して安全にプロトコルを開発し、分析情報や知識を共有できます。 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Microsoft パートナーからハードウェア アプライアンスを購入する必要はありますか?
Azure Defender for IoT センサーは、[ハードウェア仕様ガイド](./how-to-identify-required-appliances.md)で説明されている特定のハードウェア仕様で実行されます。認定ハードウェアは、お客様が Microsoft パートナーから購入することも、提供されている部品表 (BOM) を使用して独自に購入することもできます。 

認定ハードウェアは、Microsoft のラボでドライバーの安定性、パケットの破棄、およびネットワークのサイズ設定についてテストされています。


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>規制により、Microsoft のシステムをインターネットに接続することは許可されていません。 Defender for IoT を引き続き利用できますか?

はい、できます。 Azure Defender for IoT プラットフォームのオンプレミス ソリューションは、ネットワーク トラフィックを (SPAN、RSPAN、または TAP を介して) 受動的に取り込み、IT、OT、および IoT のネットワークを分析、検出、および継続的に監視する物理または仮想のセンサー アプライアンスとしてデプロイされます。 大規模な企業の場合は、複数のセンサーによってデータをオンプレミスの管理コンソールに集約できます。

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>監視ポートはネットワーク内のどこに接続すべきですか?

Azure Defender for IoT センサーを SPAN ポートまたはネットワーク TAP に接続すると、パッシブ (エージェントレス) 監視によって ICS ネットワーク トラフィックの収集が直ちに開始されます。 OT ネットワークは、データ パスに配置されておらず、OT デバイスを積極的にスキャンすることはないため、影響を受けることはありません。

次に例を示します。
- 1 つのアプライアンス (物理または仮想) を Shop Floor DMZ レイヤーに配置し、すべての Shop Floor のセル トラフィックをこのレイヤーにルーティングすることができます。
- または、Shop Floor DMZ レイヤーに存在するクラウドまたはローカルの管理を使用して、Shop Floor の各セルに小さいミニセンサーを配置します。 別のアプライアンス (仮想または物理) を使用すると、Shop Floor DMZ レイヤーのトラフィックを監視できます (SCADA、Historian、または MES の場合)。

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Defender for IoT は、競争他社製品とどのように比較されますか?

Azure Defender for IoT によって、IoT または OT デバイス全体に包括的なセキュリティが与えられます。 **エンドユーザー組織** のためには、Azure Defender for IoT からは、エージェントがなく、短期間でデプロイされるネットワーク層セキュリティが提供されます。また、商標登録されているさまざまな OT 機器やレガシ Windows システムと連動し、Azure Sentinel やその他の SOC ツールと相互運用されます。 オンプレミスまたは Azure が接続されている環境でデプロイできます。 **Iot デバイスの開発者** のためには、Azure Defender for IoT からは、デバイス層セキュリティを新しい IoT または OT イニシアティブに組み込むための軽量のエージェントが提供されます。

## <a name="do-i-have-to-be-an-azure-customer"></a>Azure の顧客になる必要がありますか。

いいえ。エージェントレス バージョンの Azure Defender for IoT では、Azure のお客様である必要はありません。 ただし、Azure Sentinel にアラートを送信する場合、ネットワーク センサーをプロビジョニングし、クラウドからその正常性を監視する場合、また、ソフトウェアや脅威インテリジェンスの自動更新を活用する場合、Azure IoT Hub にセンサーを接続する必要があります。

エージェントベース バージョンの Azure Defender for IoT の場合、Azure のお客様になる必要があります。

## <a name="can-i-create-my-own-alerts"></a>独自のアラートを作成できますか?

はい。IP アドレス、MAC アドレス、プロトコルの種類、クラス、サービス、関数、コマンドなどに加え、ペイロードに含まれるカスタム タグの値など、複数のパラメーターに基づきカスタム アラートを作成できます。  「[カスタム アラートの作成](quickstart-create-custom-alerts.md)」を参照し、カスタム アラートとその作成方法の詳細について学習してください。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>インターネット接続が機能を停止すると何が起きますか?

デバイスが実行されている限り、センサーとエージェントは引き続き実行され、データが格納されます。 データは、サイズの構成に従ってセキュリティ メッセージのキャッシュに格納されます。 デバイスが接続を再取得すると、セキュリティ メッセージが送信を再開します。

## <a name="next-steps"></a>次のステップ

Defender for IoT の開始方法の詳細については、次の記事を参照してください。

- Defender for IoT の[概要](overview.md)について確認する
- [システムの前提条件](quickstart-system-prerequisites.md)を確認する
- [Defender for IoT での作業を開始する](getting-started.md)方法について確認する
- [Defender for IoT のセキュリティ アラート](concept-security-alerts.md)について理解する