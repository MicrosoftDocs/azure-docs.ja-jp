---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102205998"
---
ローカル ターミナル ウィンドウで、ローカル Git リポジトリに Azure リモートを追加します。 *\<deploymentLocalGitUrl-from-create-step>* を、「[Web アプリを作成する](#create-a-web-app)」で保存した Git リモートの URL で置き換えます。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、Azure portal へのサインインに使用する資格情報ではなく、「**デプロイ ユーザーを構成する**」で作成した資格情報を入力してください。

```bash
git push azure main
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。
