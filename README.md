# Chinese-Transformer-XL
Under construction 

本项目提供了智源研究院"文汇"预训练模型Chinese-Transformer-XL的预训练和文本生成代码。

## 数据
本模型使用了智源研究院发布的中文预训练语料[WuDaoCorpus](https://data.baai.ac.cn/data-set-details/0c8dc71dd06ae75a10ca422fb49b0751) 。具体地，我们使用了WuDaoCorpus中来自百度百科（133G）、知乎（131G）、百度知道（38G）的语料，一共303GB数据。

## 模型


## 结果

## 安装
首先根据`requirements.txt`安装pytorch等基础依赖
```shell
pip install -r requirements.txt
```
然后安装[APEX](https://github.com/NVIDIA/apex#quick-start) 以支持`FusedLayerNorm`和`FusedAdam`
```shell
git clone https://github.com/NVIDIA/apex
cd apex
pip install -v --disable-pip-version-check --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./
```

## 使用

## 引用