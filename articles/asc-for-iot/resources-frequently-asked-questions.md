---
title: Azure Security Center for IoT プレビューに関してよく寄せられる質問| Microsoft Docs
description: Azure Security Center for IoT の機能とサービスに関してよく寄せられる質問に対する回答を確認します。
services: asc-for-iot
ms.service: ASCforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: cd9e77e09e2a2fd5fe1108e98da028d26fc623c7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786845"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Azure Security Center for IoT に関してよく寄せられる質問  

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Security Center (ASC) for IoT に関してよく寄せられる質問の一覧を提供します。 

## <a name="does-azure-provide-support-for-iot-security"></a>Azure は IoT セキュリティのサポートを提供しますか。

Azure は、総合的なセキュリティ ソリューションの一部として、Azure Security Center により、IoT セキュリティの監視と管理のための統合ビューを提供します。 アプリケーション開発者であれば、IoT アプリケーションのセキュリティを管理するために IoT Hub ビューを使用できます。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>IoT セキュリティのための Azure の一意の価値提案とは何ですか。

ASC for IoT により、企業は IoT ソリューション全体に既存のサイバー セキュリティ ビューを拡張することができます。 Azure はビジネス ソリューションのエンド ツー エンドのビューを提供します。これによりビジネス関連のアクションや意思決定を、エンタープライズ セキュリティの状態や収集されたデータに基づいて行うことができるようになります。 Azure IoT、Azure IoT Edge、Azure Sphere、Azure Central、および Azure Security Center を結合して使用するセキュリティにより、必要とするセキュリティでソリューションを作成できます。

## <a name="who-is-asc-for-iot-made-for"></a>ASC for IoT は誰を対象に作成されましたか? 

ASC for IoT は、Azure IoT Hub セキュリティ内で統合されており、日常のビジネス ソリューション セキュリティ操作の管理を提供します。 ASC for IoT は、Azure Security Center の機能にも統合され、総合的なセキュリティ ソリューションの一部として、IoT のセキュリティを監視および管理するための統合ビューを提供します。

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>ASC for IoT は、競争他社製品とどのように比較されますか?

他社のソリューションは顧客が独自のソリューションを作成できるようにする一連の機能を提供しますが、ASC for IoT は、関連するすべての Azure リソースのセキュリティ全体の広範囲なビューを提供する、一意のエンド ツー エンド IoT セキュリティ ソリューションを提供します。 Azure により、既存のデバイスの管理ツールとの簡単な統合のために、高速なデプロイと IoT Hub モジュール ツインとの完全な統合が実現します。

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Azure Security Center (ASC) の顧客になる必要はありますか?

いいえ、ただしお客様となっていただくことが推奨されています。 ASC なしで、ASC for IoT は限られた接続リソース データを受信し、潜在的な攻撃対象領域、脅威、および潜在的な攻撃の限られた分析を行います。 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Azure IoT の顧客になる必要がありますか?

はい。 ASC for IoT は、Azure IoT 接続およびインフラストラクチャに依存します。

## <a name="do-i-have-to-install-an-agent"></a>エージェントをインストールする必要はありますか?

IoT デバイス上でのエージェントのインストールは、Microsoft ASC for IoT を有効にするために必須ではありません。 選択内容に応じて、さまざまなレベルのセキュリティ監視および管理機能を提供する、次の 3 つのオプションから選択できます。

1. 変更の有無に関わらず、ASC for IoT セキュリティ エージェントをインストールします。 このオプションにより、デバイスの動作とアクセスへの最高レベルの強化されたセキュリティ洞察が提供されます。

2. 独自のエージェントを作成し、Microsoft ASC for IoT のセキュリティ メッセージ スキーマを実装します。 このオプションにより、Microsoft ASC for IoT 分析ツールを、デバイスのセキュリティ エージェント上で使用できます。

3. IoT デバイス上にセキュリティ エージェントがインストールされていません。 このオプションにより、IoT Hub 通信監視が有効になり、セキュリティ監視と管理機能は削減できます。 

## <a name="what-does-the-asc-for-iot-agent-do"></a>ASC for IoT エージェントは何を実行しますか?

ASC for IoT エージェントは、デバイスの構成、動作、および (構成をスキャンすることによる) アクセス、プロセス、接続に対して、デバイス レベルで脅威からの保護を提供します。 ASC for IoT セキュリティ エージェントは、ビジネス関連データまたはアクティビティをスキャンしません。

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>どこで ASC for IoT セキュリティ エージェントを入手できますか。

