---
title: Azure Security Center 内のセキュリティ スコア
description: Azure Security Center のセキュリティ スコアとそのセキュリティ コントロールの説明
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 9594e1ed14b017591ea2c4ddda59ba61feb81b0c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272282"
---
# <a name="enhanced-secure-score-in-azure-security-center"></a>Azure Security Center の強化されたセキュリティ スコア

## <a name="introduction-to-secure-score"></a>セキュリティ スコアの概要

Azure Security Center には、現在のセキュリティ状況を把握すること、およびセキュリティを効率的かつ効果的に向上させるという 2 つの主な目標があります。 これらの目標を達成できるようにする Security Center の中心となる要素が、セキュリティ スコアです。

Security Center は、セキュリティの問題について、リソース、サブスクリプション、および組織を継続的に評価します。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。

Security Center の [セキュリティ スコア] ページには次のものが含まれます。

- **スコア** - セキュリティ スコアはパーセント値として表示されますが、基になる値も明確です。

    [![パーセント値として表示される、基になる数値も明確なセキュリティ スコア](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **セキュリティ コントロール** - 各コントロールは、関連するセキュリティの推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 コントロールは、セキュリティに関する一連の推奨事項と、これらの推奨事項を実装するための指示内容です。 コントロール内の 1 つのリソースに関する推奨事項を "*すべて*" 修復した場合にのみ、スコアが向上します。

    組織が個々の攻撃対象領域をどの程度セキュリティで保護しているかをすぐに確認するには、各セキュリティ コントロールのスコアを確認します。

    詳細については、「[セキュリティ スコアの計算方法](secure-score-security-controls.md#how-your-secure-score-is-calculated)」を参照してください。 


>[!TIP]
> 以前のバージョンの Security Center は、推奨事項レベルでポイントを獲得しました。1 つのリソースの推奨事項を修復すると、セキュリティ スコアが向上しました。 現在は、コントロール内の 1 つのリソースに関する推奨事項を "*すべて*" 修復した場合にのみ、スコアが向上します。 そのため、リソースのセキュリティが向上した場合にのみスコアが向上します。


## <a name="accessing-your-secure-score"></a>セキュリティ スコアへのアクセス

Azure portal または Azure Security Center REST API を使用したプログラムによって、全体的なセキュリティ スコアとサブスクリプションごとのスコアを確認できます。

### <a name="getting-your-secure-score-from-the-portal"></a>ポータルからセキュリティ スコアを取得する

ポータルで、Security Center にはスコアが目立つように表示されます。スコアは、[概要] ページの最初に表示されます。 専用のセキュリティ スコア ページをクリックすると、スコアがサブスクリプション別に分類されていることがわかります。 サブスクリプションを 1 つクリックすると、優先度が設定された推奨事項と、修復によるスコアへの影響を示す詳細な一覧が表示されます。

![ポータルに表示された全体的なセキュリティ スコア](media/secure-score-security-controls/single-secure-score-via-ui.png)

### <a name="getting-your-secure-score-from-the-rest-api"></a>REST API からセキュリティ スコアを取得する

スコアには、[Secure Scores API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (現在プレビュー段階) を使用してアクセスできます。 この API メソッドを使用すると、データに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築したりするための柔軟性が得られます。 たとえば、**Secure Scores** API を使用して、特定のサブスクリプションのスコアを取得できます。 また、**Secure Score Controls** API を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

![API を使用して 1 つのセキュリティ スコアを取得する](media/secure-score-security-controls/single-secure-score-via-api.png)

Secure Scores API を使用して構築されたツールの例については、[GitHub コミュニティのセキュリティ スコアの領域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)を参照してください。 

## <a name="how-your-secure-score-is-calculated"></a>セキュリティ スコアの計算方法 

総合的なセキュリティ スコアに対する各セキュリティ コントロールの影響は、[推奨事項] ページで明確に示されています。

[![強化されたセキュリティ スコアによるセキュリティ コントロールの導入](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

セキュリティ コントロールで取得可能なポイントをすべて取得するには、すべてのリソースがセキュリティ コントロール内のすべてのセキュリティの推奨事項に準拠している必要があります。 たとえば、Security Center には、管理ポートをセキュリティで保護する方法に関する推奨事項が複数あります。 以前は、これらの相互に依存する推奨事項の一部を修復して一部を未解決のまま残していても、セキュリティ スコアを上げることができました。 客観的に見れば、すべてを解決するまでセキュリティが改善されないことは明白です。 今後は、セキュリティ スコアを改善するには、これらをすべて修復する必要があります。

たとえば、"システム更新プログラムの適用" というセキュリティ コントロールのスコアは最大 6 ポイントです。これは、コントロールの潜在的な増加値に関するヒントに表示されます。

[![セキュリティ コントロール "システム更新プログラムの適用"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

このコントロール (システム更新プログラムの適用) の最大スコアは、常に 6 です。 この例では、50 のリソースがあります。 このため、最大スコアを 50 で割ると、各リソースが 0.12 ポイントずつ貢献しているという結果になります。 

* **上昇の可能性** (0.12 x 8 (正常な状態ではないリソース数) = 0.96) - コントロール内で取得できる残りのポイント数。 このコントロールですべての推奨事項を修復した場合、スコアは 2% 上昇します (この場合、0.96 ポイントは 1 に切り上げられます)。 
* **現在のスコア** (0.12 x 42 (正常な状態のリソース数) = 5.04) - このコントロールの現在のスコア。 合計スコアは、各コントロールを基にしています。 この例のコントロールは、現在の合計セキュリティ スコアに 5.04 ポイント貢献しています。
* **最大スコア** - コントロール内のすべての推奨事項を完了することによって得られるポイントの最大数です。 コントロールの最大スコアは、そのコントロールの相対的な有意性を示します。 最大スコア値を使用して問題をトリアージし、最初に対処すべき問題を決定します。 


### <a name="calculations---understanding-your-score"></a>計算 - スコアを理解する

|メトリック|式と例|
|-|-|
|**セキュリティ コントロールの現在のスコア**|<br>![セキュリティ コントロールの現在のスコアを計算するための式](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>セキュリティ スコアは、個々のセキュリティ コントロールを基にしています。 コントロールの現在のスコアは、コントロール内の推奨事項によって影響を受ける各リソースを基にしています。 各コントロールの現在のスコアは、コントロール*内*でリソースの状態を測定したものです。<br>![セキュリティ コントロールの現在のスコアを計算するときに使用される値を示すヒント](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>この例では、最大スコア 6 は、正常なリソースと異常なリソースの合計値である 78 で割ることになります。<br>6 / 78 = 0.0769<br>これを正常なリソースの数 (4) で乗算すると、次に示す現在のスコアになります。<br>0.0769 * 4 = **0.31**<br><br>|
|**セキュリティ スコア**<br>1 つのサブスクリプション|<br>![現在のセキュリティ スコアを計算するための式](media/secure-score-security-controls/secure-score-equation.png)<br><br>![すべてのコントロールを有効にした単一サブスクリプションのセキュア スコア](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>この例では、すべてのセキュリティ コントロールを適用可能な 1 つのサブスクリプションがあります (潜在的な最大スコアは 60 ポイントです)。 スコアには取得可能な 60 ポイントのうち 28 ポイントが示され、残りの 32 ポイントは、セキュリティ コントロールの "潜在的な増加スコア" の数値に反映されます。<br>![コントロールの一覧と潜在的な増加スコア](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**セキュリティ スコア**<br>複数のサブスクリプション|<br>すべてのサブスクリプションのすべてのリソースの現在のスコアが加算され、1 つのサブスクリプションの場合と同じ方法で計算されます<br><br>複数のサブスクリプションを表示する場合、セキュリティ スコアは、すべての有効なポリシー内のすべてのリソースを評価し、各セキュリティ コントロールの最大スコアに対するその組み合わせの影響をグループ化します。<br>![すべてのコントロールが有効な複数のサブスクリプションのセキュリティ スコア](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>結合されたスコアは平均では**ありません**。これは、すべてのサブスクリプションのすべてのリソースの状態の体制を評価したものです。<br>ここでも、[推奨事項] ページにアクセスして、取得可能なポイントを追加すると、現在のスコア (24) と利用可能な最大スコア (60) の差を確認できます。|
||||

## <a name="improving-your-secure-score"></a>セキュリティ スコアを向上させる

セキュリティ スコアを向上させるには、推奨事項リストのセキュリティの推奨事項を修復してください。 各推奨事項は、リソースごとに手動で修復するか、リソースのグループに推奨設定の修復を迅速に適用するために **[クイック修正]** オプション (使用可能な場合) を使用して修復できます。 詳細については、「[推奨事項の修復](security-center-remediate-recommendations.md)」を参照してください。

>[!IMPORTANT]
> セキュリティ スコアに影響するのは、組み込みの推奨事項のみです。


## <a name="security-controls-and-their-recommendations"></a>セキュリティ コントロールとその推奨事項

次の表に、Azure Security Center のセキュリティ コントロールを示します。 コントロールごとに、"*すべて*" のリソースにおいて、コントロールに示されている "*すべて*" の推奨事項を修復した場合に取得できるセキュリティ スコアのポイントの最大数が表示されます。 

<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>セキュリティ コントロール、スコア、説明</b><br></th>
    <th class="tg-cly1"><b>Recommendations (推奨事項)</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">MFA の有効化 (最大スコア 10)</p></strong>ユーザーの認証にパスワードのみを使用する場合、攻撃ベクトルの可能性が残っています。 パスワード強度が弱いか、パスワードがどこかで流出していた場合、そのユーザー名とパスワードでサインインしようとしている人物がユーザー本人であるかどうかはわかりません。<br><a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> を有効にすると、アカウントのセキュリティが強化され、しかもユーザーはそれまでと同様、シングル サインオン (SSO) でほとんどすべてのアプリケーションに対して認証を行うことができます。</td>
    <td class="tg-lboi"; width=55%>-サブスクリプションに対する所有者アクセス許可を持つアカウントに対して、MFA を有効にする必要があります<br>-サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して、MFA を有効にする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">管理ポートのセキュリティ保護 (最大スコア 8)</p></strong>ブルート フォース攻撃は、管理ポートを標的にして、VM へのアクセスを取得します。 ポートは常に開放している必要はないため、1 つの軽減策は、Just-In-Time ネットワーク アクセス制御、ネットワーク セキュリティ グループ、仮想マシン ポート管理を使用してポートへの露出を削減することです。<br>多くの IT 組織は、ネットワークから送られてくる SSH 通信をブロックしないため、攻撃者は、感染したシステム上の RDP ポートが攻撃者のコマンドに返されてサーバーを制御できるようにする暗号化トンネルを作成することができます。 攻撃者は、Windows リモート管理サブシステムを使用して、お使いの環境全体を横断し、盗んだ資格情報を使用して、ネットワーク上の他のリソースにアクセスすることができます。</td>
    <td class="tg-lboi"; width=55%>- 仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります<br>- 仮想マシンをネットワーク セキュリティ グループに関連付ける必要があります<br>- お使いの仮想マシンの管理ポートを閉じておく必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">システム更新プログラムの適用 (最大スコア 6)</p></strong>システムの更新プログラムにより、組織は、運用効率を維持し、セキュリティ脆弱性を軽減し、エンド ユーザーにより安定した環境を提供することができます。 更新プログラムを適用しないと、修正プログラムが脆弱性に適用されないままになるため、環境は攻撃を受けやすくなります。 このような脆弱性は悪用され、データ損失、データ流出、ランサムウェア、リソースの不正使用を引き起こす可能性があります。 システムの更新プログラムを展開するには、<a href="https://docs.microsoft.com/azure/automation/automation-update-management">Update Management ソリューション</a>を使用して仮想マシンの修正プログラムと更新プログラムを管理します。 更新プログラムの管理は、ソフトウェア リリースの展開と保守を制御するプロセスです。</td>
    <td class="tg-lboi"; width=55%>- お使いのマシンで監視エージェントの正常性の問題を解決する必要があります<br>- Virtual Machine Scale Sets に監視エージェントをインストールする必要があります<br>- お使いのマシンに監視エージェントをインストールする必要があります<br>- クラウド サービス ロールの OS バージョンを更新する必要があります<br>- Virtual Machine Scale Sets にシステムの更新プログラムをインストールする必要があります<br>- お使いのマシンにシステムの更新プログラムをインストールする必要があります<br>- システムの更新プログラムを適用するには、マシンを再起動する必要があります<br>- Kubernetes サービスを脆弱性のない Kubernetes バージョンにアップグレードする必要があります<br>- お使いの仮想マシンに監視エージェントをインストールする必要があります<br>- Log Analytics エージェントを Windows ベースの Azure Arc マシン (プレビュー) にインストールする必要があります<br>- Log Analytics エージェントを Linux ベースの Azure Arc マシン (プレビュー) にインストールする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">脆弱性の修復 (最大スコア 6)</p></strong>脆弱性は、脅威アクターが、リソースの機密性、可用性、または整合性を侵害するために利用する可能性のある弱点です。 <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">脆弱性の管理</a>により、組織の露出の削減、エンドポイントの攻撃対象領域の強化、組織の回復力の向上、リソースの攻撃対象領域の縮小を実現できます。 脅威と脆弱性の管理機能は、ソフトウェアとセキュリティの構成の誤りを可視化し、軽減のための推奨事項を提示します。</td>
    <td class="tg-lboi"; width=55%>- SQL Database で Advanced Data Security を有効にする必要があります<br>- Azure Container Registry イメージの脆弱性を修復する必要があります<br>- SQL データベースの脆弱性を修復する必要があります<br>- 脆弱性評価ソリューションによって脆弱性を修復する必要があります<br>- SQL Managed Instance で脆弱性評価を有効にする必要があります<br>- SQL サーバーで脆弱性評価を有効にする必要があります<br>- お使いの仮想マシンに脆弱性評価ソリューションをインストールする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">保存時の暗号化を有効化する (最大スコア 4)</p></strong><a href="https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest">保存時の暗号化</a>は、格納されているデータのデータ保護を提供します。 保存データに対する攻撃として、データが格納されているハードウェアへの物理的なアクセスを取得しようとする試みがあります。 Azure では、対称暗号化を使用して、大量の保存データの暗号化と暗号化解除を行います。 データがストレージに書き込まれるときに、対称暗号化キーを使用してデータが暗号化されます。 その暗号化キーは、メモリで、データを使用する準備として暗号化の解除を行うためにも使用されます。 キーは、ID ベースのアクセス制御と監査ポリシーが適用される、セキュリティで保護された場所に保存する必要があります。 このようなセキュリティで保護された場所の 1 つが、Azure Key Vault です。 攻撃者は、暗号化されたデータを取得しても、暗号化キーを取得しなければ、暗号を解読できず、データにアクセスすることはできません。</td>
    <td class="tg-lboi"; width=55%>- 仮想マシンでディスク暗号化を適用する必要があります<br>- SQL Database で Transparent Data Encryption を有効にする必要があります<br>- Automation アカウント変数を暗号化する必要があります<br>- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要があります<br>- 自分のキーを使用して、SQL Server の TDE 保護機能を暗号化する必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">転送中のデータの暗号化 (最大スコア 4)</p></strong>データは、コンポーネント、場所、またはプログラムの間で転送される場合、"転送中" になります。 転送中のデータを保護しない組織は、中間者攻撃、傍受、セッション ハイジャックの影響を受けやすくなります。 データの交換には、SSL/TLS プロトコルを使用する必要があり、VPN を使用することをおすすめします。 インターネットを介して Azure 仮想マシンとオンプレミスの場所の間で暗号化されたデータを送信する場合、<a href="https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN Gateway</a> などの仮想ネットワーク ゲートウェイを使用して、暗号化されたトラフィックを送信できます。</td>
    <td class="tg-lboi"; width=55%>- API アプリには、HTTPS を介してのみアクセスできるようにする必要があります<br>- Function App には、HTTPS を介してのみアクセスできるようにする必要があります<br>- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要があります<br>- ストレージ アカウントへの安全な転送を有効にする必要があります<br>- Web アプリケーションには、HTTPS を介してのみアクセスできるようにする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">アクセスおよびアクセス許可の管理 (最大スコア 4)</p></strong>セキュリティ プログラムの中核となるのは、ユーザーがそのジョブを実行するために必要なアクセスを付与しても、それ以上の権利を付与しないことです。つまり、<a href="https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">最小限の特権モデル</a>に従う必要があります。<br>リソースへのアクセスを制御するには、<a href="https://docs.microsoft.com/azure/role-based-access-control/overview">ロールベースのアクセス制御 (RBAC)</a> を使用してロールの割り当てを作成します。 ロールの割り当ては、次の 3 つの要素で構成されます。<br>- <strong>セキュリティ プリンシパル</strong>: ユーザーがアクセスを要求するオブジェクト<br>- <strong>ロールの定義</strong>: ユーザーのアクセス許可<br>- <strong>スコープ</strong>: アクセス許可が適用される一連のリソース</td>
    <td class="tg-lboi"; width=55%>- 非推奨のアカウントは、お使いのサブスクリプションから削除する必要があります (プレビュー)<br>- 所有者アクセス許可がある非推奨のアカウントは、お使いのサブスクリプションから削除する必要があります (プレビュー)<br>- 所有者アクセス許可がある外部アカウントは、お使いのサブスクリプションから削除する必要があります (プレビュー)<br>- 書き込みアクセス許可がある外部アカウントは、お使いのサブスクリプションから削除する必要があります (プレビュー)<br>- 複数の所有者がサブスクリプションに割り当てられている必要があります<br>- Kubernetes サービスではロールベースのアクセス制御 (RBAC) を使用する必要があります (プレビュー)<br>- Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要があります<br>-サブスクリプションを保護するには、管理証明書ではなくサービス プリンシパルを使用する必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">セキュリティ構成の修復 (最大スコア 4)</p></strong>正しく構成されていない IT 資産は、攻撃の対象となる危険性が高くなります。 資産を展開するときに基本的な強化アクションを忘れてしまいがちですが、期限を満たす必要があります。 インフラストラクチャでのセキュリティ構成の誤りは、オペレーティング システムやネットワーク アプライアンスからクラウド リソースまでどのレベルでも起こり得ることです。<br>Azure Security Center では、リソースの構成が業界標準、規制、ベンチマークの要件と継続的に比較されます。 組織にとって重要な関連する "コンプライアンス パッケージ" (標準とベースライン) を構成した場合、何らかのギャップがあると、セキュリティの推奨事項が表示されます。これには、CCEID と、セキュリティへの潜在的な影響に関する説明が含まれます。<br>一般的に使用されるパッケージは、<a href="https://docs.microsoft.com/azure/security/benchmarks/introduction">Azure セキュリティ ベンチマーク</a>と <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations Benchmark バージョン 1.1.0</a> です</td>
    <td class="tg-lboi"; width=55%>- Kubernetes サービスではポッドのセキュリティ ポリシーを定義する必要があります<br>- コンテナーのセキュリティ構成の脆弱性を修復する必要があります<br>- お使いのマシンでセキュリティ構成の脆弱性を修復する必要があります<br>- Virtual Machine Scale Sets のセキュリティ構成の脆弱性を修復する必要があります<br>- お使いの仮想マシンに監視エージェントをインストールする必要があります<br>- お使いのマシンに監視エージェントをインストールする必要があります<br>- Log Analytics エージェントを Windows ベースの Azure Arc マシン (プレビュー) にインストールする必要があります<br>- Log Analytics エージェントを Linux ベースの Azure Arc マシン (プレビュー) にインストールする必要があります<br>- Virtual Machine Scale Sets に監視エージェントをインストールする必要があります<br>- お使いのマシンで監視エージェントの正常性の問題を解決する必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">未承認のネットワーク アクセスの制限 (最大スコア 4)</p></strong>組織内のエンドポイントは、仮想ネットワークからサポートされている Azure サービスへの直接接続を提供します。 サブネット内の仮想マシンはすべてのリソースと通信できます。 サブネット内のリソース間の通信を制御するには、ネットワーク セキュリティ グループを作成して、それをサブネットに関連付けます。 組織は、インバウンド規則とアウトバウンド規則を作成することにより、不正なトラフィックを制限および防止することができます。</td>
    <td class="tg-lboi"; width=55%>-お使いの仮想マシンでの IP 転送を無効にする必要があります<br>-Kubernetes サービスでは承認された IP 範囲を定義する必要があります (プレビュー)<br>- (非推奨) App Services へのアクセスを制限する必要があります (プレビュー)<br>- (非推奨) IaaS NSG 上の Web アプリケーションに対する規則を強化する必要があります<br>- 仮想マシンをネットワーク セキュリティ グループに関連付ける必要があります<br>- CORS で、必ずしもすべてのリソースに API アプリへのアクセスを許可しないようにする必要があります<br>- CORS で、必ずしもすべてのリソースに Function App へのアクセスを許可しないようにする必要があります<br>- CORS で、必ずしもすべてのリソースに Web アプリへのアクセスを許可しないようにする必要があります<br>-API アプリのリモート デバッグを無効にする必要があります<br>- Function App のリモート デバッグを無効にする必要があります<br>- Web アプリのリモート デバッグを無効にする必要があります<br>- インターネットに接続された VM を含む、制限のないネットワーク セキュリティ グループについてはアクセスを制限する必要があります<br>- インターネットに接続している仮想マシン用のネットワーク セキュリティ グループ ルールを強化する必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">適応型アプリケーション制御の適用 (最大スコア 3)</p></strong>適応型アプリケーション制御 (AAC) は、自動化されたインテリジェントなエンドツーエンドのソリューションであり、これによって、Azure マシンと Azure 以外のマシンで実行可能なアプリケーションを制御できます。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。<br>Security Center では、機械学習を利用して、マシン グループの既知の安全なアプリケーションのリストを作成します。<br>承認されたアプリケーションのリストを作成するというこの革新的なアプローチにより、管理の複雑さを生じることなくセキュリティを強化することができます。<br>AAC は、特定のアプリケーション セットを実行する必要がある専用サーバーに特に関係があります。</td>
    <td class="tg-lboi"; width=55%>- 仮想マシンで適応型アプリケーション制御を有効にする必要があります<br>- お使いの仮想マシンに監視エージェントをインストールする必要があります<br>- お使いのマシンに監視エージェントをインストールする必要があります<br>- Log Analytics エージェントを Windows ベースの Azure Arc マシン (プレビュー) にインストールする必要があります<br>- Log Analytics エージェントを Linux ベースの Azure Arc マシン (プレビュー) にインストールする必要があります<br>- お使いのマシンで監視エージェントの正常性の問題を解決する必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">データ分類の適用 (最大スコア 2)</p></strong>組織のデータを機密性とビジネスへの影響によって分類すると、データの価値を決定して割り当てることができ、ガバナンスのための戦略と基準を策定できます。<br><a href="https://docs.microsoft.com/azure/information-protection/what-is-information-protection">Azure Information Protection</a> は、データの分類に役立ちます。 これは、暗号化、ID、認証ポリシーを使用して、データを保護し、データへのアクセスを制限します。 Microsoft が使用する分類の一部としては、ビジネス以外、パブリック、一般、社外秘、極秘などがあります。</td>
    <td class="tg-lboi"; width=55%>- SQL データベースの機密データを分類する必要があります (プレビュー)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">DDoS 攻撃からアプリケーションを保護する (最大スコア 2)</p></strong>分散型サービス拒否 (DDoS) 攻撃は、リソースに過剰な負荷をかけ、アプリケーションを使用できない状態にします。 <a href="https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection Standard</a> を使用すると、次の 3 つの種類の DDoS 攻撃から組織を守ることができます。<br>- <strong>帯域幅消費型攻撃</strong>: ネットワークに大量の正当なトラフィックを送り込みます。 DDoS Protection Standard は、これらの攻撃を自動的に吸収またはスクラブして、これらを軽減します。<br>- <strong>プロトコル攻撃</strong>: レイヤー 3 とレイヤー 4 のプロトコル スタック内の弱点を悪用して、ターゲットをアクセスできない状態にします。 DDoS Protection Standard は、悪意のあるトラフィックをブロックして、これらを軽減します。<br>- <strong>リソース (アプリケーション) 層攻撃</strong>: Web アプリケーション パケットを標的にします。 この種類に対しては、Web アプリケーション ファイアウォールと DDoS Protection Standard で防御します。</td>
    <td class="tg-lboi"; width=55%>- DDoS Protection Standard を有効にする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Endpoint Protection を有効にする (最大スコア 2)</p></strong>エンドポイントをマルウェアから確実に保護するために、動作センサーは、エンドポイントのオペレーティング システムから送られてくるデータを収集し、処理して、そのデータを分析のためにプライベート クラウドに送信します。 セキュリティ分析では、ビッグデータ、機械学習、その他のソースを活用して、脅威に対する推奨される対応を提示します。 たとえば、<a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> は、脅威インテリジェンスを使用して、攻撃方法を特定し、セキュリティ アラートを生成します。<br>Security Center では、次の Endpoint Protection ソリューションがサポートされています: Windows Defender、System Center Endpoint Protection、Trend Micro、Symantec v12.1.1.1100、McAfee v10 for Windows、McAfee v10 for Linux、Sophos v9 for Linux。 Security Center でこれらのソリューションのいずれかが検出されると、Endpoint Protection のインストールを促す推奨事項は表示されなくなります。</td>
    <td class="tg-lboi"; width=55%>- Virtual Machine Scale Sets で Endpoint Protection の正常性エラーを修復する必要があります<br>- - お使いのマシンで Endpoint Protection の正常性の問題を解決する必要があります<br>- Virtual Machine Scale Sets に Endpoint Protection ソリューションをインストールする必要があります<br>- 仮想マシンに Endpoint Protection ソリューションをインストールします<br>- お使いのマシンで監視エージェントの正常性の問題を解決する必要があります<br>- Virtual Machine Scale Sets に監視エージェントをインストールする必要があります<br>- お使いのマシンに監視エージェントをインストールする必要があります<br>- お使いの仮想マシンに監視エージェントをインストールする必要があります<br>- Log Analytics エージェントを Windows ベースの Azure Arc マシン (プレビュー) にインストールする必要があります<br>- Log Analytics エージェントを Linux ベースの Azure Arc マシン (プレビュー) にインストールする必要があります<br>- お使いのマシンに Endpoint Protection ソリューションをインストールします</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">監査とログ記録を有効にする (最大スコア 1)</p></strong>データのログにより、過去の問題に関する分析情報が提供され、潜在的な問題を防止し、アプリケーションのパフォーマンスを向上させ、他の場合には手動で行うアクションを自動化できます。<br>- <strong>ログの制御と管理</strong>では、<a href="https://docs.microsoft.com/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> の動作に関する情報が提供されます。<br>- <strong>データ プレーン ログ</strong>は、Azure のリソース使用の一環として発生したイベントに関する情報を提供します。<br>- <strong>処理されたイベント</strong>では、処理されたイベントおよびアラートに関する分析情報が提供されます。</td>
    <td class="tg-lboi"; width=55%>- SQL Server の監査を有効にする必要があります<br>- App Services の診断ログを有効にする必要があります<br>- Azure Data Lake Store の診断ログを有効にする必要があります<br>- Azure Stream Analytics の診断ログを有効にする必要があります<br>- Batch アカウントの診断ログを有効にする必要があります<br>- Data Lake Analytics の診断ログを有効にする必要があります<br>- イベント ハブの診断ログを有効にする必要があります<br>- IoT Hub の診断ログを有効にする必要があります<br>- Key Vault の診断ログを有効にする必要があります<br>- Logic Apps の診断ログを有効にする必要があります<br>- Search Service の診断ログを有効にする必要があります<br>- Service Bus の診断ログを有効にする必要があります<br>- Virtual Machine Scale Sets の診断ログを有効にする必要があります<br>- Batch アカウントでメトリック アラート ルールを構成する必要があります<br>- - SQL 監査設定では、重要なアクティビティをキャプチャするように Action-Groups を構成しておく必要があります<br>- SQL Server では、監査保持期間を 90 日以上に構成する必要があります。</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Advanced Threat Protection を有効にする (最大スコア 0)</p></strong>Azure Security Center の脅威の防止によって、お使いの環境が包括的に保護されます。 Security Center は、ご自身の環境のいずれかの領域で脅威を検出すると、アラートを生成します。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。<br>脅威の防止バンドルは、それぞれが別個のオプション オファリングであり、このセキュリティ コントロールの関連する推奨事項を使用して有効にすることができます。<br><a href="https://docs.microsoft.com/azure/security-center/threat-protection">Security Center での脅威の防止についてさらに学習してください</a>。</td>
    <td class="tg-lboi"; width=55%>- Azure SQL Database サーバーで Advanced Data Security を有効にする必要があります<br>- マシン上の SQL サーバーで Advanced Data Security を有効にする必要があります<br>- Virtual Machines で Advanced Threat Protection を有効にする必要があります<br>- Azure App Service プランで Advanced Threat Protection を有効にする必要があります<br>- Azure Storage アカウントで Advanced Threat Protection を有効にする必要があります<br>- Azure Kubernetes Service クラスターで Advanced Threat Protection を有効にする必要があります<br>- Azure Container Registry レジストリで Advanced Threat Protection を有効にする必要があります<br>- Azure Key Vault コンテナーで Advanced Threat Protection を有効にする必要があります</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">セキュリティのベストプラクティスを実装する (最大スコア 0)</p></strong>最新のセキュリティ対策では、ネットワーク境界の "侵害を想定" しています。 そのため、このコントロールの多くのベスト プラクティスでは、ID の管理が重視されています。<br>キーや資格情報の紛失は、よくある問題です。 <a href="https://docs.microsoft.com/azure/key-vault/key-vault-overview">Azure Key Vault</a> では、キー、.pfx ファイル、パスワードを暗号化することにより、キーとシークレットを保護します。<br>仮想プライベート ネットワーク (VPN) は、仮想マシンへのアクセスをセキュリティで保護します。 VPN を利用できない場合は、複雑なパスフレーズと <a href="https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks">Azure Multi-Factor Authentication</a> などの 2 要素認証を使用します。 2 要素認証は、ユーザー名とパスワードのみに依存することに伴う弱点を回避します。<br>強力な認証および承認プラットフォームの使用は、もう 1 つのベスト プラクティスです。 フェデレーション ID を使用すると、組織は、承認された ID の管理を委任できます。 これは、従業員が退職し、そのアクセス権を取り消す必要がある場合にも重要です。</td>
    <td class="tg-lboi"; width=55%>- サブスクリプションには最大 3 人の所有者を指定する必要があります<br>- - 読み取りアクセス許可を持つ外部アカウントをお使いのサブスクリプションから除外する必要があります<br>- お使いのサブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要があります<br>- ファイアウォールと仮想ネットワークの構成があるストレージ アカウントへのアクセスを制限する必要があります<br>- RootManageSharedAccessKey を除くすべての承認規則をイベント ハブの名前空間から削除する必要があります<br>- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要があります<br>- マネージド インスタンスで Advanced Data Security を有効にする必要があります<br>- イベント ハブ インスタンスの承認規則を定義する必要があります<br>- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要があります<br>- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要があります<br>- サブネットはネットワーク セキュリティ グループに関連付けられている必要があります<br>- [プレビュー] Windows Exploit Guard を有効にする必要があります <br>- [プレビュー] ゲスト構成エージェントをインストールする必要があります<br>- インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要があります</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>セキュリティ スコアに関する FAQ

### <a name="why-has-my-secure-score-gone-down"></a>セキュリティ スコアが下がったのはなぜですか。
Security Center は、スコアの計算方法の変更を含む、強化されたセキュリティ スコアに切り替えられました。 現在、ポイントを受け取るにはリソースのすべての推奨事項を解決する必要があります。 さらに、スコアのスケールが 0-10 に変更されました。

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>セキュリティ コントロールの 4 つの推奨事項のうち 3 つだけに対処した場合、セキュリティ スコアは変わりますか。
いいえ。 1 つのリソースのすべての推奨事項を修復するまでスコアは変更されません。 コントロールの最大スコアを取得するには、すべてのリソースのすべての推奨事項を修復する必要があります。

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>以前のセキュリティ スコアのエクスペリエンスは引き続き利用できますか。 
いいえ。 移行を容易にするため、しばらくの間は併用されました。 以前のモデルは非推奨となりました。 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>推奨事項が自分に該当しないためポリシーで無効にした場合、セキュリティ コントロールは達成され、セキュリティ スコアが更新されますか。
はい。 お使いの環境該当しない推奨設定は、無効にすることをお勧めします。 特定の推奨事項を無効にする方法については、「[セキュリティ ポリシーの無効化](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)」を参照してください。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>特定のセキュリティ コントロールによるセキュリティ スコアのポイントが 0 の場合、無視すべきですか。
場合によっては、コントロールの最大スコアが 0 を超えているにもかかわらず、影響がないことがあります。 リソース修正によるスコアの増分がごくわずかである場合は、0 に丸められます。 これらの推薦事項は修正するとセキュリティが強化されるため、これらの推奨事項は無視しないでください。 唯一の例外は "追加のベスト プラクティス" コントロールです。 これらの推奨事項を修復してもスコアは増加しませんが、全体的なセキュリティが強化されます。

## <a name="next-steps"></a>次のステップ

この記事では、セキュリティ スコアと新しいセキュリティ コントロールについて説明しました。 関連資料については、次の記事をご覧ください。

- [推奨事項のさまざまな要素に関する詳細](security-center-recommendations.md)
- [推奨事項を修復する方法を確認する](security-center-remediate-recommendations.md)
