---
title: Azure Defender for IoT エージェントについてよく寄せられる質問
description: Azure Defender for IoT エージェントについて最もよく寄せられる質問に対する回答を確認します。
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4bd7d3c9b76dfb37e53cc51e5e16b660545cb7f0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778663"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Azure Defender for IoT エージェントについてよく寄せられる質問

この記事では、Defender for IoT エージェントについてのよく寄せられる質問と回答の一覧を示します。

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>埋め込みセキュリティ エージェントをインストールする必要はありますか?

IoT デバイス上でのエージェントのインストールは、Defender for IoT を有効にするために必須ではありません。 選択内容に応じて、さまざまなレベルのセキュリティ監視および管理機能を提供する、次の 3 つのオプションから選択できます。

- NTA (ネットワーク トラフィック分析) センサーを使用して、ネットワークやデバイスのパフォーマンスに影響を与えずに、IoT または OT のリスクを監視し、詳細な表示を提供する、パッシブで影響を与えない (エージェントレス) デプロイ。
- 変更の有無に関わらず、Defender for IoT の埋め込みのセキュリティ エージェントをインストールします。 このオプションにより、デバイスの動作とアクセスへの最高レベルの強化されたセキュリティ洞察が提供されます。

- 独自のエージェントを作成し、Defender for IoT のセキュリティ メッセージ スキーマを実装します。 このオプションにより、Defender for IoT 分析ツールを、デバイスのセキュリティ エージェント上で使用できます。

- IoT デバイス上にセキュリティ エージェントがインストールされていません。 このオプションにより、IoT Hub 通信監視が有効になり、セキュリティ監視と管理機能は削減できます。

## <a name="what-does-the-defender-for-iot-agent-do"></a>Defender for IoT エージェントは何を実行しますか?

Defender for IoT エージェントによって、デバイスの構成、動作、および (構成をスキャンすることによる) アクセス、プロセス、接続に対して、デバイス レベルで脅威から保護されます。 Defender for IoT セキュリティ エージェントでは、ビジネス関連データやアクティビティはスキャンされません。

Defender for IoT セキュリティ エージェントはオープン ソースであり、GitHub で 32 ビットと 64 ビットの Windows および Linux バージョンを入手できます (https://github.com/Azure/Azure-IoT-Security )。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>エージェントの依存関係と前提条件は何ですか?

Defender for IoT では、さまざまなプラットフォームがサポートされます。 特定のデバイスのサポートを確認するには、「[Supported Device platforms](how-to-deploy-agent.md)」(サポートされているデバイス プラットフォーム) を参照してください。

## <a name="which-data-is-collected-by-the-agent"></a>どのデータがエージェントによって収集されますか?

接続、アクセス、ファイアウォールの構成、プロセス一覧、および OS ベースラインが、エージェントによって収集されます。

## <a name="how-much-data-will-the-agent-generate"></a>エージェントはどの程度のデータを生成しますか?

エージェント データの生成は、デバイス、アプリケーション、接続の種類、および顧客のエージェントの構成によって左右されます。 デバイスと IoT ソリューションの間での高可変性により、生成されたデータの量を測定しながら、ラボでまずエージェントをデプロイするかまたは設定をテストして、ニーズに適した特定の構成を観察、学習、設定することをお勧めします。 サービスを開始したら、Defender for IoT エージェントでは、構成とカスタマイズのプロセスを支援するために、エージェントのスループットを最適化するための操作に関する推奨事項が提供されます。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>エージェントのメッセージは、IoT Hub からのクォータを使用し尽くしますか?

はい。 エージェントが送信したデータは、IoT Hub のクォータでカウントされます。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>次のステップ エージェントをインストールしましたが、アクティビティまたはログが表示されません。

1. [エージェントのタイプがデバイスの指定された OS プラットフォームに適合する](how-to-deploy-agent.md)ことを確認します。

1. [エージェントがデバイス上で実行している](how-to-agent-configuration.md)ことを確認します。

1. IoT Hub で **セキュリティ** に対して [サービスが正常に有効になっている](quickstart-onboard-iot-hub.md)ことを確認します。

1. デバイスが [IoT Hub で Defender for IoT モジュールを使用して構成されている](quickstart-create-security-twin.md)ことを確認します。

アクティビティまたはログが引き続き利用できない場合は、Defender for IoT パートナーに問い合わせて追加の支援を要請してください。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>インターネット接続が機能を停止すると何が起きますか?

デバイスが実行されている限り、センサーとエージェントは引き続き実行され、データが格納されます。 データは、サイズの構成に従ってセキュリティ メッセージのキャッシュに格納されます。 デバイスが接続を再取得すると、セキュリティ メッセージが送信を再開します。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>エージェントは、デバイスまたは他のインストールされているソフトウェアのパフォーマンスに影響を与える可能性はありますか?

エージェントは、他のアプリケーション/プロセスと同じようにマシン リソースを消費するため、通常のデバイスのアクティビティを妨害しないようにする必要があります。 エージェントが実行されているデバイス上のリソースの消費量は、その設定や構成と連動します。 運用環境にデプロイする前に、含まれている環境でのエージェントの構成を、他の IoT アプリケーションおよび機能との相互運用性とともにテストすることをお勧めします。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>デバイス上でいくらかのメンテナンスを行う予定です。 エージェントはオフにできますか?

エージェントは、オフにすることはできません。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>エージェントが正常に動作しているかどうかをテストする方法はありますか?

エージェントが通信を停止したりセキュリティ メッセージの送信に失敗したりした場合、**デバイスがサイレントである** というアラートが生成されます。

## <a name="next-steps"></a>次のステップ

Defender for IoT の開始方法の詳細については、次の記事を参照してください。

- Defender for IoT の[概要](overview.md)について確認する
- [システムの前提条件](quickstart-system-prerequisites.md)を確認する
- [Defender for IoT での作業を開始する](getting-started.md)方法について確認する
- [Defender for IoT のセキュリティ アラート](concept-security-alerts.md)について理解する
