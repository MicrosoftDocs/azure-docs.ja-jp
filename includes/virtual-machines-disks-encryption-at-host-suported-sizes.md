---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86230992"
---
最新世代のすべての VM サイズで、ホストでの暗号化がサポートされています。

|Type  |サポートされていません  |サポートされています  |
|---------|---------|---------|
|汎用     | Dv3、Dav4、Dv2、Av2        | B、DSv2、Dsv3、DC、DCv2、Dasv4        |
|コンピューティング最適化     |         | Fsv2        |
|メモリ最適化     | Ev3、Eav4        | DSv2、Esv3、M、Mv2、Easv4        |
|ストレージ最適化     |         | Ls、Lsv2 (NVMe ディスクは暗号化されていません)        |
|GPU     | NC、NV        | NCv2、NCv3、ND、NVv3、NVv4、NDv2 (プレビュー)        |
|ハイ パフォーマンス コンピューティング     | H        | HB、HC、HBv2        |
|旧世代     | F、A、D、L、G        | DS、GS、Fs、NVv2        |

VM サイズをアップグレードすると、新しい VM サイズで EncryptionAtHost 機能がサポートされているかどうかが確認されます。
