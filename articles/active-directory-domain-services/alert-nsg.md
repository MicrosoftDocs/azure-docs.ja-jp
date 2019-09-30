---
title: Azure Active Directory Domain Services:ネットワーク セキュリティ グループのトラブルシューティング | Microsoft Docs
description: Azure AD Domain Services のネットワーク セキュリティ グループ構成のトラブルシューティング
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 450ee5635b378ed7c4d4e4bedc1c4245f6b52d70
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "70743425"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>マネージド ドメインの無効なネットワーク構成のトラブルシューティング
この記事は、ネットワーク関連の構成エラーのトラブルシューティングと解決に役立ちます。次のような警告メッセージは、このようなエラーにより表示されます。

## <a name="alert-aadds104-network-error"></a>アラート AADDS104:ネットワーク エラー
**アラート メッセージ:** "*このマネージド ドメインのドメイン コントローラーに到達できません。これは、仮想ネットワークに構成されているネットワーク セキュリティ グループ (NSG) がマネージド ドメインへのアクセスをブロックしている場合に発生する可能性があります。別の理由として、インターネットからの着信トラフィックをブロックするユーザー定義ルートが存在していることが考えられます。*

無効な NSG 構成が、Azure AD Domain Services のネットワーク エラーの最も一般的な原因です。 仮想ネットワーク用に構成されたネットワーク セキュリティ グループ (NSG) は、[特定のポート](network-considerations.md#network-security-groups-and-required-ports)へのアクセスを許可する必要があります。 こうしたポートがブロックされていると、Microsoft は、マネージド ドメインを監視または更新できません。 さらに、Azure AD ディレクトリとマネージド ドメインの間の同期が影響を受けます。 NSG を作成するときは、こうしたポートを開いたままにして、サービスが中断されないようにしてください。

### <a name="checking-your-nsg-for-compliance"></a>NSG のコンプライアンス対応の確認

1. Azure Portal で、[ネットワーク セキュリティ グループ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)のページに移動します
2. テーブルから、マネージド ドメインが有効になっているサブネットに関連付けられている NSG を選択します。
3. 左側のウィンドウの **[設定]** の下で、 **[受信セキュリティ規則]** をクリックします。
4. 現在の規則を確認し、どのルールが[これらのポート](network-considerations.md#network-security-groups-and-required-ports)へのアクセスをブロックしているか確認します
5. NSG を編集し、規則を削除するか、規則を追加するか、または完全に新しい NSG を作成することによって、コンプライアンスを確認します。 [規則の追加](#add-a-rule-to-a-network-security-group-using-the-azure-portal)または準拠している新しい NSG の作成方法は以下のとおりです

## <a name="sample-nsg"></a>NSG のサンプル
次の表では、マネージド ドメインのセキュリティ保護を維持しながら、Microsoft が情報を監視、管理、更新できるようにする、NSG の例を示します。

![NSG のサンプル](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services には、仮想ネットワークからの無制限の送信アクセスが必要です。 仮想ネットワークの発信アクセスを制限する追加の NSG 規則は作成しないようにすることをお勧めします。

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Azure Portal を使用してネットワーク セキュリティ グループに規則を追加する
PowerShell を使いたくない場合は、Azure Portal を使って手動で 1 つの規則を NSG に追加できます。 ネットワーク セキュリティ グループに規則を作成するには、次の手順を実行します。

1. Azure Portal で、[ネットワーク セキュリティ グループ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)のページに移動します。
2. テーブルから、マネージド ドメインが有効になっているサブネットに関連付けられている NSG を選択します。
3. 左側のパネルの **[設定]** で、 **[受信セキュリティ規則]** または **[送信セキュリティ規則]** をクリックします。
4. **[追加]** をクリックして情報を入力し、規則を作成します。 Click **OK**.
5. 規則テーブルで検索して、規則が作成されたことを確認します。


## <a name="need-help"></a>お困りの際は、
[フィードバックの共有およびサポートについては](contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
