---
title: Azure Stack Edge Pro デバイスでの Kubernetes ロールベースのアクセス制御について | Microsoft Docs
description: Azure Stack Edge Pro デバイスで Kubernetes ロールベースのアクセス制御がどのように行われるかについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1b38a9807e05385a378fa6103710fb6b393c7b1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443150"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスでの Kubernetes ロールベースのアクセス制御

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro デバイスで、コンピューティング ロールを構成すると、Kubernetes クラスターが作成されます。 Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) を使用して、お使いのデバイス上のクラスター リソースへのアクセスを制限できます。

この記事では、Kubernetes によって提供される Kubernetes RBAC システムの概要と、Azure Stack Edge Pro デバイスに Kubernetes RBAC を実装する方法について説明します。 

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC を使用すると、ユーザーまたはユーザー グループに対して、リソースの作成または変更、実行中のアプリケーション ワークロードのログの表示などの操作を行うためのアクセス許可を割り当てることができます。 これらのアクセス許可は、スコープを 1 つの名前空間に指定することも、クラスター全体に付与することもできます。 

Kubernetes クラスターを設定すると、クラスター管理者ユーザーと呼ばれる、このクラスターに対応する単一のユーザーが作成されます。  このクラスター管理者ユーザーには `kubeconfig` ファイルが関連付けられます。 `kubeconfig` ファイルは、クラスターに接続してユーザーを認証するために必要な構成情報すべてを含むテキスト ファイルです。

## <a name="namespaces-types"></a>名前空間の型

ポッドやデプロイなどの Kubernetes リソースは、論理的に 1 つの名前空間にグループ化されます。 このグループ化により、Kubernetes クラスターを論理的に分割し、リソースを作成、表示、または管理するためのアクセスを制限する方法が提供されます。 ユーザーは、割り当てられている名前空間内のリソースのみを操作できます。

名前空間は、多数のユーザーが複数のチームまたはプロジェクトに分散している環境での使用を目的としています。 詳細については、[Kubernetes の名前空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) に関するページを参照してください。

Azure Stack Edge Pro デバイスには、次の名前空間があります。

- **システム名前空間** - この名前空間には、DNS やプロキシのようなネットワーク機能、Kubernetes ダッシュボードなど、重要なリソースが存在します。 通常は、この名前空間に独自のアプリケーションをデプロイしません。 この名前空間は、Kubernetes クラスターの問題をデバッグするために使用します。 

    デバイスには複数のシステム名前空間があり、これらのシステム名前空間に対応する名前は予約されています。 予約済みのシステム名前空間の一覧は次のとおりです。 
    - kube-system
    - metallb-system
    - dbe-namespace
    - default
    - kubernetes-dashboard
    - kube-node-lease
    - kube-public


    自分で作成するユーザー名前空間には、予約済みの名前を使用しないでください。 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **ユーザー名前空間** - **kubectl** またはデバイスの PowerShell インターフェイスを使用して作成し、アプリケーションをローカルにデプロイできる名前空間です。
 
- **IoT Edge 名前空間** - この `iotedge` 名前空間に接続して、IoT Edge を介してデプロイされたアプリケーションを管理します。

- **Azure Arc 名前空間** - この `azure-arc` 名前空間に接続して、Azure Arc を介してデプロイされたアプリケーションを管理します。Azure Arc を使用すると、他のユーザー名前空間にアプリケーションをデプロイすることもできます。 

## <a name="namespaces-and-users"></a>名前空間とユーザー

現実の世界では、クラスターを複数の名前空間に分割することが重要です。 

- **複数のユーザー**: 複数のユーザーがいる場合は、複数の名前空間を使用すると、それぞれのユーザーが互いに分離された特定の名前空間にアプリケーションとサービスをデプロイできます。 
- **単一ユーザー**: ユーザーが 1 人であっても、複数の名前空間を使用すると、そのユーザーは同じ Kubernetes クラスター内でアプリケーションの複数のバージョンを実行できます。

### <a name="roles-and-rolebindings"></a>Roles と RoleBindings

