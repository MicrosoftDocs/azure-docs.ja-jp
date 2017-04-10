---
title: "Azure RemoteApp コレクションの更新 | Microsoft Docs"
description: "Azure RemoteApp コレクションの更新方法を説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: e553d432-e581-48fe-b996-c432357eb64a
ms.service: remoteapp
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: compute
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 593718b0d0d99f813321cb9ad6a7241e2315f6d0


---
# <a name="update-a-collection-in-azure-remoteapp"></a>Azure RemoteApp コレクションの更新
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

将来、必ず Azure RemoteApp コレクション内のアプリやイメージの更新が必要になる時期が来ます。 クラウド コレクションまたはハイブリッド コレクションで、Azure RemoteApp サブスクリプションに含まれるイメージの 1 つを使用している場合は、すべての更新が Azure RemoteApp 自体によって処理されるので、安心できます。

ただし、カスタム イメージ (最初から構築したか、または提供されたイメージの 1 つを変更して作成したカスタム イメージ) を使用している場合は、イメージとアプリケーションの保守に責任を持つ必要があります。 イメージまたはイメージ内のいずれかのアプリケーションを更新する必要がある場合は、イメージの更新されたバージョンを新たに作成し、コレクション内の既存のイメージをこの新しいバージョンに置き換える必要があります。

では、どのようにして、コレクションを更新するのでしょうか。 その方法は、以下のようなわかりやすいものになっています。

1. コレクションで使用したイメージを更新します。 必要となる任意のパッチまたは更新を適用し、新しい名前を付けて保存します。
2. RemoteApp にそのイメージを[アップロード](remoteapp-uploadimage.md)または[インポート](remoteapp-image-on-azurevm.md)します。
3. [コレクション] ページで **[更新]**をクリックします。
4. **[テンプレート イメージ]** リストから新しいイメージを選択します。
5. ここは、注意が必要な手順です。コレクション内のアプリを使用中のユーザーを処理する方法を決定する必要があります。 選択肢には次の 2 つがあります。
   
   * **更新が完了してから 60 分後にユーザーをサインアウトさせる**。 更新が終了すると、すぐに Azure RemoteApp はアクティブなユーザーに対してメッセージを表示します。そのメッセージにより、作業内容を保存してからログオフし、再度ログインするよう通知します。 60 分後、ログオフしていないアクティブなユーザーは、自動的にログオフされます。 ユーザーはすぐに再度ログオンできます。
   * **すぐにユーザーをサインアウトさせる**。 更新が終了すると、警告することなく、すぐにすべてのユーザーを自動的にログオフします。 このオプションを選択すると、ユーザーはデータを失う可能性があります。 ただし、ユーザーはすぐにアプリに再接続できます。
6. チェック マークをクリックして、更新を開始します。




<!--HONumber=Nov16_HO3-->