ASC for IoT セキュリティ エージェントは、オープン ソースであり、GitHub で 32 ビットと 64 ビットの Windows および Linux バージョンを入手できます (https://github.com/Azure/Azure-Security-IoT-Preview)。

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>ASC for IoT エージェントはどこにインストールされますか? 

GitHub (https://github.com/Azure/Azure-Security-IoT-Preview) には、詳細なインストールとエージェントのデプロイ情報があります。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>エージェントの依存関係と前提条件は何ですか?

ASC for IoT は、さまざまなプラットフォームをサポートします。 特定のデバイスのサポートを確認するには、「[Supported Device platforms](how-to-deploy-agent.md)」(サポートされているデバイス プラットフォーム) を参照してください。 

## <a name="which-data-is-collected-by-the-agent"></a>どのデータがエージェントによって収集されますか?

接続、アクセス、ファイアウォールの構成、プロセス一覧、および OS ベースラインが、エージェントによって収集されます。

## <a name="how-much-data-will-the-agent-generate"></a>エージェントはどの程度のデータを生成しますか?

エージェント データの生成は、デバイス、アプリケーション、接続の種類、および顧客のエージェントの構成によって左右されます。 デバイスと IoT ソリューションの間での高可変性により、生成されたデータの量を測定しながら、ラボでまずエージェントをデプロイするかまたは設定をテストして、ニーズに適した特定の構成を観察、学習、設定することをお勧めします。 サービスを開始したら、ASC for IoT エージェントは、構成とカスタマイズのプロセスを支援するために、エージェントのスループットを最適化するための操作に関する推奨事項を提供します。

## <a name="how-can-i-control-my-billing"></a>請求はどのように管理できますか?

ASC for IoT は、エージェントによって生成されるデータの量を増減する、構成可能なエージェント スキャン、データ バッファー、およびカスタム アラートの作成機能を提供します。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>エージェントのメッセージは、IoT Hub からのクォータを使用し尽くしますか?

はい。 エージェントが送信したデータは、IoT Hub のクォータでカウントされます。 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>次のステップ エージェントをインストールしましたが、アクティビティまたはログが表示されません。

1. [エージェントのタイプがデバイスの指定された OS プラットフォームに適合する](how-to-deploy-agent.md)ことを確認します。

1. [エージェントがデバイス上で実行している](how-to-agent-configuration.md)ことを確認します。

2. IoT Hub で**セキュリティ**に対して[サービスが正常に有効になっている](quickstart-onboard-iot-hub.md)ことを確認します。 

3. デバイスが [IoT Hub で ASC for IoT モジュールを使用して構成されている](quickstart-create-security-twin.md)ことを確認します。  

アクティビティまたはログが引き続き利用できない場合は、ASC for IoT パートナーに問い合わせて追加の支援を要請してください。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>インターネット接続が機能を停止すると何が起きますか?

デバイスが実行されている限り、エージェントは引き続き実行し、データを格納します。 データは、サイズの構成に従ってセキュリティ メッセージのキャッシュに格納されます。 デバイスが接続を再取得すると、セキュリティ メッセージが送信を再開します。 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>デバイスが再起動したら、セキュリティ エージェントは自己復旧しますか?

セキュリティ エージェントは、各デバイスの再起動で自動的に再実行するように設計されています。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>エージェントは、デバイスまたは他のインストールされているソフトウェアのパフォーマンスに影響を与える可能性はありますか?

エージェントは、他のアプリケーション/プロセスと同じようにマシン リソースを消費するため、通常のデバイスのアクティビティを妨害しないようにする必要があります。 エージェントが実行されているデバイス上のリソースの消費量は、その設定や構成と連動します。 運用環境にデプロイする前に、含まれている環境でのエージェントの構成を、他の IoT アプリケーションおよび機能との相互運用性とともにテストすることをお勧めします。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>デバイス上でいくらかのメンテナンスを行う予定です。 エージェントはオフにできますか?

エージェントは、オフにすることはできません。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>エージェントが正常に動作しているかどうかをテストする方法はありますか? 

エージェントが通信を停止したりセキュリティ メッセージの送信に失敗したりした場合、**デバイスがサイレントである**というアラートが生成されます。

## <a name="can-i-create-my-own-alerts"></a>独自のアラートを作成できますか?

はい。 IP アドレスやオープン ポートなどの事前に決められた一連の動作に関する、カスタマイズされたアラートを設定できます。 「[カスタム アラートの作成](quickstart-create-custom-alerts.md)」を参照して、カスタム アラートとその作成方法の詳細について学習してください。 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>ログはどこに表示できますか? ログはカスタマイズできますか?

- 接続されている Log Analytics ワークスペースを使用して、アラートと推奨事項を表示します。 ワークスペースで、ストレージのサイズと持続期間を構成します。

- セキュリティ エージェントからの生データは、Log Analytics アカウントにも格納できます。 このオプションの構成を変更する前に、サイズ、持続期間、ストレージの要件、および関連するコストを考慮してください。 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>ASC for IoT をモジュール ID に追加する必要はありますか? 用途

ASC for IoT モジュールは、エージェントの構成と管理に使用されます。


## <a name="next-steps"></a>次の手順

ASC for IoT の開始方法の詳細については、次の記事を参照してください。


- ASC for IoT の[概要](overview.md)を参照してください
- [サービスの前提条件](service-prerequisites.md)を確認してください
- [開始する](getting-started.md)方法の詳細をご覧ください
- [ASC for IoT のセキュリティ アラート](concept-security-alerts.md)を参照してください

