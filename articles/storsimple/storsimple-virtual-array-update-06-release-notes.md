---
title: StorSimple Virtual Array Update 0.6 のリリース ノート | Microsoft Docs
description: Update 0.6 を実行する StorSimple Virtual Array の未解決の重大な問題と解決方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000914"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple Virtual Array Update 0.6 のリリース ノート

## <a name="overview"></a>概要

このリリース ノートには、Microsoft Azure StorSimple Virtual Array 更新プログラムのリリースに関する未解決の重大な問題と解決済みの問題が示されています。

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 StorSimple Virtual Array をデプロイする前に、リリース ノートに含まれる情報を十分に確認してください。

Update 0.6 はソフトウェア バージョン **10.0.10293.0** に対応しています。

> [!IMPORTANT]
> - 更新プログラムは中断を伴い、デバイスを再起動します。 I/O が進行中である場合は、デバイスにダウンタイムが発生します。 更新プログラムの適用方法に関する詳細な手順については、[Update 0.6 のインストール](storsimple-virtual-array-install-update-06.md)に関するページをご覧ください。
>
> - 重要なセキュリティ修正プログラムを含む Update 0.6 をすぐにインストールすることを強くお勧めします。


## <a name="whats-new-in-the-update-06"></a>Update 0.6 の新機能
Update 0.6 は重要な更新プログラムであるため、すぐにデプロイする必要があります。 この更新プログラムには、次の修正プログラムが含まれています。 

