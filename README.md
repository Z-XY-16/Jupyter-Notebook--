## 代码核心功能说明
### 算法基础
1. **特征独立性假设**
多项式朴素贝叶斯分类器是基于贝叶斯定理和特征条件独立假设的分类方法。其核心假设是：在给定类别的情况下，各个特征之间是相互独立的。
尽管这一假设在实际应用中可能并不完全成立（例如，在文本分类中，某些单词的出现可能与其他单词相关），但朴素贝叶斯分类器在许多实际应用中表现出了良好的性能，并且使得模型的计算变得简单高效。
2. **贝叶斯定理在邮件分类中的具体应用形式**
贝叶斯定理用于计算后验概率，即在已知特征的情况下，某个类别出现的概率。
其公式为：
P(Y∣X)= P(X)P(X∣Y)P(Y)
3. 特征选择方法
在邮件分类中，特征选择方法通常包括词频（Bag-of-Words）和 TF-IDF（词频-逆文档频率）。TF-IDF 考虑了词在文档中的频率以及词在整个语料库中的稀有程度，能够更好地捕捉文本中的关键信息。
### 数据处理流程
1. **分词处理（Tokenization）**
分词是将文本字符串分割成单词、短语或符号的过程。分词的目的是将文本转换为可以进一步处理的单元。不同的语言和应用场景可能需要不同的分词方法。
- 实现逻辑：
  + 选择分词工具：
  对于英文文本，可以使用简单的空白字符分割（如空格、换行符等）。
  对于中文文本，通常需要使用专门的分词工具，如jieba（中文分词库）。
  + 分词过程：
  将文本字符串分割成单词列表。
  例如，英文文本可以通过str.split()方法分词，中文文本可以通过jieba.cut()方法分词。
2. **停用词过滤（Stop Words Filtering）**
停用词是指在文本中频繁出现但对文本内容理解帮助不大的词汇，如“的”、“是”、“和”等（中文），或“the”、“is”、“and”等（英文）。停用词过滤的目的是去除这些词汇，减少噪声，提高模型的效率和准确性。
- 实现逻辑：
  + 准备停用词列表：
  停用词列表可以手动定义，也可以使用现成的停用词库（如nltk.corpus.stopwords）。
  对于中文，可以使用jieba自带的停用词列表或自定义的停用词文件。
  + 过滤停用词：
  遍历分词后的单词列表，移除停用词列表中的单词。
### 特征构建过程

#### 1. 数学表达形式
##### 1.1 高频词特征选择
高频词特征选择的核心是选择在文本中出现频率最高的词作为特征。其数学表达形式可以描述为：

$$ 
\text{Top-K Words} = \text{argmax}_{\text{words}} \sum_{d \in D} \text{count}(w, d) 
$$

其中：
- $$ \text{count}(w, d) $$ 表示词 $ w $ 在文档 $ d $ 中出现的次数。
- $ D $ 是文档集合。
- $$ \text{Top-K Words} $$ 是出现频率最高的 $ K $ 个词。

##### 1.2 TF-IDF 特征加权
TF-IDF（Term Frequency-Inverse Document Frequency）特征加权是一种综合考虑词频和逆文档频率的特征选择方法。其数学表达形式为：

$$
\text{TF-IDF}(t, d) = \text{TF}(t, d) \times \text{IDF}(t)
$$

其中：
- $ \text{TF}(t, d) $ 是词 $ t $ 在文档 $ d $ 中的词频，计算公式为：
  $$
  \text{TF}(t, d) = \frac{\text{count}(t, d)}{\sum_{w \in d} \text{count}(w, d)}
  $$
- $ \text{IDF}(t) $ 是词 $ t $ 的逆文档频率，计算公式为：
  $$
  \text{IDF}(t) = \log \left( \frac{|D|}{|\{d \in D : t \in d\}|} \right)
  $$
- $ |D| % 是文档总数。
- $ |\{d \in D : t \in d\}| $ 是包含词 $ t $ 的文档数量。

#### 2. 实现差异

##### 2.1 高频词特征选择
实现高频词特征选择的步骤如下：
1. **统计词频**：计算每个词在所有文档中出现的总次数。
2. **选择高频词**：根据词频排序，选择出现频率最高的 \( K \) 个词作为特征。

##### 2.2 TF-IDF 特征加权
实现 TF-IDF 特征加权的步骤如下：
1. **计算词频（TF）**：统计每个词在每个文档中的出现次数。
2. **计算逆文档频率（IDF）**：统计包含每个词的文档数量，并计算其逆文档频率。
3. **计算 TF-IDF 值**：将词频和逆文档频率相乘，得到每个词的 TF-IDF 值。



#### 3. 差异总结

##### 3.1 优势
- **高频词特征选择**：
  - 实现简单，计算效率高。
  - 适用于特征数量较少且高频词具有较强区分能力的场景。
- **TF-IDF 特征加权**：
  - 考虑了词在文档中的重要性，能够有效过滤掉常见但不重要的词。
  - 对于文本分类、信息检索等任务表现更好。

##### 3.2 劣势
- **高频词特征选择**：
  - 可能忽略一些低频但重要的词。
  - 对于包含大量停用词的文本效果不佳。
- **TF-IDF 特征加权**：
  - 计算复杂度较高，需要遍历整个文档集合。
  - 对于某些特定领域或小数据集，可能需要调整平滑参数。

通过对比分析可以看出，高频词特征选择和 TF-IDF 特征加权各有优劣。在实际应用中，可以根据具体任务的需求和数据特点选择合适的特征选择方法。
***
## 高频词/TF-IDF 两种特征模式的切换方法
实现特征选择方法的参数化切换机制，允许通过传入参数在以下两种特征提取方式间灵活选择：
- 高频词特征（即原代码的特征选择方式）
- TF-IDF加权特征

根据classify.ipynb后续代码
- 代码说明：
  + 类FeatureSelector：
  初始化时通过method参数指定特征选择方法，可选'high_frequency'或'tf_idf'。
  + fit_transform方法根据指定方法提取特征：
    * 高频词特征：使用CountVectorizer计算词频矩阵，选择词频最高的top_k个词作为特征。
    * TF-IDF加权特征：使用TfidfVectorizer直接计算TF-IDF加权特征。
  + 示例用法：
  通过method参数切换特征选择方法。分别提取高频词特征和TF-IDF加权特征，并打印结果。

***
## 样本平衡处理
***
## 增加模型评估指标

