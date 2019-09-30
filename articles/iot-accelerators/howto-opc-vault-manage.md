---
title: OPC Vault 証明書サービスを管理する方法 - Azure | Microsoft Docs
description: OPC Vault のルート CA 証明書とユーザーのアクセス許可を管理します。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4420e5b0d895f8ea30dbd39fc50dd7480d57d086
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995988"
---
# <a name="how-to-manage-the-opc-vault-certificate-service"></a>OPC Vault 証明書サービスを管理する方法

この記事では、Azure での OPC Vault 証明書管理サービスの管理タスク、発行者 CA 証明書の更新方法、証明書失効リスト (CRL) の更新方法、およびユーザー アクセスの許可および取り消し方法について説明します。

## <a name="create-or-renew-the-root-ca-certificate"></a>ルート CA 証明書を作成または更新する

ルート CA 証明書を作成するのは、デプロイ後の必須手順です。 有効な発行者 CA 証明書がない場合、アプリケーション証明書に署名して発行することはできません。<br>妥当で安全な有効期間で証明書を管理するには、[証明書の有効期間](howto-opc-vault-secure-ca.md#certificates)に関する章を参照してください。 発行者 CA 証明書は、有効期間を半分以上経過した後に更新する必要がありますが、新しく署名されるアプリケーション証明書の構成される有効期間が、発行者証明書の有効期間を超えないようにする必要があります。<br>
> [!IMPORTANT]
> 発行者 CA 証明書を作成または更新するには、"管理者" ロールが必要です。

1. `https://myResourceGroup-app.azurewebsites.net` で証明書サービスを開き、サインインします。
2. [`Certificate Groups`] ページに移動します。
3. `Default` 証明書グループが 1 つ表示されています。 `Edit` をクリックします。
4. `Edit Certificate Group Details` で、CA とアプリケーション証明書のサブジェクト名と有効期間を変更できます。<br>サブジェクトと有効期間は、最初の CA 証明書が発行される前に 1 回だけ設定する必要があります。 操作中に有効期間を変更すると、発行される証明書と CRL の有効期間が一致しなくなる可能性があります。
5. 有効なサブジェクト (`CN=My CA Root, O=MyCompany, OU=MyDepartment` など) を入力します。<br>
   > [!IMPORTANT]
   > サブジェクトを変更するには、発行者証明書を更新する必要があります。更新しないと、サービスでアプリケーション証明書に署名されません。 構成のサブジェクトは、アクティブな発行者証明書のサブジェクトに対して正常にチェックされます。 サブジェクトが一致しない場合、証明書の署名は拒否されます。
6. [`Save`] ボタンをクリックします。
7. このとき "許可されていません" のエラーが表示される場合は、ユーザーの資格情報に、ルート証明書を変更または新しく作成するための管理者アクセス許可がありません。 既定では、サービスをデプロイしたユーザーには、サービスに対する管理者ロールと署名ロールが設定されます。その他のユーザーは、AzureAD アプリケーションの登録に応じて、"承認者"、"ライター"、"管理者" のいずれかのロールに追加する必要があります。
8. [`Details`] ボタンをクリックします。 `View Certificate Group Details` には、更新された情報が表示されます。
9. [`Renew CA Certificate`] ボタンをクリックして、最初の発行者 CA 証明書を発行するか、発行者証明書を更新します。 [`Ok`] を押して続行します。
10. 数秒後、`Certificate Details` が表示されます。 [`Issuer`] または [`Crl`] を押して、最新の CA 証明書と、OPC UA アプリケーションに配布するための CRL をダウンロードします。
11. これで、OPC UA 証明書管理サービスで OPC UA アプリケーションの証明書を発行できるようになりました。

## <a name="renew-the-crl"></a>CRL を更新する

証明書失効リスト (CRL) の更新は、更新プログラムで、定期的にアプリケーションに配布される必要があります。 CRL 配布ポイント X509 拡張機能をサポートする OPC UA デバイスは、マイクロサービス エンドポイントから CRL を直接更新できます。 他の OPC UA デバイスでは手動更新が必要な場合がありますが、最善の場合は、証明書と CRL が含まれた信頼リストを更新する GDS サーバー プッシュ拡張機能 (*) を使用して更新できます。

次のワークフローでは、削除された状態のすべての証明書要求が CRL で取り消されます。これは、発行元の発行者 CA 証明書に対応しています。 CRL のバージョン番号が 1 ずつ増加します。 <br>
> [!NOTE]
> すべての発行済み CRL は、発行者 CA 証明書の有効期限が切れるまで有効です。これは、OPC UA 仕様では、CRL に対して必須の決定的な分散モデルを必要としないためです。

> [!IMPORTANT]
> 発行者 CRL を更新するには、"管理者" ロールが必要です。

1. `https://myResourceGroup.azurewebsites.net` で証明書サービスを開き、サインインします。
2. [`Certificate Groups`] ページに移動します。
3. [`Details`] ボタンをクリックします。 `View Certificate Group Details` には、現在の証明書と CRL 情報が表示されます。
4. [`Update CRL Revocation List(CRL)`] ボタンをクリックすると、OPC Vault ストレージ内のすべてのアクティブな発行者証明書に対して更新された CRL が発行されます。
5. 数秒後、`Certificate Details` が表示されます。 [`Issuer`] または [`Crl`] を押して、最新の CA 証明書と、OPC UA アプリケーションに配布するための CRL をダウンロードします。

## <a name="manage-user-roles"></a>ユーザー ロールの管理

OPC Vault マイクロサービスのユーザー ロールは、Azure Active Directory エンタープライズ アプリケーションで管理されます。

ロール定義の詳細については、「[ロール](howto-opc-vault-secure-ca.md#roles)」セクションを参照してください。

既定では、テナント内の認証されたユーザーは、"閲覧者" としてサービスにサインインできます。 特権ロールが高いほど、Azure portal で、または Powershell を使用した手動管理が必要になります。

### <a name="add-user"></a>ユーザーの追加

1. `portal.azure.com` で Azure portal を開きます。
2. `Azure Active Directory`/`Enterprise applications` に移動します。
3. OPC Vault マイクロサービスの登録を選択します。既定ではご自分の `resourceGroupName-service` です。
4. `Users and Groups` に移動します。
5. `Add User` をクリックします。
6. 特定のロールに割り当てるユーザーを選択または招待します。
7. ユーザーのロールを選択します。
8. [`Assign`] ボタンを押します。
9. `Administrator` ロールまたは `Approver` ロールのユーザーは、Azure Key Vault アクセス ポリシーの追加に進みます。

### <a name="remove-user"></a>ユーザーの削除

1. `portal.azure.com` で Azure portal を開きます。
2. `Azure Active Directory`/`Enterprise applications` に移動します。
3. OPC Vault マイクロサービスの登録を選択します。既定ではご自分の `resourceGroupName-service` です。
4. `Users and Groups` に移動します。
5. 削除するロールを持つユーザーを選択します。
6. [`Remove`] ボタンを押します。
7. 管理者と承認者を削除した場合は、Azure Key Vault ポリシーからも削除します。

### <a name="add-user-access-policy-to-azure-key-vault"></a>Azure Key Vault にユーザー アクセス ポリシーを追加する

**承認者**および**管理者**には、追加のアクセス ポリシーが必要です。

既定では、サービス ID には Key Vault にアクセスするためのアクセス許可が制限されているため、管理者特権での操作や変更をユーザーの権限借用なしで実行することはできません。 基本サービスのアクセス許可は、シークレットと証明書の両方に対して `Get` と `List` です。 シークレットの場合、例外が 1 つだけあります。サービスが、ユーザーによって受け入れられると、シークレット ストアから秘密キーを削除 (`Delete`) できます。 他のすべての操作では、ユーザーの権限借用が必要です。<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>**承認者ロール**の場合、次のアクセス許可を Key Vault に追加する必要があります。

1. `portal.azure.com` で Azure portal を開きます。
2. デプロイ時に使用した OPC Vault `resourceGroupName` に移動します。
3. Key Vault `resourceGroupName-xxxxx` に移動します。
4. `Access Policies` に移動します。
5. `Add new` をクリックします。
6. 要件に一致するテンプレートはないため、テンプレートをスキップします。
7. [`Select Principal`] をクリックし、テナントに追加するユーザーを選択するか、新しいユーザーをテナントに招待します。
8. `Key permissions`: `Get`、`List`、および最も重要な `Sign` を確認します。
9. `Secret permissions`: `Get`、`List`、`Set`、`Delete` を確認します。
10. `Certificate permissions`: `Get`、`List` を確認します。
11. [`Ok`] をクリックします。
12. 変更を保存 (`Save`) します。

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>**管理者ロール**の場合、次のアクセス許可を Key Vault に追加する必要があります。

1. `portal.azure.com` で Azure portal を開きます。
2. デプロイ時に使用した OPC Vault `resourceGroupName` に移動します。
3. Key Vault `resourceGroupName-xxxxx` に移動します。
4. `Access Policies` に移動します。
5. `Add new` をクリックします。
6. 要件に一致するテンプレートはないため、テンプレートをスキップします。
7. [`Select Principal`] をクリックし、テナントに追加するユーザーを選択するか、新しいユーザーをテナントに招待します。
8. `Key permissions`: `Get`、`List`、および最も重要な `Sign` を確認します。
9. `Secret permissions`: `Get`、`List`、`Set`、`Delete` を確認します。
10. `Certificate permissions`: `Get`、`List`、`Update`、`Create`、`Import` を確認します。
11. [`Ok`] をクリックします。
12. 変更を保存 (`Save`) します。

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Azure Key Vault からユーザー アクセス ポリシーを削除する

1. `portal.azure.com` で Azure portal を開きます。
2. デプロイ時に使用した OPC Vault `resourceGroupName` に移動します。
3. Key Vault `resourceGroupName-xxxxx` に移動します。
4. `Access Policies` に移動します。
5. 削除するユーザーを見つけ、[`... / Delete`] をクリックしてユーザー アクセスを削除します。

## <a name="next-steps"></a>次の手順

ここでは、OPC Vault 証明書とユーザーを管理する方法を学習しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [OPC デバイスの通信をセキュリティで保護する](howto-opc-vault-secure.md)
