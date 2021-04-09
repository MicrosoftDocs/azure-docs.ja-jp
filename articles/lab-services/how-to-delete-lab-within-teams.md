---
title: Azure Lab Services ラボを Teams から削除する
description: Azure Lab Services ラボを Teams から削除する方法について説明します。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433956"
---
# <a name="delete-labs-within-teams"></a>Teams 内でラボを削除する

この記事では、**Azure Lab Services** アプリからラボを削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure portal で [Lab Services アカウントを作成](tutorial-setup-lab-account.md#create-a-lab-account)します。
* [Teams 内で作業を開始して Lab Services ラボを作成します](how-to-get-started-create-lab-within-teams.md)。

## <a name="delete-labs"></a>ラボの削除

Teams に作成したラボは、「[Azure Lab Services でラボを管理する](how-to-manage-classroom-labs.md)」の説明に従い、[Lab Services の Web サイト](https://labs.azure.com)で削除できます。 

チームを削除する場合にも、ラボの削除がトリガーされます。 ラボが作成されているチームが削除されると、24 時間後にユーザー一覧の同期が自動でトリガーされるとき、ラボが自動的に削除されます。 

> [!IMPORTANT]
> タブの削除またはアプリのアンインストールで、ラボが削除されることはありません。 

タブが削除された場合でも、Web サイトでラボを削除したり、チームを削除したりすることによって、ラボの削除が明示的にトリガーされていない限り、チーム メンバーシップ リスト上のユーザーは、[Lab Services の Web サイト](https://labs.azure.com)の VM にアクセスできます。 

## <a name="next-steps"></a>次の手順

- [Teams 内での Azure Lab Services の使用の概要](lab-services-within-teams-overview.md)
- [Teams 内でのラボ ユーザー リストの管理](how-to-manage-user-lists-within-teams.md)
- [Teams 内でのラボ VM プールの管理](how-to-manage-vm-pool-within-teams.md)
- [Teams 内でのラボ スケジュールの作成と管理](how-to-create-schedules-within-teams.md)
- [Teams 内での VM へのアクセス – 学生側のビュー](how-to-access-vm-for-students-within-teams.md)

