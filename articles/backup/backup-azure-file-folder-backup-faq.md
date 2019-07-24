---
title: Azure Backup エージェントの FAQ
description: '一般的な質問への回答: Azure Backup エージェントの動作、バックアップとリテンション期間の制限。'
services: backup
author: trinadhk
manager: shreeshd
keywords: バックアップと障害復旧; バックアップ サービス
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: trinadhk
ms.openlocfilehash: c1690fe6d0ce24bd319b042a3850bbfe487ffcfc
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426258"
---
# <a name="questions-about-the-azure-backup-agent"></a>Azure Backup エージェントについての質問
この記事では、Azure Backup エージェントの構成要素が理解しやすいよう、よく寄せられる質問とその回答を記載しています。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

## <a name="configure-backup"></a>バックアップの構成
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>最新の Azure Backup エージェントはどこでダウンロードできますか。 <br/>
Windows Server、System Center DPM、または Windows クライアントをバックアップするための最新のエージェントは、 [こちら](https://aka.ms/azurebackup_agent)からダウンロードできます。 仮想マシンをバックアップする場合は、VM エージェントを使用してください (適切な拡張機能が自動的にインストールされます)。 VM エージェントは、Azure ギャラリーから作成された仮想マシン内に既に存在しています。

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Azure Backup エージェントを構成するときに、コンテナー資格情報の入力が求められます。 コンテナー資格情報には有効期限がありますか。
はい。コンテナー資格情報は 48 時間後に有効期限が切れます。 ファイルの有効期限が切れた場合は、Azure Portal にサインインし、コンテナーからコンテナー資格情報ファイルをダウンロードしてください。

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>ファイルとフォルダーのバックアップ元として、どのような種類のドライブを使用できますか。 <br/>
次のドライブやボリュームはバックアップできません。

* リムーバブル メディア:バックアップ項目のソースはすべて、固定として認識される必要があります。
* 読み取り専用ボリューム:ボリューム シャドウ コピー サービス (VSS) が機能するには、ボリュームが書き込み可能である必要があります。
* オフライン ボリューム:VSS が機能するには、ボリュームがオンラインである必要があります。
* ネットワーク共有:オンライン バックアップを使用してバックアップするボリュームは、サーバーに対してローカルである必要があります。
* BitLocker で保護されているボリューム: バックアップを行う前に、ボリュームのロックを解除する必要があります。
* ファイル システムの識別:サポートされているファイル システムは NTFS だけです。

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>サーバーからバックアップできるのはどのような種類のファイルとフォルダーですか。<br/>
次の種類がサポートされています。

* 暗号化
* 圧縮
* スパース
* 圧縮 + スパース
* ハード リンク:サポートされていません。スキップされます。
* 再解析ポイント:サポートされていません。スキップされます。
* 暗号化 + スパース:サポートされていません。スキップされます。
* 圧縮されたストリーム:サポートされていません。スキップされます。
* スパース ストリーム:サポートされていません。スキップされます。

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>VM 拡張機能を使用して Azure Backup サービスで既にバックアップされている Azure VM に Azure Backup エージェントをインストールすることはできますか。 <br/>
そして、 Azure Backup では、VM 拡張機能を使用している Azure VM には VM レベルのバックアップを提供します。 ゲスト Windows OS 上のファイルとフォルダーを保護するには、ゲスト Windows OS に Azure Backup エージェントをインストールします。

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Azure VM に Azure Backup エージェントをインストールし、Azure VM で提供される一時ストレージ上のファイルとフォルダーをバックアップすることはできますか。 <br/>
はい。 ゲスト Windows OS に Azure Backup エージェントをインストールして、ファイルやフォルダーを一時的なストレージにバックアップすることはできますが、 一時的なストレージのデータが消去されるとバックアップ ジョブは失敗します。また、一時的なストレージのデータが削除された場合、復元先に指定できるのは非揮発性ストレージのみになります。

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>キャッシュ フォルダーの最小サイズ要件を教えてください。 <br/>
キャッシュ フォルダーのサイズによって、バックアップするデータ量が決まります。 キャッシュ フォルダーのボリュームは、バックアップ データの合計サイズと比較した場合に、空き領域を少なくとも 5 から 10% にする必要があります。 ボリュームの空き領域が 5% 未満の場合は、ボリュームのサイズを増やすか、[十分な空き領域があるボリュームにキャッシュ フォルダーを移動](backup-azure-file-folder-backup-faq.md#backup)します。

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>別のデータ センターにサーバーを登録する方法を教えてください。<br/>
バックアップ データは登録されているコンテナーのデータセンターに送信されます。 データセンターを変更する最も簡単な方法は、エージェントをアンインストールしてから再インストールし、変更先のデータセンターに属している新しいコンテナーに登録する方法です。

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Windows Server 2012 の重複除去を使用するサーバーでは、Azure Backup エージェントは動作しますか。 <br/>
はい。 エージェント サービスは、バックアップ操作を準備するときに、重複除去されたデータを通常のデータに変換します。 そのデータをバックアップ用に最適化し、暗号化してから、オンライン バックアップ サービスに送信します。

## <a name="prerequisites-and-dependencies"></a>前提条件と依存関係
### <a name="what-features-of-microsoft-azure-recovery-services-mars-agent-require-net-framework-452-and-higher"></a>.NET framework 4.5.2 以上が必要となるのは、Microsoft Azure Recovery Services (MARS) エージェントのどの機能ですか?
*データの回復*ウィザードから個別のファイルやフォルダーを復元できる[インスタント リストア](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine)機能では、.NET Framework 4.5.2 以上が必要になります。

## <a name="backup"></a>バックアップ
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Azure Backup エージェント用に指定されたキャッシュの場所を変更する方法を教えてください。<br/>
キャッシュの場所は、以下の手順で変更します。

1. 管理者特権でのコマンド プロンプトで次のコマンドを実行して Backup エンジンを停止します。

    ```PS C:\> Net stop obengine```

2. ファイルを移動するのではなく、 空き容量に余裕のある別のドライブにキャッシュ領域フォルダーをコピーします。 元のキャッシュ領域は、バックアップが新しいキャッシュ領域で正常に動作していることを確認した後で削除できます。
3. 次のレジストリ エントリを、新しいキャッシュ領域のフォルダーへのパスで更新します。<br/>

    | レジストリ パス | レジストリ キー | 値 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |

4. 管理者特権でのコマンド プロンプトで次のコマンドを実行して Backup エンジンを再起動します。

    ```PS C:\> Net start obengine```

バックアップが新しいキャッシュ場所に正常に作成されたら、元のキャッシュ フォルダーを削除できます。


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>適切に動作させるためには、Azure Backup エージェントのキャッシュ フォルダーをどこに設定すればよいですか。<br/>
次の場所へのキャッシュ フォルダーの設定はお勧めしません。

* ネットワーク共有またはリムーバブル メディア:キャッシュ フォルダーは、オンライン バックアップを使用したバックアップが必要なサーバーに対してローカルにする必要があります。 ネットワーク上の場所や USB ドライブなどのリムーバブル メディアはサポートされていません
* オフライン ボリューム:キャッシュ フォルダーは、Azure Backup エージェントを使用したバックアップのために、オンラインにしておく必要があります

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>キャッシュ フォルダーの属性としてサポートされていないものはありますか。<br/>
キャッシュ フォルダーでは、次の各属性またはそれらの組み合わせはサポートされていません。

* 暗号化
* 重複除去
* 圧縮
* スパース
* 再解析ポイント

キャッシュ フォルダーとメタデータ VHD には Azure Backup エージェントに必要な属性はありません。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Backup サービスによって使用される帯域幅の量を調整する方法はありますか。<br/>
  はい。帯域幅の調整には、Backup エージェントの **[プロパティの変更]** オプションを使用します。 帯域幅の量と、帯域幅を使用する時間を調整できます。 具体的な手順については、「**[ネットワーク調整を有効にするには](backup-configure-vault.md#enable-network-throttling)**」を参照してください。

## <a name="restore"></a>復元

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>進行中の復元ジョブをキャンセルした場合、どうなりますか? 
進行中の復元ジョブがキャンセルされると、復元プロセスが停止され、すべてのファイルがキャンセル前に復元されます。ロールバックされずに、構成された変換先 (元の場所または別の場所) は保持されます。


## <a name="manage-backups"></a>バックアップの管理

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Azure にデータをバックアップしている Windows サーバーの名前を変更するとどうなりますか。<br/>
サーバーの名前を変更すると、現在構成されているすべてのバックアップが停止します。 バックアップ コンテナーにサーバーの新しい名前を登録します。 コンテナーに新しい名前を登録すると、最初のバックアップ操作は "*完全*" バックアップになります。 以前のサーバー名でコンテナーにバックアップしたデータを回復する必要がある場合は、**データの回復**ウィザードで [**[別のサーバー]**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) オプションを使用します。

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Azure Backup エージェントを使用するバックアップ ポリシーに指定できるファイル パスの最大長はどれくらいですか。 <br/>
Azure Backup エージェントでは NTFS が使用されています。 [ファイル パスの長さの指定は、Windows API による制限を受けます](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths)。 保護するファイルのファイル パスが、Windows API で許容されている長さを超える場合は、親フォルダーまたはディスク ドライブをバックアップしてください。  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Azure Backup エージェントを使用する Azure Backup ポリシーのファイル パスには、どのような文字を使用できますか。 <br>
 Azure Backup エージェントでは NTFS が使用されています。 そのため、ファイルの指定では [NTFS でサポートされている文字](/windows/desktop/FileIO/naming-a-file#naming_conventions) を使用できます。

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>バックアップ ポリシーを設定してあるのに、"このサーバーに対して Azure Backups は構成されていません" という警告が表示されるのはなぜですか。 <br/>
ローカル サーバーに保存されているバックアップ スケジュールの設定がバックアップ コンテナーに格納されている設定と異なる場合、このような警告が表示されることがあります。 サーバーまたは設定が既知の正常な状態に復旧されると、バックアップ スケジュールの同期が失われることがあります。 この警告が表示された場合は、 [バックアップ ポリシーを再構成](backup-azure-manage-windows-server.md) した後、 **[今すぐバックアップ]** を実行して、ローカル サーバーと Azure を再同期してください。
