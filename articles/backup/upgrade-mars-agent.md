---
title: Microsoft Azure Recovery Services (MARS) エージェントのアップグレード
description: Microsoft Azure Recovery Services (MARS) エージェントをアップグレードする方法について説明します。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: a1ee26db962781643e9599069282647658301bac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89181478"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントのアップグレード

この記事では、以下を行う方法について説明します。

* 以前のバージョンの MARS エージェントを使用しているサーバーを特定する
* これらのサーバーで MARS のインストールを更新する

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>以前のバージョンの MARS エージェントを使用しているサーバーを特定する

Azure Backup エージェントと Azure Backup Server をインストールする場合は、以下の手順を実行します。

1. 以前のバージョンのエージェントによってバックアップされている可能性がある登録済みサーバーがある Recovery Services コンテナーに移動します。 古い Azure Backup エージェントが記載されているコンテナーの代表的な一覧については、Azure の Azure Backup Update のアラートを参照してください。
1. Recovery Services コンテナーの左側の **[設定]** セクションで、 **[管理]** セクションの **[バックアップ インフラストラクチャ]** を選択します。
1. Azure Backup Server のインストールの一部としてインストールされている Azure Backup エージェントを検出するには、 **[管理サーバー]** の **[バックアップ管理サーバー]** を選択します。 これにより、Azure Backup Server がインストールされているサーバーと、関連付けられている Azure Backup エージェントのバージョン番号が一覧表示されます。

    ![Azure Backup Server インストールの一部としてインストールされた MARS エージェントの一覧](./media/upgrade-mars-agent/backup-management-servers.png)

1. Microsoft Azure Recovery Services (MARS) エージェントのインストールまたは Azure Backup エージェントのエージェントのバージョンを確認するには、 **[管理サーバー]** の **[保護されたサーバー]** を参照してください。 次に、[バックアップの管理の種類] で **[Azure Backup エージェント]** を選択します。 これにより、Azure Backup エージェントがインストールされているサーバーと、インストールのバージョン番号が一覧表示されます。

    ![MARS エージェントがインストールされているサーバーの一覧](./media/upgrade-mars-agent/protected-servers.png)

1. Azure Backup エージェントのバージョンの列を並べ替えるには、MARS エージェントのインストールでは **[エージェントのバージョン]** 列、または Azure Backup Server のインストールでは **[Azure Backup エージェントのバージョン]** 列を選択します。

1. 前の手順では、2.0.9083.0 未満のバージョンまたはエージェントのバージョンが空白として表示されている Azure Backup エージェントがあるサーバーの一覧が表示されます。 これらのサーバーでは、Azure Backup エージェントを更新する必要があります。

## <a name="update-the-mars-agent-installation-on-the-server"></a>サーバーにインストールされている MARS エージェントの更新

Azure Backup エージェントの更新が必要なサーバーを特定したら、特定した各サーバーに対して次の手順を実行します (Azure Backup Server または MARS エージェントを使用)。 次の手順に進む前に、[最新バージョンの Azure Backup エージェントをダウンロード](https://aka.ms/azurebackup_agent)してください。

1. Azure Backup エージェントが 2.0.9083.0 未満、または空白の行を選択します。 これにより、サーバーの詳細画面が開きます。

    ![最新ではないエージェント バージョンの保護されたサーバー](./media/upgrade-mars-agent/old-agent-version.png)

    ![最新ではないエージェント バージョンの Azure Backup Server](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. **[接続]** を選択して、サーバーに接続するためのリモート デスクトップ接続ファイルを受信するか、サーバー上のリモート デスクトップ接続を使用してサーバーに直接接続します。

    ![リモート デスクトップ接続を使用してサーバーに接続する](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 一覧表示されたサーバーが存在しない場合、または使用停止になっている場合は、以下の残りの手順を無視して、次のサーバーにスキップします。

1. 管理者のログイン詳細を入力し、サインインします。

1. サーバーまたはサーバーのプロキシでインターネット アクセスが制限されている場合は、サーバーまたはプロキシのファイアウォール設定が、使用している Azure Cloud に適した URL を許可するように構成されていることを確認します。

    Azure Cloud | URL
    --- | ---
    Azure Cloud (パブリック) |   `https://login.windows.net`
    Azure China 21Vianet Cloud   | `https://login.chinacloudapi.cn`
    Azure US Government Cloud |   `https://login.microsoftonline.us`
    Azure German Cloud  |  `https://login.microsoftonline.de`

1. Azure Backup エージェントの更新インストーラーをサーバーにコピーします。

    ![Azure Backup エージェントの更新インストーラーをサーバーにコピーする](./media/upgrade-mars-agent/copy-agent-installer.png)

1. インストーラーを実行します。 Microsoft Azure Recovery Services エージェントのアップグレード ウィザードが開きます。

    ![Microsoft Azure Recovery Services Agent のアップグレード ウィザード](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. **[次へ]** を選択します。

1. **[アップグレード]** を選択します。

    ![Microsoft Azure Recovery Services Agent のインストール](./media/upgrade-mars-agent/upgrade-installation.png)

1. 最後の確認画面に、Azure Backup エージェントが正常に更新されたことが示されます。

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>System Center Data Protection Manager (SC DPM) のお客様

System Center Data Protection Manager (SC DPM) サーバーに Azure Backup エージェントがインストールされている場合は、次の手順に従って、DPM サーバーで Azure Backup エージェントの更新が必要かどうかを確認する必要があります。

1. SC DPM サーバーに管理者としてサインインします。
2. DPM コンソールを開きます。
3. コンソールの左下のナビゲーションにある **[管理]** を選択します。
4. 左側のナビゲーションに表示される情報から、Azure Backup エージェントのバージョン情報を探します。
5. バージョンが 2.0.9083.0 未満の場合は、最新の Azure Backup エージェントのインストーラーをダウンロードし、DPM サーバーでインストーラーを実行して、Azure Backup エージェントを更新します。

環境内のすべての DPM サーバーに対して上記の手順を繰り返します。

## <a name="next-steps"></a>次のステップ

[Azure Backup MARS エージェントを使用して Windows マシンをバックアップする](backup-windows-with-mars-agent.md)方法を確認する
