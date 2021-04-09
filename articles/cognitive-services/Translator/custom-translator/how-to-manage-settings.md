---
title: 設定を管理する方法 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator で設定を管理し、ワークスペースを作成し、ワークスペースを共有し、サブスクリプション キーを管理する方法。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895783"
---
# <a name="how-to-manage-settings"></a>設定を管理する方法

カスタム翻訳ツールの設定ページでは、ワークスペースの共有、翻訳サブスクリプション キーの変更、およびワークスペースの削除が可能です。

設定ページにアクセスするには:

1. [Custom Translator](https://portal.customtranslator.azure.ai/) ポータルにサインインします。
2. Custom Translator ポータルで、サイドバーにある歯車アイコンをクリックします。

    ![リンクの設定](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Translator サブスクリプションの関連付け

モデルをトレーニングまたはデプロイするには、ワークスペースに関連付けられた Translator サブスクリプション キーが必要です。

サブスクリプションをお持ちでない場合は、次の手順を実行します。

1. サブスクライブして翻訳リソースを作成します。 「[翻訳にサインアップする方法](../translator-how-to-signup.md)」に従ってサブスクライブし、翻訳キーを取得します。
2. 翻訳サブスクリプション用のキーをメモします。 Key1、Key2 のいずれかを利用できます。
3. Custom Translator ポータルに戻ります。

## <a name="create-a-new-workspace"></a>新しいワークスペースを作成する

1. [カスタム翻訳ツール] サイドバーの [+ ワークスペースの作成] ボタンをクリックします。

    ![新しいワークスペースの作成](media/how-to/create-new-workspace.png)

2. ダイアログに新しいワークスペースの名前を入力します。
3. [次へ] をクリックします。
4. サブスクリプションの種類を選択します。
5. サブスクリプションのリージョンを選択します。 このリージョンは、翻訳リソース キーが作成されたときに選択したリージョンと一致している必要があります。
6. ダイアログに翻訳サブスクリプション用のキーを入力してから、[保存] ボタンをクリックします。

    ![[新しいワークスペースの作成] ダイアログ](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>カスタム翻訳ツールでは、 [対応 VNET](../../../api-management/api-management-using-with-vnet.md) 内に作成された Translator Text API リソース (別名: Azure サブスクリプション キー) 用のワークスペースを作成することはできません。

### <a name="modify-existing-key"></a>既存のキーを変更する

1. ワークスペースの [設定] ページに移動します。
2. [キーの変更] をクリックします

    ![サブスクリプション キーを追加する方法](media/how-to/how-to-add-subscription-key.png)

3. ダイアログに翻訳サブスクリプション用のキーを入力し、[保存] ボタンをクリックします。

    ![サブスクリプション キーを追加する方法のダイアログ](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>ワークスペースを管理する

ワークスペースは、カスタム翻訳システムを作成および構築するための作業領域です。 ワークスペースには、複数のプロジェクト、モデル、ドキュメントを含めることができます。

作業のさまざまな部分を異なる相手と共有する必要がある場合は、複数のワークスペースを作成すると便利です。

## <a name="share-your-workspace"></a>ワークスペースを共有する

作業のさまざまな部分を異なる相手と共有する必要がある場合は、Custom Translator で、ワークスペースを他のユーザーと共有することができます。

1. ワークスペースの [設定] ページに移動します。
2. [共有の設定] セクションの [ユーザーの追加] ボタンをクリックします。

    ![ワークスペースを共有する](media/how-to/share-workspace.png)

3. ダイアログに、このワークスペースを共有する相手のメール アドレスの一覧をコンマ区切りで入力します。 相手が Custom Translator へのサインインに使用しているメール アドレスと共有する必要があります。 次に、適切なレベルの共有アクセス許可を選択し、[保存] ボタンをクリックします。

    ![ワークスペース共有のダイアログ](media/how-to/share-workspace-dialog.png)

4. ワークスペース名が既定の "マイ ワークスペース" のままの場合は、ワークスペースを共有する前に変更する必要があります。
5. [保存] をクリックします。

## <a name="sharing-permissions"></a>共有アクセス許可

1. **閲覧者:** ワークスペースの閲覧者は、ワークスペースのすべての情報を表示できます。

2. **編集者:** ワークスペースの編集者は、ドキュメントの追加、モデルのトレーニング、ドキュメントとプロジェクトの削除を行うことができます。 サブスクリプション キーを追加できますが、ワークスペースの共有相手の変更、ワークスペースの削除、またはワークスペース名の変更を行うことはできません。

3. **所有者:** 所有者は、ワークスペースに対する完全なアクセス許可を持っています。

## <a name="change-sharing-permission"></a>共有アクセス許可を変更する

ワークスペースを共有すると、[共有の設定] セクションに、このワークスペースが共有されるすべてのメール アドレスが表示されます。 ワークスペースに対して所有者アクセス権を持っている場合は、各メール アドレスの既存の共有アクセス許可を変更できます。

1. 各メールの [共有の設定] セクションにあるドロップダウン メニューには、現在のアクセス許可レベルが表示されます。

2. ドロップダウン メニューをクリックし、そのメール アドレスに割り当てる新しいアクセス許可レベルを選択します。

    ![共有アクセス許可の設定](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>ワークスペースをピン留めする

最初に作成したワークスペースは、既定でピン留めされます。 サインインするたびに、サイトの読み込みで、ピン留めされたワークスペースが表示されます。 多くのワークスペースを作成して、サインイン時にそれらの 1 つを既定にしたければ、それをピン留めする必要があります。

1. サイドバーで、ピン留めするワークスペースの名前をクリックします。
2. ワークスペースの [設定] ページに移動します。
3. [ピン留め] アイコンをクリックします。

    ![ワークスペースをピン留めする](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>次のステップ

- [ワークスペースとプロジェクトを管理する方法](workspace-and-project.md)を確認する