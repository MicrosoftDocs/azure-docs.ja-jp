---
title: Microsoft Teams 内の Azure Lab Services
description: Microsoft Teams 内での Azure Lab Services の使用の概要を示します。
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433915"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Microsoft Teams 内の Azure Lab Services

Azure Lab Services は、**Azure Lab Services** Teams アプリを使用して、Microsoft Teams 内で利用できます。 ラボ アカウントの所有者/共同作成者/作成者のアクセス権を持つチームの所有者は誰でも、ラボを作成して、チームの全員に仮想マシンをプロビジョニングすることができます。

この記事では、Teams 内で Azure Lab Services を使用する利点の概要について説明します。また、チーム内でラボを作成および管理する方法に関する手順を示したその他の記事へのリンクも紹介しています。 

> [!NOTE]
>**Azure Lab Services** Teams アプリは、チームにのみ追加できます。個々のチャットまたはグループ チャットに追加することはできません。

## <a name="benefits"></a>メリット

Azure Lab Services の Microsoft Teams との統合によって、教師が教室の環境を設定して、チーム (クラス) 内での仮想ラボ環境を提供できます。 

* 教師は、学生が Teams 内から各自の VM にアクセスできるように、ラボを設定できます。これにより、Teams から離れたり、[Azure Lab Services Web サイト](https://labs.azure.com)に移動したりする必要がなくなります。
* Teams から Azure Lab Services へのシングル サインオン (SSO)。
* チームとラボの所有者は、クラス名簿を 2 つの異なるシステムに保持する必要はありません。ラボ ユーザーのリストはチーム メンバーシップから自動入力され、同期は 24 時間ごとに自動的に実行されます。 
* テンプレート VM の初期発行後、ラボの容量 (つまり、ラボ内の VM の数) は、チーム メンバーシップからのユーザーの追加/削除に基づいて自動的に調整されます。 
* チームとラボの所有者には、チームに関連するラボのみが表示されます。学生には、特定のチームに対してプロビジョニングされている VM のみが表示されます。 
* ユーザーはラボに自動的に登録され、ラボが発行された後、最初のログイン時に VM が自動的に割り当てられます。 教師は招待状を送信する必要がなく、学生はラボに個別に登録する必要はありません。  

## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。

- [作業の開始と Teams 内でのラボの作成](how-to-get-started-create-lab-within-teams.md)
- [Teams 内でのラボ ユーザー リストの管理](how-to-manage-user-lists-within-teams.md)
- [Teams 内でのラボ VM プールの管理](how-to-manage-vm-pool-within-teams.md)
- [Teams 内でのラボ スケジュールの作成と管理](how-to-create-schedules-within-teams.md)
- [Teams 内での VM へのアクセス – 学生側のビュー](how-to-access-vm-for-students-within-teams.md)
