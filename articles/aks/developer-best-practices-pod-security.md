---
title: 開発者のベスト プラクティス - Azure Kubernetes Service (AKS) でのポッドのセキュリティ
description: Azure Kubernetes Service (AKS) でポッドをセキュリティで保護する方法に関する、開発者のベスト プラクティスについて説明します
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1c2c5cbee91ddaee5f1f6af8ec17c48326f68e84
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755050"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのポッドのセキュリティに関するベスト プラクティス

Azure Kubernetes Service (AKS) でアプリケーションを開発および実行する際には、ポッドのセキュリティが重要な考慮事項になります。 ご使用のアプリケーションは、必要な最小数の特権のプリンシパルを考慮して設計する必要があります。 顧客にとって最大の関心事は、プライベート データを安全に保つことです。 外部に、データベース接続文字列、キー、シークレット、証明書などの資格情報を公開しないでください。外部では、攻撃者がそれらのシークレットを悪意のある目的で利用する恐れがあります。 これらの資格情報をコードに追加したり、コンテナー イメージに埋め込んだりしないでください。 このアプローチでは、コンテナー イメージを再ビルドする必要があるので、これらの資格情報をローテーションする機能が公開および制限されるというリスクが生じます。

このベスト プラクティスの記事では、AKS でポッドをセキュリティで保護する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * ポッドのセキュリティ コンテキストを使用して、プロセスおよびサービスへのアクセス、または特権エスカレーションを制限する
> * ポッドのマネージド ID を使用して他の Azure リソースとの認証を行う
> * Azure Key Vault などのデジタル資格情報コンテナーに対して資格情報を要求して取得する

[クラスター セキュリティ][best-practices-cluster-security]および[コンテナー イメージ管理][best-practices-container-image-management]に関するベスト プラクティスも参照できます。

## <a name="secure-pod-access-to-resources"></a>リソースへのポッドのアクセスをセキュリティで保護する

**ベスト プラクティス ガイダンス** - 別のユーザーまたはグループとして実行し、基盤となるノードのプロセスおよびサービスへのアクセスを制限するには、ポッドのセキュリティ コンテキスト設定を定義します。 必要な最小数の特権を割り当てます。

アプリケーションを正常に実行するには、*ルート*ではなく、定義済みのユーザーまたはグループとしてポッドを実行する必要があります。 ポッドまたはコンテナーに対して `securityContext` を使用すると、*runAsUser* や *fsGroup* などの設定を定義して適切なアクセス許可を想定することができます。 必要なユーザーまたはグループのアクセス許可のみを割り当てます。追加のアクセス許可を想定する手段としてセキュリティ コンテキストを使用しないでください。 ルート以外のユーザーとして実行する際に、コンテナーを 1024 下の特権ポートにバインドすることはできません。 このシナリオでは、アプリが特定のポートで実行されているという事実を偽装するために Kubernetes Services を使用できます。

ポッドのセキュリティ コンテキストでは、プロセスおよびサービスにアクセスするための追加の機能またはアクセス許可も定義できます。 次の一般的なセキュリティ コンテキスト定義を設定できます。

* **allowPrivilegeEscalation** は、ポッドが*ルート*特権を想定できるかどうかを定義します。 この設定が常に *false* に設定されるようにアプリケーションを設計します。
* **Linux 機能**を使用すると、ポッドに対して、基になるノード プロセスへのアクセスを許可できます。 これらの機能を割り当てる際には注意が必要です。 必要な最小数の特権を割り当ててください。 詳細については、[Linux 機能][linux-capabilities]に関する記事を参照してください。
* **SELinux ラベル**は、サービス、プロセス、およびファイル システム アクセスに対するアクセス ポリシーを定義するための Linux カーネル セキュリティ モジュールです。 ここでも、必要な最小数の特権を割り当ててください。 詳細については、[Kubernetes での SELinux オプション][selinux-labels]に関する記事を参照してください。

次のポッド YAML マニフェストの例では、定義するセキュリティ コンテキスト設定を実行します。

* ユーザー ID *1000*、およびグループ ID *2000* の一部として実行されるポッド
* `root` を使用するように特権をエスカレートできない
* Linux 機能に対して、ネットワーク インターフェイスおよびホストのリアルタイム (ハードウェア) クロックへのアクセスを許可する

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

クラスター オペレーターと連携して、どのようなセキュリティ コンテキスト設定が必要かを判断します。 ポッドが必要とする追加のアクセス許可およびアクセスを最小限に抑えるように、アプリケーションを設計してみてください。 クラスター オペレーターが実装できる、AppArmor および seccomp (セキュリティで保護されたコンピューティング) を使用してアクセスを制限する追加のセキュリティ機能があります。 詳細については、[リソースへのコンテナーのアクセスをセキュリティで保護する方法][apparmor-seccomp]に関する記事を参照してください。

