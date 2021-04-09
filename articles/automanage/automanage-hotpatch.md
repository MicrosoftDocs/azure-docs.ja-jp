---
title: Windows Server Azure Edition を対象にしたホットパッチ (プレビュー)
description: Windows Server Azure Edition を対象にしたホットパッチのしくみと有効化の方法について説明します
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 92b8bf240dfd73cc9191675db07f20816b7156a8
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953393"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>新しい仮想マシンのホットパッチ (プレビュー)

ホット パッチの適用は、新しい Windows Server Azure Edition の仮想マシン (VM) に更新プログラムをインストールするための新しい方法であり、インストール後に再起動を必要としません。 この記事では、Windows Server Azure Edition の VM を対象にしたホットパッチについて説明します。これの利点は次のとおりです。
* 再起動の回数が減り、ワークロードへの影響が少ない
* パッケージが小さくなり、より高速にインストールされること、Azure Update Manager でパッチ オーケストレーションが容易になることで、更新プログラムをより迅速に展開できる
* ホットパッチ更新プログラム パッケージには、再起動せずに高速にインストールされる Windows セキュリティ更新プログラムが含まれるため、保護が強化される

## <a name="how-hotpatch-works"></a>ホットパッチのしくみ

ホットパッチは、まず Windows Update の最新の累積的な更新プログラムが適用されたベースラインを確立することによって機能します。 ホットパッチは、そのベースラインに基づいて定期的 (たとえば、毎月第 2 火曜日) にリリースされます。 ホットパッチには、再起動を必要としない更新プログラムが含まれます。 ベースラインは、定期的 (最初は 3 か月ごと) に新たな最新の累積的な更新プログラムを適用して更新されます。

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="ホットパッチのサンプル スケジュール。":::

ベースラインには、**計画ベースライン** と **計画外ベースライン** の 2 種類があります。
*  **計画ベースライン** は定期的にリリースされ、その間にホットパッチのリリースがあります。  計画ベースラインは、それと同等の、その月の "_最新の累積的な更新プログラム_" の更新プログラムをすべて含み、再起動が必要です。
    * 上記のサンプル スケジュールには、年間 4 回の計画ベースライン リリース (図での合計は 5 回) と、8 回のホットパッチ リリースが示されています。
* **計画外ベースライン** は、重要な更新プログラム (ゼロデイ修正プログラムなど) がリリースされたときにリリースされます。その特定の更新プログラムをホットパッチとしてリリースすることはできません。  計画外ベースラインがリリースされるときは、その月のホットパッチ リリースが計画外ベースラインと置き換えられます。  計画外ベースラインも、それと同等の、その月の "_最新の累積的な更新プログラム_" の更新プログラムをすべて含み、やはり再起動が必要です。
    * 上記のサンプル スケジュールに示されている 2 つの計画外ベースラインによって、それらの月のホットパッチ リリースが置き換えられます (年間の計画外ベースラインの実際の数は、事前にはわかりません)。

## <a name="regional-availability"></a>リージョン別の提供状況
ホットパッチは、すべてのグローバルな Azure リージョンにおいてプレビューで利用できます。 Azure Government リージョンはプレビューでサポートされていません。

## <a name="how-to-get-started"></a>ファースト ステップ

