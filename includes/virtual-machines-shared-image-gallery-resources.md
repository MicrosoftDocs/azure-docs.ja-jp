---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bf01df2307796831dee602dad30455a4922ef90b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98873190"
---
| リソース | 説明|
|----------|------------|
| **イメージのソース** | これは、イメージ ギャラリーに **イメージ バージョン** を作成するために使用できるリソースです。 イメージのソースには、[一般化または特殊化された](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)既存の Azure VM、マネージド イメージ、スナップショット、または別のイメージ ギャラリー内のイメージ バージョンを使用できます。 |
| **イメージ ギャラリー** | Azure Marketplace などの **イメージ ギャラリー** は、イメージを管理して共有するためのリポジトリです。ただし、アクセス権の所有者を制御します。 |
| **イメージ定義** | イメージ定義はギャラリー内に作成され、内部で使用するためにイメージと要件に関する情報を伝達します。 この情報には、イメージが Windows または Linux のどちらか、リリース ノート、および最小と最大のメモリ要件が含まれます。 これは、イメージの種類の定義です。 |
| **イメージ バージョン** | **イメージ バージョン** は、ギャラリーを利用している場合に、VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 マネージド イメージのように、**イメージ バージョン** を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。 |