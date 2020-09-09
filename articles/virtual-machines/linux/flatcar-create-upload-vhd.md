---
title: Azure で使用するための Flatcar Container Linux VHD の作成とアップロード
description: Flatcar Container Linux オペレーティング システムを格納した VHD を作成してアップロードする方法について説明します。
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 555e53899ed78a5200009d04659e974f8157057e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268241"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Azure 用の事前構築済みの Flatcar イメージを使用する

Flatcar でサポートされているチャネルごとに、Flatcar Container Linux の事前構築済み Azure 仮想ハードディスク イメージをダウンロードできます。

- [stable](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

このイメージは、Azure 上で実行できるように既に完全に設定および最適化されています。 お客様は圧縮を解除すればよいだけです。

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Azure 用に独自の Flatcar ベースの仮想マシンを構築する

別の方法として、お客様は独自の Flatcar Container Linux イメージを構築することもできます。

任意の Linux ベースのコンピューター上で、「[Flatcar Container Linux developer SDK guide](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)」(Flatcar Container Linux 開発者 SDK ガイド) に詳述されている手順に従います。 `image_to_vm.sh` スクリプトを実行する場合は、必ず `--format=azure` を渡して Azure 仮想ハード ディスクを作成してください。

## <a name="next-steps"></a>次のステップ

.vhd ファイルを用意したら、結果として生成されたファイルを使用して Azure に新しい仮想マシンを作成できます。 .vhd ファイルを Azure に初めてアップロードする場合は、[カスタム ディスクからの Linux VM の作成](upload-vhd.md#option-1-upload-a-vhd)に関するページを参照してください。
