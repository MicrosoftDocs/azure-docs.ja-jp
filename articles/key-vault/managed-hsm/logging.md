---
title: Azure Managed HSM のログ
description: このチュートリアルを使用すると、Managed HSM のログを簡単に開始できます。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 7420ffbe5b365c635c1eac2620cfd54ceb649ebf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211806"
---
# <a name="managed-hsm-logging"></a>Managed HSM のログ 

1 つまたは複数のマネージド HSM の作成後、いつ、どのように、だれによって HSM がアクセスされるかを監視したいでしょう。 そうするには、ログを有効にします。これにより、指定した Azure ストレージ アカウントに情報が保存されます。 指定したストレージ アカウント用の **insights-logs-auditevent** という名前の新しいコンテナーが自動的に作成されます。 この同じストレージ アカウントを使用して、複数のマネージド HSM のログを収集することができます。

マネージド HSM の操作を行ってから最大 10 分後には、ログ情報にアクセスできます。 ほとんどの場合は、これよりも早く確認できます。  ストレージ アカウントでのログの管理はお客様に委ねられます。

* 標準的な Azure アクセス制御メソッドを使用してアクセスできるユーザーを制限することで、ログをセキュリティで保護します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。

このチュートリアルを使用すると、Managed HSM のログを簡単に開始できます。 ストレージ アカウントを作成し、ログを有効にしてから、収集されたログ情報を解釈します。  

> [!NOTE]
> マネージド HSM またはキーの作成方法については、このチュートリアルに含まれていません。 この記事では、Azure CLI を使用して診断ログを更新する方法を説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* Azure CLI バージョン 2.12.0 以降。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* お使いのサブスクリプション内のマネージド HSM。 「[クイック スタート:Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする](quick-create-cli.md)」を参照して、マネージド HSM をプロビジョニングしてアクティブにします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Azure サブスクリプションへの接続

キーのログの最初の設定手順は、ログを記録したいマネージド HSM に Azure CLI をポイントすることです。

```azurecli-interactive
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください

マネージド HSM を作成するときに使用したサブスクリプションを指定することが必要な場合があります。 アカウントのサブスクリプションを確認するには、次のコマンドを入力します。

## <a name="identify-the-managed-hsm-and-storage-account"></a>マネージド HSM およびストレージ アカウントを特定する

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>ログの有効化

Managed HSM のログを有効にするために、**az monitor diagnostic-settings create** コマンドを使用します。その際、新しいストレージ アカウントとマネージド HSM 用に作成した変数も使用します。 また、 **-Enabled** フラグを **$true** に設定し、カテゴリを **AuditEvent** (Managed HSM ログの唯一のカテゴリ) に設定します。

この出力を見れば、自分のマネージド HSM に対してログが有効になっていることと、ストレージ アカウントに情報が保存されることを確認できます。

必要に応じて、ログのアイテム保持ポリシーを、古いログが自動的に削除されるように設定することができます。 たとえば、 **-RetentionEnabled** フラグを **$true** に設定し、 **-RetentionInDays** パラメーターを **90** に設定した保持ポリシーを設定すると、90 日より前のログが自動的に削除されます。

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

ログ記録の内容:

* 認証されたすべての REST API 要求。これには、アクセス許可がないため、システム エラーのため、または不正な要求の結果として、失敗した要求が含まれます。
* マネージド HSM 自体に対する操作。これには、タグなどの属性の作成、削除、および更新が含まれます。
* 初期化とダウンロード、復旧の初期化、アップロードなど、セキュリティ ドメイン関連の操作
* HSM の完全なバックアップと復元、および選択的復元の操作
* 以下のような、キーに対する操作
  * キーの作成、変更、または削除。
  * 署名、確認、暗号化、復号化、キーのラップとラップ解除、キーの一覧表示。
  * キーのバックアップ、復元、消去
* 結果として 401 応答が発生する、認証されていない要求。 たとえば、ベアラー トークンを持たない要求、形式が正しくない要求、有効期限切れの要求、または無効なトークンを持つ要求です。  

## <a name="access-your-logs"></a>ログへのアクセス

Managed HSM ログは、自分が指定したストレージ アカウントの **insights-logs-auditevent** コンテナーに格納されます。 ログを表示するには、BLOB をダウンロードする必要があります。 Azure Storage の詳細については、「[Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](../../storage/blobs/storage-quickstart-blobs-cli.md)」を参照してください。

個々の BLOB はテキストとして格納されます (JSON 形式)。 ログ エントリの例を見てみましょう。 次の例は、完全バックアップを作成する要求がマネージド HSM に送信されるときのログ エントリを示しています。

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

次の表にフィールド名と説明を示します。

| フィールド名 | 説明 |
| --- | --- |
| **TenantId** | マネージド HSM が作成されたサブスクリプションの Azure Active Directory テナント ID |
| **time** |日付と時刻 (UTC)。 |
| **resourceId** |Azure Resource Manager リソース ID。 Managed HSM ログの場合、これは常に Managed HSM リソース ID です。 |
| **operationName** |次の表に示すような操作の名前です。 |
| **operationVersion** |クライアントによって要求された REST API バージョン。 |
| **category** |結果の種類。 Managed HSM ログの場合、**AuditEvent** は単一の使用可能な値です。 |
| **resultType** |REST API 要求の結果です。 |
| **properties** |操作 (**operationName**) によって異なる情報です|
| **resultSignature** |HTTP の状態です。 |
| **resultDescription** |結果に関する追加の説明です (使用可能な場合)。 |
| **durationMs** |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 これにネットワーク待機時間は含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| **callerIpAddress** |要求を行ったクライアントの IP アドレスです。 |
| **correlationId** |クライアントが、クライアント側のログをサービス側のログに関連付けるために渡すことができる、オプションの GUID です。 |
| **identity** |REST API 要求に提示されたトークンからの ID です。 これは通常、"ユーザー" か "サービス プリンシパル" です。 |
| **requestUri** | REST API の要求 URI |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>Azure Monitor ログの使用

Azure Monitor ログの Key Vault ソリューションを使用して、Managed HSM の **AuditEvent** ログを調査することができます。 Azure Monitor ログでは、ログ クエリを使用してデータを分析し、必要な情報を取得します。 

## <a name="next-steps"></a>次のステップ

- マネージド HSM をプロビジョニングして使用するための[ベスト プラクティス](best-practices.md)について学習します
- Managed HSM を[バックアップおよび復元する方法](backup-restore.md)について学習します
