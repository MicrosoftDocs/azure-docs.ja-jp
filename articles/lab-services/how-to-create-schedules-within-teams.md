---
title: Teams 内で Azure Lab Services のスケジュールを作成する
description: Teams 内で Lab Services のスケジュールを作成する方法を学習します。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92042339"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Teams 内で Lab Services のスケジュールを作成および管理する

スケジュールを使用すると、ラボの VM が指定した時刻に自動的に起動およびシャットダウンされるように、クラスルーム ラボを構成できます。 1 回限りのスケジュールや定期的なスケジュールを定義することができます。 クラスルーム ラボのスケジュールを作成および管理する手順を以下に示します。 

スケジュールがラボ仮想マシンに与える影響を説明します。 

- テンプレート仮想マシンはスケジュールに含まれていません。 
- 割り当てられた仮想マシンのみが起動します。 つまり、エンド ユーザー (学生) が要求していないマシンは、スケジュールされた時間に起動しません。 
- 仮想マシンはすべて（ユーザーがいるかどうかに関わらず）、ラボのスケジュールに基づいて停止されます。 

> [!IMPORTANT]
> スケジュールされている VM の実行時間は、ユーザーに割り当てられるクォータにカウントされません。 クォータは、学生が VM で消費することをスケジュールされている時間以外の時間です。 

ユーザーは、[ラボの Web サイト](https://labs.azure.com)の場合と同様に、Teams 内でのラボ スケジュールの作成、編集、削除を行うことができます。 [スケジュールの作成と管理](how-to-create-schedules-within-teams.md)に関する記事を参照してください。

## <a name="automatic-shutdown-and-disconnect-settings"></a>自動シャットダウンと切断の設定

自動シャットダウンによる複数のコスト制御機能を有効にして、仮想マシンがアクティブに使用されていないときの追加コストを事前に防ぐことができます。 次の 3 つの自動シャットダウンおよび切断機能を組み合わせると、ユーザーが誤って仮想マシンを実行状態のまま放置するケースのほとんどを捕捉できます。
 
- OS によってアイドル状態と見なされた仮想マシンから、ユーザーを自動的に切断する。
- ユーザーが切断したときに、仮想マシンを自動的にシャットダウンする。
- 起動したがユーザーが接続しない仮想マシンを自動的にシャットダウンする。

詳細については、[ラボの自動シャットダウン設定の構成](how-to-enable-shutdown-disconnect.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。

- [Teams 内での Azure Lab Services の使用の概要](lab-services-within-teams-overview.md)
- [作業の開始と Teams 内でのラボの作成](how-to-get-started-create-lab-within-teams.md)
- [Teams 内でのラボ ユーザー リストの管理](how-to-manage-user-lists-within-teams.md)
- [Teams 内でのラボ VM プールの管理](how-to-manage-vm-pool-within-teams.md)
- [Teams 内での VM へのアクセス – 学生側のビュー](how-to-access-vm-for-students-within-teams.md)
