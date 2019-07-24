---
title: オペレーターのベスト プラクティス - Kubernetes Services (AKS) でのコンテナー イメージの管理
description: Azure Kubernetes Service (AKS) でコンテナー イメージを管理およびセキュリティで保護する方法に関するクラスター オペレーターのベスト プラクティス
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 1cc91f55d3895f06176875cb9ae620685dc09a26
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605553"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes サービス (AKS) でのコンテナー イメージの管理とセキュリティに関するベスト プラクティス

Azure Kubernetes Service (AKS) でアプリケーションを開発および実行する際には、コンテナーおよびコンテナー イメージのセキュリティが重要な考慮事項になります。 期限切れの基本イメージ、または修正プログラムが適用されていないアプリケーション ランタイムを含むコンテナーは、セキュリティ上のリスクおよび考えられる攻撃ベクトルをもたらします。 これらのリスクを最小限に抑えるには、ビルド時と実行時にコンテナー内の問題をスキャンして修復するツールを統合する必要があります。 脆弱性または期限切れの基本イメージが、プロセス内のできるだけ早い段階で見つかれば、クラスターのセキュリティが高まります。 この記事で*コンテナー*は、コンテナー レジストリに格納されているコンテナー イメージと、実行中のコンテナーの両方を意味します。

この記事では、AKS でコンテナーをセキュリティで保護する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * イメージの脆弱性をスキャンして修復する
> * デジタル署名済みのコンテナー イメージを提供する信頼できるレジストリを使用する
> * 基本イメージが更新されたら、コンテナー イメージを自動的にトリガーおよび再デプロイする

[クラスター セキュリティ][best-practices-cluster-security]および[ポッド セキュリティ][best-practices-pod-security]に関するベスト プラクティスも参照できます。

## <a name="secure-the-images-and-run-time"></a>イメージおよびランタイムをセキュリティで保護する

**ベスト プラクティス ガイダンス** - コンテナー イメージをスキャンして脆弱性を見つけ、検証に合格したイメージのみをデプロイします。 基本イメージおよびアプリケーション ランタイムを定期的に更新し、AKS クラスターにワークロードを再デプロイします。

コンテナー ベースのワークロードの導入に関する 1 つ課題は、独自のアプリケーションをビルドするために使用するイメージおよびランタイムのセキュリティを検証することです。 デプロイ内にセキュリティの脆弱性が発生しないことをどのようにして確認できるでしょうか。 デプロイ ワークフローには、[Twistlock][twistlock] や [Aqua][aqua] などのツールを使用してコンテナー イメージをスキャンするプロセスを含める必要があります。その後、検証済みのイメージのみをデプロイできるようにします。

![コンテナー イメージをスキャンして修正し、検証し、デプロイする](media/operator-best-practices-container-security/scan-container-images-simplified.png)

実際の例では、継続的統合と継続的デプロイ (CI/CD) パイプラインを使用して、イメージのスキャン、検証、およびデプロイを自動化することができます。 Azure Container Registry には、これらの脆弱性スキャン機能が含まれます。

## <a name="use-a-trusted-registry"></a>信頼できるレジストリを使用する

**ベスト プラクティス ガイダンス** - ポッドおよびデプロイで使用できるイメージ レジストリを制限します。 使用可能なイメージを検証および制御する場所である、信頼できるレジストリのみを許可します。

セキュリティ強化のために、アプリケーション コードにデジタル署名できるのと同様に、コンテナー イメージにもデジタル署名できます。 その後、AKS に対して、署名済みのイメージのみをデプロイすることを許可します。 このプロセスでは、脆弱性チェックに合格しただけでなく、デジタル署名済みの信頼できるイメージのみをプルするように、AKS を制限することで、セキュリティがさらに強化されます。 また、コンテナー イメージが改ざんされたり、まったく同じ名前のイメージに置き換わったりしていないことを確認します。

デジタル署名済みのコンテナー イメージを提供する信頼できるレジストリは、環境内に複雑さをもたらすものの、特定のポリシーや規制コンプライアンスで必要となる場合があります。 Azure Container Registry では、信頼できるレジストリおよび署名済みのイメージの使用をサポートしています。

デジタル署名済みのイメージの詳細については、「[Azure Container Registry におけるコンテンツの信頼][acr-content-trust]」を参照してください。

## <a name="automatically-build-new-images-on-base-image-update"></a>基本イメージの更新時に新しいイメージを自動的にビルドする

**ベスト プラクティス ガイダンス** - アプリケーション イメージに基本イメージを使用する際には、基本イメージの更新時にオートメーションを使用して新しいイメージをビルドします。 通常、これらの基本イメージにはセキュリティ修正プログラムが含まれているので、すべてのダウンストリーム アプリケーション コンテナー イメージを更新します。

基本イメージが更新されるたびに、すべてのダウンストリーム コンテナー イメージも更新する必要があります。 このビルド プロセスを、[Azure Pipelines][azure-pipelines] や Jenkins などの検証およびデプロイのパイプラインに統合する必要があります。 によりこれらのパイプラインは、更新されたベース イメージで引き続きアプリケーションが確実に実行されます。 アプリケーション コンテナー イメージが検証されたら、AKS デプロイは、セキュリティで保護された最新のイメージを実行するように更新できます。

また、Azure Container Registry タスクは、基本イメージの更新時にコンテナー イメージを自動的に更新できます。 この機能を使用すると、少数の基本イメージをビルドし、バグおよびセキュリティ修正プログラムを使用して、それらを定期的に更新続けることができます。

基本イメージの更新の詳細については、「[Automate image builds on base image update with Azure Container Registry Tasks][acr-base-image-update]」(Azure Container Registry タスクを使用して基本イメージの更新時のコンテナー イメージ ビルドを自動化する) を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、コンテナーをセキュリティで保護する方法について説明しました。 これらの領域のいくつかを実装する場合は、次の記事を参照してください。

* [Automate image builds on base image update with Azure Container Registry タスク (Azure Container Registry タスクを使用して基本イメージの更新時のコンテナー イメージ ビルドを自動化する)][acr-base-image-update]
* [Azure Container Registry におけるコンテンツの信頼][acr-content-trust]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-content-trust]: ../container-registry/container-registry-content-trust.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
