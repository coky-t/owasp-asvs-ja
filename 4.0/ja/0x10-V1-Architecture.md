# V1: アーキテクチャ、設計、脅威モデリング要件

## 管理目標

セキュリティアーキテクチャは多くの組織で失われた技術となっています。エンタープライズアーキテクトの時代は DevSecOps で過去のものとなりました。アプリケーションセキュリティの分野では、最新のセキュリティアーキテクチャの原則をソフトウェア実務者に再導入しながら、アジャイルセキュリティの原則をキャッチアップし採用する必要があります。アーキテクチャは実装ではなく、潜在的に多くの異なる答えがあり、唯一の「正しい」答えがない問題について考える方法です。多くの場合、開発者がその問題を解決するはるかに優れた方法を知っている可能性がある場合には、セキュリティは柔軟性がなく、開発者が特定の方法でコードを修正することを要求するものとみなされます。アーキテクチャに対して唯一で単純な解決策はありません。そして、そうではないフリをすることはソフトウェアエンジニアリング分野への害となります。

Web アプリケーションの特定の実装はそのライフタイムを通じて継続的に改訂される可能性がありますが、全体的なアーキテクチャはほとんど変更されず、ゆっくりと進化します。セキュリティアーキテクチャも同様です。私たちは今日認証が必要ですし、明日も認証が必要でしょうし、五年後にも必要でしょう。今日、妥当な判断を下して、アーキテクチャに準拠したソリューションを選択して再利用すれば、多くの労力、時間、費用を節約できます。例えば、一昔前には、多要素認証はほとんど実装されていませんでした。

開発者が SAML フェデレーションアイデンティティなどの単一のセキュアなアイデンティティプロバイダモデルに注力した場合、元のアプリケーションのインタフェースを変更することなく、NIST 800-63 コンプライアンスなどの新しい要件を組み込むためにそのアイデンティティプロバイダを更新できることでしょう。多くのアプリケーションが同じセキュリティアーキテクチャ、つまり同じコンポーネントを共有している場合、すべてのアプリケーションが同時にこのアップグレードの利を得られます。但し、SAML は常に最良ないし最適な認証ソリューションとして残るわけではありません。要件変更に応じて他のソリューションと交換する必要があるかもしれません。このような変更は互いに入り組んでおり、完全な書き直しが必要になるほどコストがかかるか、セキュリティアーキテクチャなしではまったく不可能となります。

本章では、ASVS は妥当なセキュリティアーキテクチャの主要な側面である可用性、機密性、処理の完全性、否認防止、プライバシーをカバーしています。これらの各セキュリティ原則はすべてのアプリケーションに組み込まれ、本質的に備わったものでなければなりません。「シフトレフト」が重要です。セキュアコーディングチェックリスト、メンタリングとトレーニング、コーディングとテスティング、構築、展開、構成、運用で開発者の強化を開始します。そして、すべてのセキュリティ管理策が存在し機能していることを保証するために、フォローアップの独立テストで終了します。かつては業界として私たちが行うすべての作業が最後のステップでしたが、開発者が一日に数十回または数百回コードをプロダクションにプッシュするようになると、それだけでは不十分です。アプリケーションセキュリティの専門家はアジャイル技法に遅れずついていく必要があります。つまり、開発者ツールを採用し、コードを学び、開発者と協力することを意味します。他の全員が異動してから何か月も後にプロジェクトを批判するのではありません。

