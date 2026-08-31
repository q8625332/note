---
name:ljq-docx-format
description:使用python-docx精确读取、分析、修改Word文档（.docx）格式。当用户需要：(1)分析文档格式（字体/字号/颜色/缩进/行距），(2)批量修改文档格式，(3)统一文档排版规范，(4)处理中英文混排字体，(5)修改交叉引用/参考文献样式时使用此skill。
---
# Word 文档格式处理

## 概述

使用python-docx精确操作Word文档格式，适用于格式分析、格式规范化、批量修改。

**运行环境**：

```

uv run --with python-docx python3 script.py

# 或者

pip install python-docx

```

## 快速开始


```

from docx import Document
from docx.shared import Pt, RGBColor
from docx.oxml.ns import qn

doc = Document('input.docx')

# 遍历段落
for para in doc.paragraphs:
    for run in para.runs:
        # 设置字体（中英文分设）
        run.font.name = 'Times New Roman'
        run._element.rPr.rFonts.set(qn('w:eastAsia'), '宋体')
        run.font.size = Pt(10.5)

doc.save('output.docx') 


```

## 核心操作

### 遍历文档内容


```
# 遍历普通段落
for para in doc.paragraphs:
    print(para.text)

# 遍历表格（文档正文常在表格内）
for table in doc.tables:
    for row in table.rows:
        for cell in row.cells:
            for para in cell.paragraphs:
                print(para.text)
```



### 读取格式

```

def emu_to_pt(emu):
    """EMU 转 pt"""
    return round(emu / 914400 * 72, 1) if emu else None

# 字体格式
run.font.name        # 英文字体
run.font.size        # 字号（EMU，需转换）
run.font.bold        # 加粗
run.font.color.rgb   # 颜色

# 段落格式
para.paragraph_format.first_line_indent  # 首行缩进
para.paragraph_format.left_indent        # 左缩进
para.paragraph_format.line_spacing       # 行距
para.alignment                           # 对齐（0左 1中 2右 3两端）

```

### 修改格式


```
from docx.shared import Pt, RGBColor
from docx.oxml.ns import qn

# 设置中英文字体
def set_font(run, cn='宋体', en='Times New Roman', size=10.5, bold=None):
    run.font.name = en
    run._element.rPr.rFonts.set(qn('w:eastAsia'), cn)
    run.font.size = Pt(size)
    if bold is not None:
        run.font.bold = bold

# 设置颜色
run.font.color.rgb = RGBColor(0, 0, 255)  # 蓝色

# 首行缩进
para.paragraph_format.first_line_indent = Pt(21)  # 2字符

# 悬挂缩进（参考文献）
para.paragraph_format.first_line_indent = Pt(-21)
para.paragraph_format.left_indent = Pt(21)
```


## 常见任务

### 批量统一正文格式

```

for table in doc.tables:
    for row in table.rows:
        for cell in row.cells:
            for para in cell.paragraphs:
                if len(para.text.strip()) > 30:  # 正文段落
                    para.paragraph_format.first_line_indent = Pt(21)
                    for run in para.runs:
                        set_font(run, cn='宋体', en='Times New Roman', size=10.5)

```

### 格式分析统计


```
from collections import defaultdict

size_stats = defaultdict(int)
for para in doc.paragraphs:
    for run in para.runs:
        if run.font.size:
            size_stats[emu_to_pt(run.font.size)] += 1

print("字号分布:", dict(size_stats))
```


### 交叉引用变色


```
import re
ref_pattern = re.compile(r'\[\d+\]')

for para in doc.paragraphs:
    for run in para.runs:
        if ref_pattern.search(run.text):
            run.font.color.rgb = RGBColor(0, 0, 255)
```


## 常用字号参考

| 名称 | pt值   | 用途     |
| ---- | ------ | -------- |
| 三号 | 16pt   | 大标题   |
| 小三 | 15pt   | 章节标题 |
| 四号 | 14pt   | 节标题   |
| 小四 | 12pt   | 小标题   |
| 五号 | 10.5pt | 正文     |
| 小五 | 9pt    | 脚注     |

 **首行缩进** ：五号字2字符≈21pt，小四字2字符≈24pt

## 注意事项

1. **只支持 .docx** ：不支持旧版 .doc
2. **中文字体** ：必须用 `qn('w:eastAsia')` 单独设置
3. **表格内容** ：开题报告等文档正文常在表格单元格内
4. **单位转换** ：font.size 返回 EMU，需除以 914400×72 转 pt
