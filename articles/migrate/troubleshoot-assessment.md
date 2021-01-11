---
title: Azure Migrate での評価と依存関係の視覚化のトラブルシューティング
description: Azure Migrate での評価と依存関係の視覚化をトラブルシューティングするためのヘルプを提供します。
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: a6a185c61c32636dd0189bc5835f850348b196cd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020356"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>評価と依存関係の視覚化のトラブルシューティング

この記事は、[Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) を使用した評価と依存関係の視覚化に関する問題のトラブルシューティングに役立ちます。


## <a name="assessment-readiness-issues"></a>アセスメントの準備状態

評価の準備状態を修正するには、次のようにします。

**問題点** | **解決策**
--- | ---
サポートされていないブートの種類 | Azure では、ブートの種類が EFI の VM はサポートされません。 移行を実行する前に、ブートの種類を BIOS に変換することをお勧めします。 <br/><br/>Azure Migrate Server Migration を使用して、そのような VM の移行を処理することができます。 それにより、移行中に VM のブートの種類が BIOS に変換されます。
条件付きでサポートされる Windows オペレーティング システム | オペレーティング システムはサポート期間が終了しており、[Azure でのサポート](https://aka.ms/WSosstatement)のためにはカスタム サポート契約 (CSA) が必要です。 Azure に移行する前にアップグレードを検討してください。
サポートされていない Windows オペレーティング システム | Azure では、[特定の Windows OS バージョン](https://aka.ms/WSosstatement)のみがサポートされています。 Azure に移行する前に、コンピューターのアップグレードを検討してください。
条件付きで動作が保証されている Linux OS | Azure では、[特定の Linux OS バージョン](../virtual-machines/linux/endorsed-distros.md)のみがサポートされます。 Azure に移行する前に、コンピューターのアップグレードを検討してください。 詳細については、[こちらも](https://docs.microsoft.com/azure/migrate/troubleshoot-assessment#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment)参照してください。
動作が保証されていない Linux OS | マシンは Azure で起動する可能性がありますが、オペレーティング システムは Azure ではサポートされていません。 Azure に移行する前に、[サポートされている Linux バージョン](../virtual-machines/linux/endorsed-distros.md)へのアップグレードを検討してください。
オペレーティング システムが不明です | VM のオペレーティング システムが、vCenter Server で "その他" として指定されました。 この動作により、Azure Migrate による VM の Azure 対応性の検証はブロックされます。 コンピューターを移行する前に、オペレーティング システムが Azure によって[サポートされている](https://aka.ms/azureoslist)ことを確認してください。
サポートされていないビット バージョン | 32 ビット オペレーティング システムの VM は Azure で起動する可能性がありますが、Azure に移行する前に、64 ビットにアップグレードすることをお勧めします。
Microsoft Visual Studio のサブスクリプションが必要です | コンピューターで実行されている Windows クライアント オペレーティング システムは、Visual Studio のサブスクリプションによってのみサポートされます。
必要なストレージ パフォーマンスの VM が見つかりません | そのマシンに必要なストレージ パフォーマンス (1 秒あたりの入力/出力操作数 (IOPS) とスループット) が Azure VM のサポート範囲を超えています。 移行前に、そのマシンのストレージ要件を緩和します。
必要なネットワーク パフォーマンスの VM が見つかりません | そのマシンに必要なネットワーク パフォーマンス (入力/出力) が Azure VM のサポート範囲を超えています。 そのマシンのネットワーク要件を緩和します。
指定した場所で VM が見つかりません | 移行前に、別のターゲットの場所を指定します。
不適切なディスクが 1 つ以上あります | VM に接続されている 1 つ以上のディスクで、Azure の要件が満たされていません。<br/><br/> Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium マネージド ディスクのディスク制限 (32 TB) に基づいてディスクが評価されます。<br/><br/> VM に接続されている各ディスクについて、ディスクのサイズが (Ultra SSD ディスクでサポートされている) 64 TB 未満であることを確認します。<br/><br/> そうでない場合は、Azure に移行する前にディスクのサイズを減らすか、または Azure で複数のディスクを使用して[ストライピングし](../virtual-machines/premium-storage-performance.md#disk-striping)、ストレージの上限を高くします。 各ディスクで必要とされるパフォーマンス (IOPS とスループット) が Azure の[仮想マシンの管理ディスク](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)でサポートされていることを確認してください。
不適切なネットワーク アダプターが 1 つ以上あります。 | 移行前に、マシンから未使用のネットワーク アダプターを削除します。
ディスクの数が制限を超えています | 移行前に、マシンから未使用のディスクを削除します。
ディスクのサイズが制限を超えています | Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium ディスクの制限 (32 TB) に基づいてディスクが評価されます。<br/><br/> ただし、Azure では (Ultra SSD ディスクでサポートされる) 最大 64 TB のディスク サイズまでサポートされています。 移行の前にディスクを 64 TB 未満に圧縮するか、または Azure で複数のディスクを使用して[ストライピングし](../virtual-machines/premium-storage-performance.md#disk-striping)、ストレージの上限を高くします。
指定した場所でディスクを使用できません | 移行前に、ディスクがターゲットの場所にあることを確認します。
指定した冗長性でディスクを使用できません | 評価の設定 (既定では LRS) で定義されているストレージの冗長性の種類をディスクで使用する必要があります。
内部エラーが原因でディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
必要なコアとメモリを備えた VM が見つかりません | Azure が適切な VM の種類を見つけられなかったことになります。 移行前に、オンプレミスのマシンのメモリとコアの数を削減します。
内部エラーが原因で VM の適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のネットワーク アダプターの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
オファー、通貨、予約インスタンスに対する VM サイズが見つかりませんでした | 選択された RI、オファー、通貨の組み合わせに対して VM サイズが見つからなかったため、マシンは不適切とマークされました。 評価のプロパティを編集して有効な組み合わせを選択し、評価を再計算します。 
条件付きで対応しているインターネット プロトコル | Azure VMware Solution (AVS) の評価にのみ適用されます。 AVS では、IPv6 インターネット アドレスのファクターはサポートされていません。 マシンが IPv6 を使用して検出される場合の修復については、AVS チームにお問い合わせください。

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>不明とマークされるインポート ベースの AVS 評価で推奨される移行ツール

CSV ファイルを介してインポートされたマシンの場合、AVS 評価での既定の移行ツールは不明です。 ただし VMware マシンの場合は、VMware Hybrid Cloud Extension (HCX) ソリューションを使用することをお勧めします。 [詳細については、こちらを参照してください](../azure-vmware/hybrid-cloud-extension-installation.md)。

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux VM が Azure VM 評価で "条件付きで対応" になる

VMware VM および Hyper-V VM の場合、Server Assessment では、Server Assessment の既知のギャップのため、Linux VM が "条件付きで対応" とマークされます。 

- ギャップのため、オンプレミスの VM にインストールされている Linux OS のマイナー バージョンを検出できません。
- たとえば、RHEL 6.10 の場合、現在の Server Assessment では、RHEL 6 のみが OS バージョンとして検出されます。 これは、Hyper-V ホストの vCenter Server では、Linux VM オペレーティング システム用のカーネル バージョンが提供されていないためです。
-  特定のバージョンの Linux のみが Azure によって動作保証されるため、Linux VM は現在 Server Assessment において条件付き対応とマークされます。
- オンプレミスの VM で実行されている Linux OS が Azure で動作保証済みかどうかは、[Azure Linux サポート](https://aka.ms/migrate/selfhost/azureendorseddistros)のページを参照して特定できます。
-  ディストリビューションの動作保証を確認した後は、この警告を無視してかまいません。

このギャップは、VMware VM で[アプリケーション検出](./how-to-discover-applications.md)を有効にすることで対処できます。 Server Assessment では、指定されたゲスト資格情報を使用して VM から検出されたオペレーティング システムが使用されます。 このオペレーティング システムのデータでは、Windows VM と Linux VM の両方の場合に適切な OS 情報が示されます。

## <a name="operating-system-version-not-available"></a>オペレーティング システムのバージョンを入手できない

物理サーバーの場合、オペレーティング システムのマイナーバージョン情報が入手可能であるはずです。 入手できない場合は、Microsoft サポートにお問い合わせください。 VMware マシンの場合、Server Assessment では、vCenter Server でその VM に対して指定されているオペレーティング システム情報を使用します。 ただし、vCenter Server は、オペレーティング システムのマイナー バージョンを提供しません。 マイナー バージョンを検出するには、[アプリケーション検出](./how-to-discover-applications.md)を設定する必要があります。 Hyper-V VM の場合、オペレーティング システムのマイナーバージョンの検出はサポートされていません。 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 評価でオンプレミスより大きい Azure SKU

Azure Migrate Server Assessment では、評価の種類に基づいて、現在のオンプレミスの割り当てより多くのコアとメモリを備えた Azure VM SKU が推奨される場合があります。

- VM SKU の推奨は、評価のプロパティによって異なります。
- これは、Server Assessment で実行する評価の種類によって影響を受けます: "*パフォーマンスベース*" または "*オンプレミス*"。
- パフォーマンスベースの評価では、Server Assessment によりオンプレミスの VM の利用状況データ (CPU、メモリ、ディスク、ネットワークの利用状況) が考慮されて、オンプレミスの VM に対して適切なターゲット VM SKU が決定されます。 また、有効な使用率を決定するときに快適性係数が追加されます。
- オンプレミスのサイズ設定では、パフォーマンス データは考慮されず、オンプレミスの割り当てに基づいてターゲット SKU が推奨されます。

これが推奨に与える影響を確認するため、次の例を見てみましょう。

オンプレミスの VM は、4 つのコアと 8 GB のメモリを備え、CPU 使用率は 50%、メモリ使用率は 50%、指定された快適性係数は 1.3 です。

-  評価が**オンプレミス**の場合は、4 コアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。
- 評価がパフォーマンスベースの場合は、CPU およびメモリの有効な使用率 (4 コアの 50% * 1.3 = 2.6 コア、8 GB メモリの 50% * 1.3 = 5.3 GB メモリ) に基づき、4 コア (最も近いサポートされるコア数) と 8 GB メモリ (最も近いサポートされるメモリ サイズ) の最も安価な VM SKU が推奨されます。
- 評価のサイズ決定に関する[詳細を参照](concepts-assessment-calculation.md#types-of-assessments)してください。

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM の評価で、オンプレミスのものより大きい Azure ディスク SKU が推奨されるのはなぜですか?

Azure Migrate Server Assessment では、評価の種類に基づいて、より大きいディスクが推奨される場合があります。
- Server Assessment でのディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。
- サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が**自動**である場合、ディスクの IOPS とスループットの値を考慮して、ターゲット ディスクの種類 (Standard HDD、Standard SSD、または Premium) が特定されます。 その後、そのディスクの種類からディスク SKU が推奨され、この推奨ではオンプレミスのディスクのサイズ要件が考慮されます。
- サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が **Premium** である場合、オンプレミスのディスクの IOPS、スループット、サイズの要件に基づいて、Azure の Premium ディスク SKU が推奨されます。 サイズ設定基準が**オンプレミス**で、ストレージの種類が **Standard HDD**、**Standard SSD**、または **Premium** である場合は、同じロジックを使ってディスクのサイズ設定が行われます。

たとえば、オンプレミスのディスクと 32 GB のメモリを使っていても、ディスクの読み取りと書き込みの集計 IOPS が 800 IOPS である場合、Server Assessment では Premium ディスク (高い IOPS 要件であるため) が推奨された後、必要な IOPS とサイズに対応できるディスク SKU が推奨されます。 この例では、適合する最も近いものは P15 (256 GB、1100 IOPS) になります。 オンプレミスのディスクで必要なサイズは 32 GB でしたが、オンプレミスのディスクの高い IOPS 要件により、Server Assessment ではさらに大きいディスクが推奨されます。

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>評価レポートで一部または全部の VM のパフォーマンス データが見つからないのはなぜですか?

"パフォーマンス ベース" の評価では、Azure Migrate アプライアンスでオンプレミス VM のパフォーマンス データを収集できない場合、評価レポートのエクスポートに "PercentageOfCoresUtilizedMissing" または "PercentageOfMemoryUtilizedMissing" と表示されます。 次の点を確認してください。

- 評価を作成している期間中に VM の電源がオンになっていたかどうか
- メモリ カウンターのみが取得されず、Hyper-V VM を評価しようとしている場合は、これらの VM で動的メモリが有効になっているかどうかを確認してください。 現在、既知の問題のため、そのような VM のメモリ使用率は、Azure Migrate アプライアンスで収集できません。
- すべてのパフォーマンス カウンターが不足している場合は、評価のためのポート アクセス要件が満たされていることを確認します。 [VMware](./migrate-support-matrix-vmware.md#port-access-requirements)、[Hyper-V](./migrate-support-matrix-hyper-v.md#port-access)、および[物理](./migrate-support-matrix-physical.md#port-access)サーバーの評価のためのポート アクセス要件の詳細を参照してください。
注 - いずれかのパフォーマンス カウンターを取得できない場合、Azure Migrate: Server Assessment はオンプレミスの割り当てられたコアまたはメモリにフォールバックし、それに応じて VM サイズが推奨されます。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>評価の信頼度レーティングが低いのはなぜですか?

信頼度評価は、評価を計算するために必要な[使用可能データ ポイント](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#ratings)の割合に基づいて、"パフォーマンス ベース" の評価に対して計算されます。 評価の信頼度レーティングが低い理由は以下のとおりです。

- 評価を作成するための期間用の環境をプロファイルしませんでした。 たとえば、パフォーマンス期間を 1 週間に設定した評価を作成する場合は、すべてのデータポイントが収集されるまで、検出を始めてから少なくとも 1 週間待つ必要があります。 その期間待つことができない場合は、パフォーマンス期間を短くし、評価を "再計算" してください。
 
- サーバー評価では、評価期間内に一部または全部の VM のパフォーマンス データを収集できません。 評価期間中に VM の電源がオンになっていたかどうか、ポート 443 での発信接続が許可されていることを、確認してください。 Hyper-V VM では、動的メモリが有効になっている場合、メモリ カウンターが欠落し、信頼性評価が低くなります。 評価を "再計算" し、信頼性評価に最新の変更を反映してください。 

- Server Assessment で検出が開始された後で、いくつかの VM が作成されました。 たとえば、過去 1 か月間のパフォーマンス履歴の評価を作成しているのに、ほんの 1 週間前にいくつかの VM が環境内に作成されたとします。 この場合、新しい VM のパフォーマンス データは期間全体を通しては利用できず、信頼度レーティングが低くなります。

信頼度レーティングに関する[詳細についてはこちら](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#confidence-ratings-performance-based)をご覧ください。

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>オペレーティング システムのライセンスは Azure VM 評価に含まれていますか?

現在、Azure Migrate Server Assessment では、Windows マシンのオペレーティング システムのライセンス コストのみが考慮されます。 Linux マシンのライセンス コストは現在は考慮されません。

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Azure VM 評価ではパフォーマンス ベースのサイズ設定はどのように動作しますか?

Server Assessment では、オンプレミスのマシンのパフォーマンス データを継続的に収集し、それを使って Azure で VM の SKU とディスクの SKU を推奨します。 パフォーマンスベースのデータの収集方法を[確認](concepts-assessment-calculation.md#calculate-sizing-performance-based)してください。

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>予約インスタンス、VM アップタイム、割引 (%) の無効な組み合わせで作成されたことを示す警告が評価で表示されるのはなぜですか?
[予約済みインスタンス] を選択すると、[割引 (%)] および [VM のアップタイム] プロパティは適用されません。 これらのプロパティの無効な組み合わせで評価が作成されたため、編集ボタンと再計算ボタンは無効になっています。 新しい評価を作成してください。 [詳細については、こちらを参照してください](https://go.microsoft.com/fwlink/?linkid=2131554)。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>物理サーバー上の一部のネットワーク アダプターのパフォーマンス データが表示されません

これは、物理サーバーで Hyper-V 仮想化が有効になっている場合に発生する可能性があります。 これらのサーバーでは、製品のギャップのため、Azure Migrate では現在、物理と仮想両方のネットワーク アダプターが検出されます。 ネットワーク スループットは、検出された仮想ネットワーク アダプターでのみキャプチャされます。

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>物理サーバーに対して推奨される Azure VM SKU が大きすぎます

これは、物理サーバーで Hyper-V 仮想化が有効になっている場合に発生する可能性があります。 これらのサーバーの場合、Azure Migrate では現在、物理と仮想両方のネットワーク アダプターが検出されます。 そのため、 検出されるネットワーク アダプターの数は実際より多くなります。 Server Assessment によって必要な数のネットワーク アダプターをサポートできる Azure VM が選択されるため、これによりサイズが大きすぎる VM になる可能性があります。 [詳細情報](./concepts-assessment-calculation.md#calculating-sizing)で、 サイズ設定に対するネットワーク アダプターの数の影響について確認してください。 これは、今後対処される製品のギャップです。

## <a name="readiness-category-not-ready-for-my-physical-server"></a>物理サーバーの準備カテゴリが "準備ができていません" になります

Hyper-V の仮想化が有効になっている物理サーバーの場合、準備カテゴリが誤って "準備ができていません" とマークされることがあります。 これらのサーバーでは、製品のギャップのため、Azure Migrate では現在、物理と仮想両方のアダプターが検出されます。 そのため、 検出されるネットワーク アダプターの数は実際より多くなります。 オンプレミスに合わせた評価とパフォーマンス ベースの評価のどちらでも、Server Assessment によって必要な数のネットワーク アダプターをサポートできる Azure VM が選択されます。 ネットワーク アダプターの数が 32 を超えていることが検出された場合、 これは Azure VM でサポートされる NIC の最大数であり、マシンは "準備ができていません" とマークされます。  [詳細情報](./concepts-assessment-calculation.md#calculating-sizing)で、 サイズ設定に対する NIC の数の影響について確認してください。


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>検出される NIC の数が、物理サーバーの実際の数より多くなります

これは、物理サーバーで Hyper-V 仮想化が有効になっている場合に発生する可能性があります。 これらのサーバーの場合、Azure Migrate では現在、物理と仮想両方のアダプターが検出されます。 そのため、 検出される NIC の数は実際より多くなります。

## <a name="dependency-visualization-in-azure-government"></a>Azure Government での依存関係の視覚化

Azure Migrate の依存関係視覚化機能は Service Map に依存しています。 現在、Azure Government では Service Map を使用できないため、Azure Government ではこの機能を使用できません。

## <a name="dependencies-dont-show-after-agent-install"></a>エージェントのインストール後に依存関係が表示されない

オンプレミスの VM に依存関係視覚化エージェントをインストールした後、Azure Migrate ポータルで依存関係が表示されるまで 15 分から 30 分かかります。 30 分以上待っている場合は、Microsoft Monitoring Agent (MMA) が Log Analytics ワークスペースに接続できることを確認してください。

Windows VM の場合:
1. コントロール パネルで MMA を開始します。
2. **[Microsoft Monitoring Agent のプロパティ]**  >  **[Azure Log Analytics (OMS)]** で、ワークスペースの **[状態]** が緑色になっていることを確認します。
3. 状態が緑色でない場合は、ワークスペースを削除して、再度 MMA に追加してみてください。

    ![MMA のステータス](./media/troubleshoot-assessment/mma-properties.png)

Linux VM の場合、MMA と依存関係エージェントのインストールに関するすべてのコマンドが正常に実行されたことを確認します。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

- **MMS エージェント**:サポートされている [Windows](../azure-monitor/platform/agents-overview.md#supported-operating-systems) オペレーティング システムと [Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems) オペレーティング システムを確認します。
- **依存関係エージェント**: サポートされている [Windows および Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) オペレーティング システムを確認します。

## <a name="visualize-dependencies-for--hour"></a>最大 1 時間分の依存関係を視覚化する

エージェントレスの依存関係の分析では、最大 30 日間、マップ内で依存関係を視覚化したりエクスポートしたりできます。

エージェント ベースの依存関係の分析では、Azure Migrate を使用すると、過去 1 か月間の特定の日付に戻ることができますが、依存関係を視覚化できる期間は最大 1 時間です。 たとえば、依存関係マップにある期間機能を使用して、昨日の依存関係を表示することが可能ですが、1 時間の期間のみ表示できます。 ただし、Azure Monitor ログを使用すれば、長期間にわたって[依存関係データのクエリ](./how-to-create-group-machine-dependencies.md)を実行できます。

## <a name="visualized-dependencies-for--10-machines"></a>最大 10 個のマシンの依存関係を視覚化する

Azure Migrate Server Assessment では、エージェント ベースの依存関係の分析を使用すると、最大 10 個の VM を含む[グループの依存関係を視覚化](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)できます。 大きいグループの場合、依存関係を視覚化するには VM を小さいグループに分割することをお勧めします。


## <a name="machines-show-install-agent"></a>マシンに "エージェントのインストール" が表示される

依存関係の視覚化が有効になっているマシンを Azure に移行した後、マシンでは、次の動作により、"依存関係の表示" ではなく "エージェントのインストール" アクションが表示される場合があります。


- Azure に移行した後、オンプレミスのマシンはオフになり、同等の VM が Azure で起動されます。 これらのマシンは、別々の MAC アドレスを取得します。
- ユーザーがオンプレミスの IP アドレスを保持するかどうかにより、マシンは異なる IP アドレスも取得する場合があります。
- MAC と IP アドレスの両方がオンプレミスと異なる場合、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われません。 この場合、依存関係の表示ではなく、エージェントをインストールするオプションが表示されます。
- Azure へのテスト移行の後、オンプレミスのマシンでは想定どおりにオンの状態が維持されます。 Azure にスピン アップされる同等のマシンでは、異なる MAC アドレスが取得され、異なる IP アドレスが取得されることがあります。 これらのマシンから送信される Azure Monitor ログ トラフィックをブロックしない限り、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われず、依存関係が表示されるのではなく、エージェントをインストールするオプションが表示されます。

## <a name="dependencies-export-csv-shows-unknown-process"></a>依存関係のエクスポートの CSV で "不明なプロセス" と表示されます
エージェントレスの依存関係の分析では、プロセス名はベストエフォートでキャプチャされます。 特定のシナリオでは、依存元と依存先のサーバー名および依存先のポートはキャプチャされますが、依存関係の両端でプロセス名を特定することはできません。 このような場合、プロセスは "不明なプロセス" とマークされます。

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-server-assessment"></a>Server Assessment でワークスペースの構成を試みたときに自分の Log Analytics ワークスペースが表示されない
現在、Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンで OMS ワークスペースの作成がサポートされています。 ワークスペースが Azure Migrate 以外の他のリージョンで作成されている場合、現在、Azure Migrate プロジェクトに関連付けることはできません。


## <a name="capture-network-traffic"></a>ネットワーク トラフィックをキャプチャする

次のようにネットワーク トラフィック ログを収集します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. F12 キーを押して開発者ツールを起動します。 必要な場合は、 **[ナビゲーション時にエントリをクリア]** の設定をオフにします。
3. **[ネットワーク]** タブを選択し、ネットワーク トラフィックのキャプチャを開始します。
   - Chrome では、 **[Preserve log]\(ログの保持\)** を選択します。 自動で記録が開始されるはずです。 赤い円は、トラフィックがキャプチャされていることを示しています。 赤い円が表示されない場合は、黒い円を選択して開始します。
   - Microsoft Edge または Internet Explorer では、自動で記録が開始されるはずです。 開始されない場合は、緑色の再生ボタンを選択します。
4. エラーを再現してみます。
5. 記録中にエラーが発生したら、記録を停止し、記録されたアクティビティのコピーを保存します。
   - Chrome では、右クリックして **[Save as HAR with content]\(内容を HAR ファイルに保存する\)** を選択します。 この操作により、ログが .har ファイルとして圧縮されエクスポートされます。
   - Microsoft Edge または Internet Explorer で、 **[キャプチャしたトラフィックのエクスポート]** オプションを選択します。 この操作により、ログが圧縮されてエクスポートされます。
6. **[コンソール]** タブを選択し、いずれかの警告またはエラーを確認します。 コンソール ログを保存するには次の操作を行います。
   - Chrome では、コンソール ログのどこかを右クリックします。 **[名前を付けて保存]** を選択し、ログをエクスポートして zip 圧縮します。
   - Microsoft Edge または Internet Explorer では、エラーを右クリックして **[すべてコピー]** を選択します。
7. 開発者ツールを閉じます。


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>評価でのオペレーティング システムのデータはどこから検出されますか?

- VMware VM の場合、既定では、vCenter によって提供されるオペレーティング システムのデータです。 
   - VMware Linux VM の場合は、アプリケーション検出が有効になっていると、OS の詳細はゲスト VM からフェッチされます。 評価に含まれる OS の詳細を確認するには、[検出済みサーバー] ビューに移動し、[オペレーティング システム] 列の値をマウスでポイントします。 ポップアップするテキストで、表示されている OS データが vCenter Server から収集されたものか、VM の資格情報を使用してゲスト VM から収集されたものかを確認できます。 
   - Windows VM の場合は、オペレーティング システムの詳細は常に vCenter Server からフェッチされます。
- Hyper-V VM の場合は、オペレーティング システムのデータは Hyper-V ホストから収集されます。
- 物理サーバーの場合は、サーバーからフェッチされます。

## <a name="next-steps"></a>次のステップ

評価を[作成](how-to-create-assessment.md)するか[カスタマイズ](how-to-modify-assessment.md)します。
