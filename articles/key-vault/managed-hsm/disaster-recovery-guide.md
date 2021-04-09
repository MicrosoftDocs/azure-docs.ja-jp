---
title: Managed HSM に影響を与える Azure サービスの中断が発生した場合の対処方法 - Azure Key Vault | Microsoft Docs
description: Managed HSM に影響を与える Azure サービスの中断が発生した場合の対処方法について説明します。
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8c284e9993002f6e05e41ca00d00b388feef8f82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376005"
---
# <a name="managed-hsm-disaster-recovery"></a>Managed HSM のディザスター リカバリー

次のいずれかの理由によって元の HSM が失われるか使用できなくなった場合に正確なレプリカを作成したくなることがあります。

- 削除されてから消去された。
- リージョン内で致命的なエラーが発生した結果、すべてのメンバー パーティションが破棄された。

次のものがあれば、同一または別のリージョンに HSM インスタンスを再作成できます。
- ソース HSM の[セキュリティ ドメイン](security-domain.md)。
- セキュリティ ドメインを暗号化する秘密キー (少なくともクォーラム数)。
- ソース HSM からの、最新の完全な HSM [バックアップ](backup-restore.md)。

ディザスター リカバリー手順のステップは次のとおりです。

1. 新しい HSM インスタンスを作成します。
1. "セキュリティ ドメインの回復" をアクティブにします。 新しい RSA キーの組 (セキュリティ ドメイン交換キー) がセキュリティ ドメインの転送用に生成され、応答として送信された後、SecurityDomainExchangeKey (公開キー) としてダウンロードされます。
1. "セキュリティ ドメイン転送ファイル" を作成してアップロードします。 セキュリティ ドメインを暗号化する秘密キーが必要になります。 秘密キーはローカルで使用され、このプロセス内で転送されることは一切ありません。
1. 新しい HSM のバックアップを作成します。 HSM が空である場合でも、復元する前にバックアップが必要です。 バックアップによって、簡単にロールバックできます。
1. ソース HSM から最新の HSM バックアップを復元します。

以上の手順によって、HSM の内容を他のリージョンに手動でレプリケートできるようになります。 HSM の名前 (およびサービス エンドポイント URI) は異なるため、これらのキーを別の場所から利用するためには、アプリケーションの構成を変更する必要があります。

## <a name="create-a-new-managed-hsm"></a>新しいマネージド HSM を作成する

マネージド HSM を作成するには、`az keyvault create` コマンドを使用します。 このスクリプトには、3 つの必須パラメーター (リソース グループ名、HSM 名、地理的な場所) があります。

マネージド HSM リソースを作成するには、次の入力を指定する必要があります。

- HSM の名前。
- サブスクリプション内での配置先となるリソース グループ。
- Azure の場所。
- 初期管理者のリスト。

次の例では、**ContosoMHSM** という名前の HSM を、リソース グループ **ContosoResourceGroup** に作成します。これは **米国東部 2** に存在し、**現在サインインしているユーザー** が唯一の管理者となります。

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> create コマンドには数分かかることがあります。 正常に制御が戻ったら、HSM をアクティブにする準備が整います。

このコマンドの出力は、作成したマネージド HSM のプロパティを示します。 最も重要な 2 つのプロパティは、次のとおりです。

* **name**:この例では、名前は ContosoMHSM です。 この名前を他の Key Vault コマンドに使用できます。
* **hsmUri**: この例では、URI は "https://contosohsm.managedhsm.azure.net" です。 REST API から HSM を使用するアプリケーションでは、この URI を使用する必要があります。

これで、お使いの Azure アカウントは、このマネージド HSM に対して任意の操作を実行できるようになりました。 現在のところ、誰も承認されていません。

## <a name="activate-the-security-domain-recovery-mode"></a>セキュリティ ドメインの回復モードをアクティブにする

通常の作成プロセスでは、この時点で新しいセキュリティ ドメインを初期化してダウンロードします。 ただし、ここではディザスター リカバリー手順を実行しているため、HSM には、セキュリティ ドメインの回復モードに移行してセキュリティ ドメイン交換キーをダウンロードするように要求します。 セキュリティ ドメイン交換キーとは、セキュリティ ドメインを HSM にアップロードする前に暗号化する際に使用される RSA 公開キーです。 対応する秘密キーは HSM 内で保護され、転送中のセキュリティ ドメインのコンテンツを安全に保護します。

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>セキュリティ ドメインを宛先 HSM にアップロードする

この手順では、次のものが必要です。
- 前の手順でダウンロードしたセキュリティ ドメイン交換キー。
- ソース HSM のセキュリティ ドメイン。
- セキュリティ ドメインの暗号化に使用された秘密キーの、少なくともクォーラム数。

`az keyvault security-domain upload` コマンドは、次の操作を実行します。

- 指定した秘密キーを使用して、ソース HSM のセキュリティ ドメインを復号化する。 
- 前の手順でダウンロードしたセキュリティ ドメイン交換キーで暗号化された、セキュリティ ドメインのアップロード BLOB を作成する。
- セキュリティ ドメインのアップロード BLOB を HSM にアップロードして、セキュリティ ドメインの回復を完了する。

次の例では、**ContosoMHSM** のセキュリティ ドメインと、対応する 2 つの秘密キーを使用して、セキュリティ ドメインの受信を待機している **ContosoMHSM2** にアップロードします。 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

これで、ソース HSM (ContosoMHSM) と宛先 HSM (ContosoMHSM2) の両方で同じセキュリティ ドメインが使用されます。 ソース HSM から宛先 HSM に完全バックアップを復元できるようになりました。

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>新しい HSM のバックアップを (復元ポイントとして) 作成する

復元時に問題が発生した場合に備えて復元ポイントを用意するため、完全な HSM 復元を実行する前に完全バックアップを実行しておくことを常にお勧めします。

HSM バックアップを作成するには、次のものが必要です。
- バックアップが格納されるストレージ アカウント
- このストレージ アカウントの BLOB ストレージ コンテナー。ここには、暗号化されたバックアップを格納するために新しいフォルダーがバックアップ プロセスによって作成されます。

ストレージ コンテナー **mhsmbackupcontainer** 内の HSM バックアップに対して `az keyvault backup` コマンドを使用します。これは、次の例ではストレージ アカウント **ContosoBackup** にあります。 30 分後に期限切れになる SAS トークンを作成し、それを Managed HSM に指定してバックアップを書き込みます。

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>ソース HSM からバックアップを復元する

この手順では、次のものが必要です。

- ソース HSM のバックアップが格納されているストレージ アカウントと BLOB コンテナー。
- バックアップの復元元となるフォルダーの名前。 定期的なバックアップを作成する場合は、このコンテナー内に多数のフォルダーが存在することになります。


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

これで、ディザスター リカバリー プロセスがすべて完了しました。 バックアップが作成されたときのソース HSM のコンテンツは、すべてのキー、バージョン、属性、タグ、ロールの割り当てを含め、宛先 HSM にコピーされます。

## <a name="next-steps"></a>次のステップ

- 「[Managed HSM セキュリティ ドメインについて](security-domain.md)」を読み、セキュリティ ドメインの詳細を確認する
- [Managed HSM のベスト プラクティス](best-practices.md)を実施する
