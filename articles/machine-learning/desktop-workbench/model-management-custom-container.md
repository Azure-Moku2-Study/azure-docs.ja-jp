---
title: Azure ML モデルの配置に使用されるコンテナー イメージをカスタマイズする | Microsoft Docs
description: この記事では、Azure Machine Learning モデルのコンテナー イメージをカスタマイズする方法について説明します
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 7879cf1891e071da1a0ad3ddfc30f90fc7be8ca5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "35641537"
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Azure ML モデルに使用されるコンテナー イメージをカスタマイズする

この記事では、Azure Machine Learning モデルのコンテナー イメージをカスタマイズする方法について説明します。  Azure ML Workbench は、機械学習モデルを配置するためのコンテナーを使用します。 モデルは、その依存関係と共に配置され、Azure ML はモデル、依存関係、および関連付けられているファイルからイメージをビルドします。

## <a name="how-to-customize-the-docker-image"></a>Docker イメージをカスタマイズする方法
Azure ML が展開する Docker イメージを、以下を使用してカスタマイズします。

1. `dependencies.yml` ファイル: [PyPi]( https://pypi.python.org/pypi) からインストールできる依存関係を管理するため、Workbench プロジェクトの `conda_dependencies.yml` ファイルを使用するか、独自に作成することができます。 これは、pip によるインストールが可能な Python の依存関係をインストールする場合に推奨されるアプローチです。

   CLI コマンドの例:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   conda_dependencies ファイルの例: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Docker 手順ファイル: このオプションを使用して、PyPi からインストールできない依存関係をインストールすることで、展開されるイメージをカスタマイズします。 

   このファイルには、DockerFile のような Docker インストール手順を含める必要があります。 ファイルでは以下のコマンドを使用できます。 

    RUN、ENV、ARG、LABEL、EXPOSE

   CLI コマンドの例:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Image、Manifest、Service の各コマンドには、docker-file フラグを指定できます。

   Docker 手順ファイルの例:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Azure ML コンテナーの基本イメージは Ubuntu であり、変更できません。 異なる基本イメージを指定した場合は無視されます。

## <a name="next-steps"></a>次の手順
コンテナー イメージをカスタマイズしたので、大規模な使用のため、そのイメージをクラスターに展開できます。  Web サービスのデプロイ用にクラスターをセットアップする方法について詳しくは、「[モデル管理のセットアップ](deployment-setup-configuration.md)」をご覧ください。 
