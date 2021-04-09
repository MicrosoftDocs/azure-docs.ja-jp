---
title: FAQ - ローカル一時ディスクを持たない Azure VM のサイズ
description: この記事では、ローカル一時ディスクを持たない Microsoft Azure VM のサイズについてよく寄せられる質問 (FAQ) に対する回答を示します。
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.subservice: sizes
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: bd4dcbdc7ab13d18ef7f2d7102c56d1bd8d8758d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582104"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>ローカル一時ディスクを持たない Azure VM のサイズ 
この記事では、ローカル一時ディスクを持たない Azure VM のサイズについてよく寄せられる質問 (FAQ) に対する回答を示します。 これらの VM サイズの詳細については、[Dv4 および Dsv4 シリーズの仕様 (汎用ワークロード)](dv4-dsv4-series.md) または [Ev4 および Esv4 シリーズの仕様 (メモリ最適化ワークロード)](ev4-esv4-series.md) に関する記事を参照してください。

## <a name="what-does-no-local-temp-disk-mean"></a>ローカル一時ディスクがないとは何を意味しますか。 
従来は、小規模なローカル ディスクを含む VM サイズ (Standard_D2s_v3、Standard_E48_v3 など) がありました (D: ドライブなど)。 これらの新しい VM サイズでは、小規模なローカル ディスクは存在しなくなりました。ただし、依然として Standard HDD、Premium SSD または Ultra SSD を接続することもできます。

## <a name="what-if-i-still-want-a-local-temp-disk"></a>それでもローカル一時ディスクが必要な場合はどうすればよいですか。
ワークロードにローカル一時ディスクが必要な場合は、新しい [Ddv4 と Ddsv4](ddv4-ddsv4-series.md) または [Edv4 と Edsv4](edv4-edsv4-series.md) の VM サイズを使用することもできます。 これらのサイズは、前の v3 サイズと比較して 50% 大きい一時ディスクを提供します。

> [!NOTE]
> ローカル一時ディスクは永続的ではありません。データが永続的であるようにするには、Standard HDD、Premium SSD または Ultra SSD オプションを使用してください。 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>これらの新しい VM サイズと、以前の汎用の Dv3/Dsv3 またはメモリ最適化 Ev3/Esv3 の VM サイズの違いは何ですか。 
| ローカル一時ディスクを使用する v3 VM ファミリ   | ローカル一時ディスクを使用する新しい v4 ファミリ | ローカル一時ディスクのない新しい v4 ファミリ |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>ローカル一時ディスクを持つ VM サイズを、ローカル一時ディスクのない VM サイズにサイズ変更できますか。  
いいえ。 サイズ変更に使用できる組み合わせは以下に限られます。 

1. VM (ローカル一時ディスクあり) -> VM (ローカル一時ディスクあり) 
2. VM (ローカル一時ディスクなし) -> VM (ローカル一時ディスクなし) 

回避策に関心がある場合は、次の質問を参照してください。

> [!NOTE]
> イメージがリソース ディスクに依存している場合、またはページファイルやスワップファイルがローカル一時ディスクに存在する場合、ディスクレス イメージは機能しません。代わりに、"ディスクあり" の代替手段を使用します。 

## <a name="how-do-i-migrate-from-a-vm-size-with-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>ローカル一時ディスクを持つ VM サイズを、ローカル一時ディスクのない VM サイズに移行する方法を教えてください。  
次の手順を実行することで移行できます。 

1. ローカルの一時ディスク (D: ドライブなど) を持つ仮想マシンにローカル管理者として接続します。
2. 「[Windows VM のデータ ドライブとしての D: ドライブの使用](./windows/change-drive-letter.md)」の「一時的に pagefile.sys を C ドライブに移動します。」セクションのガイドラインに従って、ページ ファイルをローカル一時ディスク (D: ドライブ) から C: ドライブに移動します。

   > [!NOTE]
   > 「Windows VM のデータ ドライブとしての D: ドライブの使用」の「一時的に pagefile.sys を C ドライブに移動します。」セクションのガイドラインに従って、ページ ファイルをローカル一時ディスク (D: ドライブ) から C: ドライブに移動します。 **ここに記載されている手順から逸脱すると、"Unable to resize the VM since changing from resource disk to non-resource disk VM size and vice-versa is not allowed." (リソース ディスクからリソース ディスク以外の VM サイズへの変更またはその逆は許可されていないため、VM のサイズを変更できません) というエラー メッセージが表示されます。**

3. 「[ポータルまたは Azure CLI を使用してスナップショットを作成する](./linux/snapshot-copy-managed-disk.md)」に記載されている手順に従って、VM のスナップショットを作成します。 
4. スナップショットを使用して、「[CLI でスナップショットから仮想マシンを作成する](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot)」で説明されている手順に従って、新しいディスクレス VM (Dv4、Dsv4、Ev4、Esv4 シリーズなど) を作成します。 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>これらの VM サイズは Linux と Windows の両方のオペレーティング システム (OS) をサポートしますか。
はい。

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>このことによって、ローカル一時ディスク上にスクラッチ ファイルまたはページ ファイルがあるカスタム スクリプト、カスタム イメージ、または OS イメージが中断されますか。
カスタム OS イメージがローカル一時ディスクを指している場合、イメージはこのディスクなしのサイズで正しく動作しない可能性があります。

## <a name="have-questions-or-feedback"></a>質問やフィードバックがある場合や
[フィードバック フォーム]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u)に記入してください。 

## <a name="next-steps"></a>次のステップ 
このドキュメントでは、ローカル一時ディスクのない Azure VM に関して最もよく寄せられるご質問について説明しました。 これらの VM サイズの詳細については、次の記事を参照してください。

- [Dv4 および Dsv4 シリーズの仕様 (汎用ワークロード)](dv4-dsv4-series.md)
- [Ev4 および Esv4 シリーズの仕様 (メモリ最適化ワークロード)](ev4-esv4-series.md)