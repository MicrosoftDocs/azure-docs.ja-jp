---
title: StorSimple Virtual Array Update 1.1 のリリース ノート | Microsoft Docs
description: Update 1.1 を実行する StorSimple Virtual Array の未解決の重大な問題と解決方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: df4722a4232686b0d981c190bdc154f0d1bec62e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960206"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>StorSimple Virtual Array Update 1.1 のリリース ノート

## <a name="overview"></a>概要

このリリース ノートには、Microsoft Azure StorSimple Virtual Array 更新プログラムのリリースに関する未解決の重大な問題と解決済みの問題が示されています。

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 StorSimple Virtual Array をデプロイする前に、リリース ノートに含まれる情報を十分に確認してください。

Update 1.1 はソフトウェア バージョン **10.0.10307.0** に対応しています。

> [!IMPORTANT]
> - 更新プログラムは中断を伴い、デバイスを再起動します。 I/O が進行中である場合は、デバイスにダウンタイムが発生します。 更新プログラムの適用方法に関する詳細な手順については、[Update 1.1 のインストール](storsimple-virtual-array-install-update-11.md)に関するページをご覧ください。
>
> - Update 1.1 は、デバイスで Update 1.0 が実行されている場合にのみ、Azure portal で入手できます。

## <a name="whats-new-in-update-11"></a>Update 1.1 の新機能

この更新プログラムには、次の強化機能と修正プログラムが含まれています。

 - **バックアップのエラー** - クラウドのエラーや高い CPU 使用率に対する回復性を高めることで改善されました。
 - **ログ記録** - デバイスがサポート セッションのときの詳細モードのログ記録動作が変更されました。


## <a name="issues-fixed-in-update-11"></a>Update 1.1 で修正された問題

次の表に、このリリースで修正された問題の概要を示します。

| いいえ。 | 特徴量 | 問題 |
| --- | --- | --- |
| 1 |バックアップ| このリリースには、クラウドのエラーや高い CPU 使用率に対する回復性を高めることでバックアップのエラーを改善する変更が含まれています。|
| 2 |ログ記録| このリリースには、デバイスがサポート セッションにある間の詳細モードのログ記録動作の変更が含まれています。|


## <a name="known-issues-in-update-11"></a>Update 1.1 の既知の問題

