# ChatterBot中文适配版

ChatterBot中文适配版，使用[jieba](https://github.com/fxsjy/jieba) 中文分词组件和中文停词表，对input_statement进行中文分词处理，生成input_statement.search_text，用于从数据库中匹配训练好的对话。


## 安装

```
git clone https://github.com/fg607/ChatterBot.git
```
```
pip3 install ./ChatterBot
```

# Thanks

中文分词方法参考了简书作者 Hellooooooworld的文章[《结巴分词和NLTK----一套中文文本分析的组合拳》](https://www.jianshu.com/p/aea87adee163)