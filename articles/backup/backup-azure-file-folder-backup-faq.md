---
title: Microsoft Azure Recovery Services (MARS) エージェント – FAQ
description: Azure Backup を使用したファイルとフォルダーのバックアップに関する一般的な質問に対応します。
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 1edfaed99e60409774496c5ae75df8be99a8fe1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94917360"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>よく寄せられる質問 - Microsoft Azure Recovery Services (MARS) エージェント

この記事では、[Azure Backup](backup-overview.md) サービスにある Microsoft Azure Recovery Services (MARS) エージェントを使ったデータのバックアップに関する一般的な質問への回答を示します。

## <a name="configure-backups"></a>バックアップを構成する

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>最新バージョンの MARS エージェントは、どこでダウンロードできますか。

Windows Server マシン、System Center DPM、および Microsoft Azure Backup サーバーをバックアップするときに使用される最新の MARS エージェントは、[ダウンロード](https://aka.ms/azurebackup_agent)のページから入手できます。

### <a name="where-can-i-download-the-vault-credentials-file"></a>コンテナー資格情報ファイルはどこでダウンロードできますか。

Azure portal で、お使いのコンテナーの **[プロパティ]** に移動します。 **[Backup の資格情報]** で、 **[既に最新の Recovery Services エージェントを使用しています]** チェック ボックスをオンにします。 **[ダウンロード]** を選択します。

![資格情報のダウンロード](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>コンテナー資格情報の有効期間はどのくらいですか。

コンテナー資格情報は 10 日後に有効期限が切れます。 資格情報ファイルが期限切れになったら、Azure portal から再度ファイルをダウンロードしてください。

### <a name="what-characters-are-allowed-for-the-passphrase"></a>パスフレーズに許可されるのは、どのような文字ですか。

パスフレーズでは、ASCII 文字セットで [ASCII 値が 127 以下](/office/vba/language/reference/user-interface-help/character-set-0127)の文字を使用する必要があります。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>どのようなドライブからファイルとフォルダーをバックアップできますか。

次の種類のドライブおよびボリュームをバックアップすることはできません。

* リムーバブル メディア:バックアップ項目のソースはすべて、固定として認識される必要があります。
* 読み取り専用ボリューム:ボリューム シャドウ コピー サービス (VSS) が機能するには、ボリュームが書き込み可能である必要があります。
* オフライン ボリューム:VSS が機能するには、ボリュームがオンラインである必要があります。
* ネットワーク共有:オンライン バックアップを使用してバックアップするボリュームは、サーバーに対してローカルである必要があります。
* BitLocker で保護されているボリューム: バックアップを行う前に、ボリュームのロックを解除する必要があります。
* ファイル システムの識別:サポートされているファイル システムは NTFS だけです。

### <a name="what-file-and-folder-types-are-supported"></a>どのようなファイルとフォルダーの種類がサポートされますか。

バックアップでサポートされているファイルとフォルダーの種類についての[詳細を確認](backup-support-matrix-mars-agent.md#supported-file-types-for-backup)してください。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>MARS エージェントを使用して、Azure VM 上にファイルおよびフォルダーをバックアップできますか。  

はい。 Azure Backup では、Azure VM エージェント対応の VM 拡張機能を使用している Azure VM には、VM レベルのバックアップを提供しています。 VM にあるゲスト Windows オペレーティング システム上にファイルとフォルダーをバックアップする場合は、MARS エージェントをインストールして実現できます。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>MARS エージェントを使用して、Azure VM 用の一時ストレージ上にファイルおよびフォルダーをバックアップできますか。

はい。 MARS エージェントをインストールして、ゲスト Windows オペレーティング システムにあるファイルおよびフォルダーを一時ストレージにバックアップします。

* 一時ストレージのデータが消去されると、バックアップ ジョブは失敗します。
* 一時ストレージのデータが削除された場合、非揮発性ストレージにしか復元できません。

### <a name="how-do-i-register-a-server-to-another-region"></a>別のリージョンにサーバーを登録する方法を教えてください。

バックアップ データは、サーバーが登録されているコンテナーのデータセンターに送信されます。 データセンターを変更する最も簡単な方法は、エージェントをアンインストールしてから再インストールし、必要なリージョン内にある新しいコンテナーにマシンを登録することです。

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS エージェントでは、Windows Server 2012 の重複除去をサポートしていますか。

はい。 MARS エージェントでは、バックアップ操作を準備するときに、重複除去されたデータを通常のデータに変換します。 そのデータをバックアップ用に最適化し、データを暗号化してから、暗号化データをコンテナーに送信します。

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>MARS エージェントのインストールと構成に管理者のアクセス許可は必要ですか。

必要です。MARS エージェントのインストールと MARS コンソールを使用したバックアップの構成は、保護されるサーバー上のローカル管理者が行う必要があります。

### <a name="what-is-the-impact-on-mars-agent-backups-of-transferring-the-vault-subscription-to-a-different-azure-ad-directory"></a>コンテナーのサブスクリプションを別の Azure AD ディレクトリに転送した場合の MARS エージェントのバックアップには、どのような影響がありますか?

Azure AD ディレクトリの変更は、MARS エージェントのバックアップに影響しません。 

## <a name="manage-backups"></a>バックアップの管理

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>バックアップ用に構成された Windows マシンの名前を変更すると、どうなりますか。

Windows マシンの名前を変更すると、現在構成されているすべてのバックアップが停止します。

* Backup コンテナーに新しいマシン名を登録する必要があります。
* コンテナーに新しい名前を登録すると、最初の操作は "*完全*" バックアップになります。
* 以前のサーバー名を使ってコンテナーにバックアップしたデータを回復する必要がある場合は、データの回復ウィザードで別の場所に復元するためのオプションを使用します。 [詳細については、こちらを参照してください](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>バックアップするファイル パスの最大長はいくつですか。

MARS エージェントは NTFS に依存しており、[Windows API](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths) によって制限されているファイルパス長の仕様を利用します。 保護するファイルが許可された値よりも長くなる場合は、親フォルダーまたはディスク ドライブをバックアップします。  

### <a name="what-characters-are-allowed-in-file-paths"></a>ファイル パスに許可されるのは、どのような文字ですか。

MARS エージェントは NTFS に依存しており、ファイルの名前/パスには[サポートされている文字](/windows/win32/FileIO/naming-a-file#naming-conventions)を許可します。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>"このサーバーに対して Azure Backups が構成されていない" ことを示す警告が表示されます。

ローカル サーバーに保存されているバックアップ スケジュールの設定がバックアップ コンテナーに格納されている設定と異なる場合、バックアップ ポリシーを構成済みでも、この警告が表示されることがあります。

* サーバーまたは設定が既知の正常な状態に復旧されると、バックアップ スケジュールが同期されないことがあります。
* この警告を受信した場合は、バックアップ ポリシーをもう一度[構成](backup-azure-manage-windows-server.md)してから、オンデマンド バックアップを実行して、ローカル サーバーと Azure を再同期してください。

## <a name="manage-the-backup-cache-folder"></a>バックアップ キャッシュ フォルダーの管理

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>キャッシュ フォルダーの最小サイズ要件を教えてください。

キャッシュ フォルダーのサイズによって、バックアップするデータ量が決まります。

* キャッシュ フォルダー ボリュームには、バックアップ データの合計サイズの少なくとも 5 から 10% に相当する空き領域が必要になります。
* ボリュームの空き領域が 5% 未満の場合は、[こちらの手順](#how-do-i-change-the-cache-location-for-the-mars-agent)に従って、ボリューム サイズを増やすか、十分な容量があるボリュームにキャッシュ フォルダーを移動します。
* Windows のシステム状態をバックアップする場合は、キャッシュ フォルダーを含むボリューム内に 30 から 35 GB の追加の空き領域が必要になります。

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>スクラッチ フォルダーが有効でアクセス可能かどうかを確認する方法

1. 既定では、スクラッチ フォルダーは `\Program Files\Microsoft Azure Recovery Services Agent\Scratch` にあります。
2. スクラッチ フォルダーの場所のパスが、次に示すレジストリ キー エントリの値と一致していることを確認します。

    | レジストリ パス | レジストリ キー | 値 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>MARS エージェント用のキャッシュの場所を変更する方法を教えてください。

1. 管理者特権のコマンド プロンプトで次のコマンドを実行して、Backup エンジンを停止します。

    ```Net stop obengine```
2. システム状態のバックアップが構成されている場合は、[ディスクの管理] を開き、`"CBSSBVol_<ID>"` という形式の名前を持つディスクをマウント解除します。
3. 既定では、スクラッチ フォルダーは `\Program Files\Microsoft Azure Recovery Services Agent\Scratch` にあります。
4. 十分な容量がある別のドライブに `\Scratch`フォルダー全体をコピーします。 コンテンツがコピーされ、移動されていないことを確認します。
5. 新しく移動されたスクラッチ フォルダーのパスを使って、次のレジストリ エントリを更新します。

    | レジストリ パス | レジストリ キー | 値 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新しいスクラッチ フォルダーの場所* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新しいスクラッチ フォルダーの場所* |

6. 管理者特権のコマンド プロンプトで、Backup エンジンを再開します。

    ```command
    Net stop obengine

    Net start obengine
    ```

7. オンデマンド バックアップを実行します。 新しい場所を使ってバックアップが正常に終了した後は、元のキャッシュ フォルダーを削除できます。

### <a name="where-should-the-cache-folder-be-located"></a>キャッシュ フォルダーはどこに配置する必要がありますか。

次の場所へのキャッシュ フォルダーの設定はお勧めしません。

* ネットワークの共有/リムーバブル メディア:キャッシュ フォルダーは、オンライン バックアップを使用したバックアップが必要なサーバーに対してローカルにする必要があります。 ネットワーク上の場所や USB ドライブなどのリムーバブル メディアはサポートされていません。
* オフライン ボリューム:キャッシュ フォルダーは、Azure Backup エージェントを使用したバックアップのために、オンラインにしておく必要があります

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>サポートされていないキャッシュ フォルダーの属性はありますか。

キャッシュ フォルダーでは、次の各属性またはそれらの組み合わせはサポートされていません。

* Encrypted
* 重複除去
* Compressed
* スパース
* 再解析ポイント

キャッシュ フォルダーとメタデータ VHD には Azure Backup エージェントに必要な属性はありません。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>バックアップに使用される帯域幅の量を調整する方法はありますか。

はい。帯域幅とタイミングを調整するには、MARS エージェントにある **[プロパティの変更]** オプションを使用できます。 [詳細については、こちらを参照してください](backup-windows-with-mars-agent.md#enable-network-throttling)。

## <a name="restore"></a>復元

### <a name="manage"></a>管理する

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>パスフレーズを忘れた場合、復旧できますか?

Azure Backup エージェントでは、バックアップしたデータを復元中に暗号化を解除するには、パスフレーズ (登録時に指定したもの) が必要です。 忘れたパスフレーズを処理するためのオプションを理解するには、以下のシナリオを確認してください。

| 元のコンピューター <br> " *(バックアップが作成されているソース マシン)* " | Passphrase | 利用可能なオプション |
| --- | --- | --- |
| 利用可能 |忘れた |バックアップが作成されている元のコンピューターが利用可能であり、同じ Recovery Services コンテナーにまだ登録されている場合は、次の[手順](./backup-azure-manage-mars.md#re-generate-passphrase)に従って、パスフレーズを再生成することができます。  |
| 忘れた |忘れた |データを復旧できないか、データを利用できません |

次の条件を考慮してください。

* エージェントをアンインストールして、元の同じマシンに再登録する場合は、次のようになります。
  * "*同じパスフレーズ*" を使用すると、バックアップしたデータを復元することができます。
  * "*異なるパスフレーズ*" を使用すると、バックアップしたデータを復元することができません。
* "*別のマシン*" にエージェントをインストールする場合は、次のようになります。
  * "*同じパスフレーズ*" (元のマシンで使用していたもの) を使用すると、バックアップしたデータを復元することができます。
  * "*異なるパスフレーズ*" を使用すると、バックアップしたデータを復元することができません。
* 元のコンピューターが破損して、MARS コンソールからパスフレーズを再生成することができないものの、MARS エージェントによって使用される元のスクラッチ フォルダーを復元したり、アクセスしたりすることができる場合は、パスワードを忘れた場合に復元することができる可能性があります。 さらにヘルプが必要な場合は、カスタマー サポートにお問い合わせください。

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>バックアップが作成されている元のコンピューターを紛失した場合、復旧するにはどうすればよいですか?

元のコンピューターと同じパスフレーズ (登録時に指定したもの) がある場合は、バックアップされたデータを別のコンピューターに復元できます。 復元オプションを理解するには、以下のシナリオを確認してください。

| 元のコンピューター | Passphrase | 利用可能なオプション |
| --- | --- | --- |
| 紛失 |利用可能 |元のコンピューターの登録時に指定したものと同じパスフレーズを持つ別のコンピューターに、MARS エージェントをインストールして登録することができます。 **[復旧オプション]**  >  **[別の場所]** を選択して、復元を実行します。 詳細については、[こちらの記事](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)を参照してください。
| 忘れた |忘れた |データを復旧できないか、データを利用できません |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>バックアップジョブが失敗したか、長時間実行されていません。 保有期間を過ぎています。 引き続き復元できますか?

安全策として、Azure Backup では、保有期間が過ぎている場合でも最新の回復ポイントが保持されます。 バックアップが再開され、新しい回復ポイントが使用できるようになると、指定した保有期間に従って古い回復ポイントが削除されます。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>進行中の復元ジョブをキャンセルした場合、どうなりますか。

進行中の復元ジョブがキャンセルされると、復元プロセスが停止します。 すべてのファイルがキャンセル前の状態に復元され、ロールバックされずに、構成された宛先 (元の場所または別の場所) に保持されます。

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS エージェントでは、ファイル、フォルダー、ボリュームに設定された ACL のバックアップと復元は行われますか?

* MARS エージェントでは、ファイル、フォルダー、ボリュームに設定された ACL のバックアップが行われます。
* ボリューム復元の復旧オプションについては、MARS エージェントでは、復旧中のファイルまたはフォルダーへの ACL アクセス許可の復元をスキップするオプションが用意されています。
* 個々のファイルやフォルダーの復旧オプションについては、MARS エージェントでは、ACL アクセス許可を使用して復元が行われます (ACL 復元をスキップするオプションはありません)。

## <a name="next-steps"></a>次のステップ

Windows マシンのバックアップ方法を[確認してください](tutorial-backup-windows-server-to-azure.md)。
