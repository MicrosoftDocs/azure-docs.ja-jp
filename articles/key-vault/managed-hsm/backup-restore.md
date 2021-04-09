---
title: Azure Managed HSM の完全なバックアップと復元、および選択的な復元
description: このドキュメントでは、完全なバックアップと復元、および選択的な復元について説明します
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e926dcd4b05d137c7927bdfe5221923d25d4670c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100093490"
---
# <a name="full-backup-and-restore"></a>完全なバックアップと復元

> [!NOTE]
> この機能は、マネージド HSM というリソースの種類でのみ使用できます。

Managed HSM では、すべてのキー、バージョン、属性、タグ、およびロールの割り当てを含む、HSM のコンテンツ全体の完全バックアップを作成できます。 バックアップは、HSM のセキュリティ ドメインに関連付けられた暗号化キーを使用して暗号化されます。

バックアップはデータ プレーン操作です。 バックアップ操作を開始する呼び出し元には、データ アクション **Microsoft.KeyVault/managedHsm/backup/start/action** を実行するためのアクセス許可が必要です。

完全バックアップを実行するアクセス許可があるのは、次の組み込みロールのみです。
- Managed HSM 管理者
- Managed HSM バックアップ

完全バックアップを実行するには、次の情報を指定する必要があります。
- HSM の名前または URL
- ストレージ アカウント名
- ストレージ アカウントの BLOB ストレージ コンテナー
- アクセス許可 `crdw` を備えたストレージ コンテナーの SAS トークン

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>完全バックアップ

バックアップは実行時間の長い操作ですが、ジョブ ID は直ちに返されます。 このジョブ ID を使用して、バックアップ プロセスの状態を確認できます。 バックアップ プロセスでは、名前パターン **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** に従って、指定されたコンテナー内にフォルダーが作成されます。HSM_NAME はバックアップの対象となっているマネージド HSM の名前です。YYYY、MM、DD、HH、MM、mm、SS はそれぞれが年、月、日、時、分、秒を表し、バックアップ コマンドが受信された日付と時刻 (UTC) を示します。

バックアップの進行中は、一部の HSM パーティションで大量のバックアップ操作が実行されるため、HSM が最大のスループットで動作しない可能性があります。

```azurecli-interactive
# time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>完全復元

完全復元を実行すると、以前のバックアップを使用して、すべてのキー、バージョン、属性、タグ、ロールの割り当てを含む HSM のコンテンツを完全に復元できます。 現在 HSM に格納されているものはすべて消去され、ソースのバックアップが作成された時点と同じ状態に戻ります。

> [!IMPORTANT]
> 完全復元は、非常に破壊的であり、大規模な中断を伴う操作です。 したがって、`restore` 操作を実行するには、その前の 30 分以内に完全バックアップを完了しておく必要があります。

復元はデータ プレーン操作です。 復元操作を開始する呼び出し元には、データ アクション **Microsoft.KeyVault/managedHsm/restore/start/action** を実行するためのアクセス許可が必要です。 バックアップが作成されたソース HSM と復元が実行される宛先 HSM のセキュリティ ドメインは同じである **必要** があります。 [Managed HSM セキュリティ ドメイン](security-domain.md)の詳細を確認してください。

完全復元を実行するには、次の情報を指定する必要があります。
- HSM の名前または URL
- ストレージ アカウント名
- ストレージ アカウントの BLOB コンテナー
- アクセス許可 `rl` を備えたストレージ コンテナーの SAS トークン
- ソース バックアップが格納されているストレージ コンテナーのフォルダー名

復元は実行時間の長い操作ですが、ジョブ ID は直ちに返されます。 このジョブ ID を使用して、復元プロセスの状態を確認できます。 復元プロセスの進行中は、HSM が復元モードになり、すべてのデータ プレーン コマンドが無効になります (復元状態の確認を除く)。

```azurecli-interactive
#### time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>HSM の復元

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>次の手順
- 「[Azure CLI を使用してマネージド HSM を管理する](key-management.md)」を確認します。
- [Managed HSM セキュリティ ドメイン](security-domain.md)の詳細を確認します。
