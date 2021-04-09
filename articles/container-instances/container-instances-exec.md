---
title: 実行中のコンテナー インスタンスでコマンドを実行する
description: Azure Container Instances で現在実行されているコンテナーでコマンドを実行する方法について説明します
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "79225847"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>実行中の Azure Container Instances でコマンドを実行する

Azure Container Instances は、実行中のコンテナーでのコマンドの実行をサポートします。 既に開始されているコンテナー内でのコマンドの実行は、アプリケーションの開発とトラブルシューティング時に特に役立ちます。 この機能の最も一般的な用途は、対話型シェルを起動して、実行中のコンテナーで発生した問題をデバッグできるようにすることです。

## <a name="run-a-command-with-azure-cli"></a>Azure CLI でコマンドを実行する

[Azure CLI][azure-cli] で [az container exec][az-container-exec] を使用して、実行中のコンテナーでコマンドを実行します。

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

たとえば、Nginx コンテナーで Bash シェルを起動するには、次のように入力します。

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

次の出力例では、実行中の Linux コンテナーで Bash シェルが起動され、`ls` が実行されるターミナルが用意されます。

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

次の例では、実行中の Nanoserver コンテナーでコマンド プロンプトが起動します。

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>複数コンテナー グループ

[コンテナー グループ](container-instances-container-groups.md)に複数のコンテナー (アプリケーション コンテナーやログ記録サイドカーなど) が含まれている場合は、`--container-name` を使用して、コマンドを実行するコンテナーの名前を指定します。

たとえば、コンテナー グループ *mynginx* に、*nginx-app* と *logger* という 2 つのコンテナーがあるとします。 *nginx-app* コンテナーでシェルを起動するには、次のように入力します。

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>制限

Azure Container Instances は、現時点では [az container exec][az-container-exec] による単一のプロセスの起動をサポートしています。コマンドの引数を渡すことはできません。 たとえば、`sh -c "echo FOO && echo BAR"` のようなコマンドのチェーンや、`echo FOO` の実行は行えません。

## <a name="next-steps"></a>次のステップ

[Azure Container Instances でのコンテナーとデプロイに関する問題のトラブルシューティング](container-instances-troubleshooting.md)に関する記事で、他のトラブルシューティング ツールとデプロイの一般的な問題を確認します。

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure