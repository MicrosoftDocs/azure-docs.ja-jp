---
title: emptyDir ボリュームをコンテナー グループにマウントする
description: emptyDir ボリュームをマウントし、Azure Container Instances のコンテナー グループに属するコンテナー間でデータを共有する方法について説明します。
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77117739"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Azure Container Instances に emptyDir ボリュームをマウントする

*emptyDir* ボリュームをマウントし、Azure Container Instances のコンテナー グループに属するコンテナー間でデータを共有する方法について説明します。 コンテナー化されたワークロードのエフェメラル キャッシュとして *emptyDir* ボリュームを使用します。

> [!NOTE]
> *emptyDir* ボリュームのマウントは現在、Linux コンテナーに限定されています。 Microsoft ではすべての機能を Windows コンテナーに取り入れるように取り組んでいますが、現在のプラットフォームの違いは、[概要](container-instances-overview.md#linux-and-windows-containers)に関するページで確認できます。

## <a name="emptydir-volume"></a>emptyDir ボリューム

*emptyDir* ボリュームによって、あるコンテナー グループに属する各コンテナーにアクセスできる、書き込み可能なディレクトリが与えられます。 グループ内のコンテナーはボリューム内の同じファイルを読み書きできます。ボリュームは同じか別のパスを利用して各コンテナーにマウントできます。

*emptyDir* ボリュームの使用例:

* スクラッチ領域
* 長時間実行されるタスクの間のチェックポイント機能
* サイドカー コンテナーが取得し、アプリケーション コンテナーがサービスを提供するデータの保存

*emptyDir* ボリュームのデータはコンテナーがクラッシュしても存続します。 ただし、再開後のコンテナーが *emptyDir* ボリュームのデータを永続化するかどうかは保証されません。 コンテナー グループを停止した場合、*emptyDir* ボリュームは永続化されません。

Linux *emptyDir* ボリュームの最大サイズは 50 GB です。

## <a name="mount-an-emptydir-volume"></a>emptyDir ボリュームをマウントする

コンテナー インスタンスに emptyDir ボリュームをマウントするには、[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)、[YAML ファイル](container-instances-reference-yaml.md)、またはその他のプログラムによる方法を使用してデプロイを行って、コンテナー グループをデプロイすることができます。

最初に、ファイルのコンテナー グループ `properties` セクションに `volumes` 配列を入力します。 次に、*emptyDir* ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

たとえば、次の Resource Manager テンプレートでは、それぞれが *emptyDir* ボリュームを作成する 2 つのコンテナーから構成されるコンテナー グループが作成されます。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

コンテナー グループのデプロイの例については、「[Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする](container-instances-multi-container-group.md)」および「[YAML ファイルを使用して複数コンテナー グループをデプロイする](container-instances-multi-container-yaml.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に Azure ファイル共有をマウントする](container-instances-volume-azure-files.md)
* [Azure Container Instances に gitRepo ボリュームをマウントする](container-instances-volume-gitrepo.md)
* [Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)