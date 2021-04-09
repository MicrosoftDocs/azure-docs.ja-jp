---
title: 'クイックスタート: 概要'
description: このクイックスタートでは、Defender for IoT のデプロイに関する基本的なワークフローを理解して作業を開始する方法について学習します。
ms.topic: quickstart
ms.date: 2/18/2021
ms.openlocfilehash: c6136d734570714b691a4ba3c2a72305c4c85590
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784494"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>クイックスタート: Defender for IoT での作業を開始する

この記事では、Azure Defender for IoT を設定するために実行する手順の概要について説明します。 このプロセスでは、次のことを行う必要があります。

- Azure Defender for IoT ポータルでサブスクリプションとセンサーを登録します。
- センサーとオンプレミス管理コンソール ソフトウェアをインストールします。
- センサーと管理コンソールの初回のアクティブ化を実行します。

## <a name="prerequisites"></a>前提条件

なし

## <a name="permission-requirements"></a>権限の要件

一部の設定手順では、特定のユーザーのアクセス許可が必要です。

センサーと管理コンソールをアクティブにし、SSL/TLS 証明書をアップロードして、新しいパスワードを生成するには、管理ユーザーのアクセス許可が必要です。

Azure Defender for IoT ポータル ツールに対するユーザー アクセス許可を次の表で説明します。

| 権限 | セキュリティ閲覧者 | セキュリティ管理者 | サブスクリプションの共同作成者 | サブスクリプションの所有者 |
|--|--|--|--|--|
| Defender for IoT の画面とデータのすべてを表示する | ✓ | ✓ | ✓ | ✓ |
| センサーをオンボードする  |  |  ✓ | ✓ | ✓ |
| 料金を更新する  |  |  ✓ | ✓ | ✓ |
| パスワードを回復する  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>ソリューション インフラストラクチャを特定する

**ネットワーク設定のニーズを明確にする**

ネットワーク アーキテクチャ、監視対象の帯域幅、およびその他のネットワークの詳細を調査します。 詳細については、[Azure Defender for IoT network の設定の概要](how-to-set-up-your-network.md)に関する記事を参照してください。

**ネットワーク負荷を処理するために必要なセンサーと管理コンソール アプライアンスを明確にする**

Azure Defender for IoT では、物理的な配置と仮想デプロイの両方をサポートしています。 物理的な配置では、さまざまな認定アプライアンスを購入できます。 詳細については、「[必要なアプライアンスを特定する](how-to-identify-required-appliances.md)」を参照してください。

監視するデバイスのおおよその数を計算することをお勧めします。 後で Azure サブスクリプションをポータルに登録するときに、この番号を入力するように求められます。 数値は 1,000 秒間隔で追加できます。 監視対象デバイスの数は、"*コミット済みデバイス*" と呼ばれます。

## <a name="register-with-azure-defender-for-iot"></a>Azure Defender for IoT に登録する

登録には次のものが含まれます。

- Defender for IoT への Azure サブスクリプションのオンボード。
- コミット済みデバイスの定義。
- オンプレミス管理コンソールのアクティブ化ファイルのダウンロード。

登録するには、次の手順を実行します。

1. Azure Defender for IoT ポータルに移動します。
1. **[Onboard subscription]\(サブスクリプションのオンボード\)** を選択します。
1. **[価格]** ページでサブスクリプションを選択するか、新しいサブスクリプションを作成してコミット済みデバイスの数を追加します。
1. **[Download the on-premises management console]\(オンプレミス管理コンソールのダウンロード\)** タブを選択し、ダウンロードしたアクティブ化ファイルを保存します。 このファイルには、定義したコミット済みデバイスがまとめられています。 このファイルは、最初のサインイン後に管理コンソールにアップロードされます。

サブスクリプションをオフボードする方法について詳しくは、「[サブスクリプションをオフボードする](how-to-manage-sensors-on-the-cloud.md#offboard-a-subscription)」を参照してください。

## <a name="install-and-set-up-the-on-premises-management-console"></a>オンプレミス管理コンソールをインストールして設定する

オンプレミス管理コンソール アプライアンスを取得した後:

- Azure Defender for IoT ポータルから ISO パッケージをダウンロードします。
- このソフトウェアをインストールします。
- 管理コンソールの初期設定をアクティブにして実行します。

インストールして設定するには:

1. Defender for IoT ポータルから **[作業の開始]** を選択します。
1. **[On-premises management console]\(オンプレミス管理コンソール\)** タブを選択します。
1. バージョンを選択し、 **[ダウンロード]** を選択します。
1. オンプレミス管理コンソール ソフトウェアをインストールします。 詳細については、[Defender for IoT のインストール](how-to-install-software.md)に関する記事を参照してください。
1. 管理コンソールをアクティブにして設定します。 詳細については、「[オンプレミス管理コンソールをアクティブにしてセットアップする](how-to-activate-and-set-up-your-on-premises-management-console.md)」を参照してください。

## <a name="onboard-a-sensor"></a>センサーをオンボードする

センサーを Azure Defender for IoT に登録してセンサー アクティブ化ファイルをダウンロードし、センサーをオンボードします。

1. センサー名を定義し、サブスクリプションに関連付けます。
1. センサーの管理モードを定義します。

   - **クラウド接続センサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 さらに、アラート情報は、IoT ハブを通じて配信され、Azure Sentinel などの他の Azure サービスと共有できます。

   - **ローカル管理のセンサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 エアギャップ ネットワークで作業していて、複数のローカル管理センサーによって検出されるすべての情報を一元的に表示したい場合は、オンプレミスの管理コンソールを使用します。 

1. センサーのアクティブ化ファイルをダウンロードする

詳細については、「[Defender for IoT ポータルでのセンサーのオンボードと管理](how-to-manage-sensors-on-the-cloud.md)」を参照してください。

## <a name="install-and-set-up-the-sensor"></a>センサーをインストールして設定する

Azure Defender for IoT ポータルから ISO パッケージをダウンロードし、ソフトウェアをインストールして、センサーを設定します。

1. Defender for IoT ポータルから **[作業の開始]** を選択します。
1. **[Set up sensor]\(センサーの設定\)** を選択します。
1. バージョンを選択し、 **[ダウンロード]** を選択します。
1. センサー ソフトウェアをインストールします。 詳細については、[Defender for IoT のインストール](how-to-install-software.md)に関する記事を参照してください。
1. センサーをアクティブにして設定します。 詳細については、[センサーのサインインとアクティブ化](how-to-activate-and-set-up-your-sensor.md)に関する記事を参照してください。

## <a name="connect-sensors-to-an-on-premises-management-console"></a>センサーをオンプレミス管理コンソールに接続します。

センサーを管理コンソールに接続して、次のことを確認します。

- センサーからオンプレミス管理コンソールにアラートとデバイス インベントリ情報が送信されること。

- オンプレミス管理コンソールを使用して、センサーのバックアップを実行し、センサーによって検出されたアラートを管理し、センサーの切断を調査し、接続されたセンサーに対してその他のアクティビティを実行できること。

同じネットワークを監視する複数のセンサーを 1 つのゾーンにグループ化することをお勧めします。 こうすることで、複数のセンサーによって収集された情報がまとめられます。

詳細については、「[センサーをオンプレミス管理コンソールに接続する](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)」を参照してください。

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Azure Sentinel にアラート情報を設定する (省略可能)

Azure Sentinel を構成して Azure Sentinel にアラート情報を送信します。 「[Defender for IoT からのデータを Azure Sentinel に接続する](how-to-configure-with-sentinel.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Defender for IoT へようこそ](overview.md)
> [Azure Defender for IoT のアーキテクチャ](architecture.md)