次の表では、StorSimple Virtual Array の既知の問題の概要と、以前のリリースのリリース ノートに記載されていた問題を説明します。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
| **1.** |更新プログラム |プレビュー リリースで作成された仮想アレイは、サポートされている一般提供版に更新することはできません。 |これらの仮想アレイは、ディザスター リカバリー (DR) ワークフローを使用して一般提供リリースにフェールオーバーする必要があります。 |
| **2.** |プロビジョニング済みのデータ ディスク |特定の指定されたサイズのデータ ディスクをプロビジョニングし、対応する StorSimple Virtual Array を作成した後は、データ ディスクを拡張または縮小することはできません。 これを行おうとすると、デバイスのローカル層のすべてのデータが失われます。 | |
| **3.** |グループ ポリシー |デバイスがドメインに参加しているときに、グループ ポリシーを適用すると、デバイスの動作に悪影響を及ぼすことがあります。 |仮想アレイが Active Directory の独自の組織単位 (OU) にあり、グループ ポリシー オブジェクト (GPO) が適用されていないことを確認します。 |
| **4.** |ローカル Web UI |Internet Explorer で拡張セキュリティ機能 (IE ESC) を有効にすると、[Troubleshooting] や [Maintenance] など、ローカル Web UI の一部のページが正しく動作しないことがあります。 これらのページ上のボタンも動作しないことがあります。 |Internet Explorer で拡張セキュリティ機能を無効にします。 |
| **5.** |ローカル Web UI |Hyper-V 仮想マシンでは、Web UI のネットワーク インターフェイスは 10 Gbps のインターフェイスとして表示されます。 |この動作には、Hyper-V の動作が反映されています。 Hyper-V では、仮想ネットワーク アダプターに対して常に 10 Gbps と表示されます。 |
| **6.** |階層化ボリューム/共有 |StorSimple の階層化ボリュームを使用するアプリケーションのバイト範囲ロックはサポートされていません。 バイト範囲ロックを有効にすると、StorSimple の階層化が機能しなくなります。 |推奨される方法は次のとおりです。 <br></br>アプリケーション ロジックでバイト範囲ロックを無効にします。<br></br>階層化ボリュームではなく、ローカル固定ボリュームにこのアプリケーションのデータを配置します。<br></br>"*注意*": ローカル固定ボリュームを使用しているときにバイト範囲ロックを有効にすると、復元が完了する前でも、ローカル固定ボリュームがオンラインになる場合があります。 このような場合、復元中であれば、復元が完了するまで待つ必要があります。 |
| **7.** |階層化共有 |複数の大きなファイルを処理すると、階層化に時間がかかる可能性があります。 |複数の大きなファイルを処理するときは、最も大きなファイルのサイズを共有のサイズの 3% 未満にすることをお勧めします。 |
| **8.** |共有の使用済み容量 |共有にデータがなくても、共有の使用量が表示されることがあります。 この使用量は、共有の使用済み容量にメタデータが含まれているためです。 | |
| **9.** |障害復旧 |ファイル サーバーの障害復旧はソース デバイスと同じドメインに対してのみ実行できます。 このリリースでは、別のドメインのターゲット デバイスに対する障害復旧はサポートされていません。 |これは今後のリリースで実装されます。 詳細については、[StorSimple Virtual Array のフェールオーバーと障害復旧](storsimple-virtual-array-failover-dr.md)に関するページをご覧ください。 |
| **10.** |Azure PowerShell |このリリースでは、Azure PowerShell を使用して StorSimple Virtual Array を管理することはできません。 |仮想デバイスの管理はすべて、Azure Portal とローカル Web UI を使用して行う必要があります。 |
| **11.** |パスワードの変更 |仮想アレイ デバイス コンソールは、en-US キーボード形式の入力のみを受け入れます。 | |
| **12.** |CHAP |作成された CHAP 資格情報は、削除できません。 また、CHAP 資格情報を変更する場合は、変更を有効にするために、ボリュームをオフラインにしてからオンラインにする必要があります。 |この問題は今後のリリースで対処されます。 |
| **13.** |iSCSI サーバー |iSCSI ボリュームに対して表示される '使用済みストレージ' は、StorSimple デバイス マネージャー サービスと iSCSI ホストで異なる場合があります。 |iSCSI ホストにはファイル システム ビューがあります。<br></br>デバイスでは、ボリュームが最大サイズのときに割り当てられるブロックを確認できます。 |
| **14.** |ファイル サーバー |フォルダー内のファイルが代替データ ストリーム (ADS) に関連付けられている場合、ADS のバックアップまたは復元には、障害復旧、複製、および項目レベルの回復が使用されません。 | |
| **15.** |ファイル サーバー |シンボリック リンクはサポートされていません。 | |
| **16.** |ファイル サーバー |Windows 暗号化ファイル システム (EFS) で保護されているファイルを StorSimple Virtual Array のファイル サーバーにコピーまたは保存すると、サポートされていない構成になります。  | |
| **17.** |更新プログラム |ローカルの UI で修正プログラムをインストールしようとしてエラー コード: 2359302 (16 進数 0x240006) が表示される場合は、修正プログラムがデバイスに既にインストールされていることを示しています。   | |
| **18.** |更新プログラム |ローカル Web UI を使用して仮想アレイに Update 1 をインストールする場合は、Update 0.6 が実行されていることを確認する必要があります。 実行されているバージョンが Update 0.6 よりも低い場合は、Update 0.6 を最初にインストールしてから、Update 1 を適用してください。 Update 0.6 より前のバージョンから直接 Update 1.0 をインストールすると、一部の更新プログラムが不足し、監視チャートは機能しません。   | |


## <a name="next-steps"></a>次のステップ
StorSimple Virtual Array に [Update 1.1 をインストールする](storsimple-virtual-array-install-update-11.md)。

## <a name="references"></a>リファレンス
以前のリリース ノートをお探しですか。 参照先:
* [StorSimple Virtual Array Update 1.0 のリリース ノート](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 のリリース ノート](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 のリリース ノート](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 のリリース ノート](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 のリリース ノート](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 および 0.2 のリリース ノート](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 一般公開リリース ノート](./storsimple-virtual-array-update-06-release-notes.md)