## <a name="limit-credential-exposure"></a>資格情報の公開を制限する

**ベスト プラクティス ガイダンス** - アプリケーション コードで資格情報を定義しないでください。 その他のリソースへのアクセスをポッドが要求できるようにするには、Azure リソースのマネージド ID を使用します。 デジタル キーと資格情報を格納および取得するために、Azure Key Vault などのデジタル資格情報コンテナーも使用する必要があります。

アプリケーション コードで資格情報が公開されるリスクを制限するには、固定または共有の資格情報を使用しないようにします。 資格情報またはキーをコードに直接含めないでください。 これらの資格情報が公開されている場合は、アプリケーションを更新して再デプロイする必要があります。 より適切なアプローチとしては、ポッドに対して独自の ID と自己認証の方法を指定するか、またはデジタル資格情報コンテナーから資格情報を自動的に取得します。

次の[関連付けられた AKS オープン ソース プロジェクト][aks-associated-projects]を使用すると、ポッドを自動的に認証するか、デジタル資格情報コンテナーから資格情報とキーを要求することができます。

* Azure リソースのマネージド ID
* Azure Key Vault FlexVol ドライバー

関連付けられた AKS オープン ソース プロジェクトは Azure テクニカル サポートではサポートされません。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらのプロジェクトを運用環境で使用することはお勧めできません。

### <a name="use-pod-managed-identities"></a>ポッドのマネージド ID を使用する

Azure リソースのマネージド ID を使用すると、ポッドは、Storage や SQL など、この機能をサポートする Azure 内の任意のサービスに対して自己認証を行うことができます。 ポッドには、Azure Active Directory に対して自己認証を行い、デジタル トークンを受信するために、Azure ID が割り当てられます。 このデジタル トークンを他の Azure サービスに提示すると、これらのサービスは、ポッドにそのサービスへのアクセス権が付与されているかどうかを確認し、必要なアクションを実行します。 つまり、このアプローチでは、(たとえばデータベース接続文字列用に) シークレットは何も必要ありません。 次の図に、ポッドのマネージド ID の簡略化されたワークフローを示します。

![Azure でのポッドのマネージド ID の簡略化されたワークフロー](media/developer-best-practices-pod-security/basic-pod-identity.png)

マネージド ID を使用すると、Azure Storage などのサービスにアクセスするためにアプリケーション コードに資格情報を含める必要はありません。 各ポッドが自己の ID で認証を行うので、アクセスを監査および確認できます。 アプリケーションが他の Azure サービスに接続する場合は、マネージド ID を使用して、資格情報の再利用と公開のリスクを制限します。

ポッドの ID の詳細については、[お使いのアプリケーションでポッドのマネージド ID を使用するよう AKS クラスターを構成する方法][aad-pod-identity]に関するページを参照してください

### <a name="use-azure-key-vault-with-flexvol"></a>FlexVol と共に Azure Key Vault を使用する

ポッドのマネージド ID は、サポートされる Azure サービスに対する認証において的確に動作します。 Azure リソースのマネージド ID を使用しない独自のサービスまたはアプリケーションでは、引き続き資格情報またはキーを使用して使用して認証を行います。 これらの資格情報を格納するには、デジタル資格情報コンテナーを使用できます。

アプリケーションが資格情報を必要とする場合は、デジタル資格情報コンテナーと通信し、最新の資格情報を取得してから、必要なサービスに接続します。 Azure Key Vault は、このデジタル資格情報コンテナーとして使用できます。 次の図に、ポッドのマネージド ID を使用して Azure Key Vault から資格情報を取得する際の簡略化されたワークフローを示します。

![ポッドのマネージド ID を使用して Key Vault から資格情報を取得する際の簡略化されたワークフロー](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Key Vault では、資格情報、ストレージ アカウント キー、証明書などのシークレットを格納し、定期的にローテーションします。 FlexVolume を使用して、Azure Key Vault を AKS クラスターと統合できます。 FlexVolume ドライバーを使用すると、AKS クラスターは、Key Vault からネイティブに資格情報を取得し、要求元のポッドにのみ安全に提供することができます。 クラスター オペレーターと連携して、AKS ノードに Key Vault FlexVol ドライバーをデプロイしてください。 ポッドのマネージド ID を使用して、Key Vault へのアクセスを要求し、FlexVolume ドライバーを介して必要な資格情報を取得することができます。

## <a name="next-steps"></a>次の手順

この記事では、ポッドをセキュリティで保護する方法について説明しました。 これらの領域のいくつかを実装する場合は、次の記事を参照してください。

* [AKS で Azure リソースのマネージド ID を使用する][aad-pod-identity]
* [Azure Key Vault を AKS と統合する][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
