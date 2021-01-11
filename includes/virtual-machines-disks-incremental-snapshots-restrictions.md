---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102791"
---
- 増分スナップショットは、現時点ではサブスクリプション間で移動できません。
- 現時点では、任意の時点で特定のスナップショット ファミリの最大 5 つのスナップショットの SAS URI のみを生成することができます。
- 特定のディスクの増分スナップショットを、そのディスクのサブスクリプションの外部で作成することはできません。
- ディスクあたり最大 7 つの増分スナップショットを 5 分ごとに作成できます。
- 1 つのディスクに対して合計 200 の増分スナップショットを作成できます。
- 4 TB の境界をまたいで親ディスクのサイズを変更する際、変更の前と後に作成されたスナップショットの間の変更を取得することはできません。 たとえば、ディスクのサイズが 2 TB のとき、増分スナップショット snapshot-a を作成したとします。 ここで、ディスクのサイズを 6 TB に増加させ、別の増分スナップショット snapshot-b を作成したとします。 snapshot-a と snapshot-b の間の変更を取得することはできません。 サイズ変更後に作成された snapshot-b の完全なコピーを再度ダウンロードする必要があります。 その後、snapshot-b と、snapshot-b の後に作成されたスナップショットの間の変更を取得することができます。 