Kubernetes には、ロールとロール バインドの概念があります。これにより、名前空間レベルとクラスター レベルでユーザーまたはリソースにアクセス許可を付与できます。 

- **Roles**:ユーザーへのアクセス許可を **ロール** として定義し、**Roles** を使用して名前空間内でアクセス許可を付与できます。 
- **RoleBindings**: ロールを定義したら、**RoleBindings** を使用して、特定の名前空間にロールを割り当てることができます。 

この手法によって、1 つの Kubernetes クラスターを論理的に分離でき、ユーザーは自分に割り当てられた名前空間内のアプリケーション リソースのみにアクセスできます。 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro での Kubernetes RBAC

現在実装されている Kubernetes RBAC では、Azure Stack Edge Pro を使用すると、制限付きの PowerShell 実行空間から次の操作を実行できます。

- 名前空間を作成する。  
- 追加のユーザーを作成する。
- 作成した名前空間への管理者アクセス権を付与する。 クラスター管理者ロールやクラスター全体のリソースのビューにはアクセスできないことに注意してください。
- Kubernetes クラスターにアクセスするための情報を含む `kubeconfig` ファイルを取得する。


Azure Stack Edge Pro デバイスには複数のシステム名前空間があります。また、それらの名前空間にアクセスするために、`kubeconfig` ファイルを使用してユーザー名前空間を作成できます。 ユーザーはこれらの名前空間を完全に制御でき、ユーザーを作成または変更したり、ユーザーにアクセスを許可したりできます。 システム名前空間とクラスター全体のリソースに対するフル アクセス権を持っているのはクラスター管理者のみです。 `aseuser` には、システム名前空間に対する読み取り専用アクセス権が付与されています。

Azure Stack Edge Pro デバイスでの Kubernetes RBAC の実装を示す図は次のとおりです。

![Azure Stack Edge Pro デバイスでの Kubernetes RBAC](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

この図では、Alice、Bob、Chuck がアクセスできるのは割り当てられたユーザー名前空間 (この場合、それぞれ `ns1`、`ns2`、`ns3`) のみです。 これらの名前空間内で、管理者アクセス権を所有しています。 その一方で、クラスター管理者は、システム名前空間とクラスター全体のリソースに対する管理者アクセス権を持っています。

ユーザーは、名前空間とユーザーの作成、名前空間へのユーザーの割り当て、または`kubeconfig` のダウンロードを行うことができます。 詳細な手順については、[Azure Stack Edge Pro での kuebctl を使用した Kubernetes クラスターへのアクセス](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関するページをご覧ください。


Azure Stack Edge Pro デバイス上で名前空間とユーザーを操作する場合、次の注意事項が適用されます。

- いずれのシステム名前空間に対しても、ユーザーの作成、ユーザーへの名前空間アクセスの許可または取り消しなどの操作を実行することは許可されていません。 システム名前空間の例として、`kube-system`、`metallb-system`、`kubernetes-dashboard`、`default`、`kube-node-lease`、`kube-public` などがあります。 また、システム名前空間には、`iotedge` (IoT Edge 名前空間) や `azure-arc` (Azure Arc 名前空間) などのデプロイの種類のために予約されている名前空間も含まれます。
- ユーザー名前空間を作成し、それらの名前空間内で、追加のユーザーを作成し、そのユーザーに対して名前空間アクセスを許可または取り消すことができます。
- システム名前空間と同じ名前の名前空間を作成することはできません。 システム名前空間の名前は予約されています。  
- 他のユーザー名前空間で既に使用されている名前でユーザー名前空間を作成することはできません。 たとえば、作成済みの `test-ns` がある場合に、もう 1 つ `test-ns` 名前空間を作成することはできません。
- 既に予約されている名前でユーザーを作成することはできません。 たとえば、`aseuser` は予約済みユーザーのため、使用できません。


## <a name="next-steps"></a>次のステップ

ユーザーを作成し、名前空間を作成して、その名前空間へのアクセス権をユーザーに付与する方法については、[kubectl を使用した Kubernetes クラスターへのアクセス](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関するページを参照してください。

