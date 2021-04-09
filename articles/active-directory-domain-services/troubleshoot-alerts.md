---
title: Azure AD Domain Services での一般的なアラートと解決策 | Microsoft Docs
description: Azure Active Directory Domain Services の正常性状態の一部として生成される一般的なアラートを解決する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 4caf804a274956556d6e9ca396c8f08594b11a87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092879"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>既知の問題:Azure Active Directory Domain Services での一般的なアラートと解決策

アプリケーションの ID と認証の中心部分として、Azure Active Directory Domain Services (Azure AD DS) では問題が発生することがあります。 問題が発生した場合、動作の再開に役立ついくつかの一般的なアラートと、関連するトラブルシューティングの手順があります。 また、追加のトラブルシューティング支援を求めて、いつでも [Azure サポート リクエストを開く][azure-support]ことができます。

この記事では、Azure AD DS での一般的なアラートのトラブルシューティング情報を示します。

## <a name="aadds100-missing-directory"></a>AADDS100:不足しているディレクトリ

### <a name="alert-message"></a>アラート メッセージ

"*マネージド ドメインに関連付けられている Azure AD ディレクトリが削除されている可能性があります。マネージド ドメインはもうサポートされる構成に含まれていません。Microsoft は、マネージド ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。* "

### <a name="resolution"></a>解決方法

通常、このエラーは、Azure サブスクリプションを新しい Azure AD ディレクトリに移動し、Azure AD DS に関連付けられている古い Azure AD ディレクトリを削除したときに発生します。

このエラーは回復できません。 アラートを解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、新しいディレクトリ内に再作成します。 マネージド ドメインの削除に問題がある場合は、[Azure サポート リクエストを開いて][azure-support]、追加のトラブルシューティングの支援を受けてください。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101:このディレクトリで Azure AD B2C が実行されている

### <a name="alert-message"></a>アラート メッセージ

"*Azure AD B2C ディレクトリで Azure AD Domain Services を有効にできません。* "

### <a name="resolution"></a>解決方法

Azure AD DS は、Azure AD ディレクトリと自動的に同期します。 Azure AD ディレクトリが B2C 用に構成されている場合、Azure AD DS をデプロイして同期することはできません。

Azure AD DS を使用するには、次の手順に従って、非 Azure AD B2C ディレクトリにマネージド ドメインを再作成する必要があります。

1. 既存の Azure AD ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。
1. Azure AD B2C ディレクトリではない新しい Azure AD ディレクトリを作成します。
1. [代替のマネージド ドメインを作成します](tutorial-create-instance.md)。

マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103:アドレスがパブリック IP 範囲内である

### <a name="alert-message"></a>アラート メッセージ

"*Azure AD Domain Services を有効にしている仮想ネットワークの IP アドレス範囲がパブリック IP 範囲内にあります。Azure AD Domain Services は、プライベート IP アドレス範囲にある仮想ネットワークで有効にする必要があります。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。* "

### <a name="resolution"></a>解決方法

開始する前に、[プライベート IP v4 アドレス空間](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)について理解しておく必要があります。

仮想ネットワーク内では、VM は、サブネット用に構成されたのと同じ IP アドレス範囲内にある Azure リソースに対して要求を行うことができます。 サブネットのパブリック IP アドレス範囲を構成すると、仮想ネットワーク内でルーティングされた要求が、目的の Web リソースに届かない場合があります。 この構成により、Azure AD DS で予期しないエラーが発生する可能性があります。

> [!NOTE]
> 仮想ネットワークに構成されているインターネットの IP アドレス範囲を保有している場合は、このアラートを無視できます。 ただし、予期しないエラーが発生する可能性があるため、Azure AD Domain Services は、この構成で [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) にコミットすることはできません。

このアラートを解決するには、既存のマネージド ドメインを削除し、プライベート IP アドレス範囲を持つ仮想ネットワーク内に再作成します。 このプロセスは、マネージド ドメインが使用できず、OU やサービス アカウントのような作成したカスタム リソースが失われるため、破壊的です。

1. ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。
1. 仮想ネットワークの IP アドレス範囲を更新するには、Azure portal で *仮想ネットワーク* を検索して選択します。 誤ってパブリック IP アドレス範囲が設定されている Azure AD DS の仮想ネットワークを選択します。
1. *[設定]* で、 **[アドレス空間]** を選択します。
1. 既存のアドレス範囲を選択し、それを編集するかアドレス範囲を追加することでアドレス範囲を更新します。 新しい IP アドレス範囲がプライベート IP 範囲にあることを確認します。 準備ができたら、変更を **保存** します。
1. 左側のナビゲーション メニューで、 **[サブネット]** を選択します。
1. 編集するサブネットを選択するか、追加のサブネットを作成します。
1. プライベート IP アドレス範囲を更新または指定してから、変更を **保存** します。
1. [代替のマネージド ドメインを作成します](tutorial-create-instance.md)。 必ず、プライベート IP アドレス範囲で更新された仮想ネットワーク サブネットを選択してください。

マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106:Azure サブスクリプションが見つからない

### <a name="alert-message"></a>アラート メッセージ

*ご利用のマネージド ドメインに関連付けられた Azure サブスクリプションが削除されています。Azure AD Domain Services が引き続き正しく動作するには、アクティブなサブスクリプションが必要です。*

### <a name="resolution"></a>解決方法

Azure AD DS にはアクティブなサブスクリプションが必要です。別のサブスクリプションに移動することはできません。 マネージド ドメインが関連付けられていた Azure サブスクリプションが削除されている場合は、Azure サブスクリプションとマネージド ドメインを再作成する必要があります。

1. [Azure サブスクリプションを作成します](../cost-management-billing/manage/create-subscription.md)。
1. 既存の Azure AD ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。
1. [代替のマネージド ドメインを作成します](tutorial-create-instance.md)。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107:Azure サブスクリプションが無効

### <a name="alert-message"></a>アラート メッセージ

*ご利用のマネージド ドメインに関連付けられた Azure サブスクリプションがアクティブではありません。Azure AD Domain Services が引き続き正しく動作するには、アクティブなサブスクリプションが必要です。*

### <a name="resolution"></a>解決方法

Azure AD DS には、アクティブなサブスクリプションが必要です。 マネージド ドメインが関連付けられていた Azure サブスクリプションがアクティブでない場合は、それを更新して、サブスクリプションを再度アクティブにする必要があります。

1. [Azure サブスクリプションを更新してください](../cost-management-billing/manage/subscription-disabled.md)。
2. サブスクリプションが更新されたら、Azure AD DS 通知を使用して、マネージド ドメインを再び有効にすることができます。

マネージド ドメインが再度有効にされると、マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108:サブスクリプションのディレクトリが移動した

### <a name="alert-message"></a>アラート メッセージ

*The subscription used by Azure AD Domain Services has been moved to another directory. (Azure AD Domain Services によって使用されるサブスクリプションは、別のディレクトリに移動されました。)Azure AD Domain Services needs to have an active subscription in the same directory to function properly. (Azure AD Domain Services を正常に機能させるには、アクティブなサブスクリプションを同じディレクトリに配置する必要があります。)*

### <a name="resolution"></a>解決方法

Azure AD DS にはアクティブなサブスクリプションが必要です。別のサブスクリプションに移動することはできません。 マネージド ドメインが関連付けられていた Azure サブスクリプションが移動されている場合は、サブスクリプションを前のディレクトリに戻すか、既存のディレクトリから[マネージド ドメインを削除](delete-aadds.md)し、[選択したサブスクリプションに、代替のマネージド ドメインを作成します](tutorial-create-instance.md)。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109:マネージド ドメインのリソースが見つからない

### <a name="alert-message"></a>アラート メッセージ

*ご利用のマネージド ドメインに使用されているリソースが削除されています。このリソースは、Azure AD Domain Services が正しく動作するために必要です。*

### <a name="resolution"></a>解決方法

