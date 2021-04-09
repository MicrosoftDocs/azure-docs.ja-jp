---
title: Azure DevTest Labs に関する FAQ | Microsoft Docs
description: この記事では、Azure DevTest Labs に関連する、よく寄せられる質問 (FAQ) の一部の回答を示します。
ms.topic: article
ms.date: 07/17/2020
ms.openlocfilehash: 9fcdc160754822d5c6f22b7349d0e72f0cf22633
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590275"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs に関する FAQ
Azure DevTest Labs について特に多く寄せられる質問にお答えします。

## <a name="blog-post"></a>ブログ記事
DevTest Labs チームのブログは 2019 年 3 月 20 日時点で廃止されました。 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>今後の機能更新はどこで追跡できるのでしょうか。
今後、Azure ブログと Azure の更新情報で、機能更新や情報ブログ記事を投稿していきます。 これらのブログ記事は、必要に応じて、Microsoft のドキュメントにもリンクされます。

[DevTest Labs Azure ブログ](https://azure.microsoft.com/blog/tag/azure-devtest-labs/)と [DevTest Labs Azure の更新情報](https://azure.microsoft.com/updates/?product=devtest-lab)をご覧になり、DevTest Labs の新機能に関する情報を入手するようにしてください。

### <a name="what-happens-to-the-existing-blog-posts"></a>既存のブログ記事はどうなりますか。
現在、既存のブログ記事 (障害更新情報を除く) の [DevTest Labs ドキュメント](devtest-lab-overview.md)への移行を進めています。 MSDN ブログが非推奨になると、DevTest Labs のドキュメントの概要にリダイレクトされます。 リダイレクトされたら、タイトルの 'フィルター条件' で探している記事を検索できます。 まだすべての記事を移行していませんが、今月末までに完了するはずです。 


### <a name="where-do-i-see-outage-updates"></a>障害更新情報はどこで確認できますか。
今後、Twitter ハンドルを使用して、障害更新情報を投稿する予定です。 Twitter でフォローして、障害と既知のバグに関する最新の更新情報を取得してください。

### <a name="twitter"></a>Twitter
Twitter ハンドル: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>全般
### <a name="what-if-my-question-isnt-answered-here"></a>ここに質問の答えがない場合はどうすればいいですか。
ご自分の質問がここに表示されていない場合はご連絡ください。答えを見つけるお手伝いをします。

- この FAQ の末尾で質問を投稿してください。
- さらに多くの人と情報交換するには、[Azure DevTest Labs に関する Microsoft Q&A 質問ページ](/answers/topics/azure-devtestlabs.html)に質問を投稿してください。 Azure DevTest Labs チームや他のコミュニティ メンバーと情報交換できます。
- 機能を要求する場合は、要求とアイデアを [Azure DevTest Labs のユーザーの声](https://feedback.azure.com/forums/320373-azure-devtest-labs)に送信してください。

### <a name="what-is-a-microsoft-account"></a>Microsoft アカウントとは何ですか。
Microsoft アカウントは、Microsoft のデバイスとサービスで実行するほぼすべての操作に使用するアカウントです。 Skype、Outlook.com、OneDrive、Windows Phone、Azure、Xbox Live へのサインインに使用する電子メール アドレスとパスワードです。 1 つのアカウントで、どのデバイスからでもファイル、写真、連絡先、設定を利用できます。

> [!NOTE]
> Microsoft アカウントは、以前は Windows Live ID と呼ばれていました。

### <a name="why-should-i-use-azure-devtest-labs"></a>なぜ Azure DevTest Labs を使用する必要があるのですか。
Azure DevTest Labs を使用すると、チームの時間と費用を節約できます。 開発者は、複数の異なるベースを使用して独自の環境を作成できます。 また、アーティファクトを使用してアプリケーションを速やかにデプロイし、構成することもできます。 カスタム イメージと数式を使用して、仮想マシン (VM) をテンプレートとして保存し、チームで簡単に再現できます。 さらに、DevTest Labs には複数の構成可能なポリシーも用意されています。ラボ管理者は、これらのポリシーを使用して無駄を削減し、チームの環境を管理できます。 これらのポリシーには、自動シャットダウン、コストのしきい値、ユーザーごとの最大 VM 数、最大 VM サイズなどが含まれます。 DevTest Labs の詳細については、[概要](devtest-lab-overview.md)を確認するか、[入門ビデオ](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)をご覧ください。

### <a name="what-does-worry-free-self-service-mean"></a>"心配無用のセルフ サービス" とはどういう意味ですか。
"心配無用のセルフ サービス" とは、開発者とテスト担当者が、必要に応じて独自の環境を作成することを意味します。 管理者は、DevTest Labs を使用することで、適切なアクセスを設定したり、無駄を最小限に抑えたり、コストを管理したりできるという安心感が得られます。 管理者は、許可される VM のサイズ、VM の最大数、VM の起動およびシャットダウン時間を指定できます。 また、DevTest Labs では、ラボ リソースの使用状況を常に把握できるように、コストの監視とアラートの設定も簡単に行うことできます。

### <a name="how-can-i-use-devtest-labs"></a>DevTest Labs を使用するにはどうすればよいですか。
DevTest Labs は、開発環境やテスト環境が必要であり、これらを迅速に再現したり、コスト節約ポリシーを使用して管理したりする場合に常に役立ちます。
お客様は、次のようなシナリオで DevTest Labs を使用しています。

- 開発環境とテスト環境を 1 か所で管理する。 ポリシーを使用してコストを削減し、カスタム イメージを作成してチーム全体でビルドを共有する。
- 開発段階全体にわたってディスクの状態を保存するために、カスタム イメージを使用してアプリケーションを開発する。
- 進行状況に関連してコストを追跡する。
- 品質保証テスト用の大容量テスト環境を作成する。
- アーティファクトと数式を使用して、さまざまな環境でアプリケーションを簡単に構成し、再現する。
- ハッカソン (共同開発またはテスト作業) 用の VM を配布し、イベントの終了時に簡単にプロビジョニング解除する。

### <a name="how-am-i-billed-for-devtest-labs"></a>DevTest Labs の課金方法を教えてください。
DevTest Labs は無料サービスです。 DevTest Labs でのラボの作成や、ポリシー、テンプレート、アーティファクトの構成は無料です。 VM、ストレージ アカウント、仮想ネットワークなど、ラボ内で使用する Azure リソースに対してのみ課金されます。 ラボ リソースのコストの詳細については、「[Azure DevTest Labs の価格](https://azure.microsoft.com/pricing/details/devtest-lab/)」をご覧ください。

## <a name="security"></a>Security

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>DevTest Labs の各種セキュリティ レベルはどのようなものですか。
セキュリティ アクセスは、Azure のロールベースのアクセス制御 (Azure RBAC) によって決定されます。 アクセスのしくみを理解するには、Azure RBAC で定義されているアクセス許可、ロール、スコープの違いを理解することが有用です。

- **アクセス許可**: アクセス許可とは、特定のアクションへのアクセスを定義したものです。 たとえば、すべての VM への読み取りアクセス許可などがあります。
- **ロール**: ロールとは、グループ化してユーザーに割り当てることができる一連のアクセス許可です。 たとえば、サブスクリプション所有者ロールが割り当てられたユーザーは、サブスクリプション内のすべてのリソースにアクセスできます。
- **[スコープ]** : スコープとは、Azure リソースの階層内のレベルです。 たとえば、リソース グループ、単一のラボ、またはサブスクリプション全体をスコープとして指定できます。

DevTest Labs のスコープ内には、ユーザーのアクセス許可を定義する次の 2 種類のロールがあります。

- **ラボ所有者**:ラボ所有者は、ラボ内のすべてのリソースにアクセスできます。 ポリシーの変更、任意の VM に対する読み取りと書き込み、仮想ネットワークの変更などを行うことができます。
- **ラボ ユーザー**:ラボ ユーザーは、VM、ポリシー、仮想ネットワークなど、すべてのラボ リソースを表示できます。 ただし、ラボ ユーザーは他のユーザーが作成したポリシーや VM を変更することはできません。

DevTest Labs にカスタム ロールを作成することもできます。 DevTest Labs にカスタム ロールを作成する方法については、「[特定のラボ ポリシーに対するアクセス許可をユーザーに付与する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)」をご覧ください。

スコープは階層構造を持つため、特定のスコープでアクセス許可を持つユーザーには、そのスコープのすべての下位のスコープでそれらのアクセス許可が自動的に付与されます。 たとえば、ユーザーにサブスクリプション所有者のロールが割り当てられている場合、そのユーザーはサブスクリプションのすべてのリソースにアクセスできます。 これには、VM、仮想ネットワークおよびラボが含まれます。 サブスクリプション所有者は、ラボ所有者のロールを自動的に継承します。 ただし、その逆は真ではありません。 ラボ所有者はラボにアクセスできます。これはサブスクリプション レベルよりも下位のスコープです。 そのため、ラボ所有者はラボの外部の VM、仮想ネットワーク、またはその他のリソースを表示できません。

### <a name="how-do-i-define-azure-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>IT 部門が管理作業を行い開発者/テスト担当者がそれぞれの作業を行うことができるように、DevTest Labs 環境に対して Azure ロールベースのアクセス制御を定義するにはどうすればよいですか。
おおまかなパターンはありますが、詳細は組織によって異なります。

全社的 IT 部門は必要なもののみを所有し、プロジェクトおよびアプリケーション チームが必要なレベルの制御を行えるようにする必要があります。 一般にこれは、全社的 IT 部門はサブスクリプションを所有し、ネットワークの構成などの中核的 IT 機能を扱うことを意味します。 サブスクリプションの **所有者** のセットは小規模にする必要があります。 これらの所有者は、必要に応じて追加の所有者を指名したり、"パブリック IP なし" などのサブスクリプション レベルのポリシーを適用したりできます。

レベル 1 やレベル 2 のサポートなど、サブスクリプション全体へのアクセスを必要とするユーザーのサブセットが存在する場合があります。 その場合は、このようなユーザーに **共同作成者** のアクセス権を付与してリソースを管理できるようにしながら、ポリシーへのアクセスやその変更は許可しないことをお勧めします。

DevTest Labs のリソースは、プロジェクト/アプリケーション チームの近くにいる所有者によって所有される必要があります。 それは、彼らがマシンおよび必須のソフトウェアの要件を理解しているためです。 ほとんどの組織では、一般に、この DevTest Labs リソースの所有者はプロジェクト/開発リーダーです。 この所有者は、ラボ環境内のユーザーとポリシーを管理でき、DevTest Labs 環境にあるすべての VM を管理できます。

プロジェクトおよびアプリケーション チームのメンバーは、**DevTest Labs ユーザー** ロールに追加する必要があります。 これらのユーザーは、仮想マシンを (ラボおよびサブスクリプション レベルのポリシーに従って) 作成できます。 また、自分が所有する仮想マシンを管理することもできます。 他のユーザーに属している仮想マシンを管理することはできません。

詳細については、「[Azure エンタープライズ スキャフォールディング:サブスクリプションの規範的なガバナンス](/azure/architecture/cloud-adoption/appendix/azure-scaffold)」ドキュメントをご覧ください。


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>特定の 1 つのタスクの実行をユーザーに許可するようにロールを作成するにはどうすればよいですか。
カスタム ロールを作成し、ロールにアクセス許可を割り当てる方法の詳細については、「[特定のラボ ポリシーに対するアクセス許可をユーザーに付与する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)」をご覧ください。 ラボ内のすべての VM を起動および停止するアクセス許可を持つ **DevTest Labs Advanced User** ロールを作成するスクリプトの例を次に示します。


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>組織で企業のセキュリティ ポリシーを適用するにはどうすればよいですか。
次の操作を実行することによって実現できます。

- 包括的なセキュリティ ポリシーを開発して発行します。 ポリシーでは、クラウドの資産であるソフトウェアに関連付けられている許容される使用方法の規則を明確に示します。 また、ポリシーに明らかに違反することも定義します。
- カスタム イメージ、カスタム成果物、および Active Directory で定義されているセキュリティ領域内のオーケストレーションを可能にするデプロイ プロセスを開発します。 このアプローチにより、会社の境界が強制され、共通の環境コントロールのセットが設定されます。 開発者は、環境に対するこれらのコントロールを、全体的なプロセスの一部としてセキュリティ保護された開発ライフサイクルを開発して従うときに考慮できます。 また、開発の妨げになる可能性のある過剰な制限のない環境を提供しながら、妥当なコントロールのセットを提供するという目的もあります。 ラボの仮想マシンを含む組織単位 (OU) でのグループ ポリシーは、運用環境の全体的なグループ ポリシーのサブセットでもかまいません。 または、識別されたリスクを適切に軽減するための追加セットでもかまいません。

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>リモート開発者がコードを削除したり、マルウェアや未承認のソフトウェアを導入したりできないようにするデータ整合性を確保するにはどうすればよいですか。
DevTest Labs を使用してリモートで共同作業する外部のコンサルタント、請負業者、または従業員からの脅威を軽減するための複数のコントロール レイヤーがあります。

前に説明したように、最初のステップでは、ユーザーがポリシーに違反したときの影響が明確に説明されている許容される使用ポリシーを起草して定義する必要があります。

リモート アクセスに対するコントロールの最初のレイヤーでは、ラボに直接接続されていない VPN 接続経由でリモート アクセス ポリシーを適用します。

コントロールの第 2 のレイヤーでは、リモート デスクトップからのコピーと貼り付けを禁止するグループ ポリシー オブジェクトのセットを適用します。 環境外の FTP や RDP サービスなど、環境からの送信サービスを許可しないネットワーク ポリシーを実装できます。 ユーザー定義のルーティングですべての Azure ネットワーク トラフィックをオンプレミスに強制的に戻すことはできますが、プロキシでコンテンツとセッションをスキャンして制御しない限り、データのアップロードを許可する可能性があるすべての URL をルーティングできません。 外部ネットワーク リソースのブリッジを禁止するように、DevTest Labs をサポートする仮想ネットワーク内でパブリック IP アドレスを制限できます。

最終的には、同じ種類の制限を組織全体に適用する必要があります。コンテンツの送信を受け入れる可能性があるリムーバブル メディアまたは外部 URL のすべての可能な方法に対してもです。 セキュリティ担当者とセキュリティ ポリシーを検討して実装してください。 詳しい推奨事項については、[Microsoft のサイバー セキュリティ](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)に関するページをご覧ください。

## <a name="lab-configuration"></a>ラボの構成

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Resource Manager テンプレートからラボを作成するにはどうすればよいですか。
Microsoft では、そのままデプロイしたり、変更してラボ用のカスタム テンプレートを作成したりできる、[ラボの Azure Resource Manager テンプレートの GitHub リポジトリ](https://azure.microsoft.com/resources/templates/101-dtl-create-lab)を提供しています。 各テンプレートには、ご自分の Azure サブスクリプションにラボをそのままデプロイできるリンクが含まれています。 また、テンプレートをカスタマイズし、[PowerShell または Azure CLI を使用してデプロイ](../azure-resource-manager/templates/deploy-powershell.md)することもできます。


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>仮想マシンをその仮想マシン独自のリソース グループにそれぞれ入れるのではなく、すべての仮想マシンを共有のリソース グループに入れて作成することはできますか。
はい。ラボの所有者であるあなたは、リソース グループの割り当てをあなたの代わりにラボに処理させることも、あなたが指定した共通のリソース グループにすべての仮想マシンを入れて作成することも可能です。

別のリソース グループのシナリオ:
-   DevTest Labs が、あなたが作成したすべてのパブリックおよびプライベート IP の仮想マシンに対し、新しいリソース グループを作成します。
-   DevTest Labs は、同じサイズの、IP を共有するマシンに対し、リソース グループを作成します。

共有のリソース グループのシナリオ:
-   すべての仮想マシンは、指定した共通のリソース グループに作成されます。 詳細については、[ラボでのリソース グループの割り当て](./resource-group-control.md)に関するページを参照してください。

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>DevTest Labs 環境全体で名前付け規則を維持するにはどうすればよいですか?
現在の社内の名前付け規則を Azure の運用環境にまで拡張し、DevTest Labs 環境全体で一貫性を持たせたいことがあります。 DevTest Labs をデプロイするときは、開始時のポリシーを具体的に設けることをお勧めします。 一元化されたスクリプトと JSON テンプレートを使用してポリシーをデプロイすることで、一貫性を適用できます。 名前付けポリシーは、サブスクリプション レベルで適用される Azure のポリシーによって実装できます。 Azure Policy の JSON サンプルについては、「[Azure Policy のサンプル](../governance/policy/samples/index.md)」をご覧ください。

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>同じサブスクリプションにラボをいくつ作成できますか。
サブスクリプションごとに作成できるラボの数に特定の制限はありません。 ただし、サブスクリプションごとに使用できるリソースの量には制限があります。 [Azure サブスクリプションの制限とクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)および[これらの制限を引き上げる方法](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)に関する記事をご覧ください。


### <a name="how-many-vms-can-i-create-per-lab"></a>ラボごとにいくつの VM を作成できますか。
ラボごとに作成できる VM の数に特定の制限はありません。 ただし、使用できるリソース (VM コア、パブリック IP など) はサブスクリプションごとに制限されています。 [Azure サブスクリプションの制限とクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)および[これらの制限を引き上げる方法](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)に関する記事をご覧ください。

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>ラボあたりのユーザー数と組織全体で必要なラボの総数の比率を決める方法はありますか。
同じ開発プロジェクトに関連付けられている部署と開発グループは、同じラボに関連付けることをお勧めします。 両方のグループに同じ種類のポリシー、イメージ、およびシャットダウン ポリシーを適用できます。

また、地理的境界を考慮することが必要な場合があります。 たとえば、米国 (US) 北東部の開発者は、米国東部 2 でプロビジョニングされたラボを使用できます。 また、テキサス州ダラスおよびコロラド州デンバーの開発者は、米国中南部のリソースを使用できます。 外部のサード パーティと共同作業する場合は、社内開発者によって使用されていないラボに割り当てることができます。

また、Azure DevOps Projects 内の特定のプロジェクトのラボを使用することもできます。 その後、指定された Azure Active Directory グループによってセキュリティを適用すると、両方のリソースのセットにアクセスできます。 ラボに割り当てられた仮想ネットワークは、ユーザーを統合する別の境界になる場合があります。

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>ラボ内のリソースの削除を防ぐにはどうすればよいですか?
ラボ レベルで適切なアクセス許可を設定し、承認されたユーザーのみがリソースを削除したりラボのポリシーを変更したりできるようにすることをお勧めします。 開発者は、**DevTest Labs ユーザー** グループ内に配置する必要があります。 開発リーダーまたはインフラストラクチャ リーダーが、**DevTest Labs 所有者** になる必要があります。 ラボの所有者は 2 人のみにすることをお勧めします。 破損を防ぐため、このポリシーをコード リポジトリに拡張します。 ラボのユーザーは、リソースを使用する権限はありますが、ラボのポリシーを更新することはできません。 各組み込みグループがラボ内で持っているロールと権限の一覧については、「[Azure DevTest Labs での所有者とユーザーの追加](devtest-lab-add-devtest-user.md)」を参照してください。

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>ラボへの直接リンクを共有するにはどうすればよいですか。

1. [Azure portal](https://portal.azure.com) でラボに移動します。
2. ブラウザーから **ラボの URL** をコピーし、ラボ ユーザーと共有します。

> [!NOTE]
> ラボ ユーザーが Microsoft アカウントを持つ外部ユーザーであり、あなたの組織の Active Directory インスタンスのメンバーではない場合、そのユーザーが共有リンクにアクセスしようとすると、エラー メッセージが表示される場合があります。 外部ユーザーにエラー メッセージが表示された場合は、そのユーザーに、Azure Portal の右上隅に表示されている自分の名前をまず選択してもらいます。 これで、ユーザーはメニューの [ディレクトリ] セクションで、ラボが存在するディレクトリを選択できるようになります。

## <a name="virtual-machines"></a>仮想マシン

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>DevTest Labs で表示されている VM が [仮想マシン] ページに表示されないのはなぜですか。
DevTest Labs で VM を作成すると、その VM にアクセスするためのアクセス許可が付与されます。 この VM は、Labs ページと **[仮想マシン]** ページの両方に表示されます。 **DevTest ラボの所有者** ロールが割り当てられているユーザーは、ラボの **[すべての仮想マシン]** ページで、ラボで作成されたすべての VM を確認できます。 ただし、**DevTest ラボの所有者** ロールが割り当てられているユーザーには、他のユーザーが作成した VM リソースへの読み取りアクセス権は自動的に付与されません。 そのため、これらの VM は **[仮想マシン]** ページには表示されません。


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>同じテンプレートから複数の VM を一度に作成するにはどうすればよいですか。
同じテンプレートから複数の VM を一度に作成する場合、次の 2 つのオプションがあります。

- [Azure DevOps Tasks の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)を使用することができます。
- VM の作成中に [Resource Manager テンプレートを生成](devtest-lab-add-vm.md#save-azure-resource-manager-template)し、[Windows PowerShell から Resource Manager テンプレートをデプロイ](../azure-resource-manager/templates/deploy-powershell.md)できます。
- また、仮想マシンの作成時にマシンのインスタンスが 1 つ以上作成されるように指定することもできます。 仮想マシンのインスタンスを複数作成する方法の詳細については、[ラボの仮想マシンの作成](devtest-lab-add-vm.md)に関するドキュメントをご覧ください。

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>既存の Azure VM を DevTest Labs ラボに移動するにはどうすればよいですか。
既存の VM を DevTest Labs にコピーするには、次の手順に従います。

1.  [Windows PowerShell スクリプト](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)を使用して、既存の VM の VHD ファイルをコピーします。
2.  ご自分の DevTest Labs ラボ内に[カスタム イメージを作成](devtest-lab-create-template.md)します。
3.  カスタム イメージからラボ内に VM を作成します。

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>複数のディスクを VM に接続できますか。

はい。複数のディスクを VM に接続できます。

### <a name="are-gen-2-images-supported-by-devtest-labs"></a>Gen 2 イメージは DevTest Labs によってサポートされていますか。
はい。 DevTest Labs サービスでは、[Gen 2 イメージ](../virtual-machines/generation-2.md)がサポートされています。 ただし、1 つのイメージに対して Gen 1 と Gen 2 の両方のバージョンを使用できる場合、DevTest Labs では、VM を作成するときに Gen 1 バージョンのイメージのみが表示されます。 Gen 2 バージョンのみが使用可能な場合は、そのイメージが表示されます。 

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>テストに Windows OS イメージを使用する場合、MSDN サブスクリプションを購入する必要はありますか。
Azure でのご自分の開発またはテストに Windows クライアント OS イメージ (Windows 7 以降) を使用するには、次のいずれかの手順を実行します。

- [MSDN サブスクリプションを購入する](https://www.visualstudio.com/products/how-to-buy-vs)。
- Enterprise Agreement をご利用の場合は、[Enterprise Dev/Test オファー](https://azure.microsoft.com/offers/ms-azr-0148p)で Azure サブスクリプションを作成する。

各 MSDN サービスの Azure クレジットの詳細については、「[Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>ラボ内の VM をすべて削除するプロセスを自動化するにはどうすればよいですか。
ラボの所有者として、Azure portal のお使いのラボから VM を削除できます。 また、PowerShell スクリプトを使用して、ラボ内の VM をすべて削除することもできます。 次の例では、**values to change** コメントの下のパラメーター値を変更します。 `subscriptionId`、`labResourceGroup`、`labName` の各値は、Azure Portal のラボ ウィンドウから取得できます。

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>環境

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>DevTest Labs 環境で Resource Manager テンプレートを使用するにはどうすればよいですか。
[DevTest Labs での環境機能](devtest-lab-test-env.md)に関する記事で説明されている手順を使用して、DevTest Labs 環境にご自分の Resource Manager テンプレートをデプロイします。 基本的には、Resource Manager テンプレートを Git リポジトリ (Azure Repos または GitHub) にチェックインし、[テンプレート用のプライベート リポジトリ](devtest-lab-test-env.md)をラボに追加します。 このシナリオは、開発マシンのホストに DevTest Labs を使用している場合には役立たないことがありますが、運用環境の典型であるステージング環境を構築している場合には役立つことがあります。

また、ラボごとまたはユーザー オプションごとの仮想マシン数は、ラボ自体にネイティブに作成されるマシンの数のみを制限し、任意の環境 (Resource Manager テンプレート) によって作成されるマシンの数は制限しないこと注意することも重要です。

## <a name="custom-images"></a>カスタム イメージ

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>カスタム組織イメージを DevTest Labs 環境に展開する簡単に反復可能なプロセスをセットアップするにはどうすればよいですか。
次の[イメージ ファクトリ パターンに関するビデオ](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4)をご覧ください。 これは高度なシナリオであり、提供されているスクリプトはサンプル スクリプトのみです。 何らかの変更が必要な場合は、環境で使用されるスクリプトを自分で管理および保守する必要があります。

イメージ ファクトリの作成の詳細については、「[Azure DevTest Labs でカスタム イメージ ファクトリを作成する](image-factory-create.md)」を参照してください。

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>カスタム イメージと数式の違いは何ですか。
カスタム イメージは、マネージド イメージです。 数式は、追加設定で構成し、保存して再現できるイメージです。 同じ基本的な不変イメージを使用して複数の環境をすばやく作成する場合は、カスタム イメージをお勧めします。 数式は、最新のビットで、仮想ネットワークまたはサブネットの一部として、または特定のサイズの VM として、VM の構成を再現する場合に適しています。 詳細については、「[DevTest ラボのカスタム イメージと数式の比較](devtest-lab-comparing-vm-base-image-types.md)」をご覧ください。

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>定型イメージとカスタム イメージをどのように使い分ける必要がありますか。
通常、このシナリオでの決定要因はコストと再利用です。 基本イメージに多くのソフトウェアが追加されたイメージを多くのユーザーおよびラボが必要とするシナリオでは、カスタム イメージを作成してコストを減らすことができます。 つまり、イメージを 1 回だけ作成します。 仮想マシンのセットアップ時間が短縮され、セットアップ時に仮想マシンの実行が原因で発生するコストが減ります。

ただし、注意する必要のあるもう 1 つの要素は、ソフトウェア パッケージに対する変更の頻度です。 ビルドを毎日実行し、ソフトウェアをユーザーの仮想マシンに展開する必要がある場合は、カスタム イメージではなく定型イメージの使用を検討してください。

詳細な説明については、「[DevTest Labs のカスタム イメージと数式の比較](devtest-lab-comparing-vm-base-image-types.md)」をご覧ください。

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>カスタム イメージを作成するために VHD ファイルをアップロードするプロセスを自動化するにはどうすればよいですか。

カスタム イメージを作成するために VHD ファイルのアップロードを自動化する場合、次の 2 つのオプションがあります。

- [AzCopy](../storage/common/storage-use-azcopy-v10.md) を使用して、ラボに関連付けられているストレージ アカウントに VHD ファイルをコピーまたはアップロードします。
- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用します。 ストレージ エクスプローラーは、Windows、OS X、Linux で動作するスタンドアロン アプリです。

ラボに関連付けられているコピー先ストレージ アカウントを検索するには、次の手順に従います。

1.  [Azure portal](https://portal.azure.com) にサインインします。
2.  左側のメニューの **[リソース グループ]** を選択します。
3.  ラボに関連付けられているリソース グループを見つけて選択します。
4.  **[概要]** で、いずれかのストレージ アカウントを選択します。
5.  **[BLOB]** を選択します。
6.  一覧内でアップロードを検索します。 存在しない場合は、手順 4. に戻り、別のストレージ アカウントを試します。
7.  AzCopy コマンドで、コピー先として **URL** を使用します。

Azure Marketplace イメージと組織独自のカスタム イメージをどのように使い分ける必要がありますか。

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Azure Marketplace イメージと組織独自のカスタム イメージをどのように使い分ける必要がありますか。
特定の問題または組織の要件がない限り、Azure Marketplace を既定で使用する必要があります。 問題や要件とは、たとえば次のようなものです。

- 基本イメージの一部としてアプリケーションを含める必要がある複雑なソフトウェアのセットアップ。
- アプリケーションのインストールとセットアップに多くの時間がかかる場合。これは、Azure Marketplace イメージに追加される計算時間を効率的に使用していないことになります。
- 開発者やテスト担当者が迅速に仮想マシンにアクセスする必要があり、新しい仮想マシンのセットアップ時間を最小限にしたい場合。
- コンプライアンスまたは法規制の条件 (たとえば、セキュリティ ポリシー) をすべてのマシンに適用する必要がある場合。
- カスタム イメージの使用を軽々しく考えてはいけません。 基になっている基本イメージの VHD ファイルを管理する必要が生じるため、複雑さが増します。 また、基本イメージにソフトウェア更新プログラムを定期的に適用する必要があります。 これらの更新プログラムには、新しいオペレーティング システム (OS) の更新と、ソフトウェア パッケージ自体に必要なすべての更新または構成変更が含まれます。

## <a name="artifacts"></a>アーティファクト

### <a name="what-are-artifacts"></a>アーティファクトとは何ですか。
アーティファクトは、最新のビットまたは開発用ツールを VM にデプロイするために使用できるカスタマイズ可能な要素です。 VM の作成時に、アーティファクトを VM に接続します。 VM がプロビジョニングされると、アーティファクトによって VM がデプロイされ、構成されます。 [パブリック GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)で、さまざまな既存のアーティファクトを利用できます。 また、[独自のアーティファクトを作成](devtest-lab-artifact-author.md)することもできます。

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM の作成時にアーティファクトでエラーが発生しました。 どのようにトラブルシューティングすればよいですか。
失敗したアーティファクトのログを取得する方法については、[DevTest Labs でアーティファクトの失敗を診断する方法](devtest-lab-troubleshoot-artifact-failure.md)に関する記事をご覧ください。

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>DevTest Labs でのパブリック成果物リポジトリとプライベート成果物リポジトリはどのように使い分ける必要がありますか。
[パブリック成果物リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)では、最もよく使用されるソフトウェア パッケージの初期セットが提供されます。 共通の開発ツールとアドインの再生成に時間をかける必要がなく、すばやくデプロイするのに役立ちます。独自のプライベート リポジトリのデプロイを選択できます。 パブリック リポジトリとプライベート リポジトリを並列で使用できます。 パブリック リポジトリを無効にすることもできます。 プライベート リポジトリをデプロイする条件は、以下の質問と考慮事項を基に決定する必要があります。

- 組織には、DevTest Labs オファリングの一部として企業でライセンスされたソフトウェアを使用する要件がありますか。 答えが "はい" の場合は、プライベート リポジトリを作成する必要があります。
- 組織は特定の操作を提供するカスタム ソフトウェアを開発しており、それは全体的なプロビジョニング プロセスの一部として必要ですか。 答えが "はい" の場合は、プライベート リポジトリをデプロイする必要があります。
- 組織のガバナンス ポリシーで分離が必要であり、外部リポジトリの構成を組織が直接管理できない場合は、プライベートの成果物リポジトリをデプロイする必要があります。 このプロセスの一環として、パブリック リポジトリの初期コピーをコピーし、プライベート リポジトリと統合できます。 その後、パブリック リポジトリを無効にして、組織内のだれもそれ以上アクセスできないようにすることができます。 この方法では、組織内のすべてのユーザーに対して、組織によって承認された単一のリポジトリのみを使用することが強制され、構成のずれが最小限になります。


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>組織では単一のリポジトリを計画する必要がありますか、または複数のリポジトリでもかまいませんか。
組織の全体的なガバナンスと構成管理戦略の一環として、集中管理されたリポジトリを使用することをお勧めします。 複数のリポジトリを使用すると、時間の経過と共に管理されていないソフトウェアのサイロになる可能性があります。 中央リポジトリでは、複数のチームがプロジェクト用にこのリポジトリから成果物を使用できます。 それにより、標準化とセキュリティが強制され、管理しやすく、作業の重複がなくなります。 一元化の一環として、長期的な管理と持続性のために以下の操作が推奨されます。

- Azure Repos と、Azure サブスクリプションで認証と承認に使用されているものと同じ Azure Active Directory テナントを関連付けます。
- 一元管理する `All DevTest Labs Developers` という名前のグループを Azure Active Directory に作成します。 成果物の開発に関与するすべての開発者を、このグループに入れる必要があります。
- 同じ Azure Active Directory グループを使用して、Azure Repos リポジトリおよびラボへのアクセスを提供できます。
- Azure Repos でブランチまたはフォークを使用して、開発中のリポジトリとプライマリ運用リポジトリを分離する必要があります。 コンテンツは、適切なコード レビュー後に pull request でメイン ブランチのみに追加されます。 コード レビューで変更が承認されたら、メイン ブランチのメンテナンスを担当する開発リーダーが、更新されたコードをマージします。

## <a name="cicd-integration"></a>CI/CD の統合

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs は、CI/CD ツールチェーンと統合されますか。
Azure DevOps を使用している場合は、[DevTest Labs Tasks の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)を使用して、DevTest Labs でのご自分のリリース パイプラインを自動化できます。 この拡張機能を使用して実行できるタスクの一部を次に示します。

- VM を自動的に作成してデプロイします。 Azure ファイル コピーまたは PowerShell の Azure DevOps Services タスクを使用して、最新のビルドで VM を構成することもできます。
- テストの終了後、詳しい調査を目的として同じ VM 上でバグを再現するために、VM の状態を自動的にキャプチャします。
- VM が不要になったら、リリース パイプラインの最後で VM を削除します。

次のブログ記事では、Azure DevOps Services 拡張機能の使用方法に関するガイダンスと情報を提供しています。

- [DevTest Labs と Azure DevOps の拡張機能](integrate-environments-devops-pipeline.md)
- [Azure DevOps Services から既存の DevTest Labs ラボに新しい VM をデプロイする](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Azure DevOps Services のリリース管理を使用した DevTest Labs への継続的なデプロイ](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

他の継続的インテグレーション (CI)/継続的デリバリー (CD) ツールチェーンの場合、[Azure PowerShell コマンドレット](../azure-resource-manager/templates/deploy-powershell.md)と [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) を使用して [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/)をデプロイすることによって、同じシナリオを実現できます。 [DevTest Labs 用 REST API](https://aka.ms/dtlrestapis) を使用して、お使いのツールチェーンと統合することもできます。

## <a name="networking"></a>ネットワーク

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>どのような場合に DevTest ラボ環境用の新しい仮想ネットワークを作成する必要があり、どのような場合に既存の仮想ネットワークを使用できますか。
お使いの VM が既存のインフラストラクチャと通信する必要がある場合は、お使いの DevTest Labs 環境内の既存の仮想ネットワークを使用することを検討してください。 ExpressRoute を使用する場合は、サブスクリプションで使用するために割り当てられた IP アドレス空間をフラグメント化しないように、仮想ネットワークおよびサブネットの数を最小限に抑えることをお勧めします。

また、こちら ([ハブ - スポーク モデル](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) では、仮想ネットワーク ピアリング パターンの使用も検討してください。 このアプローチでは、サブスクリプション間で vnet およびサブネットでの通信が可能になります。 それ以外の場合は、各 DevTest Labs 環境で専用の仮想ネットワークを使用できます。

サブスクリプションあたりの仮想ネットワークの数には[制限](../azure-resource-manager/management/azure-subscription-service-limits.md)があります。 既定の数は 50 ですが、この制限は 100 まで増やすことができます。

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>共有 IP アドレス、パブリック IP アドレス、プライベート IP アドレスはどのように使い分ける必要がありますか。

サイト間 VPN または ExpressRoute を使用する場合は、マシンが内部ネットワーク経由ではアクセスできてもパブリック インターネット経由ではアクセスできないように、プライベート IP アドレスの使用を検討します。

> [!NOTE]
> ラボの所有者は、このサブネット ポリシーを変更して、ユーザーが各自の VM に誤ってパブリック IP アドレスを作成できないようにすることができます。 サブスクリプションの所有者は、パブリック IP アドレスが作成されるのを防ぐサブスクリプション ポリシーを作成する必要があります。

共有パブリック IP アドレスを使用すると、ラボ内の仮想マシンはパブリック IP アドレスを共有します。 この方法は、特定のサブスクリプションに対するパブリック IP アドレスの制限が違反されるのを防ぐのに役立ちます。

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>開発用とテスト用の仮想マシンがパブリック インターネットに接続できないようにするにはどうすればよいですか。 ネットワーク構成の設定に推奨されるパターンはありますか。

はい。 受信トラフィックと送信トラフィックの 2 つの側面を考慮する必要があります。

- **受信トラフィック**: 仮想マシンがパブリック IP アドレスを持っていない場合、その仮想マシンにはインターネットから到達できません。 ユーザーがパブリック IP アドレスを作成できないように、サブスクリプション レベルのポリシーを設定するのが、一般的なアプローチです。
- **送信トラフィック**: 仮想マシンがパブリック インターネットに直接アクセスできないようにし、企業のファイアウォールをトラフィックが通過するよう強制するには、強制ルーティングを使用して、オンプレミスのトラフィックを ExpressRoute または VPN 経由でルーティングします。

> [!NOTE]
> プロキシ設定のないトラフィックをブロックするプロキシ サーバーがある場合は、ラボの成果物ストレージ アカウントに対する例外を追加することを忘れないでください。

仮想マシンまたはサブネットに対してネットワーク セキュリティ グループを使用することもできます。 このステップにより、トラフィックを許可/ブロックする保護レイヤーが追加されます。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>既存の仮想ネットワークが正しく保存されないのはなぜですか。
仮想ネットワーク名にピリオドが含まれていることが原因となっている可能性があります。 その場合は、ピリオドを削除するか、ハイフンに置き換えてみます。 その後、仮想ネットワークをもう一度保存してみてください。

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>PowerShell から VM をプロビジョニングしたときに、"親リソースが見つからない" ことを示すエラーが発生するのはなぜですか。
リソースが別のリソースの親である場合、子リソースを作成するには、親リソースが存在している必要があります。 親リソースが存在しない場合、**ParentResourceNotFound** メッセージが表示されます。 親リソースに依存関係を指定していない場合、子リソースは親の前にデプロイされる可能性があります。

VM は、リソース グループ内のラボの下の子リソースです。 PowerShell で Resource Manager テンプレートを使用して VM をデプロイした場合、PowerShell スクリプトで指定されたリソース グループ名がラボのリソース グループ名になります。 詳細については、[Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)に関する記事をご覧ください。

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>VM のデプロイが失敗した場合、さらに詳しいエラー情報はどこで確認できますか。
VM のデプロイ エラーは、アクティビティ ログに記録されます。 ラボの VM のアクティビティ ログは、ラボの VM ページにあるリソース メニューの **[監査ログ]** または **[仮想マシンの診断]** で確認できます (VM ページは、[自分の仮想マシン] の一覧から VM を選択すると表示されます)。

VM のデプロイが開始される前に、デプロイ エラーが発生する場合もあります。 たとえば、VM で作成されたリソースのサブスクリプションの制限を超えた場合です。 この場合、ラボ レベルのアクティビティ ログにエラーの詳細が記録されます。 アクティビティ ログは、 **[Configuration and policies]\(構成とポリシー\)** 設定の下部にあります。 Azure でのアクティビティ ログ使用の詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)」を参照してください。

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>ラボを作成しようとすると、"location is not available for resource type" (リソースの種類に使用できる場所がありません) というエラーが表示されるのはなぜですか。
ラボを作成しようとすると、次のようなエラー メッセージが表示される場合があります。

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

このエラーを解決するには、次のいずれかの手順を実行します。

#### <a name="option-1"></a>方法 1
「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで、Azure リージョンでのリソースの種類の可用性を確認します。 リソースの種類が特定のリージョンで使用不可の場合、DevTest Labs では、そのリージョンでのラボの作成をサポートしていません。 ラボを作成するときに別のリージョンを選択してください。

#### <a name="option-2"></a>方法 2
リソースの種類がご利用のリージョンで使用可能な場合は、お使いのサブスクリプションに登録されているかどうかを確認します。 [この記事](../azure-resource-manager/management/resource-providers-and-types.md)で説明されているように、これはサブスクリプション所有者のレベルで実行できます。