---
title: MARS エージェントについて
description: MARS エージェントでサポートされるバックアップ シナリオについて説明します
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87562601"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントについて

この記事では、Azure Backup サービスが Microsoft Azure Recovery Services (MARS) エージェントを使用して、ファイル、フォルダー、およびボリュームまたはシステム状態をオンプレミスのコンピューターから Azure にバックアップおよび復元する方法について説明します。

## <a name="backup-scenarios"></a>バックアップ シナリオ

MARS エージェントでは、次のバックアップ シナリオがサポートされています。

![MARS のバックアップ シナリオ](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **ファイルとフォルダー**:Windows のファイルとフォルダーを選択的に保護します。
- **ボリューム レベル**:ご使用のマシンの Windows ボリューム全体を保護します。
- **システム レベル**:Windows システム状態全体を保護します。

### <a name="additional-scenarios"></a>その他のシナリオ

- **Azure 仮想マシン内の特定のファイルやフォルダーをバックアップ**:Azure 仮想マシン (VM) をバックアップする主な方法は、VM 上で Azure Backup 拡張機能を使用することです。 この拡張機能は、VM 全体をバックアップします。 VM 内の特定のファイルやフォルダーをバックアップする場合は、Azure VM に MARS エージェントをインストールできます。 詳細については、「[アーキテクチャ:組み込みの Azure VM バックアップ](./backup-architecture.md#architecture-built-in-azure-vm-backup)」を参照してください。

- **オフライン シード処理**:通常、Azure へのデータの初回完全バックアップでは大量のデータが転送されるため、より広いネットワーク帯域幅が必要になります。 後続のバックアップでは、差分または増分のデータ量のみが転送されます。 Azure Backup では、初回バックアップが圧縮されます。 *オフライン シード処理* プロセスによって、Azure Backup ではディスクを使用して、圧縮済みの初回バックアップ データをオフラインで Azure にアップロードすることができます。 詳細については、「[Azure Data Box を使用した Azure Backup オフライン バックアップ](offline-backup-azure-data-box.md)」を参照してください。

## <a name="restore-scenarios"></a>復元シナリオ

MARS エージェントでは、次の復元シナリオがサポートされています。

![MARS の復旧シナリオ](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **同じサーバー**:バックアップが最初に作成されたサーバー。
  - **ファイルとフォルダー**:復元する個々のファイルとフォルダーを選択します。
  - **ボリューム レベル**:復元するボリュームと回復ポイントを選択します。 次に、同じ場所または同じマシン上の別の場所に復元します。  既存のファイルのコピーを作成するか、既存のファイルを上書きするか、既存のファイルの回復をスキップします。
  - **システム レベル**:システム状態と回復ポイントを選択して、指定した場所にある同じマシンに復元します。

- **代替サーバー**:バックアップが作成されたサーバー以外のサーバー。
  - **ファイルとフォルダー**:回復ポイントをターゲットマシンに復元する、個々のファイルとフォルダーを選択します。
  - **ボリューム レベル**:別の場所に復元する、ボリュームと回復ポイントを選択します。 既存のファイルのコピーを作成するか、既存のファイルを上書きするか、既存のファイルの回復をスキップします。
  - **システム レベル**:システム状態ファイルとして別のマシンに復元する、システム状態と回復ポイントを選択します。

## <a name="backup-process"></a>バックアップ プロセス

1. Azure portal から [Recovery Service コンテナー](install-mars-agent.md#create-a-recovery-services-vault)を作成し、 **[Backup goals]\(バックアップの目標\)** からファイル、フォルダー、システム状態を選択します。
2. オンプレミスのマシンに [Recovery Service コンテナーの資格情報とエージェント インストーラーをダウンロードします](./install-mars-agent.md#download-the-mars-agent)。

3. [エージェントをインストール](./install-mars-agent.md#install-and-register-the-agent)して、ダウンロードしたコンテナーの資格情報を使用し、マシンを Recovery Services コンテナーに登録します。
4. クライアントのエージェント コンソールから[バックアップを構成](./backup-windows-with-mars-agent.md#create-a-backup-policy)して、バックアップの対象、バックアップのタイミング (スケジュール)、Azure にバックアップを保持する期間 (保持ポリシー) を指定し、保護を開始します。

![Azure Backup エージェントの図](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>関連情報

- **初期バックアップ** (最初のバックアップ) は、バックアップ設定に従って実行されます。  MARS エージェントでは、VSS を使用して、バックアップ用に選択されたボリュームの特定の時点のスナップショットを取得します。 エージェントでは Windows システム ライターの操作のみを使用して、スナップショットをキャプチャします。 アプリケーションの VSS ライターは使用されないため、アプリ整合性スナップショットはキャプチャされません。 VSS を使用してスナップショットを取得した後、MARS エージェントでは、バックアップの構成時に指定したキャッシュ フォルダーに仮想ハード ディスク (VHD) が作成されます。 エージェントでは、各データ ブロックのチェックサムも格納されます。

- **増分バックアップ** (後続バックアップ) は、指定したスケジュールに従って実行されます。 増分バックアップ中は、変更されたファイルが識別され、新しい VHD が作成されます。 VHD は圧縮および暗号化されて、コンテナーに送信されます。 増分バックアップが完了した後、新しい VHD は初期レプリケーション後に作成された VHD にマージされます。 このマージされた VHD では、進行中のバックアップの比較に使用される最新の状態が提供されます。

- MARS エージェントからは、USN (更新シーケンス番号) の変更ジャーナルを使用して **最適化されたモード** でバックアップ ジョブを実行できます。また、ボリューム全体をスキャンすることによってディレクトリまたはファイルの変更を確認すると、**最適化されていないモード** で実行されます。 エージェントでは、ボリューム上のそれぞれのファイルをスキャンし、メタデータと比較して変更されたファイルを判断する必要があるため、最適化されていないモードはより遅くなります。  **初期バックアップ** は、常に最適化されていないモードで実行されます。 前のバックアップが失敗した場合、次のスケジュールされたバックアップ ジョブは最適化されていないモードで実行されます。 これらのモードとその検証方法の詳細については、[こちらの記事](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)を参照してください。

## <a name="next-steps"></a>次のステップ

[MARS エージェントのサポート マトリックス](./backup-support-matrix-mars-agent.md)

[MARS エージェントに関する FAQ](./backup-azure-file-folder-backup-faq.md)
