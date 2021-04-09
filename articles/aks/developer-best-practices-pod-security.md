---
title: 開発者のベスト プラクティス - Azure Kubernetes Service (AKS) でのポッドのセキュリティ
description: Azure Kubernetes Service (AKS) でポッドをセキュリティで保護する方法に関する、開発者のベスト プラクティスについて説明します
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92900825"
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

アプリケーションを正常に実行するには、*ルート* ではなく、定義済みのユーザーまたはグループとしてポッドを実行する必要があります。 ポッドまたはコンテナーに対して `securityContext` を使用すると、*runAsUser* や *fsGroup* などの設定を定義して適切なアクセス許可を想定することができます。 必要なユーザーまたはグループのアクセス許可のみを割り当てます。追加のアクセス許可を想定する手段としてセキュリティ コンテキストを使用しないでください。 *runAsUser*、権限昇格、およびその他の Linux 機能の設定は、Linux のノードとポッドでのみ使用可能です。

ルート以外のユーザーとして実行する際に、コンテナーを 1024 下の特権ポートにバインドすることはできません。 このシナリオでは、アプリが特定のポートで実行されているという事実を偽装するために Kubernetes Services を使用できます。

ポッドのセキュリティ コンテキストでは、プロセスおよびサービスにアクセスするための追加の機能またはアクセス許可も定義できます。 次の一般的なセキュリティ コンテキスト定義を設定できます。

* **allowPrivilegeEscalation** は、ポッドが *ルート* 特権を想定できるかどうかを定義します。 この設定が常に *false* に設定されるようにアプリケーションを設計します。
* **Linux 機能** を使用すると、ポッドに対して、基になるノード プロセスへのアクセスを許可できます。 これらの機能を割り当てる際には注意が必要です。 必要な最小数の特権を割り当ててください。 詳細については、[Linux 機能][linux-capabilities]に関する記事を参照してください。
* **SELinux ラベル** は、サービス、プロセス、およびファイル システム アクセスに対するアクセス ポリシーを定義するための Linux カーネル セキュリティ モジュールです。 ここでも、必要な最小数の特権を割り当ててください。 詳細については、[Kubernetes での SELinux オプション][selinux-labels]に関する記事を参照してください。

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
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

クラスター オペレーターと連携して、どのようなセキュリティ コンテキスト設定が必要かを判断します。 ポッドが必要とする追加のアクセス許可およびアクセスを最小限に抑えるように、アプリケーションを設計してみてください。 クラスター オペレーターが実装できる、AppArmor および seccomp (セキュリティで保護されたコンピューティング) を使用してアクセスを制限する追加のセキュリティ機能があります。 詳細については、「[リソースへのコンテナー アクセスをセキュリティで保護する][apparmor-seccomp]」を参照してください。

## <a name="limit-credential-exposure"></a>資格情報の公開を制限する

**ベスト プラクティス ガイダンス** - アプリケーション コードで資格情報を定義しないでください。 その他のリソースへのアクセスをポッドが要求できるようにするには、Azure リソースのマネージド ID を使用します。 デジタル キーと資格情報を格納および取得するために、Azure Key Vault などのデジタル資格情報コンテナーも使用する必要があります。 ポッドで管理されている ID は、Linux のポッドおよびコンテナー イメージのみでの使用を目的としています。

アプリケーション コードで資格情報が公開されるリスクを制限するには、固定または共有の資格情報を使用しないようにします。 資格情報またはキーをコードに直接含めないでください。 これらの資格情報が公開されている場合は、アプリケーションを更新して再デプロイする必要があります。 より適切なアプローチとしては、ポッドに対して独自の ID と自己認証の方法を指定するか、またはデジタル資格情報コンテナーから資格情報を自動的に取得します。

### <a name="use-azure-container-compute-upstream-projects"></a>Azure コンテナー コンピューティング上流プロジェクトを使用する

> [!IMPORTANT]
> 関連付けられた AKS オープン ソース プロジェクトは Azure テクニカル サポートではサポートされません。 ユーザーがクラスターに自己インストールし、コミュニティからフィードバックを収集するために提供されています。

