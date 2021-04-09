---
author: baanders
description: Azure Digital Twins サンプルで DefaultAzureCredential のローカル認証を設定するためのインクルード ファイル - 概要なし
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212818"
---
このサンプルでは、`DefaultAzureCredential` を使用して、ローカル環境から資格情報が検索されます。たとえば、ローカルの [Azure CLI](/cli/azure/install-azure-cli) や Visual Studio または Visual Studio Code での Azure サインインなどです。 このため、サンプルの資格情報を設定するために、これらのメカニズムのいずれかを使用して、*Azure にローカルでサインイン* する必要があります。

Visual Studio または Visual Studio Code を使用してコード サンプルを実行する場合は、Azure Digital Twins インスタンスへのアクセスに使用する Azure 資格情報でそのエディターにサインインしてください。

または、[ローカル Azure CLI をインストール](/cli/azure/install-azure-cli)してマシンのコマンド プロンプトを起動し、`az login` コマンドを実行して Azure アカウントにサインインしてもかまいません。 サインイン後、コード サンプルを実行すると、自動的にログイン処理が行われます。