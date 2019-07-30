---
title: クラスルーム ラボの概念 - Azure Lab Services | Microsoft Docs
description: Lab Services の基本概念と、それを使用してラボの作成および管理を容易にする方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 8bbb486b0dbf1a5e25f5ee4d1f8e5e01b999a8ba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067388"
---
# <a name="classroom-labs-concepts"></a>クラスルーム ラボの概念
次の一覧に、Lab Services の主要な概念と定義を記載しています。

## <a name="quota"></a>Quota
クォータとは、ラボ VM を使用するために先生が学生に対して設定できる時間制限 (時間単位) です。 これには、0 または特定の時間数を設定できます。 クォータが 0 に設定されている場合、学生が仮想マシンを使用できるのは、学生がその学生用の仮想マシンを実行しているとき、または先生がその仮想マシンを手動で有効にしたときのみになります。
 
## <a name="schedules"></a>スケジュール
スケジュールは、先生がクラスに対して作成できる時間スロット (1 回のみ、または繰り返し) です。 ラボ内のすべての仮想マシンは、スケジュール開始時に自動的に開始され、スケジュール終了時に停止されます。 クォータ時間は、スケジュールが実行されているときは使用されません。

## <a name="template-virtual-machine"></a>テンプレート仮想マシン
ラボ内のテンプレート仮想マシンは基本の仮想マシン イメージで、すべてのユーザーの仮想マシンはこのイメージから作成されます。 トレーナー/ラボ作成者は、テンプレート仮想マシンを設定し、ラボを実行するためにトレーニング出席者に提供するソフトウェアを使用してその仮想マシンを構成します。 テンプレート VM を発行すると、Azure Lab Services は、そのテンプレート VM に基づいてラボ VM を作成または更新します。 


## <a name="user-profiles"></a>ユーザー プロファイル
ここでは、Azure Lab Services でのさまざまなユーザー プロファイルについて説明します。 

### <a name="lab-account-owner"></a>ラボ アカウント所有者
通常は、Azure サブスクリプションを所有する組織のクラウド リソースの IT 管理者がラボ アカウント所有者として機能し、次のタスクを行います。   

- 組織用にラボ アカウントを設定します。
- すべてのラボに適用されるポリシーを管理して構成します。
- ラボ アカウントでラボを作成するアクセス許可を、組織内のユーザーに付与します。

### <a name="professor"></a>教授
通常、教師やオンライン トレーナーなどのユーザーは、ラボ アカウントでクラスルーム ラボを作成します。 教師は、次のタスクを実行します。 

- クラスルーム ラボを作成する。
- ラボに仮想マシンを作成します。 
- 適切なソフトウェアを仮想マシンにインストールします。
- ラボにアクセスできるユーザーを指定します。
- ラボへの登録リンクを学生に提供する。

### <a name="student"></a>生徒
学生は、次のタスクを実行します。

- ラボ作成者から受け取った登録リンクを使って、ラボに登録します。 
- ラボ内の仮想マシンに接続し、授業、課題、およびプロジェクトに使用します。 

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してクラスルーム ラボを作成するために必要なラボ アカウントを設定します。

- [ラボ アカウントを設定する](tutorial-setup-lab-account.md)
