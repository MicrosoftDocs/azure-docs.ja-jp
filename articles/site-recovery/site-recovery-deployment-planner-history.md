---
title: Azure Site Recovery Deployment Planner のバージョン履歴
description: 既知のさまざまな Site Recovery Deployment Planner のバージョンの修正および既知の制限事項とそれらのリリース日。
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133800"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery Deployment Planner のバージョン履歴

この記事では、Azure Site Recovery Deployment Planner のすべてのバージョンの履歴と修正、それぞれの既知の制限とそのリリース日を紹介します。

## <a name="version-252"></a>バージョン 2.52

**リリース日:2020 年 6 月 4 日**

**修正:**

- vCenter 7.0 のサポートを追加しました
- 次のオペレーティング システムのサポートが追加されました。

    - SUSE Linux Enterprise 15 (すべてのマイナー バージョンを含む)
    - Red Hat Enterprise Linux 8 (すべてのマイナー バージョンを含む)


## <a name="version-251"></a>バージョン 2.51

**リリース日:2019 年 8 月 22 日**

**修正:**

- Deployment Planner バージョン 2.5 でのコストに関する推奨事項の問題を修正

## <a name="version-25"></a>バージョン 2.5

**リリース日:2019 年 7 月 29 日**

**修正:**

- VMware 仮想マシンと物理マシンの場合、Managed Disks のレプリケーションに基づいてレコメンデーションが更新されます。
- Windows 10 (x64)、Windows 8.1 (x64)、Windows 8 (x64)、Windows 7 (x64) SP1 以降のサポートが追加されました。

## <a name="version-24"></a>バージョン 2.4

**リリース日:2019 年 4 月 17 日**

**修正:**

- 特にローカライズベースのエラーを処理する場合の、オペレーティング システムの互換性を向上しました。
- 互換性チェックリストに最大 20 Mbps のデータ変更率 (チャーン) の VM を追加しました。
- エラー メッセージを改善しました
- vCenter 6.7 のサポートを追加しました。
- Windows Server 2019 および Red Hat Enterprise Linux (RHEL) ワークステーションのサポートを追加しました。



## <a name="version-23"></a>バージョン 2.3

**リリース日:2018 年 12 月 3 日**

**修正:**

- Deployment Planner でターゲットの場所とサブスクリプションを指定してレポートを生成できなかった問題を修正しました。

## <a name="version-22"></a>バージョン 2.2 

**リリース日:2018 年 4 月 25 日**

**修正:**

- GetVMList の操作:
  - 指定したフォルダーが存在しない場合に GetVMList が失敗する問題を修正しました。 既定のディレクトリが作成されるか、outputfile パラメーターに指定したディレクトリが作成されるようになりました。
  - GetVMList のより詳細な失敗理由を追加しました。
- Deployment Planner レポートの互換 VM シートに列として VM の種類情報を追加しました。
- Hyper-V から Azure へのディザスター リカバリー:
  - 共有 VHD と PassThrough ディスクがある VM をプロファイリングから除外しました。 Startprofiling 操作で、除外された VM の一覧がコンソールに表示されます。
  - 互換性のない VM の一覧に 64 を超えるディスクがある VM を追加しました。
  - 初期レプリケーション (IR) と差分レプリケーション (DR) の圧縮率を更新しました。
  - SMB ストレージの制限付きサポートを追加しました。

## <a name="version-21"></a>バージョン 2.1

**リリース日:2018 年 1 月 3 日**

**修正:**

- Excel レポートを更新しました。
- GetThroughput 操作のバグを修正しました。
- レポートをプロファイルまたは生成する VM 数を制限するオプションを追加しました。 既定の制限は 1,000 個の VM です。
- VMware から Azure へのディザスター リカバリー:
  - 互換性のないテーブルで発生する Windows Server 2016 VM の問題を修正しました。 
  - 拡張ファームウェア インターフェイス (EFI) Windows VM の互換性に関するメッセージを更新しました。
- VMware から Azure へ、Hyper-V から Azure へ、VM ごとの VM データ チャーン制限を更新しました。 
- VM 一覧ファイルの解析の信頼性を向上しました。

