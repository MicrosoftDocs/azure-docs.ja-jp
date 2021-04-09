---
title: System Center Data Protection Manager のトラブルシューティング
description: この記事では、System Center Data Protection Manager を使用しているときに発生する可能性がある問題の解決方法について説明します。
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: 84646f34d905b570855f655465529eb19b717cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86513814"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>System Center Data Protection Manager のトラブルシューティング

この記事では、Data Protection Manager を使用しているときに発生する可能性がある問題の解決方法について説明します。

System Center Data Protection Manager の最新リリース ノートについては、[System Center のドキュメント](/system-center/dpm/dpm-release-notes)を参照してください。 Data Protection Manager のサポートの詳細については、[この一覧](/system-center/dpm/dpm-protection-matrix)を参照してください。

## <a name="error-replica-is-inconsistent"></a>エラー: レプリカに整合性がありません

レプリカは、以下の理由で整合性が失われることがあります。

- レプリカ作成ジョブが失敗した。
- 変更ジャーナルに問題がある。
- ボリューム レベルのフィルター ビットマップに、エラーが含まれている。
- ソース マシンが予期せずにシャットダウンされた。
- 同期ログがオーバーフローしている。
- 実際にレプリカに整合性がない。

この問題を解決するには、次のアクションを実行してください。

- 不整合状態を解消するには、整合性チェックを手動で実行するか、または整合性チェックを毎日実行するようにスケジュールします。
- Microsoft Azure Backup Server および Data Protection Manager の最新バージョンを使用していることを確認します。
- **自動整合性** 設定が有効になっていることを確認します。
- コマンド プロンプトからサービスの再起動を試みてください。 `net stop dpmra` コマンドに続いて `net start dpmra` コマンドを使用します。
- ネットワーク接続と帯域幅の要件が満たされていることを確認します
- ソース マシンが予期せずにシャットダウンされたかどうかを確認します。
- ディスクが正常であり、レプリカに十分な領域があることを確認します。
- 同時に実行されている重複するバックアップ ジョブが存在しないことを確認します。

## <a name="error-online-recovery-point-creation-failed"></a>エラー: オンライン回復ポイントを作成できませんでした

この問題を解決するには、次のアクションを実行してください。

- 最新バージョンの Azure Backup エージェントを使用していることを確認します。
- 保護タスク領域に回復ポイントを手動で作成してみます。
- データ ソースで整合性チェックを実行します。
- ネットワーク接続と帯域幅の要件が満たされていることを確認します
- レプリカ データが不整合な状態になっている場合は、このデータ ソースのディスク回復ポイントを作成します。
- レプリカが存在し、欠落していないことを確認します。
- レプリカに、Update Sequence Number (USN) ジャーナルを作成するための十分な領域があることを確認します。

## <a name="error-unable-to-configure-protection"></a>エラー: 保護を構成できません

このエラーは、Data Protection Manager サーバーが、保護されるサーバーに接続できない場合に発生します。

この問題を解決するには、次のアクションを実行してください。

- 最新バージョンの Azure Backup エージェントを使用していることを確認します。
- Data Protection Manager サーバーと保護されるサーバーの間に接続 (ネットワーク/ファイアウォール/プロキシ) があることを確認します。
- SQL Server を保護している場合は、 **[ログインのプロパティ]**  >  **[NT AUTHORITY\SYSTEM]** プロパティで、**sysadmin** 設定が有効になっていることを確認します。

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>エラー: サーバーが、コンテナー資格情報ファイルに指定されているとおりに登録されていません

このエラーは、Data Protection Manager/Azure Backup Server のデータの回復プロセス中に発生します。 回復プロセスで使用されるコンテナー資格情報ファイルが、Data Protection Manager/Azure Backup Server の Recovery Services コンテナーに含まれていません。

この問題を解決するには、次の手順を実行してください。

1. Data Protection Manager/Azure Backup Server が登録されている Recovery Services コンテナーからコンテナー資格情報ファイルをダウンロードします。
2. ダウンロードした最新のコンテナー資格情報ファイルを使って、コンテナーへのサーバー登録を試みます。

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>エラー: 回復可能なデータがないか、選択したサーバーは Data Protection Manager サーバーではありません

このエラーは、次の理由で発生します。

- Data Protection Manager/Azure Backup Server が、Recovery Services コンテナーに登録されていない。
- サーバーでメタデータをまだアップロードしていない。
- 選択したサーバーが Data Protection Manager/Azure Backup Server ではない。

別の Data Protection Manager/Azure Backup Server が Recovery Services コンテナーに登録されている場合は、次の手順を実行して問題を解決してください。

1. 最新の Azure Backup エージェントがインストールされていることを確認します。
2. 最新のエージェントがインストールされていることを確認したら、1 日待ってから回復プロセスを開始します。 夜間のバックアップ ジョブによって、保護されたすべてのバックアップのメタデータがクラウドにアップロードされます。 このバックアップ データを回復に利用できます。

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>エラー: 指定した暗号化パスフレーズが、サーバーのパスフレーズと一致しません

このエラーは、Data Protection Manager/Azure Backup Server のデータを回復する際の暗号化プロセス中に発生します。 回復プロセスで使用される暗号化パスフレーズが、サーバーの暗号化パスフレーズと一致していません。 その結果、エージェントがデータの暗号化を解除できず、回復が失敗します。

> [!IMPORTANT]
> 暗号化パスフレーズを忘れたり紛失したりした場合、データを回復する他の方法はありません。 唯一の選択肢はパスフレーズを再生成することです。 今後のバックアップ データは、新しいパスフレーズを使用して暗号化します。
>
> データを回復する際は、常に、Data Protection Manager/Azure Backup Server に関連付けられているのと同じ暗号化パスフレーズを指定してください。
>
