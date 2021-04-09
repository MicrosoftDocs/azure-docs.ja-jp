---
author: baanders
description: Azure Digital Twins のインクルード ファイル - インスタンスを設定するための前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 3f46f7131d5465ec6542d9212e310c04a656f50c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774206"
---
この記事で Azure Digital Twins を操作するには、まず **Azure Digital Twins インスタンス** とそれを使用するために必要なアクセス許可を設定する必要があります。 以前の作業で Azure Digital Twins インスタンスが既に設定されている場合は、そのインスタンスを使用できます。

それ以外の場合は、[*インスタンスと認証を設定する方法*](../articles/digital-twins/how-to-set-up-instance-portal.md)に関するページを参照してください。 説明には、各手順が正常に完了し、新しいインスタンスを使用する準備ができていることを確認するための手順も含まれています。

Azure Digital Twins インスタンスを設定したら、後でインスタンスに接続するために必要な次の値をメモしておきます。
* インスタンスの "**ホスト名**"。 この値は、Azure portal ([[概要]](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) で確認できます。
* インスタンスの作成に使用した **Azure サブスクリプション** (名前または ID のいずれか)。 Azure Digital Twins インスタンスのサブスクリプションを確認するには、[Azure portal](https://portal.azure.com) でインスタンスの同じ *[概要]* ページを表示します。
