---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 のリリース ノート | Microsoft Docs
description: Update 1.2 を実行する StorSimple Virtual Array に関する未解決の重大な問題と解決方法について説明します。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960192"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1.2 のリリース ノート

このリリース ノートには、Microsoft Azure StorSimple Virtual Array 更新プログラムのリリースに関する未解決の重大な問題と解決済みの問題が示されています。

リリース ノートは、随時更新されます。 回避策を必要とする重大な問題が見つかった場合は、追加されます。 StorSimple Virtual Array をデプロイする前に、リリース ノートに含まれる情報を十分に確認してください。

Update 1.2 はソフトウェア バージョン **10.0.10311.0** に対応しています。

> [!IMPORTANT]
> - 更新プログラムは中断を伴い、デバイスを再起動します。 I/O が進行中である場合は、デバイスにダウンタイムが発生します。 この更新プログラムを適用するために使用されるパッケージに関する詳細な手順については、「[Update 1.2 のダウンロード](#download-update-12)」に進んでください。
> - Update 1.2 は、デバイス上で Update 1.0 または 1.1 が実行されている場合にのみ、Azure portal 経由で入手できます。

## <a name="whats-new-in-update-12"></a>Update 1.2 の新機能

この更新プログラムには、以下に示すバグの修正が含まれています。

- 削除されたファイルを処理するときの回復性が改善されました。
- コードのスタートアップ パスでのエラー処理が改善され、バックアップ、復元、クラウドでの読み込み、自動化された領域回復でのエラーが減少しました。

## <a name="download-update-12"></a>Update 1.2 のダウンロード

この更新プログラムをダウンロードするには、[Microsoft Update カタログ](https://www.catalog.update.microsoft.com/Home.aspx)のサーバーへ移動して、KB4502035 パッケージをダウンロードします。 このパッケージには、次のパッケージが含まれています。

 - 2012 R2 から 2019 年 4 月までの累積的な Windows Update が含まれる **KB4493446**。 このロールアップに含まれる内容の詳細については、[4 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)に関するページをご覧ください。
 - Microsoft Update スタンドアロン パッケージ ファイルの WindowsTH-KB3011067-x64 である **KB3011067**。 このファイルは、デバイス ソフトウェアを更新するために使用されます。

KB4502035 をダウンロードして、[ローカル Web UI を使用して更新プログラムを適用する](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)ための手順に従います。

## <a name="issues-fixed-in-update-12"></a>Update 1.2 で修正された問題

次の表に、このリリースで修正された問題の概要を示します。

| いいえ。 | 特徴量 | 問題 |
| --- | --- | --- |
| 1 |削除| 以前のバージョンのソフトウェアでは、ファイルが削除されてもデバイスの使用率に変化がない場合に問題が発生していました。 この問題は、今回のバージョンで修正されました。 削除されたファイルを処理するときの、コード パスの階層化での回復性が向上しました。|
| 2 |例外処理| 以前のバージョンのソフトウェアでは、バックアップ、復元、クラウドからの読み込み、自動化された領域回復において潜在的にエラーを引き起こす可能性がある、システム リブートに伴う問題が発生していました。 今回のリリースには、スタートアップ パス内での例外の処理方法に関する変更が含まれています。|

## <a name="known-issues-in-update-12"></a>Update 1.2 の既知の問題

Update 1.2 では、新しくリリース ノートに記載された問題はありませんでした。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 以前のリリースから含まれている既知の問題の概要を確認するには、[Update 1.1 での既知の問題](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)に関する記事に進んでください。

## <a name="next-steps"></a>次のステップ

KB4502035 をダウンロードして、[ローカル Web UI を使用して更新プログラムを適用](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)します。

## <a name="references"></a>リファレンス

以前のリリース ノートをお探しですか。 参照先:
* [StorSimple Virtual Array Update 1.1 のリリース ノート](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 のリリース ノート](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 のリリース ノート](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 のリリース ノート](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 のリリース ノート](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 のリリース ノート](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 および 0.2 のリリース ノート](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 一般公開リリース ノート](./storsimple-virtual-array-update-06-release-notes.md)