## <a name="version-201"></a>バージョン 2.0.1

**リリース日:2017 年 12 月 7 日**

**修正:**

- ネットワーク帯域幅を最適化するための推奨事項を追加しました。

## <a name="version-20"></a>バージョン 2.0

**リリース日:2017 年 11 月 28 日**

**修正:**

- Hyper-V から Azure へのディザスター リカバリーのサポートを追加しました。
- コスト計算ツールを追加しました。
- VM が保護に対して互換性があるかどうかを判断するために、Azure ディザスター リカバリーに対する VMware の OS バージョン チェックを追加しました。 このツールには、その VM の vCenter サーバーから返された OS バージョン文字列が使用されます。 これは、VMware に VM を作成するときにユーザーが選択したゲスト オペレーティング システムのバージョンです。

**既知の制限事項:**

- Hyper-V から Azure へのディザスター リカバリーでは、`,`、`"`、`[`、`]`、および ``` ` ``` のような文字を含む名前の VM はサポートされません。 プロファイルすると、レポートの生成は失敗するか、誤った結果になります。
- VMware から Azure へのディザスター リカバリーでは、名前にコンマを含む VM はサポートされません。 プロファイルすると、レポートの生成は失敗するか、誤った結果になります。

## <a name="version-131"></a>バージョン 1.3.1

**リリース日:2017 年 7 月 19 日** 

**修正:**

- レポートの生成に (1 TB を超える) 大容量ディスクのサポートを追加しました。 今後は Deployment Planner を使用して、ディスク サイズが 1 TB を超える仮想マシン (最大 4,095 GB) のレプリケーションを計画することができます。
詳細については、[Azure Site Recovery における大容量ディスクのサポート](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)に関するページを参照してください。

## <a name="version-13"></a>バージョン 1.3

**リリース日:2017 年 5 月 9 日**

**修正:**

- レポート生成にマネージド ディスクのサポートを追加しました。 1 つのストレージ アカウントに配置できる VM の数は、フェールオーバー/テスト フェールオーバーに使用するマネージド ディスクが選択されているかどうかに基づいて計算されます。

## <a name="version-12"></a>バージョン 1.2

**リリース日:2017 年 4 月 7 日**

**修正:**

- VM が保護の対象として適合しているかどうかを判断するために、各 VM にブートの種類 (BIOS または EFI) のチェックを追加しました。
- 適合 VM ワークシートと不適合 VM ワークシートに、各仮想マシンに関する OS の種類の情報を追加しました。
- 米国政府と中国の Microsoft Azure リージョンの GetThroughput 操作のサポートを追加しました。
- vCenter と ESXi サーバーに関して前提条件のチェックをいくつか追加しました。
- ロケール設定が英語以外に設定されているときに不正確なレポートが生成される問題を修正しました。

## <a name="version-11"></a>バージョン 1.1

**リリース日:2017 年 3 月 9 日**

**修正:**

- さまざまな vCenter ESXi ホストにまたがる同じ名前または IP アドレスを持つ VM が複数あると、VM をプロファイルできない問題を修正しました。
- 適合 VM ワークシートと不適合 VM ワークシートのコピーと検索が無効になる問題を修正しました。

## <a name="version-10"></a>バージョン 1.0

**リリース日:2017 年 2 月 23 日**

**既知の制限事項:**

- VMware から Azure へのディザスター リカバリー シナリオのみをサポートします。 Hyper-V から Azure へのディザスター リカバリーのシナリオについては、[Hyper-V Capacity Planner ツール](./hyper-v-deployment-planner-overview.md)を利用してください。
- 米国政府と中国の Microsoft Azure リージョンの GetThroughput 操作はサポートしてません。
- このツールでは、同じ名前または IP アドレスの複数の VM が異なる ESXi ホストにまたがって vCenter サーバーに存在する場合に、VM をプロファイルできません。
このバージョンでは、VMListFile 内の重複する VM 名または IP アドレスに対するプロファイリングがスキップされます。 これを回避するには、vCenter サーバーを対象にするのではなく、ESXi ホストを対象にして VM をプロファイリングしてください。 ESXi ホストごとに 1 つのインスタンスを実行する必要があります。
