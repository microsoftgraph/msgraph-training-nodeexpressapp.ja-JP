# <a name="microsoft-graph-training-module---build-nodejs-express-apps-with-microsoft-graph"></a>Microsoft Graph トレーニングモジュール-Microsoft Graph を使用した node.js Express アプリのビルド

このモジュールでは、node.js Express web アプリケーションを構築することにより、Microsoft Graph を使用して Office 365 のデータにアクセスする方法について説明します。

## <a name="lab---build-nodejs-express-apps-with-microsoft-graph"></a>Lab-node.js Express アプリを Microsoft Graph でビルドする

このラボでは、Azure AD v2 認証エンドポイントを使用して、Microsoft Graph を使用して Office 365 のデータにアクセスする node.js Express web アプリケーションを作成します。

- [Node.js の Microsoft Graph チュートリアル](https://docs.microsoft.com/graph/training/node-tutorial)

## <a name="demos"></a>デモ

このリポジトリの[デモ](./Demos)ディレクトリには、チュートリアルの個々のセクションの完成に対応するプロジェクトのコピーが格納されています。 チュートリアルの特定のセクションをデモするだけの場合は、前のセクションのバージョンから始めることができます。

- [01-アプリ](Demos/01-create-app): [node.js Express Web app の作成の](https://docs.microsoft.com/graph/training/node-tutorial?tutorial-step=1)完了
- [02-aad](Demos/02-add-aad-auth)-認証: 完了した[Azure AD 認証の追加](https://docs.microsoft.com/graph/training/node-tutorial?tutorial-step=3)
- [03-msgraph](Demos/03-add-msgraph): 完了[予定表データの取得](https://docs.microsoft.com/graph/training/node-tutorial?tutorial-step=4)

## <a name="completed-sample"></a>完成したサンプル

このラボをフォローすることで完成したサンプルを生成する場合は、ここで見つけることができます。

- [完了したプロジェクト](Demos/03-add-msgraph)

## <a name="watch-the-module"></a>モジュールを見る

このモジュールは、「 [Microsoft Graph で Node.js Express アプリをビルド](https://youtu.be/n6q8Cm-pTYY)する」という Office 開発 YouTube チャネルで記録されています。

## <a name="contributors"></a>多様

|           ロール            |                                           作成者 (s)                                           |
| -------------------------- | --------------------------------------------------------------------------------------------- |
| コード/チュートリアル/サポート | Jason ジョンストン (Microsoft) [@jasonjoh](//github.com/jasonjoh)                                 |
| Slides                     | Jeremy Thake (Microsoft) [@jthake-msft](//github.com/jthake-msft)                             |
| QA                         | Andrew Connell (Microsoft MVP、Voitanos) [@andrewconnell](//github.com/andrewconnell)         |
| QA                         | ジュリー Turner (Microsoft MVP、Sympraxis コンサルティング) [@juliemturner](//github.com/juliemturner) |

## <a name="version-history"></a>バージョン履歴

| バージョン |       日付       |                     コメント                     |
| ------- | ---------------- | ------------------------------------------------ |
| 1.5     | 2019年6月18日    | 更新された readme を screencast レコーディングに更新しました |
| 1.4     | 2019年5月24日     | 2019Q4 コンテンツの更新                           |
| 1.3     | 2019年5月17日     | AAD アプリの登録手順を更新しました               |
| 1.2     | 2019年3月10日   | 2019Q3 コンテンツの更新                           |
| 1.1     | 2019年2月8日 | 追加されたスライド                                     |
| 1.0     | 2018             | Published                                        |

## <a name="disclaimer"></a>免責事項

**このコードは、 ** 特定の目的、市販性、または非侵害に対する暗黙の保証を含め、明示的または黙示的ないかなる種類の保証なしに提供されます。**

このプロジェクトでは、[Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) が採用されています。詳細については、「[Code of Conduct の FAQ](https://opensource.microsoft.com/codeofconduct/faq/)」を参照してください。また、その他の質問やコメントがあれば、[opencode@microsoft.com](mailto:opencode@microsoft.com) までお問い合わせください。
