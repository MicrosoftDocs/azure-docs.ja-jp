---
title: グループ管理サービス アカウントのセキュリティ保護 | Azure Active Directory
description: グループ管理サービス アカウントのコンピューター アカウントのセキュリティ保護に関するガイド。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4c1adddbf4b13f8e299bd656443c9aaab1d55b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644829"
---
# <a name="securing-group-managed-service-accounts"></a>グループ管理サービス アカウントのセキュリティ保護

グループ管理サービス アカウント (gMSA) は、サービスをセキュリティで保護するために使用されるマネージド ドメイン アカウントです。 gMSA は、1 台のサーバー上、またはサーバー ファーム (ネットワーク ロード バランサー (NLB) やインターネット インフォメーション サービス (IIS) サーバーの背後にあるシステムなど) で実行できます。 gMSA プリンシパルを使用するようにサービスを構成すると、そのアカウントのパスワード管理は Windows によって処理されます。

## <a name="benefits-of-using-gmsas"></a>gMSA を使用する利点

gMSA では、次のことにより、管理オーバーヘッドを削減しながら、セキュリティが強化された単一 ID ソリューションが提供されます。

* **強力なパスワードを設定する**。 gMSA では、240 バイトのランダムに生成された複雑なパスワードが使用されます。 gMSA パスワードの複雑さと長さにより、ブルート フォースや辞書攻撃によってサービスが侵害される可能性が最小限に抑えられます。

* **パスワードを定期的に循環させる**。 gMSA では、パスワード管理は Windows に移行されます。それにより、パスワードは 30 日ごとに変更されます。 サービスおよびドメインの管理者は、サービス アカウントのセキュリティを維持するために、パスワードの変更をスケジュールしたり、サービスの停止を管理したりする必要がなくなりました。 

* **サーバー ファームへのデプロイをサポートする**。 gMSA を複数のサーバーにデプロイする機能を使用すると、複数のホストで同じサービスが実行される負荷分散ソリューションをサポートできます。 

* **簡略化されたサーバー プリンシパル名 (SPN) 管理をサポートする**。 アカウントの作成時に PowerShell を使用して SPN を設定できます。 また、gMSA のアクセス許可が正しく設定されていれば、自動 SPN 登録がサポートされているサービスでは、gMSA に対してそれを行うことができます。 

## <a name="when-to-use-gmsas"></a>どのような場合に gMSA を使用するか

フェールオーバー クラスタリングなどのサービスでサポートされていない場合を除き、gMSA はオンプレミス サービスの優先アカウントの種類として使用します。

> [!IMPORTANT]
> 運用環境にデプロイする前に、gMSA でサービスをテストする必要があります。 これを行うには、テスト環境を設定し、アプリケーションが gMSA を使用できること、およびアクセスする必要があるリソースにアクセスできることを確認します。 詳細については、「[グループ管理サービス アカウントのサポート](/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019)」を参照してください。


サービスで gMSA の使用がサポートされていない場合、次に最適な選択肢は、スタンドアロン管理サービス アカウント (sMSA) を使用することです。 sMSA では gMSA と同じ機能が提供されますが、単一サーバーでのデプロイのみを対象としています。

gMSA を使用できない場合や sMSA がサービスによってサポートされている場合は、標準ユーザー アカウントとして実行するようにサービスを構成する必要があります。 アカウントのセキュリティを維持するために、サービスとドメインの管理者は強力なパスワード管理プロセスを監視する必要があります。

## <a name="assess-the-security-posture-of-gmsas"></a>gMSA のセキュリティ体制を評価する

gMSA は、継続的なパスワード管理を必要とする標準ユーザー アカウントよりも本質的に安全性が高くなります。 ただし、全体的なセキュリティ体制を見るときには、gMSA のアクセス スコープを考慮することが重要です。

次の表に、gMSA を使用する場合の潜在的なセキュリティ問題とその軽減策を示します。

| セキュリティの問題| 軽減策 |
| - | - |
| gMSA は特権グループのメンバーです。 | グループ メンバーシップを確認します。 これを行うには、すべてのグループ メンバーシップが列挙される PowerShell スクリプトを作成し、結果の CSV ファイルを gMSA ファイルの名前でフィルター処理します。 <br>特権グループから gMSA を削除します。<br> gMSA に、そのサービスを実行するために必要な権限とアクセス許可のみを付与します (サービス ベンダーに問い合わせてください)。 
| gMSA には、機密リソースへの読み取りおよび書き込みアクセス権があります。 | 機密リソースへのアクセスを監査します。 監査ログを分析のために SIEM (たとえば、Azure Log Analytics や Azure Sentinel) にアーカイブします。 望ましくないレベルのアクセスが検出された場合は、不要なリソース アクセス許可を削除します。 |


## <a name="find-gmsas"></a>gMSA を検出する

ご自身の組織で既に gMSA が作成されている場合があります。 次の PowerShell コマンドレットを実行して、これらのアカウントを取得します。

gMSA は、効果的に機能するには、管理サービス アカウント組織単位 (OU) に含まれている必要があります。

  
![管理サービス アカウント OU のスクリーンショット。](./media/securing-service-accounts/secure-gmsa-image-1.png)

そこにない可能性のあるサービス MSA を検出するには、次のコマンドを参照してください。

**gMSA と sMSA を含むすべてのサービス アカウントを検出するには、次のようにします。**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>gMSA を管理する

gMSA を管理するには、次の Active Directory PowerShell コマンドレットを使用できます。

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Windows Server 2012 以降、*-ADServiceAccount コマンドレットは既定で gMSA で動作します。 上記のコマンドの使用法について詳しくは、[**グループ管理サービス アカウントの概要**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)に関するページを参照してください。

## <a name="move-to-a-gmsa"></a>gMSA に移動する
gMSA は、オンプレミスのニーズにとって最も安全な種類のサービス アカウントです。 それに移動できる場合は、そうすることをお勧めします。 さらに、お使いのサービスを Azure に移動し、お使いのサービス アカウントを Azure Active Directory に移動することもご検討ください。

1.  [KDS ルート キーがフォレストにデプロイ](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)されていることを確認します。 これは 1 回限りの操作です。

2. [新しい gMSA を作成します](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)。

3. サービスが実行されている各ホストに新しい gMSA をインストールします。
   > [!NOTE] 
   > ホストでの gMSA の作成とインストールの詳細については、gMSA を使用するようにサービスを構成する前に、[グループ管理サービス アカウントの概要](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))に関する記事を参照してください。

 
4. お使いのサービス ID を gMSA に変更し、空白のパスワードを指定します。

5. お使いのサービスが新しい gMSA ID で動作していることを確認します。

6. 古いサービス アカウント ID を削除します。

 

## <a name="next-steps"></a>次のステップ
サービス アカウントのセキュリティ保護に関する次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)

* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)

* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)

* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)

* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)

* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)