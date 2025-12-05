- [首页](../README.md "Java Interview Docs")
1. 
    - Tokenization（分词）：通过字节对编码（BPE）分词方法将文本文本分解成token，每个词元都对应一个模型可以使用的整数 ID：token ID
    - Embedding向量化：每个 token ID 到 embedding_matrix 查找对应向量，把单词转换成它在高维空间中的语义位置
    - 加入Positional Encoding：让模型理解句子结构
    - Prefill 阶段：输入向量值会一次性通过所有层（例如 32 层）
    - 预测第一个输出 token：经过最后一层 Transformer 后，hidden_states 会投影到词表大小
    - Decode 阶段（逐 token 自回归生成）：生成一个 token 后，为了生成下一个 token只需为“新 token”计算一次 Q、K、V（注意力输入），之前 token 的 K 和 V 都从 KV Cache 中读取，不需要再算
    - Detokenization：把生成序列的 token ID解码成自然语言文本