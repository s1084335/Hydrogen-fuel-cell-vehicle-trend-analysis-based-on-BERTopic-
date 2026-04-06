🧠 Overview
本專案針對氫能車相關專利資料進行分析，透過自然語言處理（NLP）與主題建模技術，從大量非結構化文本中萃取技術主題，並進一步分析技術趨勢與發展方向。

❗ 問題定義（Problem）
1️⃣ 文本語意複雜，難以有效表示
專利文件通常包含大量技術術語與長文本描述，傳統基於詞頻（如 TF-IDF、LDA）的主題模型，無法充分捕捉語意關係與上下文資訊，導致主題品質與可解釋性有限。
2️⃣ 技術主題會隨時間演變
在氫能車領域中，技術發展具有明顯的時間動態性，傳統靜態主題模型（如 LDA）僅能提供「單一時間點」的主題分布，無法反映技術演進過程。
3️⃣ 缺乏語意層級的主題聚合能力
專利文本中常出現：同義詞（e.g., hydrogen storage vs hydrogen tank）、不同表達方式描述相同技術，傳統方法難以將語意相近的文本聚合為同一主題。

🎯 本專案的核心目標
建立一套能夠同時滿足以下需求的主題建模方法：
✔ 能捕捉語意關係（semantic understanding）
✔ 能進行高品質主題分群（topic clustering）
✔ 能分析主題隨時間的演變（dynamic topic modeling）

<img width="512" height="768" alt="流程圖" src="https://github.com/user-attachments/assets/785430e4-8a22-4a80-9ae7-50532d74e2f4" />

① Text Preprocessing
對專利文本進行清理與標準化，包含去除雜訊、停用詞與格式統一，以提升後續語意建模品質。

② Sentence Embedding
使用 Sentence-Transformers 將文本轉換為語意向量，使模型能捕捉上下文語意，而非僅依賴詞頻。

③ Dimensionality Reduction（UMAP）
將高維 embedding 降維，同時保留語意結構，提升後續分群效果與穩定性。

④ Clustering（KMeans）
在語意空間中將相似文本分群，形成初步主題結構。

⑤ Topic Modeling（BERTopic）
透過 BERTopic 結合 c-TF-IDF，將分群結果轉換為具可解釋性的主題關鍵詞。

⑥ Topic Number Selection（Coherence + Diversity） ⭐
在主題建模過程中，主題數並非固定，而是針對多組候選主題數進行評估：
Coherence：衡量單一主題內關鍵詞的語意一致性
Diversity：衡量不同主題之間的區辨程度（以關鍵詞不重複比例計算）
透過比較不同主題數下的 coherence 與 diversity 表現，選擇在「主題一致性」與「主題區辨性」之間取得平衡的設定，使主題結果更穩定且具解釋性。

⑦ Topic Representation（LLM）
使用 LLaMA 2 對主題關鍵詞進行摘要與標籤生成，提升主題的可讀性與語意理解。

⑧ Topics Over Time（Dynamic Analysis）
結合專利公告年份（publication year），分析各主題隨時間的變化趨勢，識別技術的興起與衰退。
[topics_over_time.html](https://github.com/user-attachments/files/26506202/topics_over_time.html)

🔧 Tools（工具 / 套件 / 實作層）
Python
BERTopic
Sentence-Transformers
Scikit-learn
Pandas / NumPy
LLaMA 2（用於主題摘要生成）