- **Windows セキュリティ修正プログラム** - このリリースには **Windows の重要なセキュリティ上の修正** があります。 セキュリティの問題と関連する修正プログラムの詳細については、次のセキュリティ更新プログラムを確認します。
    - [2016 年 12 月 Windows 8.1 および Windows Server 2012 R2 用のセキュリティのみ品質更新プログラム](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [2017 年 3 月 Windows 8.1 および Windows Server 2012 R2 用のセキュリティのみ品質更新プログラム](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [2017 年 5 月 9 日 - KB4019213 (セキュリティ専用の更新プログラム)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **復元の修正プログラム** - 以前のリリースで、復元の完了を妨げるバグが発生しました。 このバグは今回のリリースで修正されています。


## <a name="issues-fixed-in-the-update-06"></a>Update 0.6 で修正された問題

次の表に、このリリースで修正された問題の概要を示します。

| いいえ。 | 特徴量 | 問題 |
| --- | --- | --- |
| 1 |セキュリティ| このリリースには、重要なWindows セキュリティ更新プログラムが含まれています。 この更新プログラムをすぐにインストールすることをお勧めします。|
| 2 |復元| 復元中に、復元ジョブを完了できない競合状態が発生しました。 このバグ修正では、この競合状態に対処します。|


## <a name="known-issues-in-the-update-06"></a>Update 0.6 の既知の問題

次の表では、StorSimple Virtual Array の既知の問題の概要と、以前のリリースのリリース ノートに記載されていた問題を説明します。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
| **1.** |更新プログラム |プレビュー リリースで作成された仮想デバイスは、サポートされている一般提供版に更新することはできません。 |これらの仮想デバイスは、障害復旧 (DR) ワークフローを使用して一般提供リリースにフェールオーバーする必要があります。 |
| **2.** |プロビジョニング済みのデータ ディスク |特定の指定されたサイズのデータ ディスクをプロビジョニングし、対応する StorSimple 仮想デバイスを作成した後は、データ ディスクを拡張または縮小することはできません。 これを行おうとすると、デバイスのローカル層のすべてのデータが失われます。 | |
| **3.** |グループ ポリシー |デバイスがドメインに参加しているときに、グループ ポリシーを適用すると、デバイスの動作に悪影響を及ぼすことがあります。 |仮想アレイが Active Directory の独自の組織単位 (OU) にあり、グループ ポリシー オブジェクト (GPO) が適用されていないことを確認します。 |
| **4.** |ローカル Web UI |Internet Explorer で拡張セキュリティ機能 (IE ESC) を有効にすると、[Troubleshooting] や [Maintenance] など、ローカル Web UI の一部のページが正しく動作しないことがあります。 これらのページ上のボタンも動作しないことがあります。 |Internet Explorer で拡張セキュリティ機能を無効にします。 |
| **5.** |ローカル Web UI |Hyper-V 仮想マシンでは、Web UI のネットワーク インターフェイスは 10 Gbps のインターフェイスとして表示されます。 |この動作には、Hyper-V の動作が反映されています。 Hyper-V では、仮想ネットワーク アダプターに対して常に 10 Gbps と表示されます。 |
| **6.** |階層化ボリューム/共有 |StorSimple の階層化ボリュームを使用するアプリケーションのバイト範囲ロックはサポートされていません。 バイト範囲ロックを有効にすると、StorSimple の階層化が機能しなくなります。 |推奨される方法は次のとおりです。 <br></br>アプリケーション ロジックでバイト範囲ロックを無効にします。<br></br>階層化ボリュームではなく、ローカル固定ボリュームにこのアプリケーションのデータを配置します。<br></br>"*注意*": ローカル固定ボリュームを使用しているときにバイト範囲ロックを有効にすると、復元が完了する前でも、ローカル固定ボリュームがオンラインになる場合があります。 このような場合、復元中であれば、復元が完了するまで待つ必要があります。 |
| **7.** |階層化共有 |複数の大きなファイルを処理すると、階層化に時間がかかる可能性があります。 |複数の大きなファイルを処理するときは、最も大きなファイルのサイズを共有のサイズの 3% 未満にすることをお勧めします。 |
| **8.** |共有の使用済み容量 |共有にデータがなくても、共有の使用量が表示されることがあります。 この使用量は、共有の使用済み容量にメタデータが含まれているためです。 | |
| **9.** |障害復旧 |ファイル サーバーの障害復旧はソース デバイスと同じドメインに対してのみ実行できます。 このリリースでは、別のドメインのターゲット デバイスに対する障害復旧はサポートされていません。 |これは今後のリリースで実装されます。 詳細については、[StorSimple Virtual Array のフェールオーバーと障害復旧](storsimple-virtual-array-failover-dr.md)に関するページをご覧ください。 |
| **10.** |Azure PowerShell |このリリースでは、Azure PowerShell を使用して StorSimple 仮想デバイスを管理することはできません。 |仮想デバイスの管理はすべて、Azure Portal とローカル Web UI を使用して行う必要があります。 |
| **11.** |パスワードの変更 |仮想アレイ デバイス コンソールは、en-US キーボード形式の入力のみを受け入れます。 | |
| **12.** |CHAP |作成された CHAP 資格情報は、削除できません。 また、CHAP 資格情報を変更する場合は、変更を有効にするために、ボリュームをオフラインにしてからオンラインにする必要があります。 |この問題は今後のリリースで対処されます。 |
| **13.** |iSCSI サーバー |iSCSI ボリュームに対して表示される '使用済みストレージ' は、StorSimple デバイス マネージャー サービスと iSCSI ホストで異なる場合があります。 |iSCSI ホストにはファイル システム ビューがあります。<br></br>デバイスでは、ボリュームが最大サイズのときに割り当てられるブロックを確認できます。 |
| **14.** |ファイル サーバー |フォルダー内のファイルが代替データ ストリーム (ADS) に関連付けられている場合、ADS のバックアップまたは復元には、障害復旧、複製、および項目レベルの回復が使用されません。 | |
| **15.** |ファイル サーバー |シンボリック リンクはサポートされていません。 | |
| **16.** |ファイル サーバー |Windows 暗号化ファイル システム (EFS) で保護されているファイルを StorSimple Virtual Array のファイル サーバーにコピーまたは保存すると、サポートされていない構成になります。  | |
| **17.** |更新プログラム |ローカルの UI で修正プログラムをインストールしようとしてエラー コード: 2359302 (16 進数 0x240006) が表示される場合は、修正プログラムがデバイスに既にインストールされていることを示しています。   | |

## <a name="next-step"></a>次のステップ
StorSimple Virtual Array に [Update 0.6 をインストール](storsimple-virtual-array-install-update-06.md)します。

## <a name="references"></a>リファレンス
以前のリリース ノートをお探しですか。 参照先:

* [StorSimple Virtual Array Update 0.5 のリリース ノート](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 のリリース ノート](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 のリリース ノート](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 および 0.2 のリリース ノート](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 一般公開リリース ノート]()