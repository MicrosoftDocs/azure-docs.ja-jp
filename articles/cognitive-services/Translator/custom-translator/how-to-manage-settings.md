---
title: 設定を管理する方法 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator で設定を管理し、ワークスペースを作成し、ワークスペースを共有し、サブスクリプション キーを管理する方法。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 6e8406cdd265063622bc5dcebbb9f22549b3633a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766803"
---
# <a name="how-to-manage-settings"></a>設定を管理する方法

Custom Translator の設定ページでは、新しいワークスペースを作成し、ワークスペースを共有し、Microsoft Translation サブスクリプション キーを追加または変更できます。

設定ページにアクセスするには:

1. [Custom Translator](https://portal.customtranslator.azure.ai/) ポータルにサインインします。
2. Custom Translator ポータルで、サイドバーにある歯車アイコンをクリックします。

    ![リンクの設定](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Microsoft Translator サブスクリプションの関連付け

モデルをトレーニングまたはデプロイするには、ワークスペースに関連付けられた Microsoft Translator Text API サブスクリプション キーが必要です。

サブスクリプションをお持ちでない場合は、次の手順を実行します。

1. Microsoft Translator Text API にサブスクライブします。 この記事では、Microsoft Translator Text API にサブスクライブする方法について説明します。
2. 翻訳者のサブスクリプション用キーをメモします。 Key1、Key2 のいずれかを利用できます。
3. Custom Translator ポータルに戻ります。

### <a name="add-existing-key"></a>既存のキーを追加する

1.  ワークスペースの [設定] ページに移動します。
2.  [Add Key]\(キーの追加\) をクリックします

    ![サブスクリプション キーを追加する方法](media/how-to/how-to-add-subscription-key.png)

3. ダイアログに翻訳者のサブスクリプション用キーを入力し、[追加] ボタンをクリックします。

    ![サブスクリプション キーを追加する方法](media/how-to/how-to-add-subscription-key-dialog.png)
4.  キーを追加した後は、いつでもキーを変更または削除できます。

    ![追加後のサブスクリプション キー](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>ワークスペースを管理する

ワークスペースは、カスタム翻訳システムを作成および構築するための作業領域です。 ワークスペースには、複数のプロジェクト、モデル、ドキュメントを含めることができます。

作業のさまざまな部分を異なる相手と共有する必要がある場合は、複数のワークスペースを作成すると便利です。

## <a name="create-a-new-workspace"></a>新しいワークスペースを作成する

1.  ワークスペースの [設定] ページに移動します。
2.  [新しいワークスペースの作成] セクションの [新しいワークスペース] ボタンをクリックします。

    ![新しいワークスペースの作成](media/how-to/create-new-workspace.png)

4.  ダイアログに新しいワークスペースの名前を入力します。
5.  [作成] をクリックします。

    ![[新しいワークスペースの作成] ダイアログ](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>ワークスペースを共有する

作業のさまざまな部分を異なる相手と共有する必要がある場合は、Custom Translator で、ワークスペースを他のユーザーと共有することができます。

1.  ワークスペースの [設定] ページに移動します。
2.  [共有の設定] セクションの [共有] ボタンをクリックします。

    ![ワークスペースを共有する](media/how-to/share-workspace.png)

3.  ダイアログに、このワークスペースを共有する相手のメール アドレスの一覧をコンマ区切りで入力します。 相手が Custom Translator へのサインインに使用しているメール アドレスと共有する必要があります。 次に、適切なレベルの共有アクセス許可を選択します。

4.  ワークスペース名が既定の "マイ ワークスペース" のままの場合は、ワークスペースを共有する前に変更する必要があります。
5.  [保存] をクリックします。

## <a name="sharing-permissions"></a>共有アクセス許可

1.  **閲覧者:** ワークスペースの閲覧者は、ワークスペースのすべての情報を表示できます。

2.  **編集者:** ワークスペースの編集者は、ドキュメントの追加、モデルのトレーニング、ドキュメントとプロジェクトの削除を行うことができます。 サブスクリプション キーを追加できますが、ワークスペースの共有相手の変更、ワークスペースの削除、またはワークスペース名の変更を行うことはできません。

3.  **所有者:** 所有者は、ワークスペースに対する完全なアクセス許可を持っています。

## <a name="change-sharing-permission"></a>共有アクセス許可を変更する

ワークスペースを共有すると、[共有の設定] セクションに、このワークスペースが共有されるすべてのメール アドレスが表示されます。 ワークスペースに対して所有者アクセス権を持っている場合は、各メール アドレスの既存の共有アクセス許可を変更できます。

1.  各メールの [共有の設定] セクションのドロップダウン メニューには、現在のアクセス許可レベルが表示されます。

2.  ドロップダウン メニューをクリックし、そのメール アドレスに割り当てる新しいアクセス許可レベルを選択します。

    ![共有アクセス許可の設定](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>次の手順

- [Microsoft Translator Hub](https://hub.microsofttranslator.com) から[ワークスペースとプロジェクトを移行する方法](how-to-migrate.md)について説明します。
