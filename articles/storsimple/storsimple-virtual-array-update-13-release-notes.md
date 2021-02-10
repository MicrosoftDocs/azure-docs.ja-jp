---
title: Microsoft Azure StorSimple Virtual Array Update 1.3 のリリース ノート | Microsoft Docs
description: Update 1.3 を実行する Azure StorSimple Virtual Array に関する未解決の重大な問題と解決方法について説明します。
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 02611bdf9689d2f62f661f558fd547ea46bd4d36
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744130"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array Update 1.3 のリリース ノート

このリリース ノートには、Microsoft Azure StorSimple Virtual Array 更新プログラムのリリースに関する未解決の重大な問題と解決済みの問題が示されています。

リリース ノートは、随時更新されます。 回避策を必要とする重大な問題が見つかった場合は、追加されます。 StorSimple Virtual Array をデプロイする前に、リリース ノートに含まれる情報を十分に確認してください。

Update 1.3 はソフトウェア バージョン 10.0.10319.0 に対応しています。

> [!IMPORTANT]
> - Update 1.3 は重要な更新プログラムです。 できるだけ早くインストールすることを強くお勧めします。
> - Update 1.3 は、Update 1.2 を実行しているデバイスにのみインストールできます。
> - 更新プログラムは中断を伴い、デバイスを再起動します。 I/O が進行中である場合は、デバイスにダウンタイムが発生します。 この更新プログラムを適用するために使用されるパッケージに関する詳細な手順については、「[Update 1.3 のダウンロード](#download-update-13)」に進んでください。

## <a name="whats-new-in-update-13"></a>Update 1.3 の新機能

この更新プログラムには、以下に示す機能強化が含まれています。

- トランスポート層セキュリティ (TLS) 1.2 は必須の更新プログラムであり、インストールする必要があります。 今回のリリースから、TLS 1.2 はすべての Azure portal 通信の標準プロトコルになります。
- ガベージ コレクションのバグ修正によって、デバイスとストレージ アカウントが 2 つの離れたリージョンにあるときに、ガベージ コレクション サイクルのパフォーマンスが向上します。
- BLOB タイムアウトによるバックアップ エラーを修正します。
- 更新された OS/.NET Framework のセキュリティ パッチ。
  - [KB4540725](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4540725\V1.001\free\NEU\X64):2020 年 3 月の SSU (サービス スタック更新プログラム)
  - [KB4565541](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4565541\V1.014\free\NEU\X64):2020 年 7 月のロールアップ
  - [KB4565622](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Partner\DOTNET47x\KB4565622\V1.000\free\NEU\x64):2020 年 7 月の .NET Framework 更新プログラム

## <a name="download-update-13"></a>Update 1.3 のダウンロード

この更新プログラムをダウンロードするには、[Microsoft Update カタログ](https://www.catalog.update.microsoft.com/Home.aspx)のサーバーへ移動して、KB4575898 パッケージをダウンロードします。 このパッケージには、次のパッケージが含まれています。

- 2012 R2 から 2020 年 3 月までの累積的な Windows Update が含まれる **KB4540725**。 このロールアップに含まれる内容の詳細については、[3 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4540725)に関するページをご覧ください。
- 2012 R2 から 2020 年 7 月までの累積的な Windows Update が含まれる **KB4565541**。 このロールアップに含まれる内容の詳細については、[2 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4565541)に関するページをご覧ください。
- 2020 年 7 月までの累積的な .NET Framework の更新プログラムが含まれる **KB4565622**。 このロールアップに含まれる内容の詳細については、[2 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4565622)に関するページをご覧ください。
- デバイス ソフトウェアの更新プログラムが含まれる **KB3011067**。

KB4575898 をダウンロードして、[ローカル Web UI を使用して更新プログラムを適用する](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)ための手順に従います。

## <a name="known-issues-in-update-13"></a>Update 1.3 の既知の問題
Update 1.3 では、新しくリリース ノートに記載された問題はありませんでした。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 以前のリリースから含まれている既知の問題の概要を確認するには、[Update 1.2 での既知の問題](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)に関する記事に進んでください。

## <a name="next-steps"></a>次のステップ
KB4575898 をダウンロードして、[ローカル Web UI を使用して更新プログラムを適用](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)します。

## <a name="references"></a>リファレンス
以前のリリース ノートをお探しですか。 参照先:

- [StorSimple Virtual Array Update 1.2 のリリース ノート](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1.0 のリリース ノート](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0.6 のリリース ノート](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0.5 のリリース ノート](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0.4 のリリース ノート](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0.3 のリリース ノート](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0.1 および 0.2 のリリース ノート](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Virtual Array 一般公開リリース ノート](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)