Azure AD DS では、パブリック IP アドレス、仮想ネットワーク インターフェイス、ロード バランサーなど、適切に機能するために追加のリソースが作成されます。 これらのリソースのいずれかが削除されると、マネージド ドメインはサポートされない状態になり、ドメインが管理されなくなります。 これらのリソースの詳細については、「[Azure AD DS によって使用されるネットワーク リソース](network-considerations.md#network-resources-used-by-azure-ad-ds)」を参照してください。

このアラートは、これらの必要なリソースのいずれかが削除されたときに生成されます。 リソースが削除されてから 4 時間未満の場合は、削除されたリソースが Azure プラットフォームによって自動的に再作成される可能性があります。 次の手順では、リソースの削除の正常性状態とタイムスタンプを確認する方法について説明します。

1. Azure portal で、**Domain Services** を検索して選択します。 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. 左側のナビゲーションで、 **[正常性]** を選択します。
1. 正常性ページで、ID *AADDS109* のアラートを選択します。
1. アラートには、最初に検出されたときのタイムスタンプがあります。 そのタイムスタンプから 4 時間未満の場合、Azure プラットフォームは、リソースを自動的に再作成して、それ自体でアラートを解決できる可能性があります。

    アラートが 4 時間以上前のものである場合は、マネージド ドメインは回復不可能な状態にあります。 [マネージド ドメインを削除](delete-aadds.md)してから、[代替マネージド ドメインを作成します](tutorial-create-instance.md)。

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110:マネージド ドメインに関連付けられているサブネットが満杯

### <a name="alert-message"></a>アラート メッセージ

*Azure AD Domain Services のデプロイ用に選択されたサブネットがいっぱいになり、作成する必要がある追加のドメイン コントローラー用の領域がありません。*

### <a name="resolution"></a>解決方法

Azure AD DS の仮想ネットワーク サブネットには、自動的に作成されたリソースのための十分な IP アドレスが必要です。 この IP アドレス空間には、メンテナンス イベントがある場合に代替リソースを作成する必要性があります。 使用可能な IP アドレスが不足するリスクを最小限に抑えるために、独自の VM などの追加リソースをマネージド ドメインと同じ仮想ネットワーク サブネットにデプロイしないでください。

このエラーは回復できません。 アラートを解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、新たに作成します。 マネージド ドメインの削除に問題がある場合は、[Azure サポート リクエストを開いて][azure-support]、追加のトラブルシューティングの支援を受けてください。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111:サービス プリンシパルが承認されていない

### <a name="alert-message"></a>アラート メッセージ

*A service principal that Azure AD Domain Services uses to service your domain is not authorized to manage resources on the Azure subscription. (ご使用のドメインへのサービス提供のために Azure AD Domain Services によって使用されるサービス プリンシパルに、Azure サブスクリプション上のリソースを管理する権限がありません。)The service principal needs to gain permissions to service your managed domain. (このサービス プリンシパルは、マネージド ドメインにサービスを提供するためのアクセス許可を取得する必要があります。)*

### <a name="resolution"></a>解決方法

自動的に生成されるサービス プリンシパルの中には、マネージド ドメインのリソースの管理と作成に使用されるものがあります。 これらのいずれかのサービス プリンシパルのアクセス許可が変更されると、ドメインではリソースを正しく管理できません。 次の手順では、サービス プリンシパルに対するアクセス許可を理解し、付与する方法について説明します。

1. [Azure ロールベースのアクセス制御と Azure portal を使ったアプリケーションへのアクセス権の付与方法](../role-based-access-control/role-assignments-portal.md)に関する記事を参照してください。
2. ID *abba844e-bc0e-44b0-947a-dc74e5d09022* のサービス プリンシパルが持っているアクセス権を確認し、以前に拒否されたアクセス権を付与します。

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112:マネージド ドメインに十分な数の IP アドレスがない

### <a name="alert-message"></a>アラート メッセージ

*このドメインの仮想ネットワークのサブネットに十分な IP アドレスがない可能性があることを確認しました。Azure AD Domain Services では、それが有効にされているサブネット内に少なくとも 2 つの使用可能な IP アドレスが必要です。サブネット内に少なくとも 3 - 5 個のスペア IP アドレスを用意することをお勧めします。他の仮想マシンがサブネット内にデプロイされ、使用可能な数の IP アドレスがすべて使用されたか、サブネット内の使用可能 IP アドレスの数に制限がある場合にこの状況が発生した可能性があります。*

### <a name="resolution"></a>解決方法

Azure AD DS の仮想ネットワーク サブネットには、自動的に作成されたリソースのための十分な IP アドレスが必要です。 この IP アドレス空間には、メンテナンス イベントがある場合に代替リソースを作成する必要性があります。 使用可能な IP アドレスが不足するリスクを最小限に抑えるために、独自の VM などの追加リソースをマネージド ドメインと同じ仮想ネットワーク サブネットにデプロイしないでください。

このアラートを解決するには、既存のマネージド ドメインを削除し、十分大きい IP アドレス範囲を持つ仮想ネットワーク内に再作成します。 このプロセスは、マネージド ドメインが使用できず、OU やサービス アカウントのような作成したカスタム リソースが失われるため、破壊的です。

1. ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。
1. 仮想ネットワークの IP アドレス範囲を更新するには、Azure portal で *仮想ネットワーク* を検索して選択します。 IP アドレス範囲が小さいマネージド ドメインの仮想ネットワークを選択します。
1. *[設定]* で、 **[アドレス空間]** を選択します。
1. 既存のアドレス範囲を選択し、それを編集するかアドレス範囲を追加することでアドレス範囲を更新します。 新しい IP アドレス範囲がマネージド ドメインのサブネット範囲にとって十分な大きさであることを確認してください。 準備ができたら、変更を **保存** します。
1. 左側のナビゲーション メニューで、 **[サブネット]** を選択します。
1. 編集するサブネットを選択するか、追加のサブネットを作成します。
1. 十分大きい IP アドレス範囲を更新または指定してから、変更を **保存** します。
1. [代替のマネージド ドメインを作成します](tutorial-create-instance.md)。 必ず、十分な IP アドレス範囲で更新された仮想ネットワーク サブネットを選択してください。

マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113:リソースが復旧できない

### <a name="alert-message"></a>アラート メッセージ

*Azure AD Domain Services で使用されているリソースが、予期しない状態で検出され、復旧することができません。*

### <a name="resolution"></a>解決方法

このエラーは回復できません。 アラートを解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、新たに作成します。 マネージド ドメインの削除に問題がある場合は、[Azure サポート リクエストを開いて][azure-support]、追加のトラブルシューティングの支援を受けてください。

## <a name="aadds114-subnet-invalid"></a>AADDS114:サブネットが無効

### <a name="alert-message"></a>アラート メッセージ

*The subnet selected for deployment of Azure AD Domain Services is invalid, and cannot be used. (Azure AD Domain Services のデプロイ用に選択されたサブネットが無効であるため、使用できません。)*

### <a name="resolution"></a>解決方法

このエラーは回復できません。 アラートを解決するには、[既存のマネージド ドメインを削除](delete-aadds.md)し、新たに作成します。 マネージド ドメインの削除に問題がある場合は、[Azure サポート リクエストを開いて][azure-support]、追加のトラブルシューティングの支援を受けてください。

## <a name="aadds115-resources-are-locked"></a>AADDS115:リソースがロックされている

### <a name="alert-message"></a>アラート メッセージ

*ターゲット スコープがロックされているため、マネージド ドメインで使用されている 1 つ以上のネットワーク リソースを操作できません。*

### <a name="resolution"></a>解決方法

変更や削除を防ぐために、Azure リソースにリソース ロックを適用できます。 Azure AD DS はマネージド サービスであるため、Azure プラットフォームは構成を変更する機能を必要とします。 リソース ロックが一部の Azure AD DS コンポーネントに適用されている場合、Azure プラットフォームはその管理タスクを実行できません。

Azure AD DS コンポーネントのリソース ロックを確認して削除するには、次の手順を実行します。

1. リソース グループ内のマネージド ドメインのネットワーク コンポーネント (仮想ネットワーク、ネットワーク インターフェイス、パブリック IP アドレスなど) のそれぞれについて、Azure portal の操作ログを確認します。 これらの操作ログには、操作が失敗した理由とリソース ロックが適用されている場所が示されている必要があります。
1. ロックが適用されているリソースを選択し、 **[ロック]** の下でロックを選択して削除します。

## <a name="aadds116-resources-are-unusable"></a>AADDS116:リソースが使用できない

### <a name="alert-message"></a>アラート メッセージ

*ポリシー制限により、マネージド ドメインで使用されている 1 つ以上のネットワーク リソースを操作できません。*

### <a name="resolution"></a>解決方法

ポリシーは、許可する構成アクションを制御するために、Azure リソースとリソース グループに適用されます。 Azure AD DS はマネージド サービスであるため、Azure プラットフォームは構成を変更する機能を必要とします。 ポリシーが一部の Azure AD DS コンポーネントに適用されている場合、Azure プラットフォームはその管理タスクを実行できない可能性があります。

Azure AD DS コンポーネントに適用されているポリシーを確認して更新するには、次の手順を実行します。

1. リソース グループ内のマネージド ドメインのネットワーク コンポーネント (仮想ネットワーク、NIC、パブリック IP アドレスなど) のそれぞれについて、Azure portal の操作ログを確認します。 これらの操作ログには、操作が失敗した理由と制限の厳しいポリシーが適用されている場所が示されています。
1. ポリシーが適用されているリソースを選択し、 **[ポリシー]** から、制限が緩和されるように、ポリシーを選択して編集します。

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500:同期がしばらく行われていない

### <a name="alert-message"></a>アラート メッセージ

"*マネージド ドメインが Azure AD と最後に同期されたのは [date] です。ユーザーがマネージド ドメインでサインインできない、またはグループ メンバーシップが Azure AD と同期されていない可能性があります。* "

### <a name="resolution"></a>解決方法

[Azure AD DS の正常性をチェック](check-health.md)して、マネージド ドメインの構成の問題を示しているアラートがないか確認します。 ネットワーク構成に問題があると、Azure AD からの同期がブロックされる可能性があります。 構成の問題を示しているアラートを解決できる場合は、2 時間待機してから、同期が正常に完了したかどうかを再度確認してください。

一般的に、次のような理由により、マネージド ドメインで同期が停止します。

* 必要なネットワーク接続がブロックされている。 Azure 仮想ネットワークで問題がないか、何が必要かを確認する方法については、[ネットワーク セキュリティ グループのトラブルシューティング](alert-nsg.md) と [Azure AD DS のネットワーク要件](network-considerations.md)に関する記事を参照してください。
*  マネージド ドメインがデプロイされたときに、パスワード同期がセットアップされなかったか、正常に完了しなかった。 パスワード同期は、[クラウド専用ユーザー](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)または[オンプレミスのハイブリッド ユーザー](tutorial-configure-password-hash-sync.md)を対象にして設定できます。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501:バックアップがしばらく行われていない

### <a name="alert-message"></a>アラート メッセージ

"*マネージド ドメインが最後にバックアップされたのは [date] です。* "

### <a name="resolution"></a>解決方法

[Azure AD DS の正常性をチェック](check-health.md)して、マネージド ドメインの構成の問題を示しているアラートがないか確認します。 ネットワーク構成に問題があると、Azure プラットフォームが正常にバックアップを取れない場合があります。 構成の問題を示しているアラートを解決できる場合は、2 時間待機してから、同期が正常に完了したかどうかを再度確認してください。

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503:無効にしたサブスクリプションが原因の中断

### <a name="alert-message"></a>アラート メッセージ

"*マネージド ドメインは、ドメインに関連付けられている Azure サブスクリプションがアクティブでないため中断されます。* "

### <a name="resolution"></a>解決方法

> [!WARNING]
> マネージド ドメインは長時間中断されると、削除される危険性があります。 中断の理由をできるだけ早く解決してください。 詳細については、[Azure AD DS の中断状態の理解](suspension.md)に関する記事を参照してください。

Azure AD DS には、アクティブなサブスクリプションが必要です。 マネージド ドメインが関連付けられていた Azure サブスクリプションがアクティブでない場合は、それを更新して、サブスクリプションを再度アクティブにする必要があります。

1. [Azure サブスクリプションを更新してください](../cost-management-billing/manage/subscription-disabled.md)。
2. サブスクリプションが更新されたら、Azure AD DS 通知を使用して、マネージド ドメインを再び有効にすることができます。

マネージド ドメインが再度有効にされると、マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504:無効な構成が原因の中断

### <a name="alert-message"></a>アラート メッセージ

"*マネージド ドメインは、無効な構成により中断されます。サービスは、マネージド ドメインのドメイン コントローラーの管理、パッチ適用、または更新を長い間行うことができませんでした。* "

### <a name="resolution"></a>解決方法

> [!WARNING]
> マネージド ドメインは長時間中断されると、削除される危険性があります。 中断の理由をできるだけ早く解決してください。 詳細については、[Azure AD DS の中断状態の理解](suspension.md)に関する記事を参照してください。

[Azure AD DS の正常性をチェック](check-health.md)して、マネージド ドメインの構成の問題を示しているアラートがないか確認します。 構成の問題を示しているアラートを解決できる場合は、2 時間待機してから、同期が完了したかどうかを再度確認してください。 準備ができたら、マネージド ドメインを再度有効にするために、[Azure サポート リクエストを開きます][azure-support]。

## <a name="next-steps"></a>次のステップ

まだ問題が解決しない場合は、さらなるトラブルシューティングの支援を求めて、[Azure サポート リクエストを開いて][azure-support]ください。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md