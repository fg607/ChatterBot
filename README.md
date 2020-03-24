# ChatterBot中文适配版

ChatterBot中文适配版，使用[jieba](https://github.com/fxsjy/jieba) 中文分词组件和中文停词表，对input_statement进行中文分词处理，生成input_statement.search_text，用于从数据库中精确匹配训练好的对话数据。


## 安装

```
git clone https://github.com/fg607/ChatterBot.git
```
```
pip3 install ./ChatterBot
```
### 中文分词核心代码
中文分词核心代码位于chatterbot/tagging.py中的ChineseTagger类
```
class ChineseTagger(object):

    """
    Handling chinese text 
    """

    def __init__(self, language=None):
        
        self.stopword=[]
        #从停用词文件中加载中文停用词
        cfp=open(os.path.dirname(__file__) + '/data/cn_stopwords.txt','r+','utf-8')  
        for line in cfp:
            for word in line.split():
                self.stopword.append(word)
        cfp.close()

    def get_bigram_pair_string(self, text):
        """
        Return a string of text containing part-of-speech, lemma pairs.
        """
        bigram_pairs = []

	 #利用正则表达式去掉一些一些标点符号之类的符号。
        text = re.sub(r'\s+', ' ', text)  # trans 多空格 to空格
        text = re.sub(r'\n+', ' ', text)  # trans 换行 to空格
        text = re.sub(r'\t+', ' ', text)  # trans Tab to空格
        text = re.sub("[\s+\.\!\/_,$%^*(+\"\']+|[+——；！，”。《》，。：“？、~@#￥%……&*（）1234567①②③④)]+".\
                          encode().decode("utf8"), "".encode().decode("utf8"), text)
  
        wordlist = list(jieba.cut(text))#jieba.cut  把字符串切割成词并添加至一个列表
        for word in wordlist:
            if word not in self.stopword:#词语的清洗：去停用词
                if word != '\r\n'  and word!=' ' and word != '\u3000'.encode().decode('unicode_escape') \
                        and word!='\xa0'.encode().decode('unicode_escape'):#词语的清洗：去全角空格
                    bigram_pairs.append(word)

       
        return ' '.join(bigram_pairs)
```
**中文停用词文件位于chatterbot/data/cn_stopwords.txt,可替换为适用于自己项目的中文停用词**

### 如何改回默认的英文版本
修改chatterbot/storage/storage_adapter.py为如下内容：
```
from chatterbot.tagging import PosHypernymTagger #将ChineseTagger改为默认的PosHypernymTagger


class StorageAdapter(object):
    """
    This is an abstract class that represents the interface
    that all storage adapters should implement.
    """

    def __init__(self, *args, **kwargs):
        """
        Initialize common attributes shared by all storage adapters.
        """
        self.logger = kwargs.get('logger', logging.getLogger(__name__))

        self.tagger = PosHypernymTagger(language=kwargs.get(
            'tagger_language', languages.ENG
        ))#将ChineseTagger改为默认的PosHypernymTagger，并将languages.CHI改为默认的languages.ENG
```

# Thanks

中文分词方法参考了简书作者 Hellooooooworld的文章[《结巴分词和NLTK----一套中文文本分析的组合拳》](https://www.jianshu.com/p/aea87adee163)