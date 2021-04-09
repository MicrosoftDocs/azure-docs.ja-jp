---
title: Azure Sentinel でカスタマー マネージド キーを設定する | Microsoft Docs
description: Azure Sentinel でカスタマー マネージド キー (CMK) を設定する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: a1c2754fcae5768c6b87d6280fc882acd46d9a0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585331"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Azure Sentinel のカスタマー マネージド キーの設定

この記事では、Azure Sentinel 用のカスタマー マネージド キー (CMK) を構成するための背景情報と手順について説明します。 CMK を使用すると、Azure Sentinel に保存または送信されたすべてのデータを、自分が作成または所有している Azure Key Vault キーを使用して、関連するすべてのストレージ リソースで暗号化することができます。

> [!NOTE]
> - Azure Sentinel CMK 機能は、**新規のお客様** にのみ提供されます。
>
> - この機能へのアクセスは、Azure 機能の登録によって制御されます。  アクセスを要求するには、azuresentinelCMK@microsoft.com に問い合わせてください。 保留中の要求は、使用可能な容量に応じて承認されます。
>
> - CMK 機能は、1 日あたり 1 TB 以上を送信しているお客様のみご利用いただけます。 ご利用の Azure サブスクリプションで Microsoft に CMK のプロビジョニングを申請すると、追加料金に関する情報が表示されます。 詳しくは、[Log Analytics の価格](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters)に関する記事をご覧ください。

## <a name="how-cmk-works"></a>CMK のしくみ 

Azure Sentinel ソリューションでは、ログの収集と機能に複数のストレージ リソースが使用されます (Log Analytics などを含む)。 Azure Sentinel CMK 構成の一部として、関連するストレージ リソースにも CMK 設定を構成する必要があります。 Log Analytics 以外のストレージ リソースに保存されているデータも暗号化されます。

詳細については、[CMK](../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview) に関する記事をご覧ください。

> [!NOTE]
> Azure Sentinel で CMK を有効にすると、CMK をサポートしていないパブリック プレビュー機能は有効になりません。

## <a name="enable-cmk"></a>CMK を有効にする 

CMK をプロビジョニングするには、次の手順を実行します。 

1.  Azure Key Vault を作成し、キーを格納します。

2.  Log Analytics ワークスペースで CMK を有効にします。

3.  Cosmos DB に登録します。

4.  Azure Key Vault インスタンスにアクセス ポリシーを追加します。

5.  Azure Sentinel で CMK を有効にします。

6.  Azure Sentinel を有効にします。

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>手順 1: Azure Key Vault を作成し、キーを格納する

1.  [Azure Key Vault リソースを作成](/azure-stack/user/azure-stack-key-vault-manage-portal)してから、データの暗号化に使用するキーを生成またはインポートします。
    > [!NOTE]
    >  キーとアクセスを保護するため、Azure Key Vault を回復可能として構成する必要があります。

1.  [回復オプションを有効にする:](../key-vault/general/key-vault-recovery.md)

    -   [[論理的な削除]](../key-vault/general/soft-delete-overview.md) が有効になっていることを確認します。

    -   論理的な削除の後でもシークレットまたはコンテナーの強制削除を防ぐには、[消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)を有効にする必要があります

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>手順 2: Log Analytics ワークスペースで CMK を有効にする

以降の手順で Azure Sentinel ワークスペースとして使用する CMK ワークスペースを作成するには、「[Azure Monitor のカスタマー マネージド キーの構成](../azure-monitor/logs/customer-managed-keys.md)」の手順に従います。

### <a name="step-3-register-for-cosmos-db"></a>手順 3:Cosmos DB に登録する

Azure Sentinel では、追加のストレージ リソースとして Cosmos DB が使用されます。 必ず Cosmos DB に登録してください。

Cosmos DB の指示に従って、ご利用の Azure サブスクリプションに [Azure Cosmos DB リソース プロバイダーを登録](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)します。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>手順 4:Azure Key Vault インスタンスにアクセス ポリシーを追加する

Cosmos DB からご使用の Azure Key Vault インスタンスに確実にアクセス権を追加します。 Cosmos DB の指示に従って、Azure Cosmos DB プリンシパルで[ご使用の Azure Key Vault インスタンスにアクセス ポリシーを追加](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)します。

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>手順 5:Azure Sentinel で CMK を有効にする

Azure Sentinel の CMK 機能は、Azure 製品グループから直接アクセスを受けた場合のみ、新規のお客様だけに提供されます。 Microsoft に登録したご自分の連絡先を使用して、Azure Sentinel チームからの承認を受け取り、お使いのソリューションで CMK を有効にします。

承認を得ると、CMK 機能を有効にするために、次の情報を入力するように求められます。

-  CMK を有効にするワークスペース ID

-  Key Vault の URL:キーの "キー識別子" (最後のスラッシュのところまで) をコピーします。  
    

    ![キー識別子](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel チームは、提供されたワークスペースに対して Azure Sentinel CMK 機能を有効にします。

-  この機能を使用することを承認された Azure Sentinel 製品チームからの確認。 続行する前に、これを行う必要があります。

### <a name="step-6-enable-azure-sentinel"></a>手順 6:Azure Sentinel を有効にする


Azure portal にアクセスし、CMK を設定するワークスペースで Azure Sentinel を有効にします。 詳細については、[Azure Sentinel のオンボード](quickstart-onboard.md)に関するページを参照してください。

## <a name="key-encryption-key-revocation-or-deletion"></a>キー暗号化キーの失効または削除


ユーザーがキー暗号化キーを削除するか Azure Sentinel のアクセス権を削除してキー暗号化キーを取り消した場合、1 時間以内に Azure Sentinel は変更を受け入れ、データが使用できなくなった場合と同様に動作します。 この時点で、データ インジェスト、永続的な構成の変更、インシデントの作成など、永続ストレージ リソースを使用する操作はすべて実行できなくなります。 以前に格納されたデータは削除されませんが、アクセスできなくなります。 アクセスできないデータは、データ保持ポリシーによって管理され、そのポリシーに従って消去されます。

暗号化キーが失効または削除されてから実行可能な操作は、アカウントの削除だけです。

失効後にアクセスが復元されると、Azure Sentinel によって 1 時間以内にデータへのアクセスが復元されます。

Azure Monitor でのこの動作の詳細については、[Azure Monitor の CMK の失効](../azure-monitor/logs/customer-managed-keys.md#key-revocation)に関するセクションを参照してください。

## <a name="key-encryption-key-rotation"></a>キー暗号化キーのローテーション


Azure Sentinel と Log Analytics では、キーのローテーションがサポートされています。 ユーザーが Key Vault でキーのローテーションを実行すると、Azure Sentinel では 1 時間以内に新しいキーがサポートされます。

Key Vault では、キーの新しいバージョンを作成することによってキーのローテーションを実行できます。

![キーのローテーション](./media/customer-managed-keys/key-rotation.png)

キーの以前のバージョンは、24 時間後に無効にするか、または Azure Key Vault の監査ログに以前のバージョンを使用するアクティビティが表示されないようになってから無効にすることができます。

Azure Sentinel と Log Analytics で同じキーを使用している場合は、キーのローテーションを実行する必要があります。Azure Key Vault キーの新しいバージョンで Log Analytics のクラスター リソースを明示的に更新する必要があります。 詳細については、[Azure Monitor の CMK のローテーション](../azure-monitor/logs/customer-managed-keys.md#key-rotation)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel でカスタマー マネージド キーを設定する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。