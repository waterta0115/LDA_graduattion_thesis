# LDA

卒業論文プロジェクト：LDA（潜在ディリクレ配分法）を用いたトピックモデルの実装・評価・可視化を行った研究成果です。

## 概要
- **目的**: LDA の推論実装を拡張し、ディリクレ事前分布（ハイパーパラメータ）の自動更新を取り入れたモデルを構築・評価する。
- **可視化**: 最終的に `pyLDAvis` を用いてトピック間の関係や各トピックを特徴づける単語群を可視化した。

## 手法
- **推論**: マルコフ連鎖モンテカルロ法（MCMC）の一種である Collapsed Gibbs Sampling を用いた。
- **ハイパーパラメータ更新**: 周辺同時尤度（marginal joint likelihood）を最大化することを目標に、不動点反復法を用いてディリクレ事前分布のハイパーパラメータを更新した。

## 実装のポイント
- **参考実装**: 主に `lda/lda-project` のコードをベースに実装・改良を行った。
- **補助ツール**: 実装・検討の過程で外部ツール（例: Google の Gemini 等）を参照して設計・実装の補助を行った。
- **データ前処理**: 辞書やコーパス（`dictionary`、`customer_text`、`corpus` など）は `gensim` を用いて作成した。
- **評価指標**: トピック品質の評価には `gensim` の `CoherenceModel`の   `NPMI` を使用した。
- **Bag-of-Words**: BoW 行列は `scikit-learn` の `CountVectorizer` を用いて作成した。

## 可視化・評価
- `pyLDAvis` によるトピック可視化で、トピック間の距離や各トピックを代表する単語群を確認できるようにした。
- Coherence 指標などを用いて、トピックの妥当性を定量的に評価した。

## 主要依存ライブラリ（例）
- `gensim`
- `scikit-learn`
- `pyLDAvis`
- `numpy`, `pandas`

## 参照
- ベース実装: `lda/lda-project`

---
必要であれば、実行手順（依存関係のインストール、スクリプトの起動方法）、サンプルデータや再現手順を README に追記します。追加希望があれば教えてください。

## 参考文献
- Blei, D. M., Ng, A. Y., & Jordan, M. I. (2003). Latent Dirichlet Allocation. Journal of Machine Learning Research, 3, 993–1022. http://www.jmlr.org/papers/volume3/blei03a/blei03a.pdf
- Griffiths, T. L., & Steyvers, M. (2004). Finding scientific topics. Proceedings of the National Academy of Sciences, 101(Suppl 1), 5228–5235. DOI: 10.1073/pnas.0307752101. https://www.pnas.org/doi/10.1073/pnas.0307752101
- Minka, T. P. (2000). Estimating a Dirichlet distribution. Technical report. https://tminka.github.io/papers/dirichlet/
 - Mimno, D., Wallach, H. M., Talley, E., Leenders, M., & McCallum, A. (2011). Optimizing semantic coherence in topic models. In Proceedings of the Conference on Empirical Methods in Natural Language Processing (EMNLP).

(上記のうち、Griffiths & Steyvers は Collapsed Gibbs Sampling を用いた LDA 推論の代表的な適用例、Minka と Mimno らはディリクレ事前分布やトピックコヒーレンス（NPMI 等）に関する参考資料です。）

# Network_analysis

ネットワーク分析には `igraph` を用い、LDAで得られるトピック-語分布（\(\phi_{k v}\)）から隣接行列を作成してネットワーク化しました。これにより、トピック間や単語間の関係性をグラフ構造として可視化・定量化できます。

## 主な手順
- **隣接行列の作成**: 各トピックの語分布 \(\phi_{k v}\) を用いて、トピック間（または語間）の類似度・共起に基づく隣接行列を構築します。
- **スパース化**: ノイズや過剰なエッジを除去するため、しきい値や上位割合に基づいてエッジを刈り取りネットワークをスパース化します。
- **コミュニティ検出**: Louvain（ルーヴァン）法などを用いてネットワークをクラスタリングし、得られたコミュニティをトピック群と対応づけて解析します。
- **ネットワーク指標の計算**: 各ノード（トピック／単語）について次数、媒介中心性、PageRank、参加係数、コミュニティ内次数などを算出し、重要度や役割を評価します。

## 得られる分析結果
- コミュニティ構造を通じたトピック間のまとまりの把握（コミュニティ ≒ トピックのグループ）
- 重要なキーワード（高 PageRank や高媒介中心性）や、コミュニティ間で橋渡しする語（高参加係数）の抽出
- ネットワーク可視化によるトピック間・語間の直感的理解（ノード色にコミュニティ、サイズに中心性などを割り当てる）

## 実装上の注意点
-補助ツールとしてgoogleのgemini等を使用した。
- 隣接行列の定義（類似度尺度や共起スコア）やスパース化の閾値設定によって、得られるネットワーク構造が大きく変わります。複数設定で比較することを推奨します。
- 大規模語彙では計算量と可視化の観点から前処理（頻度下位語の除外や頻度上位語の制限）を行うと扱いやすくなります。
