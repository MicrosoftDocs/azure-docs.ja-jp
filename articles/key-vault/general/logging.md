---
title: Azure Key Vault のログ記録 | Microsoft Docs
description: Azure Key Vault のログ記録を有効にすることで、お使いのキー コンテナーへのアクセスを監視する方法について説明します。これにより、指定した Azure ストレージ アカウントに情報が保存されます。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 99313cf1248ef5e90d7cd60d528a58c7925298d0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499528"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault のログ記録

1 つまたは複数のキー コンテナーを作成したら、いつ、どのように、誰によってキー コンテナーがアクセスされるのかを監視するのが一般的です。 監視を行うには、Azure Key Vault のログ記録を有効にします。これにより、指定した Azure ストレージ アカウントに情報が保存されます。 この設定の詳しい手順については、「[Key Vault のログ記録を有効にする方法](howto-logging.md)」を参照してください。

キー コンテナーの操作を行ってから最大 10 分後には、ログ情報にアクセスできます。 ほとんどの場合は、これよりも早く確認できます。  ストレージ アカウントでのログの管理はお客様に委ねられます。

* ストレージ アカウントに標準的な Azure アクセス制御方法を使用し、ログにアクセスできるユーザーを制限することでログのセキュリティを保護します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。

Key Vault の概要については、「[Azure Key Vault とは](overview.md)」を参照してください。 Key Vault が使用可能な場所については、[価格に関するページ](https://azure.microsoft.com/pricing/details/key-vault/)をご覧ください。 [Azure Monitor for Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md) の使用に関する詳細です。

## <a name="interpret-your-key-vault-logs"></a>Key Vault のログを解釈する

ログ記録を有効にすると、指定したストレージ アカウント用の **insights-logs-auditevent** という名前の新しいコンテナーが自動的に作成されます。 このストレージ アカウントを使用して複数のキー コンテナーのログを収集することができます。

個々の BLOB はテキストとして格納されます (JSON BLOB 形式)。 ログ エントリの例を見てみましょう。 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

次の表にフィールド名と説明を示します。

| フィールド名 | 説明 |
| --- | --- |
| **time** |日付と時刻 (UTC)。 |
| **resourceId** |Azure Resource Manager リソース ID。 Key Vault のログの場合は、常に Key Vault リソース ID となります。 |
| **operationName** |次の表に示すような操作の名前です。 |
| **operationVersion** |クライアントによって要求された REST API バージョン。 |
| **category** |結果の種類。 Key Vault のログの場合、`AuditEvent` は使用可能な唯一の値です。 |
| **resultType** |REST API 要求の結果です。 |
| **resultSignature** |HTTP の状態です。 |
| **resultDescription** |結果に関する追加の説明です (使用可能な場合)。 |
| **durationMs** |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 これにネットワーク待機時間は含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| **callerIpAddress** |要求を行ったクライアントの IP アドレスです。 |
| **correlationId** |オプションの GUID であり、クライアント側のログとサービス側の (Key Vault) ログを対応付ける場合に渡します。 |
| **identity** |REST API 要求に提示されたトークンからの ID です。 これは、通常、Azure PowerShell コマンドレットの実行結果として生じる要求の場合と同様に、"user"、"service principal"、または組み合わせ "user+appId" となります。 |
| **properties** |操作によって異なる情報です (**operationName**)。 ほとんどの場合、このフィールドには、クライアント情報 (クライアントから渡されたユーザー エージェント文字列)、正確な REST API 要求 URI、および HTTP 状態コードが含まれます。 さらに、要求 (**KeyCreate** や **VaultGet** など) を行った結果としてオブジェクトが返される場合、キーの URI (`id` として)、コンテナーの URI、またはシークレットの URI も含まれます。 |

**operationName** フィールドの値は、*ObjectVerb* 形式となります。 次に例を示します。

* キー コンテナーに関するすべての操作は、`Vault<action>` 形式となります (`VaultGet` や `VaultCreate` など)。
* キーに関するすべての操作は、`Key<action>` 形式となります (`KeySign` や `KeyList` など)。
* シークレットに関するすべての操作は、`Secret<action>` 形式となります (`SecretGet` や `SecretListVersions` など)。

次の表に、**operationName** の値と、対応する REST API コマンドを示します。

### <a name="operation-names-table"></a>操作名の表

# <a name="vault"></a>[コンテナー](#tab/Vault)

| operationName | REST API コマンド |
| --- | --- |
| **認証** |Azure Active Directory エンドポイント経由で認証します |
| **VaultGet** |[キー コンテナーに関する情報を取得します](/rest/api/keyvault/vaults) |
| **VaultPut** |[キー コンテナーを作成または更新します](/rest/api/keyvault/vaults) |
| **VaultDelete** |[キー コンテナーを削除します](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Key Vault を更新します](/rest/api/keyvault/vaults) |
| **VaultList** |[リソース グループ内のすべてのキー コンテナーを一覧表示します](/rest/api/keyvault/vaults) |
| **VaultPurge** |[削除されたコンテナーを消去します](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |削除されたコンテナーを復旧します|
| **VaultGetDeleted** |[削除されたコンテナーを取得します](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[削除されたコンテナーを一覧表示します](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | コンテナーのアクセス ポリシーが変更されたイベントが公開されました |

# <a name="keys"></a>[[キー]](#tab/Keys)

| operationName | REST API コマンド |
| --- | --- |
| **KeyCreate** |[キーを作成します](/rest/api/keyvault/createkey) |
| **KeyGet** |[キーに関する情報を取得します](/rest/api/keyvault/getkey) |
| **KeyImport** |[コンテナーにキーをインポートします](/rest/api/keyvault/vaults) |
| **KeyDelete** |[キーを削除します](/rest/api/keyvault/deletekey) |
| **KeySign** |[キーで署名します](/rest/api/keyvault/sign) |
| **KeyVerify** |[キーで確認します](/rest/api/keyvault/vaults) |
| **KeyWrap** |[キーをラップします](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[キーのラップを解除します](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[キーで暗号化します](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[キーで復号化します](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[キーを更新します](/rest/api/keyvault/updatekey) |
| **KeyList** |[コンテナー内のキーを一覧表示します](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[キーのバージョンを一覧表示します](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[キーを消去します](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[キーをバックアップします](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[キーを復元します](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[キーを復旧します](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[削除されたキーを取得します](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[コンテナー内の削除されたキーを一覧表示します](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |有効期限が近づいているキー イベントが公開されました |
| **KeyExpiredEventGridNotification** |期限切れのキー イベントが公開されました |

# <a name="secrets"></a>[シークレット](#tab/Secrets)

| operationName | REST API コマンド |
| --- | --- |
| **SecretSet** |[シークレットを作成します](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[シークレットを取得します](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[シークレットを更新します](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[シークレットを削除します](/rest/api/keyvault/deletesecret) |
| **SecretList** |[コンテナー内のシークレットを一覧表示します](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[シークレットのバージョンを一覧表示します](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[シークレットを消去します](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[シークレットをバックアップします](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[シークレットを復元します](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[シークレットを復旧します](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[削除されたシークレットを取得します](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[コンテナー内の削除されたシークレットを一覧表示します](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |有効期限が近づいているシークレット イベントが公開されました |
| **SecretExpiredEventGridNotification** |期限切れのシークレット イベントが公開されました |

# <a name="certificates"></a>[証明書](#tab/Cerificates)

| operationName | REST API コマンド |
| --- | --- |
| **CertificateGet** |[証明書に関する情報を取得する](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[証明書を作成します](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[証明書をコンテナーにインポートします](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[証明書を更新します](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[コンテナー内の証明書を一覧表示します](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[証明書のバージョンを一覧表示します](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[証明書を削除します](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[証明書を消去します](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[証明書をバックアップします](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[証明書を復元します](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[証明書を復旧します](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[削除された証明書を取得します](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[コンテナー内の削除された証明書を一覧表示します](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[証明書ポリシーを取得します](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[証明書ポリシーを更新します](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[証明書ポリシーを作成します](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[証明書の連絡先を取得します](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[証明書の連絡先を設定します](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[証明書の連絡先を削除します](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[証明書の発行者を取得します](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[証明書の発行者を設定します](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[証明書の発行者を更新します](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[証明書の発行者を削除します](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[証明書の発行者を一覧表示します](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |証明書を登録します |
| **CertificateRenew** |証明書を更新する |
| **CertificatePendingGet** |保留中の証明書を取得します |
| **CertificatePendingMerge** |証明書のマージを保留しています |
| **CertificatePendingUpdate** |証明書の更新を保留しています |
| **CertificatePendingDelete** |保留中の証明書を削除します |
| **CertificateNearExpiryEventGridNotification** |有効期限が近づいている証明書イベントが公開されました |
| **CertificateExpiredEventGridNotification** |期限切れの証明書イベントが公開されました |

---

## <a name="use-azure-monitor-logs"></a>Azure Monitor ログの使用

Azure Monitor ログの Key Vault ソリューションを使用して、Key Vault の `AuditEvent` ログを調査することができます。 Azure Monitor ログでは、ログ クエリを使用してデータを分析し、必要な情報を取得します。 

この設定方法などの詳細については、[Azure Monitor の Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md) に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- [Key Vault のログ記録を有効にする方法](howto-logging.md)
- [Azure Monitor](../../azure-monitor/index.yml)
- .NET Web アプリケーションでの Azure Key Vault の使用方法に関するチュートリアルについては、「[Web アプリケーションからの Azure Key Vault の使用](tutorial-net-create-vault-azure-web-app.md)」を参照してください。
- プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](developers-guide.md)」を参照してください。
- Azure Key Vault の Azure PowerShell 1.0 のコマンドレットの一覧については、[Azure Key Vault コマンドレット](/powershell/module/az.keyvault/#key_vault)に関するページを参照してください。