次の[関連付けられた AKS オープン ソース プロジェクト][aks-associated-projects]を使用すると、ポッドを自動的に認証するか、デジタル資格情報コンテナーから資格情報とキーを要求することができます。 これらのプロジェクトは、Azure コンテナー コンピューティング上流チームによって管理されており、[使用できるプロジェクトのより広範な一覧](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support)に含まれています。

 * [Azure Active Directory のポッド ID][aad-pod-identity]
 * [シークレット ストア CSI ドライバーの Azure Key Vault プロバイダー](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>ポッドのマネージド ID を使用する

Azure リソースのマネージド ID を使用すると、ポッドは、Storage や SQL など、この機能をサポートする Azure サービスに対して自己認証を行うことができます。 ポッドには、Azure Active Directory に対して自己認証を行い、デジタル トークンを受信するために、Azure ID が割り当てられます。 このデジタル トークンを他の Azure サービスに提示すると、これらのサービスは、ポッドにそのサービスへのアクセス権が付与されているかどうかを確認し、必要なアクションを実行します。 つまり、このアプローチでは、(たとえばデータベース接続文字列用に) シークレットは何も必要ありません。 次の図に、ポッドのマネージド ID の簡略化されたワークフローを示します。

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Azure でのポッドのマネージド ID の簡略化されたワークフロー":::

マネージド ID を使用すると、Azure Storage などのサービスにアクセスするためにアプリケーション コードに資格情報を含める必要はありません。 各ポッドが自己の ID で認証を行うので、アクセスを監査および確認できます。 アプリケーションが他の Azure サービスに接続する場合は、マネージド ID を使用して、資格情報の再利用と公開のリスクを制限します。

ポッドの ID の詳細については、[お使いのアプリケーションでポッドのマネージド ID を使用するよう AKS クラスターを構成する方法][aad-pod-identity]に関するページを参照してください

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>シークレット ストア CSI ドライバーで Azure Key Vault を使用する

ポッド ID プロジェクトを使用すると、サポートされる Azure サービスに対する認証が有効になります。 Azure リソースのマネージド ID を使用しない独自のサービスまたはアプリケーションでは、引き続き資格情報またはキーを使用して使用して認証を行うことができます。 これらの機密コンテンツを格納するには、デジタル資格情報コンテナーを使用できます。

アプリケーションで資格情報が必要な場合は、デジタル資格情報コンテナーと通信し、最新の機密コンテンツを取得してから、必要なサービスに接続します。 Azure Key Vault は、このデジタル資格情報コンテナーとして使用できます。 次の図に、ポッドのマネージド ID を使用して Azure Key Vault から資格情報を取得する際の簡略化されたワークフローを示します。

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="ポッドのマネージド ID を使用して Key Vault から資格情報を取得する際の簡略化されたワークフロー":::

Key Vault では、資格情報、ストレージ アカウント キー、証明書などのシークレットを格納し、定期的にローテーションします。 [シークレット ストア CSI ドライバーの Azure Key Vault プロバイダー](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)を使用して、Azure Key Vault を AKS クラスターと統合できます。 シークレット ストア CSI ドライバーを使用すると、AKS クラスターで Key Vault から機密コンテンツをネイティブに取得し、要求されているポッドだけに安全に提供できます。 クラスター オペレーターと連携して、シークレット ストア CSI ドライバーを AKS worker ノードにデプロイします。 ポッドのマネージド ID を使用して Key Vault へのアクセスを要求したり、シークレット ストア CSI ドライバーを使用して必要な機密コンテンツを取得したりすることができます。

Kubernetes バージョン 1.16 以上が必要な Linux ノードおよびポッドでは、シークレット ストア CSI ドライバーを使用した Azure Key Vault を使用できます。 Windows ノードおよびポッドの場合は、バージョン 1.18 以上の Kubernetes が必要です。

## <a name="next-steps"></a>次のステップ

この記事では、ポッドをセキュリティで保護する方法について説明しました。 これらの領域のいくつかを実装する場合は、次の記事を参照してください。

* [AKS で Azure リソースのマネージド ID を使用する][aad-pod-identity]
* [Azure Key Vault を AKS と統合する][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
