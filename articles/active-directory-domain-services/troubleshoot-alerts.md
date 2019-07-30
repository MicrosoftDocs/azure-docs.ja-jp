---
title: Azure Active Directory Domain Services:アラートのトラブルシューティング | Microsoft Docs
description: Azure AD Domain Services のアラートのトラブルシューティング
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 63f2e12b9b769e806f8bd052af5bc78aed6a1fe0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472304"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - アラートのトラブルシューティング
この記事では、マネージド ドメインで発生する可能性があるすべてのアラート向けのトラブルシューティング ガイドを提供します。


アラートの ID またはメッセージに対応するトラブルシューティング手順を選択してください。

| **アラート ID** | **アラート メッセージ** | **解決策** |
| --- | --- | :--- |
| AADDS001 | "*マネージド ドメインに対してインターネット経由のセキュリティで Secure LDAP が有効になっています。ただし、ポート 636 へのアクセスはネットワーク セキュリティ グループを使用してロックダウンされていません。これにより、マネージド ドメインのユーザー アカウントがパスワードの総当り攻撃の対象になる可能性があります。* " | [不適切なセキュリティで保護された LDAP 構成](alert-ldaps.md) |
| AADDS100 | "*マネージド ドメインに関連付けられている Azure AD ディレクトリが削除されている可能性があります。マネージド ドメインはもうサポートされる構成に含まれていません。Microsoft は、マネージド ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。* " | [不足しているディレクトリ](#aadds100-missing-directory) |
| AADDS101 | "*Azure AD B2C ディレクトリで Azure AD Domain Services を有効にできません。* " | [このディレクトリで Azure AD B2C が実行されている](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | "*Azure AD Domain Services が正常に機能するために必要なサービス プリンシパルが Azure AD ディレクトリから削除されています。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。* " | [不足しているサービス プリンシパル](alert-service-principal.md) |
| AADDS103 | "*Azure AD Domain Services を有効にしている仮想ネットワークの IP アドレス範囲がパブリック IP 範囲内にあります。Azure AD Domain Services は、プライベート IP アドレス範囲にある仮想ネットワークで有効にする必要があります。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。* " | [アドレスがパブリック IP 範囲内である](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | "*このマネージド ドメインのドメイン コントローラーに到達できません。これは、仮想ネットワークに構成されているネットワーク セキュリティ グループ (NSG) がマネージド ドメインへのアクセスをブロックしている場合に発生する可能性があります。他の理由として、インターネットからの着信トラフィックをブロックしているユーザー定義ルートが存在する可能性があります。* " | [ネットワーク エラー](alert-nsg.md) |
| AADDS105 | *アプリケーション ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" のサービス プリンシパルは削除されましたが、その後再作成されました。The recreation leaves behind inconsistent permissions on Azure AD Domain Services resources needed to service your managed domain. (再作成により、マネージド ドメインのサービスに必要な Azure AD Domain Services リソースに整合性のないアクセス許可が残っています。)Synchronization of passwords on your managed domain could be affected. (マネージド ドメインでのパスワードの同期が影響を受ける可能性があります。)* | [パスワード同期アプリケーションが古い](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *ご利用のマネージド ドメインに関連付けられた Azure サブスクリプションが削除されています。Azure AD Domain Services が引き続き正しく動作するには、アクティブなサブスクリプションが必要です。* | [Azure サブスクリプションが見つからない](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *ご利用のマネージド ドメインに関連付けられた Azure サブスクリプションがアクティブではありません。Azure AD Domain Services が引き続き正しく動作するには、アクティブなサブスクリプションが必要です。* | [Azure サブスクリプションが無効](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *The subscription used by Azure AD Domain Services has been moved to another directory. (Azure AD Domain Services によって使用されるサブスクリプションは、別のディレクトリに移動されました。)Azure AD Domain Services needs to have an active subscription in the same directory to function properly. (Azure AD Domain Services を正常に機能させるには、アクティブなサブスクリプションを同じディレクトリに配置する必要があります。)* | [サブスクリプションのディレクトリが移動した](#aadds108-subscription-moved-directories) |
| AADDS109 | *ご利用のマネージド ドメインに使用されているリソースが削除されています。このリソースは、Azure AD Domain Services が正しく動作するために必要です。* | [リソースが削除されている](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Azure AD Domain Services のデプロイ用に選択されたサブネットがいっぱいになり、作成する必要がある追加のドメイン コントローラー用の領域がありません。* | [サブネットが満杯](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *A service principal that Azure AD Domain Services uses to service your domain is not authorized to manage resources on the Azure subscription. (ご使用のドメインへのサービス提供のために Azure AD Domain Services によって使用されるサービス プリンシパルに、Azure サブスクリプション上のリソースを管理する権限がありません。)The service principal needs to gain permissions to service your managed domain. (このサービス プリンシパルは、マネージド ドメインにサービスを提供するためのアクセス許可を取得する必要があります。)* | [サービス プリンシパルが承認されていない](#aadds111-service-principal-unauthorized) |
| AADDS112 | *このドメインの仮想ネットワークのサブネットに十分な IP アドレスがない可能性があることを確認しました。Azure AD Domain Services では、それが有効にされているサブネット内に少なくとも 2 つの使用可能な IP アドレスが必要です。サブネット内に少なくとも 3 - 5 個のスペア IP アドレスを用意することをお勧めします。他の仮想マシンがサブネット内にデプロイされ、使用可能な数の IP アドレスがすべて使用されたか、サブネット内の使用可能 IP アドレスの数に制限がある場合にこの状況が発生した可能性があります。* | [十分な IP アドレスがない](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Azure AD Domain Services で使用されているリソースが、予期しない状態で検出され、復旧することができません。* | [リソースが復旧できない](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *The subnet selected for deployment of Azure AD Domain Services is invalid, and cannot be used. (Azure AD Domain Services のデプロイ用に選択されたサブネットが無効であるため、使用できません。)* | [サブネットが無効](#aadds114-subnet-invalid) |
| AADDS115 | *ターゲット スコープがロックされているため、マネージド ドメインで使用されている 1 つ以上のネットワーク リソースを操作できません。* | [リソースがロックされている](#aadds115-resources-are-locked) |
| AADDS116 | *ポリシー制限により、マネージド ドメインで使用されている 1 つ以上のネットワーク リソースを操作できません。* | [リソースが使用できない](#aadds116-resources-are-unusable) |
| AADDS500 | "*マネージド ドメインが Azure AD と最後に同期されたのは [date] です。ユーザーがマネージド ドメインでサインインできない、またはグループ メンバーシップが Azure AD と同期されていない可能性があります。* " | [同期がしばらく行われていない](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | "*マネージド ドメインが最後にバックアップされたのは [date] です。* " | [バックアップがしばらく行われていない](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | "*マネージド ドメインのセキュリティで保護された LDAP 証明は [日付] に有効期限が切れます。* " | [セキュリティで保護された LDAP 証明書の期限切れ間近](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | "*マネージド ドメインは、ドメインに関連付けられている Azure サブスクリプションがアクティブでないため中断されます。* " | [無効にしたサブスクリプションが原因の中断](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | "*マネージド ドメインは、無効な構成により中断されます。サービスは、マネージド ドメインのドメイン コントローラーの管理、パッチ適用、または更新を長い間行うことができませんでした。* " | [無効な構成が原因の中断](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100:不足しているディレクトリ
**アラート メッセージ:**

"*マネージド ドメインに関連付けられている Azure AD ディレクトリが削除されている可能性があります。マネージド ドメインはもうサポートされる構成に含まれていません。Microsoft は、マネージド ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。* "

**解決策:**

通常、このエラーは、Azure サブスクリプションを新しい Azure AD ディレクトリに移動し、まだ Azure AD Domain Services に関連付けられている古い Azure AD ディレクトリを削除したことによって発生します。

このエラーは回復できません。 解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、新しいディレクトリ内に再作成する必要があります。 削除で問題が発生した場合は、Azure Active Directory Domain Services 製品チームに[お問い合わせ](contact-us.md)ください。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101:このディレクトリで Azure AD B2C が実行されている
**アラート メッセージ:**

"*Azure AD B2C ディレクトリで Azure AD Domain Services を有効にできません。* "

**解決策:**

>[!NOTE]
>Azure AD Domain Services の使用を続行するには、Azure AD B2C 以外のディレクトリで Azure AD Domain Services インスタンスを再作成する必要があります。

サービスを復元するには、次の手順に従います。

1. 既存の Azure AD ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。
2. Azure AD B2C 以外の新しいディレクトリを作成します。
3. [使用の開始](create-instance.md)ガイドに従って、マネージド ドメインを再作成します。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103:アドレスがパブリック IP 範囲内である

**アラート メッセージ:**

"*Azure AD Domain Services を有効にしている仮想ネットワークの IP アドレス範囲がパブリック IP 範囲内にあります。Azure AD Domain Services は、プライベート IP アドレス範囲にある仮想ネットワークで有効にする必要があります。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。* "

**解決策:**

> [!NOTE]
> この問題に対処するには、既存のマネージド ドメインを削除し、プライベート IP アドレス範囲にある仮想ネットワーク内に再作成します。 このプロセスは中断を伴います。

開始する前に、[こちらの記事](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)の「**private IP v4 address space**」(プライベート IP v4 アドレス空間) セクションを参照してください。

仮想ネットワーク内では、マシンが、サブネット用に構成されたものと同じ IP アドレス範囲内にある Azure リソースを要求することがあります。 ただし、仮想ネットワークはこの範囲に構成されているため、これらの要求は仮想ネットワーク内でルーティングされ、目的の Web リソースに到達しません。 この構成により、Azure AD Domain Services で予期しないエラーが発生する可能性があります。

**仮想ネットワークに構成されているインターネットの IP アドレス範囲を保有している場合は、このアラートを無視できます。ただし、予期しないエラーが発生する可能性があるため、Azure AD Domain Services は、この構成で [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] にコミットすることはできません。**


1. ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。
2. サブネットの IP アドレス範囲を修正します。
   1. [Azure Portal の [仮想マシン] ページ](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)に移動します。
   2. Azure AD Domain Services で使用する予定の仮想ネットワークを選択します。
   3. [設定] の下の **[アドレス空間]** をクリックします。
   4. 既存のアドレス範囲をクリックし、それを編集するかアドレス範囲を追加することでアドレス範囲を更新します。 新しいアドレス範囲がプライベート IP 範囲にあることを確認します。 変更を保存します。
   5. 左側のナビゲーション メニューで、 **[サブネット]** をクリックします。
   6. テーブルの編集するサブネットをクリックします。
   7. アドレス範囲を更新し、変更を保存します。
3. [Azure AD Domain Services の使用開始に関するガイド](create-instance.md)に従って、マネージド ドメインを再作成します。 プライベート IP アドレス範囲にある仮想ネットワークを選択していることを確認します。
4. 仮想マシンを新しいドメインに参加させるには、[こちらのガイド](join-windows-vm.md)に従います。
8. アラートが解決されたことを確認するために、ドメインの正常性を 2 時間以内に確認します。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106:Azure サブスクリプションが見つからない

**アラート メッセージ:**

*ご利用のマネージド ドメインに関連付けられた Azure サブスクリプションが削除されています。Azure AD Domain Services が引き続き正しく動作するには、アクティブなサブスクリプションが必要です。*

**解決策:**

Azure AD Domain Services が機能するには、サブスクリプションが必要で、別のサブスクリプションに移行することはできません。 ご利用のマネージド ドメインに関連付けられた Azure サブスクリプションが削除されているため、Azure サブスクリプションと Azure AD Domain Services を再作成する必要があります。

1. Azure サブスクリプションを作成します。
2. 既存の Azure AD ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。
3. [使用の開始](create-instance.md)ガイドに従って、マネージド ドメインを再作成します。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107:Azure サブスクリプションが無効

**アラート メッセージ:**

*ご利用のマネージド ドメインに関連付けられた Azure サブスクリプションがアクティブではありません。Azure AD Domain Services が引き続き正しく動作するには、アクティブなサブスクリプションが必要です。*

**解決策:**


1. [Azure サブスクリプションを更新してください](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. サブスクリプションを更新すると、Azure から Azure AD Domain Services にマネージド ドメインを再度有効にするための通知が送信されます。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108:サブスクリプションのディレクトリが移動した

**アラート メッセージ:**

*The subscription used by Azure AD Domain Services has been moved to another directory. (Azure AD Domain Services によって使用されるサブスクリプションは、別のディレクトリに移動されました。)Azure AD Domain Services needs to have an active subscription in the same directory to function properly. (Azure AD Domain Services を正常に機能させるには、アクティブなサブスクリプションを同じディレクトリに配置する必要があります。)*

**解決策:**

Azure AD Domain Services に関連付けられているサブスクリプションを以前のディレクトリに移動するか、または既存のディレクトリから[マネージド ドメインを削除](delete-aadds.md)し、選択したディレクトリに再作成する必要があります (新しいサブスクリプションを使用するか、Azure AD Domain Services のインスタンスが置かれているディレクトリを変更します)。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109:マネージド ドメインのリソースが見つからない

**アラート メッセージ:**

*ご利用のマネージド ドメインに使用されているリソースが削除されています。このリソースは、Azure AD Domain Services が正しく動作するために必要です。*

**解決策:**

Azure AD Domain Services では、適切に機能するために、デプロイ中にパブリック IP アドレス、NIC、ロード バランサーなどの特定のリソースが作成されます。 これらのいずれかが削除された場合は、これが原因でご利用のマネージド ドメインがサポートされない状態になり、ご利用のドメインが管理されなくなります。 このアラートは、Azure AD Domain Services のリソースを編集できる人が必要なリソースを削除した場合に表示されます。 次の手順では、マネージド ドメインを復元する方法を説明します。

1. Azure AD Domain Services の正常性ページに移動します
   1.    Azure portal で、[Azure AD Domain Services ページ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)に移動します。
   2.    左側のナビゲーションで、 **[正常性]** をクリックします。
2. アラートが 4 時間未満のものかどうかを確認します
   1.    正常性ページで、ID **AADDS109** のアラートをクリックします
   2.    アラートには、最初に検出されたときのタイムスタンプがあります。 そのタイムスタンプが 4 時間未満の場合は、Azure AD Domain Services で削除されたリソースを再作成できる可能性があります。
3. アラートが 4 時間以上前のものである場合は、マネージド ドメインは回復不可能な状態にあります。 Azure AD Domain Services を削除して再作成する必要があります。


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110:マネージド ドメインに関連付けられているサブネットが満杯

**アラート メッセージ:**

*Azure AD Domain Services のデプロイ用に選択されたサブネットがいっぱいになり、作成する必要がある追加のドメイン コントローラー用の領域がありません。*

**解決策:**

このエラーは回復できません。 解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、[マネージド ドメインを再作成する](create-instance.md)必要があります。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111:サービス プリンシパルが承認されていない

**アラート メッセージ:**

*A service principal that Azure AD Domain Services uses to service your domain is not authorized to manage resources on the Azure subscription. (ご使用のドメインへのサービス提供のために Azure AD Domain Services によって使用されるサービス プリンシパルに、Azure サブスクリプション上のリソースを管理する権限がありません。)The service principal needs to gain permissions to service your managed domain. (このサービス プリンシパルは、マネージド ドメインにサービスを提供するためのアクセス許可を取得する必要があります。)*

**解決策:**

サービス プリンシパルには、マネージド ドメインのリソースを管理、作成するためのアクセス権が必要です。 サービス プリンシパルのアクセスを拒否したユーザーがいるため、現在リソースを管理することができません。 次の手順に従って、サービス プリンシパルにアクセス権を付与してください。

1. [RBAC 制御と Azure portal を使ったアプリケーションへのアクセス権の付与方法](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)に関する記事を読んでください
2. ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` のサービス プリンシパルのアクセス権をレビューし、以前の日付で拒否されたアクセス権を付与します。


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112:マネージド ドメインに十分な数の IP アドレスがない

**アラート メッセージ:**

*このドメインの仮想ネットワークのサブネットに十分な IP アドレスがない可能性があることを確認しました。Azure AD Domain Services では、それが有効にされているサブネット内に少なくとも 2 つの使用可能な IP アドレスが必要です。サブネット内に少なくとも 3 - 5 個のスペア IP アドレスを用意することをお勧めします。他の仮想マシンがサブネット内にデプロイされ、使用可能な数の IP アドレスがすべて使用されたか、サブネット内の使用可能 IP アドレスの数に制限がある場合にこの状況が発生した可能性があります。*

**解決策:**

1. テナントからマネージド ドメインを削除します。
2. サブネットの IP アドレス範囲を修正します。
   1. [Azure Portal の [仮想マシン] ページ](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)に移動します。
   2. Azure AD Domain Services で使用する予定の仮想ネットワークを選択します。
   3. [設定] の下の **[アドレス空間]** をクリックします。
   4. 既存のアドレス範囲をクリックし、それを編集するかアドレス範囲を追加することでアドレス範囲を更新します。 変更を保存します。
   5. 左側のナビゲーション メニューで、 **[サブネット]** をクリックします。
   6. テーブルの編集するサブネットをクリックします。
   7. アドレス範囲を更新し、変更を保存します。
3. [Azure AD Domain Services の使用開始に関するガイド](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)に従って、マネージド ドメインを再作成します。 プライベート IP アドレス範囲にある仮想ネットワークを選択していることを確認します。
4. 仮想マシンを新しいドメインに参加させるには、[こちらのガイド](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)に従います。
5. 手順を正しく完了したことを確認するには、2 時間後にドメインの正常性をチェックします。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113:リソースが復旧できない

**アラート メッセージ:**

*Azure AD Domain Services で使用されているリソースが、予期しない状態で検出され、復旧することができません。*

**解決策:**

このエラーは回復できません。 解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、[マネージド ドメインを再作成する](create-instance.md)必要があります。

## <a name="aadds114-subnet-invalid"></a>AADDS114:サブネットが無効

**アラート メッセージ:**

*The subnet selected for deployment of Azure AD Domain Services is invalid, and cannot be used. (Azure AD Domain Services のデプロイ用に選択されたサブネットが無効であるため、使用できません。)*

**解決策:**

このエラーは回復できません。 解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、[マネージド ドメインを再作成する](create-instance.md)必要があります。

## <a name="aadds115-resources-are-locked"></a>AADDS115:リソースがロックされている

**アラート メッセージ:**

*ターゲット スコープがロックされているため、マネージド ドメインで使用されている 1 つ以上のネットワーク リソースを操作できません。*

**解決策:**

1.  ネットワーク リソースに対する Resource Manager の操作ログ (これはどのロックが変更を妨げているかに関する情報を提供します) を確認します。
2.  Azure AD Domain Services のサービス プリンシパルがリソースを操作できるように、リソースのロックを削除します。

## <a name="aadds116-resources-are-unusable"></a>AADDS116:リソースが使用できない

**アラート メッセージ:**

*ポリシー制限により、マネージド ドメインで使用されている 1 つ以上のネットワーク リソースを操作できません。*

**解決策:**

1.  ご利用のマネージド ドメインのネットワーク リソースに対する Resource Manager の操作ログを確認します。
2.  AAD-DS のサービス プリンシパルがリソースを操作できるように、リソースに対するポリシー制限を緩和します。



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500:同期がしばらく行われていない

**アラート メッセージ:**

"*マネージド ドメインが Azure AD と最後に同期されたのは [date] です。ユーザーがマネージド ドメインでサインインできない、またはグループ メンバーシップが Azure AD と同期されていない可能性があります。* "

**解決策:**

[ご利用のドメインの正常性](check-health.md)で、マネージド ドメインの構成の問題を示している可能性があるアラートをすべて確認します。 構成の問題によって、マネージド ドメインの同期を行うための Microsoft の機能がブロックされることがあります。 アラートを解決できる場合は、2 時間待機してから、同期が完了したかどうかを再度確認してください。

管理対象ドメインで同期が停止する一般的な理由を次に示します。
- 管理対象ドメインでネットワーク接続がブロックされる。 ネットワークに問題がないか確認することの詳細については、[ネットワーク セキュリティ グループをトラブルシューティングする方法](alert-nsg.md)および[Azure AD Domain Services に対するネットワーク要件](network-considerations.md)を参照してください。
-  パスワード同期が設定されないか、完了しない。 パスワード同期を設定するには、[この記事](active-directory-ds-getting-started-password-sync.md)を読んでください。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501:バックアップがしばらく行われていない

**アラート メッセージ:**

"*マネージド ドメインが最後にバックアップされたのは [date] です。* "

**解決策:**

[ご利用のドメインの正常性](check-health.md)で、マネージド ドメインの構成の問題を示している可能性があるアラートをすべて確認します。 構成の問題によって、マネージド ドメインのバックアップを行うための Microsoft の機能がブロックされることがあります。 アラートを解決できる場合は、2 時間待機してから、バックアップが完了したかどうかを再度確認してください。


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503:無効にしたサブスクリプションが原因の中断

**アラート メッセージ:**

"*マネージド ドメインは、ドメインに関連付けられている Azure サブスクリプションがアクティブでないため中断されます。* "

**解決策:**

> [!WARNING]
> 管理対象ドメインが長時間中断する場合、削除される危険性があります。 中断にできるだけ早く対処することをお勧めします。 詳細については、[この記事](suspension.md)を参照してください。

サービスを復元するには、ご利用のマネージド ドメインに関連付けられた [Azure サブスクリプションを更新](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)します。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504:無効な構成が原因の中断

**アラート メッセージ:**

"*マネージド ドメインは、無効な構成により中断されます。サービスは、マネージド ドメインのドメイン コントローラーの管理、パッチ適用、または更新を長い間行うことができませんでした。* "

**解決策:**

> [!WARNING]
> 管理対象ドメインが長時間中断する場合、削除される危険性があります。 中断にできるだけ早く対処することをお勧めします。 詳細については、[この記事](suspension.md)を参照してください。

[ご利用のドメインの正常性](check-health.md)で、マネージド ドメインの構成の問題を示している可能性があるアラートをすべて確認します。 これらのアラートのいずれかを解決できる場合は、それを解決します。 その後、サポートに連絡して、サブスクリプションを再度有効にしてください。


## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
