---
title: 更新プログラムとメンテナンスの概要
description: Azure の仮想マシンで使用できる更新プログラムとメンテナンスのオプションについて説明します。
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103021991"
---
# <a name="updates-and-maintenance-overview"></a>更新プログラムとメンテナンスの概要
この記事では、Azure 仮想マシン (VM) のさまざまな更新およびメンテナンスのオプションの概要について説明します。

## <a name="automatic-os-image-upgrade"></a>OS イメージの自動アップグレード

スケール セットで [OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context)を有効にすると、スケール セット内のすべてのインスタンスの OS ディスクが安全かつ自動的にアップグレードされるようになり、更新プログラムの管理が容易になります。

[OS の自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context)には、次の特徴があります。

- 構成すると、イメージ発行元によって公開された最新の OS イメージが、ユーザーの介入なしでスケール セットに自動的に適用されます。
- 新しいイメージが発行元によって発行されるたびに、ローリングの方法でバッチごとにインスタンスをアップグレードします。
- アプリケーション正常性プローブおよび[アプリケーション正常性拡張機能](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context)と統合されます。
- すべての VM サイズについて、Windows と Linux の両方のイメージで動作します。
- 自動アップグレードはいつでも停止できます (OS のアップグレードは、手動でも開始できます)。
- VM の OS ディスクが、最新バージョンのイメージで作成された新しい OS ディスクに置き換えられます。 永続化されているデータ ディスクは保持されたまま、構成済みの拡張機能とカスタム データ スクリプトが実行されます。
- [拡張機能のシーケンス処理](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context)がサポートされています。
- 自動 OS イメージ アップグレードは、任意の規模のスケール セットで有効にすることができます。


## <a name="automatic-vm-guest-patching-preview"></a>VM ゲストの自動パッチ適用 (プレビュー)

Azure VM で [VM ゲストの自動パッチ適用](automatic-vm-guest-patching.md)を有効にすると、安全かつ自動的に仮想マシンにパッチを適用することで、更新プログラムの管理が容易になり、セキュリティ コンプライアンスを維持することができます。

[VM ゲストの自動パッチ適用](automatic-vm-guest-patching.md)には、次の特徴があります。
- *[重大]* または *[セキュリティ]* に分類されているパッチは、自動的にダウンロードされ、VM に適用されます。
- パッチは、VM のタイムゾーンでピーク外の時間帯に適用されます。
- パッチ オーケストレーションが Azure によって管理され、[可用性優先の原則](automatic-vm-guest-patching.md#availability-first-patching)に従ってパッチが適用されます。
- プラットフォーム正常性シグナルによって特定された仮想マシンの正常性は、パッチ適用の失敗を検出するために監視されます。
- すべての VM サイズで機能します。


## <a name="automatic-extension-upgrade-preview"></a>拡張機能の自動アップグレード (プレビュー)

[拡張機能の自動アップグレード](automatic-extension-upgrade.md)は、Azure VM と Azure Virtual Machine Scale Sets 用のプレビューで利用できます。 VM またはスケール セットで拡張機能の自動アップグレードが有効になっていると、拡張機能の発行元がその拡張機能の新しいバージョンをリリースするたびに、拡張機能が自動的にアップグレードされます。

 拡張機能の自動アップグレードには、次の機能があります。
- Azure VM と Azure Virtual Machine Scale Sets でサポートされています。 Service Fabric Virtual Machine Scale Sets は現在はサポートされていません。
- アップグレードは、可用性優先のデプロイ モデルで適用されます。
- Virtual Machine Scale Sets の場合、20% を超えるスケール セットの仮想マシンが 1 回のバッチでアップグレードされることはありません。 最小バッチ サイズは 1 仮想マシンです。
- すべての VM サイズ、および Windows と Linux の両方の拡張機能で動作します。
- いつでも自動アップグレードをオプトアウトできます。
- 拡張機能の自動アップグレードは、任意のサイズの Virtual Machine Scale Sets で有効にすることができます。
- サポートされている各拡張機能は個別に登録され、どの拡張機能を自動的にアップグレードするかを選択できます。
- すべてのパブリック クラウド リージョンでサポートされています。

## <a name="hotpatch"></a>ホットパッチ 

[ホット パッチの適用](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context)は、新しい Windows Server Azure Edition の仮想マシン (VM) に更新プログラムをインストールするための新しい方法であり、インストール後に再起動を必要としません。 Windows Server Azure Edition の VM を対象にしたホットパッチには、次の利点があります。

- 再起動の回数が減り、ワークロードへの影響が少ない
- パッケージが小さくなり、より高速にインストールされること、Azure Update Manager でパッチ オーケストレーションが容易になることで、更新プログラムをより迅速に展開できる
- ホットパッチ更新プログラム パッケージには、再起動せずに高速にインストールされる Windows セキュリティ更新プログラムが含まれるため、保護が強化される


## <a name="azure-update-management"></a>Azure Update Management

[Azure Automation の Update Management](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) を使用すると、Azure、オンプレミス環境、およびその他のクラウド環境で、Windows 仮想マシンと Linux 仮想マシンに対するオペレーティング システムの更新プログラムを管理できます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。

## <a name="maintenance-control"></a>メンテナンス管理

[メンテナンス コントロール](maintenance-control.md)を使用して、プラットフォームの再起動が不要な更新プログラムを管理します。 Azure は、信頼性、パフォーマンス、セキュリティを改善し、新機能を導入する目的で、インフラストラクチャを頻繁に更新しています。 ほとんどの更新は、ユーザーからは透過的に行われます。 ゲーム、メディア ストリーミング、金融取引などの一部のセンシティブなワークロードでは、数秒間であっても、メンテナンスのために VM がフリーズしたり切断したりすることが許されません。 メンテナンス コントロールが提供するオプションを使用すると、プラットフォームの更新を待機し、35 日間のローリング期間内にそれらの更新を適用できます。 

メンテナンス コントロールを使用すると、分離された VM や Azure 専用ホストに更新プログラムを適用するタイミングをユーザーが決定できます。

[メンテナンス コントロール](maintenance-control.md)を使用すると、次のことができます。
- 更新プログラムを 1 つの更新プログラム パッケージにまとめる。
- 最大 35 日間待機して更新プログラムを適用する。 
- メンテナンス スケジュールを構成するか、[Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)を使用して、プラットフォームの更新を自動化します。
- メンテナンス構成が、複数のサブスクリプションやリソース グループ全体で機能するようにする。 


## <a name="scheduled-events"></a>スケジュールされたイベント

スケジュールされたイベントとは、仮想マシン (VM) のメンテナンスに備えるための時間をアプリケーションに与える Azure Metadata Service です。 今後のメンテナンス イベント (再起動など) に関する情報を提供することで、アプリケーションがイベントの準備を行い、中断を制限できるようにします。 このサービスは、Windows および Linux の、PaaS と IaaS を含むすべての Azure Virtual Machine の種類で利用できます。 

Scheduled Events の詳細については、[Windows VM 用の Scheduled Events](./windows/scheduled-events.md) に関する記事と [Linux 用の Scheduled Events](./linux/scheduled-events.md) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

アプリケーションとサービスのアップタイムを向上させる方法については、[可用性とスケール](availability.md)に関するドキュメントを参照してください。 