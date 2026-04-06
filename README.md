## 🧠 Overview

本專案針對氫能車相關專利資料進行分析，透過自然語言處理（NLP）與主題建模技術，從大量非結構化文本中萃取技術主題，並進一步分析技術趨勢與發展方向。

---

## ❗ 問題定義（Problem）

### 1️⃣ 文本語意複雜，難以有效表示
專利文件通常包含大量技術術語與長文本描述，傳統基於詞頻（如 TF-IDF、LDA）的主題模型，無法充分捕捉語意關係與上下文資訊，導致主題品質與可解釋性有限。

### 2️⃣ 技術主題會隨時間演變
在氫能車領域中，技術發展具有明顯的時間動態性，傳統靜態主題模型（如 LDA）僅能提供「單一時間點」的主題分布，無法反映技術演進過程。

### 3️⃣ 缺乏語意層級的主題聚合能力
專利文本中常出現：同義詞（e.g., hydrogen storage vs hydrogen tank）、不同表達方式描述相同技術，傳統方法難以將語意相近的文本聚合為同一主題。

---

## 🎯 本專案的核心目標

建立一套能夠同時滿足以下需求的主題建模方法：

- ✔ 能捕捉語意關係（semantic understanding）
- ✔ 能進行高品質主題分群（topic clustering）
- ✔ 能分析主題隨時間的演變（dynamic topic modeling）

---

## 🧩 分析架構

<p align="center">
  <img width="420" alt="專利文件分析流程圖" src="https://github.com/user-attachments/assets/ce20d097-ef75-4b61-84f9-81731b45c1cb" />
</p>

### ①Text Preprocessing
本專案在進行主題建模前，先對專利文本進行系統化的預處理，以降低雜訊並提升語意表示品質。主要處理流程如下：
- **文本合併**：將專利標題（Title）與摘要（Abstract）整合為單一文本欄位，以保留完整語意資訊  
- **文字正規化**：統一小寫、移除標點符號與數字，並進行空白字元整理  
- **斷詞與詞形還原**：使用 spaCy 進行 tokenization，並結合 NLTK 進行詞形還原（lemmatization），降低詞彙變形影響  
- **停用詞處理**：整合 spaCy 預設 stopwords 與自訂領域 stopwords，過濾高頻但低資訊量詞彙  
- **向量化前處理分離**：區分 preprocessing 與 vectorizer（CountVectorizer）使用的 stopwords，以避免過度刪除關鍵技術詞  
👉 此設計可有效減少雜訊干擾，並提升後續 embedding 與主題建模的語意品質與穩定性。

### ② Sentence Embedding
使用 Sentence-Transformers 將文本轉換為語意向量，使模型能捕捉上下文語意，而非僅依賴詞頻。

### ③ Dimensionality Reduction（UMAP）
將高維 embedding 降維，同時保留語意結構，提升後續分群效果與穩定性。

### ④ Clustering（KMeans）
在語意空間中將相似文本分群，形成初步主題結構。

### ⑤ Topic Modeling（BERTopic）
透過 BERTopic 結合 c-TF-IDF，將分群結果轉換為具可解釋性的主題關鍵詞。

### ⑥ Topic Evoluation（Coherence + Diversity）
在主題建模過程中，主題數並非固定，而是針對多組候選主題數進行評估：
- Coherence：衡量單一主題內關鍵詞的語意一致性
- Diversity：衡量不同主題之間的區辨程度（以關鍵詞不重複比例計算）
透過比較不同主題數下的 coherence 與 diversity 表現，選擇在「主題一致性」與「主題區辨性」之間取得平衡的設定，使主題結果更穩定且具解釋性。

### ⑦ Topic Summary（LLM）
使用 LLaMA 2 對主題關鍵詞和代表性文件進行摘要生成，提升主題的可讀性與語意理解。

### ⑧ Topics Trend Analysis
結合專利公告年份（publication year），分析各主題隨時間的變化趨勢，識別技術的興起與衰退。

---

## 🔧 Tools（工具 / 套件 / 實作層）
- Python  
- BERTopic  
- Sentence-Transformers  
- Scikit-learn  
- spaCy  
- NLTK  
- Pandas / NumPy  
- LLaMA 2（主題摘要生成）

## 📊 Results

### 🔹 Key Findings
本專案成功從氫能車專利文本中萃取出多個具語意一致性的技術主題，並透過動態主題分析（Topics over Time）觀察各主題隨時間的變化趨勢。結果顯示，不同技術主題在各年份呈現出明顯的成長與衰退模式，能有效反映技術發展脈絡與創新方向。

---

### 🔹 Topic Trends Over Time
本研究透過時間序列分析，觀察主題在不同年份的分布變化。部分主題呈現持續成長，代表該技術領域具發展潛力；亦有主題逐漸衰退，顯示技術可能進入成熟或被替代階段。

👉 視覺化結果：
<p align="center">
<img width="821" height="271" alt="image" src="https://github.com/user-attachments/assets/98d617c4-2346-424d-bd3a-5ad61854344e" />
</p>

---

### 🔹 Topic Representation
透過 BERTopic 建立主題後，進一步結合 LLM（LLaMA 2）生成主題摘要，使每個主題從關鍵詞集合轉換為具語意的描述，有助於理解技術內容與應用場景。
<p align="center">
<img width="821" height="542" alt="image" src="https://github.com/user-attachments/assets/ed30bca4-561a-4bde-b62f-7f6823dd7e0a" />
</p>
---

### 🔹 Topic Quality Evaluation
為確保主題建模品質，本專案使用以下指標進行評估：

- **Coherence**：衡量主題內關鍵詞的語意一致性  
- **Diversity**：衡量不同主題之間的區辨程度  
<p align="center">
<img width="376" height="226" alt="image" src="https://github.com/user-attachments/assets/da276e94-9711-434a-be11-32aa4c808616" />
</p>

透過比較不同主題數設定下的 coherence 與 diversity 表現，選擇在主題一致性與區辨性之間取得平衡的模型設定。

---

### 🔹 Output Artifacts
本專案產出多項分析結果，可用於後續研究或應用：

- `topic_info.xlsx`：各主題關鍵詞與基本資訊  
- `topic_summaries.xlsx`：LLM 生成之主題摘要  
- `topics_over_time.html`：主題隨時間變化之互動視覺化結果  
