---
title: Avere vFXT のクラスター チューニング - Azure
description: Avere vFXT for Azure のパフォーマンスを最適化するためのカスタム設定の概要
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54197439"
---
# <a name="cluster-tuning"></a>クラスター チューニング


ほとんどの vFXT クラスターにとって、パフォーマンス設定のカスタマイズは有益です。 これらの設定は、特定のワークフロー、データセット、およびツールでクラスターを最適に動作させるうえで役立ちます。 

このカスタマイズは、通常、Avere Control Panel では使用できない機能を構成することになるため、サポート担当者と一緒に行う必要があります。

このセクションでは、実行できるいくつかのカスタム チューニングについて説明します。

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>一般的な最適化

これらの変更は、データセットの品質またはワークフロー スタイルに基づいて推奨される場合があります。

* ワークロードの書き込み負荷が高い場合は、書き込みキャッシュのサイズを既定の 20% から増やします。 
* データセットに多数の小さなファイルが含まれている場合は、クラスターのキャッシュにおけるファイル数の上限を引き上げます。 
* 2 つのリポジトリ間のデータのコピーまたは移動が作業に含まれている場合は、データの移動に使用されるスレッドの数を調整します。 
  * 速度を向上させるには、使用する並列スレッドの数を増やします。
  * バックエンドのストレージ ボリュームがオーバーロードしつつある場合は、使用する並列スレッドの数を減らすことが必要になる可能性があります。
* NFSv4 ACL を使用するコア ファイラーのデータをクラスターでキャッシュする場合は、特定のクライアントに対するファイル認可を合理化するために、アクセス モード キャッシングを有効にします。

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>クラウド NAS またはクラウド ゲートウェイの最適化

クラウド NAS またはゲートウェイのシナリオ (vFXT クラスターがクラウド コンテナーへの NAS スタイルのアクセスを提供するシナリオ) で vFXT クラスターとクラウド ストレージの間のより速いデータ速度を活用するために、お客様の担当者が、より積極的にデータをキャッシュからストレージ ボリュームにプッシュするように、以下のような設定の変更を推奨する場合があります。

* クラスターとストレージ コンテナーの間の TCP 接続数を増やす
* 書き込みがすぐに成功しない場合、より早く再試行するために、クラスターとストレージ間の通信の REST タイムアウト値を減らす  
* 各バックエンド書き込みセグメントが 1 MB ではなく 8 MB のデータ チャンクを転送するように、セグメント サイズを増やす

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>クラウド バーストまたはハイブリッド WAN の最適化

クラウド バーストのシナリオまたはハイブリッド ストレージ WAN の最適化のシナリオ (vFXT クラスターが、クラウドとオンプレミス ハードウェア ストレージ間の統合を提供するシナリオ) では、次のような変更が役に立ちます。

* クラスターとコア ファイラー間で許可される TCP 接続数を増やす
* リモート コア ファイラーの WAN 最適化設定を有効にする (この設定は、別の Azure リージョンのリモート オンプレミス ファイラーまたはクラウド コア ファイラーに使用できます)
* TCP ソケットのバッファー サイズを増やす (ワークロードとパフォーマンスのニーズに応じて)
* "常に転送" する設定を有効にして、冗長にキャッシュされているファイル減らす (ワークロードとパフォーマンスのニーズに応じて)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Avere vFXT for Azure の最適化のヘルプ

これらの最適化に関してサポート スタッフに問い合わせるには、「[Get help with your system (システムに関するサポートを受ける)](avere-vfxt-open-ticket.md)」で説明されている手順を使用してください。 