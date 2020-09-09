---
title: Azure Notebooks で使用するユーザー プロファイルと ID のプレビュー
description: Azure Notebooks でユーザー プロファイルとユーザー ID (共有ノートブックの URL の一部となる) を作成して管理する方法。
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 9a1ff7f92faec21f537f068f0a33473700ddfed8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831354"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Azure Notebooks のプロファイルとユーザー ID のプレビュー

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

機能豊富な共同作業領域の Azure Notebooks 内では、自分のユーザー プロファイルに他のユーザーへのパブリック イメージが示されます。

[![Azure Notebooks プロファイル ページ](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

ユーザー ID は、プロジェクトとノートブックの共有に使用する URL の一部です。 次の一覧はさまざまな URL パターンを示しています。

- `https://notebooks.azure.com/<user_id>`:自分のプロファイル ページ。
- `https://notebooks.azure.com/<user_id>/projects`:自分のプロジェクト。 自分のプロジェクトをすべて表示できます。他のユーザーはお客様のパブリック プロジェクトのみを表示できます。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`:プロジェクト ファイル。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`:特定のプロジェクトの複製。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`:特定のノートブックまたはファイルの HTML のプレビュー。

## <a name="your-user-id"></a>自分のユーザー ID

初めて Azure Notebooks にサインインしている場合、アカウントには一時的なユーザー ID ("anon-idr3ca" など) が自動的に割り当てられます。 "anon-" から始まるユーザー ID がある限り、サインインするたびに、Azure Notebooks でユーザー ID を変更するように求められます。

![Azure Notebooks にログインするときにユーザー ID の作成を確認するメッセージ](media/accounts/create-user-id.png)

**[Configure User ID]\(ユーザー ID の構成\)** コマンドは、一時的なユーザー名の横にも表示されます。

![一時的な ID を使用している場合に表示される [Configure User ID]\(ユーザー ID の構成\) コマンド](media/accounts/configure-user-id-command.png)

いつでもプロファイル ページで自分のユーザー ID を変更することもできます。

ユーザー ID は、4 から 6 つのアルファベット、数字、およびハイフンで構成する必要があります。 他の文字は許可されていません。また、ユーザー ID の先頭または末尾にハイフンを使用したり、複数のハイフンを連続して使用したりすることはできません。 ユーザー ID はすべての Azure Notebooks アカウントで一意であるため、"User ID is already in use"\(ユーザー ID は既に使用中です\) というメッセージが表示される場合があります (Microsoft の商標をユーザー ID として使用しようとした場合も、このメッセージが表示されます)。この場合は、他のユーザー ID を選択します。

> [!Important]
> ID を変更すると、前の ID を使用して共有している可能性がある任意の URL が無効になります。 自分の ID を前の ID に戻して、リンクを再検証することができます。 しかし、それまでの間、別のユーザーが使用されていない ID を要求することができます。

## <a name="your-profile"></a>自分のプロファイル

プロファイルは、URL (`https://notebooks.azure.com/<user_id>`) で公開用の表示できる情報で構成されます。 また、自分のプロファイル ページには、最近使ったプロジェクトと星付きのプロジェクトも示されます。

プロファイルを編集するには、自分のプロファイル ページで **[Edit Profile Information]\(プロファイル情報の編集\)** コマンドを使用します。 プロファイルのセクションは次のとおりです。

| Section | 内容 |
| --- | --- |
| プロファイル写真 | 自分のプロファイル ページで示されるイメージ。 |
| アカウント情報 | 表示名、ユーザー ID、およびパブリック電子メール アドレス。 ここでの電子メール アカウントは、他のユーザーにお客様と連絡する手段を提供します。このアカウントは、Azure Notebooks 自体にサインインするために使用する[アカウント](azure-notebooks-user-account.md)とは異なる場合があります。 |
| プロファイル情報 | 自分の位置情報、会社、役職、Web サイト、自分自身の簡単な説明。 |
| ソーシャル プロファイル | 共有する場合は、自分の GitHub、Twitter、Facebook ID。 |
| プライバシー設定 | 2 つのコマンドを提供します。<ul><li>**[Export My Profile]\(マイ プロファイルのエクスポート\)** : 写真、プロファイル情報、セキュリティ ログなど、Azure Notebooks で自分のプロファイルに保存されるすべての情報が含まれる、 *.zip* ファイルを作成してダウンロードします。</li><li>**[Delete My Account]\(マイ アカウントの削除\)** : Azure Notebooks に格納されたすべての個人情報を完全に削除します。</li></ul> |
| サイトの機能を有効にする | Azure Notebooks の動作のさまざまな部分を制御できます。<ul><li>**[Unified Frontend for Notebooks]\(Notebooks 用の統合フロントエンド\)** : よりすばやくノートブックを起動し、永続性を向上させることができます。</li><li>**[Run in JupyterLab by default]\(既定で JupyterLab で実行する\)** :既定では、Azure Notebooks によってほとんどのユーザーに適したシンプルなユーザー インターフェイスが提供されます。 JupyterLab では、経験豊富なユーザー向けのより豊富ですが、より複雑なインターフェイスが提供されます。</li><li>**[VNext Website]\(VNext Web サイト\)** : このドキュメントに示された現代化された Web レイアウトを有効にします。</li></ul> |

## <a name="next-steps"></a>次のステップ  

> [!div class="nextstepaction"]
> [チュートリアル: 線形回帰を行う Jupyter ノートブックを作成して実行する](tutorial-create-run-jupyter-notebook.md)
