---
title: トレーニング用のクラスルーム ラボを使用する - Azure Lab Services| Microsoft Docs
description: トレーニングの各種シナリオを実現するために Azure DevTest Labs を使用する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56809425"
---
# <a name="use-classroom-labs-for-trainings"></a>トレーニング用のクラスルーム ラボを使用する
トレーニング用のラボを設定することができます。 Azure Lab Services のクラスルーム ラボでは、各受講者がトレーニングで同じ分離環境を使用する、トレーニング用のラボを作成することができます。 各受講者が必要としている場合にのみトレーニング環境を利用できるようにし、トレーニング環境にトレーニングに必要なリソース (仮想マシンなど) が十分に含まれるようにするポリシーを適用できます。 

![クラスルーム ラボ](../media/classroom-labs-scenarios/classroom.png)

クラスルーム ラボは、任意の仮想環境でトレーニングを行うために必要な、次の要件を満たしています。 

- 受講者が各自のトレーニング環境を迅速にプロビジョニングできる。
- すべてのトレーニング マシンが同じである。
- 受講者は、他の受講者によって作成された VM を見ることができない。
- 受講者がトレーニングに必要な数以上の VM を使用できないようにし、コストを管理できる。また、受講者が使用していない VM をシャットダウンできる。
- トレーニング ラボを各受講者と簡単に共有できる。
- トレーニング ラボを繰り返し再利用できる。

この記事では、先ほど説明したトレーニング要件を満たすために使用できる各種 Azure Lab Services 機能のほか、トレーニング用のラボを設定するために使用できる詳細な手順について学習します。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>ラボ アカウント管理者としてラボ アカウントを作成する
Azure Lab Services を使用する最初の手順は、Azure portal でラボ アカウントを作成することです。 ラボ アカウント管理者は、ラボ アカウントを作成した後、**ラボ作成者**ロールにラボを作成するユーザーを追加します。 トレーナーは、指導するコースの演習を学生が行うための仮想マシンを含むラボを作成します。 詳細については、[ラボ アカウントの作成と管理](how-to-manage-lab-accounts.md)に関するページを参照してください。

## <a name="create-and-manage-classroom-labs"></a>クラスルーム ラボを作成して管理する
ラボ アカウントのラボ作成者ロールのメンバーであるトレーナーは、ラボ アカウントで 1 つまたは複数のラボを作成できます。 コースで演習を行うために必要なすべてのソフトウェアを含むテンプレート VM を作成して構成します。 クラスルーム ラボを作成するために使用できるイメージから既存のイメージを選び、ラボで必要なソフトウェアをインストールして、それをカスタマイズします。 詳細については、[クラスルーム ラボの作成と管理](how-to-manage-classroom-labs.md)に関するページを参照してください。

## <a name="configure-usage-settings-and-policies"></a>使用設定とポリシーを構成する
ラボ作成者はラボにおけるユーザーの追加や削除、ラボ ユーザーに送信する登録リンクの取得、ユーザーごとの個別のクォータの設定などに関するポリシーの設定、ラボで使用できる VM の数の更新などを行うことができます。 詳細については、「[使用設定とポリシーを構成する](how-to-configure-student-usage.md)」を参照してください。

## <a name="create-and-manage-schedules"></a>スケジュールの作成と管理
スケジュールを使用すると、ラボの VM が指定した時刻に自動的に起動およびシャットダウンされるように、クラスルーム ラボを構成できます。 1 回限りのスケジュールや定期的なスケジュールを定義することができます。 詳細については、[クラスルーム ラボのスケジュールの作成と管理](how-to-create-schedules.md)に関するページを参照してください。

## <a name="set-up-and-publish-a-template-vm"></a>テンプレート VM を設定して発行する
ラボ内のテンプレートは仮想マシンの基本イメージで、すべてのユーザーの仮想マシンがこのイメージに基づいて作成されます。 テンプレート VM を設定して、トレーニングの参加者に提供する正しい VM が構成されるようにします。 ラボ ユーザーに表示されるテンプレートの名前と説明を指定できます。 その後、テンプレートを発行し、ラボ ユーザーがテンプレート VM のインスタンスを利用できるようにします。 テンプレートを発行すると、Azure Lab Services がそのテンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。 詳細については、[テンプレート仮想マシンの設定と発行](how-to-create-manage-template.md)に関するページを参照してください。 

## <a name="use-vms-in-the-classroom-lab"></a>クラスルーム ラボで VM を使用する
学生またはトレーニングの出席者はラボに登録し、VM に接続してコースの演習を行います。 詳細については、[クラスルーム ラボへのアクセス方法](how-to-use-classroom-lab.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
まず、クラスルーム ラボでラボ アカウントを作成します。その場合、「[チュートリアル:Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」という記事の手順に従います。