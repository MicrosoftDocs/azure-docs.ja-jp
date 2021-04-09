---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: e1d4d29f8edca87ec1cca0ffced7b3e1bca90717
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99808483"
---
## <a name="create-the-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成

このセクションでは、仮想ネットワークとサブネットを作成します。

1. 画面の左上で、 **[リソースの作成] > [ネットワーク] > [仮想ネットワーク]** の順に選択するか、検索ボックスで **Virtual network** を検索します。

2. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | Azure サブスクリプションを選択します。                                  |
    | リソース グループ   | **[新規作成]** を選択し、「 **\<resource-group-name>** 」と入力して [OK] を選択するか、パラメーターに基づいて既存の **\<resource-group-name>** を選択します。 |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 「 **\<virtual-network-name>** 」と入力します。                                    |
    | リージョン           | [ **] \<region-name> [** ] を選択します。 |

3. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

4. **[IP アドレス]** タブで、次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | IPv4 アドレス空間 | 「 **\<IPv4-address-space>** 」と入力します。 |

5. **[サブネット名]** で、 **[default]\(既定\)** という単語を選択します。

6. **[サブネットの編集]** に次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | サブネット名 | 「 **\<subnet-name>** 」と入力します。 |
    | サブネットのアドレス範囲 | 「 **\<subnet-address-range>** 」と入力します。

7. **[保存]** を選択します。

8. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

9. **［作成］** を選択します