## V1.1 セキュアなソフトウェア開発ライフサイクル要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.1.1** | すべての開発ステージにおいてセキュリティに対処するセキュアなソフトウェア開発ライフサイクルを用いている。 ([C1](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | |
| **1.1.2** | 脅威の特定、対策の計画、適切なリスク対応の促進、セキュリティテスティングのガイドを行うために、すべての設計変更またはスプリント計画ごとに脅威モデリングを用いている。 | | ✓ | ✓ | 1053 |
| **1.1.3** | すべてのユーザストーリーと機能に、「ユーザとして、自分のプロファイルを閲覧および編集できる必要があります。他のユーザのプロファイルを閲覧および編集できてはいけません」などの機能的なセキュリティ制約が含まれている。 |  | ✓ | ✓ | 1110 |
| **1.1.4** | アプリケーションのすべての信頼境界線、コンポーネント、重要なデータフローのドキュメントと正当性がある。 | | ✓ | ✓ | 1059 |
| **1.1.5** | アプリケーションの上位レベルアーキテクチャと接続されているすべてのリモートサービスの定義とセキュリティ分析がなされている。 ([C1](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 1059 |
| **1.1.6** | 重複、欠落、無効、非セキュアな管理策を回避するために、一元化され、シンプル (経済的な設計) であり、承認され、セキュアであり、再利用可能なセキュリティ管理策を実装している。 ([C10](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 637 |
| **1.1.7** | 全ての開発者およびテスト担当者に対するセキュアコーディングチェックリスト、セキュリティ要件、ガイドライン、ポリシーの可用性がある。 | | ✓ | ✓ | 637 |
| **1.1.8** | [追加] アプリケーションのルートまたは .well-known ディレクトリにあり、セキュリティ上の問題について所有者に連絡するためのリンクや電子メールのアドレスを明確に定義した、公開されている [security.txt](https://securitytxt.org/) ファイルの可用性がある。 |  | ✓ | ✓ | 1059 |

## V1.2 認証アーキテクチャ要件

認証を設計する際、攻撃者がコールセンターを呼び出して一般的に知らせている質問に答えることでアカウントをリセットできる場合、強力なハードウェア対応の多要素認証があるかどうかは関係ありません。同一性を証明する場合、すべての認証経路に同じ強度が必要です。

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.2.1** | [1.14.7 へ移動] | | | |  |
| **1.2.2** | [修正] API、ミドルウェア、データ層などのアプリケーションコンポーネント間の通信が認証され、個々のユーザアカウントを使用している。 ([C3](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 306 |
| **1.2.3** | アプリケーションが、セキュアであることが確認されており、強力な認証を含むように拡張でき、アカウントの悪用や違反を検出するための十分なログ記録と監視を備えている、単一の承認済み認証メカニズムを使用している。 | | ✓ | ✓ | 306 |
| **1.2.4** | アプリケーションのリスクごとに脆弱な代替手段がないように、すべての認証経路と ID 管理 API が一貫した認証セキュリティ制御強度を実装している。 | | ✓ | ✓ | 306 |

## V1.3 セッション管理アーキテクチャ要件

これは将来のアーキテクチャ要件のためのプレースホルダです。

## V1.4 アクセス制御アーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.4.1** | アクセス制御ゲートウェイ、サーバ、サーバレス機能などの信頼できる実施ポイントがアクセス制御を実施している。クライアントでアクセス制御を実施してはいけない。 | | ✓ | ✓ | 602 |
| **1.4.2** | 選択したアクセス制御ソリューションがアプリケーションのニーズを満たすのに十分な柔軟性を備えている。 | | ✓ | ✓ | 284 |
| **1.4.3** | 機能、データファイル、URL、コントローラ、サービス、およびその他のリソースにおける最小特権の原則を実施している。これはなりすましや権限昇格に対する保護を意味する。 |  | ✓ | ✓ | 272 |
| **1.4.4** | 保護されたデータおよびリソースにアクセスするために、アプリケーションが単一で十分に吟味されたアクセス制御メカニズムを使用している。コピー＆ペーストやセキュアではない代替パスを回避するために、すべてのリクエストはこの単一のメカニズムを通過する必要がある。 ([C7](https://owasp.org/www-project-proactive-controls/#div-numbering)) |  | ✓ | ✓ | 284 |
| **1.4.5** | 属性または機能ベースのアクセス制御が使用されていることにより、コードが単に役割ではなく機能やデータ項目に対するユーザの認可を確認している。権限は役割を使用して割り当てる必要がある。 ([C7](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 275 |
| **1.4.6** | [追加] API、ミドルウェア、データ層などのアプリケーションコンポーネント間の通信が最小限の権限で実行されている。 ([C3](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 272 |

## V1.5 入力および出力アーキテクチャ要件

4.0 では、ロードされた信頼境界線の用語として「サーバサイド」という用語から離れました。信頼境界線は依然として懸念されています。信頼できないブラウザやクライアントデバイスでの決定はバイパス可能です。しかし、今日の主流のアーキテクチャ展開では、信頼実施ポイントが劇的に変わりました。したがって、ASVS で「信頼できるサービスレイヤ」という用語が使用されている場合、マイクロサービス、サーバレス API、サーバサイド、セキュアブートを備えたクライアントデバイス上の信頼された API、パートナー API や外部 API など、場所に関係なく、信頼された実施ポイントを意味します。

ここでの「信頼できないクライアント」という用語はプレゼンテーション層を提供するクライアントサイドのテクノロジを指し、一般に「フロントエンド」テクノロジと呼ばれます。「シリアル化」という用語は値の配列のようにネットワーク経由でデータを送信することや JSON 構造体を取得して読み取ることだけでなく、ロジックを含む複雑なオブジェクトを渡すことも意味します。

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.5.1** | 入力および出力要件が、タイプ、コンテンツ、および適用される法律、規制、およびその他のポリシーコンプライアンスに基づいて、データを対処および処理する方法を明確に定義している。 |  | ✓ | ✓ | 1029 |
| **1.5.2** | [追加] 信頼できないクライアントと通信する場合には、シリアル化が使用されていない。これが不可能な場合、逆シリアル化攻撃を防ぐために、オブジェクトタイプのホワイトリストのみを許可するか、逆シリアル化するオブジェクトタイプをクライアントが定義できないようにするなど、逆シリアル化が安全に実行されていることを確認する。 | | ✓ | ✓ | 502 |
| **1.5.3** | 信頼できるサービスレイヤで入力バリデーションが実施されている。 ([C5](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 602 |
| **1.5.4** | 出力エンコーディングが対象のインタプリタの近くまたはインタプリタにより発生する。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 116 |

## V1.6 暗号アーキテクチャ要件

アプリケーションはデータ資産を分類に従って保護するために強力な暗号化アーキテクチャで設計する必要があります。すべてを暗号化することは無駄であり、なにも暗号化しないことは法的に過失となります。通常、アーキテクチャ設計や高レベル設計、デザインスプリントやアーキテクチャスパイクの際に、バランスをとる必要があります。暗号を自ら設計したり追加導入したりすることは、最初から単純に組み込むよりも、セキュアに実装するために必然的により多くのコストがかかります。

アーキテクチャ要件はコードベース全体に内在するため、単体テストや統合テストは困難です。アーキテクチャ要件はコーディングフェーズを通じてコーディング標準を考慮する必要があり、セキュリティアーキテクチャ、ピアレビューやコードレビュー、振り返りの際にレビューする必要があります。

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.6.1** | 暗号化鍵の管理に明示的なポリシーがあり、暗号化鍵ライフサイクルが NIST SP 800-57 などの鍵管理標準に準拠している。 | | ✓ | ✓ | 320 |
| **1.6.2** | 暗号化サービスの利用者が鍵マテリアルおよびその他のシークレットを key vault や API ベースの代替手段を使用して保護している。 | | ✓ | ✓ | 320 |
| **1.6.3** | すべての鍵およびパスワードは置き換え可能であり、機密データを再暗号化するための明確に定義されたプロセスの一部である。 | | ✓ | ✓ | 320 |
| **1.6.4** | アーキテクチャが対称鍵、パスワード、API トークンなどのクライアントサイドのシークレットを安全でないものとして扱い、機密データの保護やアクセスにそれらを使用していない。 | | ✓ | ✓ | 320 |

## V1.7 エラー、ログ記録および監査アーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.7.1** | システム全体で共通のログ記録形式とアプローチが使用されている。 ([C9](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 1009 |
| **1.7.2** | 分析、検出、警告、エスカレーションのために、ログができればリモートシステムにセキュアに転送されている。 ([C9](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | |

## V1.8 データ保護およびプライバシーアーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.8.1** | すべての機密データが識別され、保護レベルに分類されている。 | | ✓ | ✓ | |
| **1.8.2** | すべての保護レベルに、暗号要件、完全性要件、リテンション、プライバシー、その他の機密性要件などの関連する一連の保護要件があり、これらがアーキテクチャに適用されている。 | | ✓ | ✓ | |

## V1.9 通信アーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.9.1** | 特にコンポーネントが異なるコンテナ、システム、サイト、またはクラウドプロバイダにある場合、アプリケーションがコンポーネント間の通信を暗号化している。 ([C3](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 319 |
| **1.9.2** | 中間者攻撃を防ぐために、アプリケーションコンポーネントが通信リンクのそれぞれの側の信頼性を検証している。例えば、アプリケーションコンポーネントは TLS 証明書とチェーンを妥当性確認する必要がある。 |  | ✓ | ✓ | 295 |

## V1.10 悪意のあるソフトウェアアーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.10.1** | チェックインが issue やチケットの変更と紐づいていることを確認する手続きを用いて、ソースコード管理システムが使用されている。ソースコート管理システムには変更を追跡できるようにアクセス制御と識別可能なユーザが必要である。 | | ✓ | ✓ | 284 |

## V1.11 ビジネスロジックアーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.11.1** | 提供するビジネスやセキュリティ機能の観点で、すべてのアプリケーションコンポーネントの定義とドキュメントがある。 | | ✓ | ✓ | 1059 |
| **1.11.2** | 認証、セッション管理、アクセス制御など、価値の高いビジネスロジックフローのすべてが非同期状態を共有しない。 | | ✓ | ✓ | 362 |
| **1.11.3** | 認証、セッション管理、アクセス制御など、価値の高いビジネスロジックフローのすべてがスレッドセーフであり、time-of-check time-of-use 競合状態に耐えられる。 | | | ✓ | 367 |

## V1.12 セキュアなファイルアップロードアーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.12.1** | ユーザがアップロードしたファイルが Web ルートの外側に保存されている。 | | ✓ | ✓ | 552 |
| **1.12.2** | アプリケーションから表示またはダウンロードする必要がある場合、ユーザがアップロードしたファイルが octet stream download 、または cloud file storage bucket などの無関係なドメインから提供されている。適切なコンテンツセキュリティポリシー (Content Security Policy, CSP) を実装して、XSS ベクターやアップロードされたファイルからの他の攻撃のリスクを軽減している。 | | ✓ | ✓ | 646 |

## V1.13 API アーキテクチャ要件

これは将来のアーキテクチャ要件のためのプレースホルダです。

## V1.14 構成アーキテクチャ要件

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **1.14.1** | 明確に定義されたセキュリティコントロール、ファイアウォールルール、API ゲートウェイ、リバースプロキシ、クラウドベースのセキュリティグループ、または同様のメカニズムを通じて、異なる信頼レベルのコンポーネントを分離している。 | | ✓ | ✓ | 923 |
| **1.14.2** | リモートデバイスにバイナリをデプロイするために、バイナリ署名、信頼できる接続、検証済みエンドポイントが使用されている。 | | ✓ | ✓ | 494 |
| **1.14.3** | ビルドパイプラインが期限切れコンポーネントやセキュアではないコンポーネントについて警告し、適切なアクションを実行している。 | | ✓ | ✓ | 1104 |
| **1.14.4** | 特にアプリケーションインフラストラクチャがクラウド環境ビルドスクリプトなどのソフトウェアで定義されている場合、ビルドパイプラインにアプリケーションのセキュアなデプロイメントを自動的にビルドおよび検証するビルドステップが含まれている。 | | ✓ | ✓ | |
| **1.14.5** | 特に逆シリアル化などの機密性の高いまたは危険なアクションを実行している場合、攻撃者が他のアプリケーションを攻撃するのを遅延および阻止するために、アプリケーションのデプロイメントが適切にサンドボックス化、コンテナ化、ネットワークレベルで分離されている。 ([C5](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 265 |
| **1.14.6** | NSAPI プラグイン, Flash, Shockwave, ActiveX, Silverlight, NACL, クライアントサイド Java アプレットなど、サポートされていない、セキュアではない、推奨されていないクライアントサイドテクノロジーを使用していない。 | | ✓ | ✓ | 477 |
| **1.14.7** | [1.2.1 から移動] すべてのアプリケーションコンポーネント、サービス、サーバに対して、一意または特別な低権限のオペレーティングシステムアカウントが使用されている。 ([C3](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 250 |

## 参考情報

詳しくは以下の情報を参照してください。

* [OWASP Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
* [OWASP Attack Surface Analysis Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Attack_Surface_Analysis_Cheat_Sheet.html)
* [OWASP Threat modeling](https://owasp.org/www-community/Application_Threat_Modeling)
* [OWASP Software Assurance Maturity Model Project](https://owasp.org/www-project-samm/)
* [Microsoft SDL](https://www.microsoft.com/en-us/sdl/)
* [NIST SP 800-57](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)
