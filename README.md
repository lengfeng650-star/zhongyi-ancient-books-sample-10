---
license: Apache License 2.0
tags:
- 中医药古籍
- 古籍数字化
- traditional-chinese-medicine
- classical-chinese
- OCR
configs:
- config_name: default
  data_files:
  - split: train
    path: metadata/books_manifest.jsonl
---

# 未分卷中医药古籍样本数据集

本数据集收录 10 种中医药古籍数字化资料，每种古籍保留一个独立目录，包含 Markdown 识别文本、PDF 原件和分卷表。数据来源于“未分卷中医药古籍 / Part1 / 未分卷ZP”内部整理目录，本次发布为其中 10 个书目的开源样本。

数据不是 JSON-only。仓库中既有结构化清单 JSONL，也有古籍全文 Markdown、PDF 原件和 Excel 分卷表。ModelScope 的 `configs` 指向 `metadata/books_manifest.jsonl`，用于数据预览和 SDK 加载；完整古籍文件在 `data/books/` 下按书目目录保存。

如需了解更多中医药古籍数字化数据、定制语料整理、OCR 处理、知识库建设或批量授权合作，可发送邮件至 zhouhaoran@shujuyoupu.com。

## 数据集简介

- 数据类型：中医药古籍数字化文本、PDF 原件、分卷元数据。
- 书目数量：10 种。
- 文件数量：30 个核心文件，另含目录占位文件。
- 主题范围：养生、妇产、儿科、伤寒舌诊、白喉专病、方药丸散、综合医论等。
- 文本形态：以繁体字、异体字和古籍 OCR/整理文本为主，保留部分缺字、疑似识别符号和页码结构。
- 适用任务：古籍 OCR 后处理、繁简/异体字处理、专名识别、方药信息抽取、古籍问答、医学文献检索、数字人文语料分析。

## 数据结构

```text
open_data/modelscope/zhongyi-ancient-books-sample-10/
  README.md
  data/
    books/
      保命集_03-05-046/
        保命集.md
        保命集.pdf
        分卷表.xlsx
      ...
  metadata/
    books_manifest.jsonl
    files_manifest.jsonl
```

每个书目目录通常包含：

- `*.md`：古籍正文数字化文本，按页组织，常见标题形式为 `## 第 N 页`。
- `*.pdf`：古籍扫描或整理原件。
- `分卷表.xlsx`：分卷、页码或结构信息表。

## 书目清单

| book_id | 题名 | 主题 | Markdown 字符数 | PDF 大小 |
|---|---|---|---:|---:|
| book-001 | 保产经验简便良方 | 妇产科方书 | 896 | 0.62 MB |
| book-002 | 保命集 | 综合医论与方药 | 69,849 | 118.49 MB |
| book-003 | 保生碎事 | 胎教与养生 | 4,595 | 4.28 MB |
| book-004 | 安老怀幼书 | 养老幼科与家庭调护 | 133,646 | 218.98 MB |
| book-005 | 敖氏伤寒金镜录 | 伤寒舌诊 | 6,673 | 14.97 MB |
| book-006 | 白喉吹药方 | 白喉专病方药 | 2,201 | 1.14 MB |
| book-007 | 白喉条辨 | 白喉专病辨治 | 12,914 | 17.94 MB |
| book-008 | 白喉条辨 | 白喉专病辨治 | 14,474 | 32.75 MB |
| book-009 | 白喉治法忌表抉微 | 白喉专病治法 | 10,789 | 14.18 MB |
| book-010 | 胞与堂丸散谱 | 丸散方谱 | 31,794 | 38.81 MB |

说明：`book-007` 和 `book-008` 均为《白喉条辨》，但来源编号不同，目录和 PDF 文件不同，保留为两个独立样本。

## 清单字段

`metadata/books_manifest.jsonl` 每行是一种古籍：

```json
{
  "book_id": "book-001",
  "title": "保产经验简便良方",
  "folder": "保产经验简便良方_DLJ0033934",
  "topic": "妇产科方书",
  "description": "篇幅较短，正文以产科经验方、补益调养药味和用量为主。",
  "markdown_path": "data/books/保产经验简便良方_DLJ0033934/保产经验简便良方.md",
  "pdf_path": "data/books/保产经验简便良方_DLJ0033934/保产经验简便良方.pdf",
  "volume_table_path": "data/books/保产经验简便良方_DLJ0033934/分卷表.xlsx",
  "markdown_chars": 896,
  "markdown_lines": 74,
  "pdf_size_mb": 0.62,
  "sample_text": ["保産經驗簡便良方"]
}
```

`metadata/files_manifest.jsonl` 每行是一个文件，包含 `book_id`、`title`、`file_name`、`file_path`、`file_type`、`size_bytes`、`size_mb`。

## 内容特点

本批样本保留了古籍文本的原始整理状态，适合做真实场景下的古籍文本处理实验：

- 正文多为繁体字和古文行文。
- 部分文本包含缺字、异体字、OCR 不确定字符或排版残留。
- Markdown 按页组织，便于和 PDF 原件进行页级对照。
- 白喉相关书目较集中，可作为近代专病文献的小样本集合。
- 《敖氏伤寒金镜录》涉及伤寒舌诊，《胞与堂丸散谱》偏方药制剂，《安老怀幼书》《保生碎事》偏养生、胎教和家庭调护。

## 加载方式

通过 ModelScope SDK 加载结构化清单：

```python
from modelscope import MsDataset

ds = MsDataset.load(
    "zhongyi-ancient-books-sample-10",
    namespace="SHPDataGR",
    subset_name="default",
    split="train",
    version="master",
)

print(ds[0]["title"])
print(ds[0]["markdown_path"])
```

如果需要读取全文，请根据 `markdown_path` 定位到对应 Markdown 文件；如需核对原件，请使用 `pdf_path`。

## 数据质量和限制

- 本数据集是 10 种古籍样本，不代表完整中医药古籍库。
- Markdown 文本可能存在 OCR 错误、缺字、异体字、断行和标点不一致。
- PDF 文件体积差异较大，下载和预览速度取决于网络环境。
- `分卷表.xlsx` 的字段结构可能随原始书目整理方式不同而变化。
- 本数据集仅用于文献整理、研究、模型训练与评测等合规用途，不构成医学诊疗建议。

## 合规和版权说明

发布前应确认本批古籍资料具备开源发布条件，且不包含现代个人隐私信息、内部审核记录或未授权资料。若使用者基于本数据进行研究、训练或再发布，应遵守数据集许可证及所在平台规则。

## 合作与联系

本数据集为中医药古籍数字化样本。若需要更多古籍全文、高清影像、结构化目录、方药知识抽取、私有化语料建设或模型训练数据合作，请发送邮件至 zhouhaoran@shujuyoupu.com。

## 引用方式

暂无固定论文引用格式。使用时建议注明数据集名称、书目题名、版本和访问平台。
