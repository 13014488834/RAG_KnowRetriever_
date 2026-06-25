# RAG 问答系统

基于 **LangChain + DeepSeek + Chroma** 的知识库问答系统，支持 **混合检索（BM25 + 语义）+ Reranker 精排**。

**PDF / TXT 知识库 · CLI 命令行 · Web 聊天界面 · Streamlit Cloud 公网部署。**

## 使用方式

### 🌐 在线版（无需安装）

👉 **https://aucodhdcwzwwjmucdqhxqz.streamlit.app/**

浏览器打开即用，上传 PDF/TXT 知识文件，填 DeepSeek API Key，直接问答。

### 💻 本地版（完整功能：混合检索 + Reranker）

```bash
# 1. 装依赖
pip install -r requirements.txt

# 2. 下载模型（~1.4GB，只需一次）
python setup_models.py

# 3. 配置 Key
#    创建 .env，写入: DEEPSEEK_API_KEY=sk-你的密钥

# 4. 启动 Web
streamlit run web_app.py
# → http://localhost:8501
```

- 📁 上传 PDF/TXT，自动向量化
- 🔀 混合检索（BM25 + 语义，RRF 融合）
- 🎯 Cross-Encoder Reranker 精排
- 🔒 API Key 不存储、不泄露

### ⌨️ CLI 命令行

```bash
python rag_system.py                              # 纯 knowledge.txt
python rag_system.py --pdf contract.pdf            # 带 PDF
python rag_system.py --rebuild                     # 强制重建向量库
python rag_system.py --no-hybrid --no-reranker     # 关闭混合检索/Reranker
```

## 项目结构

```
├── rag_core.py          # 核心管道（嵌入、向量库、LLM、混合检索、Reranker）
├── pdf_loader.py        # PDF/TXT 文件加载
├── rag_system.py        # CLI 命令行入口
├── web_app.py           # 本地 Web 界面（完整功能）
├── web_app_cloud.py     # 云端 Web 界面（Streamlit Cloud）
├── setup_models.py      # 模型下载脚本（嵌入 + Reranker）
├── knowledge.txt        # 默认知识库
├── requirements.txt     # 依赖
├── .env                 # API Key（不上传 Git）
```

## 技术栈

| 组件 | 用途 |
|------|------|
| LangChain | RAG 管道编排 |
| DeepSeek API | 大模型生成答案 |
| Chroma | 向量数据库（语义检索） |
| BM25 | 关键词检索（混合检索） |
| RRF | Reciprocal Rank Fusion（双路融合） |
| Cross-Encoder | Reranker 精排 |
| Streamlit | Web 界面 |
| pypdf | PDF 文本提取 |

## 常见问题

**Q: 嵌入模型下载失败？**
A: 国内用户 `setup_models.py` 默认走 ModelScope。如果还是失败，手动设镜像：
```bash
set HF_ENDPOINT=https://hf-mirror.com
python setup_models.py
```

**Q: DeepSeek API 返回 401 错误？**
A: 检查 `.env` 里的 Key 格式，应该是 `sk-` 开头，不要有多余的 `sk-` 前缀。去 https://platform.deepseek.com 重新生成一个。

**Q: PDF 上传后提示"未找到可提取的文本"？**
A: 你的 PDF 是扫描件（图片），需要先 OCR 识别。换成文字型 PDF 或直接上传 TXT。

**Q: 回答和知识库内容不相关？**
A: 可能是向量库没重建。删除 `chroma_db/` 文件夹，重新启动即可自动重建。或者用 `--rebuild` 参数。

**Q: 启动报 `port 8501 already in use`？**
A: 端口被占用了。换个端口：
```bash
streamlit run web_app.py --server.port 8502
```

**Q: Chroma 报错或向量库损坏？**
A: 删掉 `chroma_db/` 文件夹重新启动，系统会自动重建向量库。

**Q: Reranker 模型加载失败？**
A: 确保 `python setup_models.py` 完整执行。如果 ModelScope 不可用，脚本会自动回退到 HuggingFace 镜像下载。