> [!NOTE]
> プレビュー段階では、[このリンク](https://aka.ms/AzureAutomanageHotPatch)を使用して Azure portal でのみ開始できます。

新しい VM でホットパッチの使用を開始するには、次の手順に従います。
1.  プレビューへのアクセスを有効にする
    * サブスクリプションごとに 1 回、プレビュー アクセスの有効化が必要になります。
    * プレビュー アクセスは、次のセクションで説明するように、API、PowerShell、または CLI を使用して有効にすることができます。
1.  Azure portal から VM を作成する
    * プレビュー期間中は、[こちらのリンク](https://aka.ms/AzureAutomanageHotPatch)を使用して開始する必要があります。
1.  VM の詳細を指定する
    * イメージのドロップダウンで _Windows Server 2019 Datacenter: Azure Edition_ が確実に選択されているようにします
    * [管理] タブの手順で、[Guest OS updates]\(ゲスト OS の更新プログラム\) まで下にスクロールします。 ホット パッチの適用がオンに設定され、パッチのインストールが既定の Azure オーケストレーションによるパッチ適用に設定されていることがわかります。
    * VM ベスト プラクティスの自動管理が既定で有効になります
1. 新しい VM を作成する

## <a name="enabling-preview-access"></a>プレビュー アクセスの有効化

### <a name="rest-api"></a>REST API

次の例では、お使いのサブスクリプションでプレビューを有効にする方法について説明します。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

```Register-AzProviderFeature``` コマンドレットを使用してサブスクリプションでのプレビューを有効にします。

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

```az feature register``` を使用して、サブスクリプションでのプレビューを有効にします。

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>パッチのインストール

プレビュー期間中は、_Windows Server 2019 Datacenter: Azure Edition_ で作成されたすべての VM に対して [VM ゲストの自動パッチ適用](../virtual-machines/automatic-vm-guest-patching.md)が自動的に有効になります。 VM ゲストの自動パッチ適用を有効にすると、次のようになります。
* [重大] または [セキュリティ] に分類されているパッチは、自動的にダウンロードされ、VM に適用されます。
* パッチは、VM のタイムゾーンでピーク外の時間帯に適用されます。
* パッチ オーケストレーションが Azure によって管理され、[可用性優先の原則](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)に従ってパッチが適用されます。
* プラットフォーム正常性シグナルによって特定された仮想マシンの正常性は、パッチ適用の失敗を検出するために監視されます。

### <a name="how-does-automatic-vm-guest-patching-work"></a>VM ゲストの自動パッチ適用はどのように機能しますか?

VM に対して [VM ゲストの自動パッチ適用](../virtual-machines/automatic-vm-guest-patching.md)が有効になっている場合、使用可能になった [重大] および [セキュリティ] パッチが自動的にダウンロードされて適用されます。 このプロセスは、毎月新しいパッチがリリースされるたびに自動的に開始されます。 パッチの評価とインストールは自動的に実行され、このプロセスでは必要に応じて VM の再起動が行われます。

_Windows Server 2019 Datacenter: Azure Edition_ の VM でホットパッチが有効になっている場合、毎月のセキュリティ更新プログラムのほとんどは、再起動を必要としないホットパッチとして配信されます。 計画または計画外ベースラインの月に送信される最新の累積的な更新プログラムについては、VM の再起動が必要です。 また、 VM の再起動が必要な場合がある、追加の [重要] または [セキュリティ] パッチが定期的に利用可能になることもあります。

VM は、その VM に適用可能なパッチを決定するため、30 日間の期間内に複数回、数日ごとに自動的に評価されます。 この自動評価により、不足しているパッチができるだけ早く検出されるようになります。

パッチは、[可用性優先の原則](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)に従って、毎月のパッチ リリースから 30 日以内にインストールされます。 パッチは、VM のタイムゾーンに応じて、VM のピーク外の時間帯にのみインストールされます。 パッチが自動的にインストールされるようにするには、ピーク外の時間帯に VM が実行されている必要があります。 定期的な評価のときに VM の電源がオフになっていた場合、次回の定期的な評価中に VM の電源がオンになっていれば VM が評価され、適用するパッチが自動的にインストールされます。 通常、次回の定期的な評価は数日以内に行われます。

定義の更新プログラムや [重要] または [セキュリティ] として分類されていない他のパッチは、VM ゲストの自動パッチ適用によってインストールされません。

## <a name="understanding-the-patch-status-for-your-vm"></a>VM のパッチの状態について理解する

VM のパッチの状態を表示するには、Azure portal で VM の **[ゲスト + ホストの更新プログラム]** セクションに移動します。 **[Guest OS updates]\(ゲスト OS の更新プログラム\)** セクションで、[Go to Hotpatch (Preview)]\(ホットパッチ (プレビュー) にアクセスする\) をクリックすると、VM のパッチの最新状態が表示されます。

この画面には、VM のホットパッチの状態が表示されます。 また、VM にインストールされていない利用可能なパッチがあるかどうかを確認することもできます。 上記の「パッチのインストール」セクションで説明したように、すべての [セキュリティ] および [重要] 更新プログラムは、[VM ゲストの自動パッチ適用](../virtual-machines/automatic-vm-guest-patching.md)を使用して VM に自動的にインストールされます。追加の操作は必要ありません。 その他の更新プログラムに分類されるパッチは、自動的にインストールされません。 その代わりに、[更新プログラムのコンプライアンス] タブにある利用可能なパッチの一覧に表示されます。[更新履歴] で VM への更新プログラムの展開の履歴を表示することもできます。 過去 30 日間の更新履歴がパッチのインストール詳細と共に表示されます。


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="ホットパッチの管理。":::

VM ゲストの自動パッチ適用を使用すると、VM は利用可能な更新プログラムの有無について定期的かつ自動的に評価されます。 このような定期的な評価によって、利用可能なパッチの検出が確実に行われます。 上記の [更新プログラム] 画面で、前回の評価がいつだったかを含め、評価の結果を確認できます。 [今すぐ評価] オプションを使用して、いつでも VM のオンデマンドパッチ評価をトリガーし、評価完了後に結果を確認することもできます。

オンデマンドでの評価と同様に、[Install updates now]\(今すぐ更新プログラムをインストールする\) オプションを使用して VM 用のパッチをオンデマンドでインストールすることもできます。 ここでは、特定のパッチ分類のすべての更新プログラムをインストールすることを選択できます。 個々のサポート技術情報の記事の一覧を提供することによって、追加または除外する更新プログラムを指定することもできます。 オンデマンドでインストールするパッチは、可用性優先の原則を使用してインストールされず、更新プログラムのインストールのために、より多くの再起動が必要になることや、VM のダウンタイムが発生することがあります。

## <a name="supported-updates"></a>サポートされる更新プログラム

ホットパッチは Windows セキュリティ更新プログラムを対象としており、通常の (ホットパッチでない) Windows Update チャネルで公開されたセキュリティ更新プログラムの内容と同等です。

ホットパッチを有効にして Windows Server Azure Edition の VM を実行することに関して、いくつかの重要な考慮事項があります。 ホットパッチ プログラムに含まれていない更新プログラムをインストールするには、依然として再起動が必要です。 また、新しいベースラインがインストールされた後に、定期的に再起動が必要になります。 これらの再起動により、最新の累積的な更新プログラムに含まれているセキュリティ以外のパッチと VM の同期が維持されます。
* ホットパッチ プログラムに現在含まれていないパッチには、Windows 用にリリースされたセキュリティ以外の更新プログラムと、Windows 以外の更新プログラム (.NET のパッチなど) があります。  これらの種類のパッチは、ベースラインの月にインストールする必要があり、再起動が必要になります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-is-hotpatching"></a>ホット パッチの適用とは、どのようなものですか?

* ホット パッチの適用は、Azure で Windows Server 2019 Datacenter: Azure Edition の VM に更新プログラムをインストールするための新しい方法であり、インストール後に再起動を必要としません。 実行中のプロセスのメモリ内コードにパッチを適用することで機能し、プロセスを再起動する必要がありません。

### <a name="how-does-hotpatching-work"></a>ホット パッチの適用はどのように機能しますか?

* ホット パッチの適用は、Windows Update の最新の累積的な更新プログラムが適用されたベースラインを確立することで機能し、そのベースラインを基にして、有効化するために再起動を必要としない更新プログラムを適用します。  ベースラインは、新しい累積的な更新プログラムを適用して定期的に更新されます。 累積的な更新プログラムには、セキュリティと品質のすべての更新プログラムが含まれており、再起動が必要です。

### <a name="why-should-i-use-hotpatch"></a>ホットパッチを使用すべき理由は何ですか?

* Windows Server 2019 Datacenter: Azure Edition でホットパッチを使用すると、VM の可用性が向上し (再起動の回数が減少する)、更新が高速になります (パッケージが小さくなり、より高速にインストールされ、プロセスを再起動する必要がない)。 このプロセスにより、VM は常に最新かつセキュリティで保護された状態になります。

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>どの種類の更新プログラムがホットパッチの対象になるのですか?

* 現在、ホットパッチは Windows セキュリティ更新プログラムを対象としています。

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>最初のホットパッチ更新プログラムはいつ提供されますか?

* ホットパッチの更新プログラムは、通常、毎月第 2 火曜日にリリースされます。 詳細については、以下を参照してください。

### <a name="what-will-the-hotpatch-schedule-look-like"></a>ホットパッチのスケジュールはどのようになりますか?

* ホット パッチの適用は、Windows Update の最新の累積的な更新プログラムが適用されたベースラインを確立することによって機能し、そのベースラインを基にして、毎月リリースされるホットパッチ更新プログラムを適用します。  プレビュー期間中、最初は 3 か月ごとにベースラインがリリースされます。 次の図は、3 か月間隔の年間スケジュールの例です (ゼロデイ修正プログラムによる計画外ベースラインの例を含む)。

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="ホットパッチのサンプル スケジュール。":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>ホットパッチに登録した VM でも再起動は必要ですか?

* ホットパッチ プログラムに含まれていない更新プログラムをインストールする場合は、依然として再起動が必要です。また、ベースライン (Windows Update の最新の累積的な更新プログラム) がインストールされた後に、定期的に必要になります。 この再起動によって、VM は、累積更新プログラムに含まれているすべてのパッチと同期した状態に維持されます。 ベースライン (再起動が必要) は、3 か月の周期で始まり、経時的に延長されます。

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>ホットパッチの更新プログラムがインストールされるときにアプリケーションへの影響はありますか?

* ホットパッチは実行中のプロセスのメモリ内コードを修正し、プロセスを再起動する必要がないため、お使いのアプリケーションはパッチ適用プロセスの影響を受けません。 これは、パッチ自体によるパフォーマンスと機能への潜在的な影響とは別のものです。

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>VM に対するホットパッチを無効にできますか?

* VM に対するホットパッチは、Azure portal でオフにすることができます。  ホットパッチをオフにすると、VM のホットパッチ登録が解除されます。これにより、VM は Windows Server の一般的な更新動作に戻ります。  VM に対するホットパッチ登録を解除した後、次回のホットパッチ ベースラインがリリースされたときに、その VM を再登録することができます。

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>既存の Windows Server OS からアップグレードできますか?

* 現在、Windows Server の既存バージョン (Windows Server 2016 または 2019 の Azure でないエディション) からのアップグレードはサポートされていません。 Windows Server Azure Edition の今後のリリースへのアップグレードがサポートされる予定です。

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>プレビュー期間中に、ホットパッチを実稼働ワークロードに使用できますか?

* プレビューはテストのみを目的としており、運用環境での使用を想定していません。

### <a name="will-i-be-charged-during-the-preview"></a>プレビュー期間中は課金されますか?

* Windows Server Azure Edition のライセンスは、プレビュー期間中は無料です。 ただし、VM 用に設定されている基になるインフラストラクチャ (ストレージ、コンピューティング、ネットワークなど) のコストは、引き続きサブスクリプションに課金されます。

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>ホット パッチの適用に関するトラブルシューティングのサポートを受けるには、どうすればよいですか?

* [テクニカル サポート ケースのチケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出できます。 [サービス] オプションで、[コンピューティング] の **[Windows を実行している仮想マシン]** を検索して選択します。 問題の種類として **[Azure Features]\(Azure の機能\)** 、問題のサブタイプとして **[Automatic VM Guest Patching]\(VM ゲストの自動パッチ適用\)** を選択します。

## <a name="next-steps"></a>次のステップ

* Azure Update Management については、[こちら](../automation/update-management/overview.md)をご覧ください。
* VM ゲストの自動パッチ適用の詳細については、[こちら](../virtual-machines/automatic-vm-guest-patching.md)をご覧ください。