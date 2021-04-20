# Chinese-Transformer-XL

Under construction

本项目提供了智源研究院"文汇"
预训练模型Chinese-Transformer-XL的预训练和文本生成代码。[[应用主页]](https://gpt-3.aminer.cn/) [[模型下载]](http://dorc-model-team.ks3-cn-beijing.ksyun.com/ren-zhi/my-model/mp_rank_00_model_states.pt)

## 数据

本模型使用了智源研究院发布的中文预训练语料[WuDaoCorpus](https://data.baai.ac.cn/data-set-details/0c8dc71dd06ae75a10ca422fb49b0751)
。具体地，我们使用了WuDaoCorpus中来自百度百科+搜狗百科（133G）、知乎（131G）、百度知道（38G）的语料，一共303GB数据。

## 模型

本模型使用了[GPT-3](https://arxiv.org/abs/2005.14165)
的训练目标，同时使用能够更好地处理长序列建模的[Transformer-XL](https://arxiv.org/abs/1901.02860) 替代了GPT中的Transformer。模型的结构与GPT-3
2.7B（32层，隐表示维度2560，每层32个注意力头）基本相同，因为Transformer-XL的结构改动，模型参数增加到了29亿。

## 结果

为了验证模型的生成能力，我们在中文的开放域长文问答上进行了评测。我们从[知乎](https://www.zhihu.com)
上随机选择了100个不同领域的、不在训练语料中的问题。对每个问题，由人类测试员对一个高赞同数回答、3个模型生成的回答和3个[CPM](https://github.com/TsinghuaAI/CPM-Generate)
生成的回答在流畅度、信息量、相关度、总体四个维度进行打分。测评结果如下：

|模型|流畅度(1-5)|信息量(1-5)|相关度(1-5)|总体(1-10)|
|---|---|---|---|---|
|CPM|2.66|2.47|2.36|4.32|
|文汇|3.44|3.25|3.21|5.97|
|人类答案|3.80|3.61|3.67|6.85|

可以看到相比起CPM，"文汇"更接近人类所写的高赞答案。

## 安装

根据`requirements.txt`安装pytorch等基础依赖

```shell
pip install -r requirements.txt
```

如果要finetune模型参数，还需要安装[DeepSpeed](https://github.com/microsoft/DeepSpeed)

```shell
DS_BUILD_OPS=1 pip install deepspeed
```

## 推理

首先下载模型的[checkpoint](http://dorc-model-team.ks3-cn-beijing.ksyun.com/ren-zhi/my-model/mp_rank_00_model_states.pt) ，目录结构如下

```
.
└─ txl-2.9B
       └─ mp_rank_00_model_states.pt
```

然后运行交互式生成脚本

```shell
bash scripts/generate_text.sh ./txl-2.9B
```

## Finetune

模型的finetune基于使用DeepSpeed。首先在`scripts/ds_finetune_gpt_2.9B.sh`中修改`NUM_WORKERS`和`NUM_GPUS_PER_WORKER`
为使用的节点数目和每个节点的GPU数量。如果使用多机训练的话，还要修改`HOST_FILE_PATH`
为hostfile的路径（DeepSpeed使用[OpenMPI风格的hostfile](https://www.deepspeed.ai/getting-started/#resource-configuration-multi-node)
）。

然后运行finetune脚本

```shell
bash scripts/ds_finetune_gpt_2.9B.sh ./txl-2.9B ./data.json
```

其中`./txl-2.9B`为checkpoint目录。`./data.json`为finetune数据，格式为[jsonl文件](https://jsonlines.org/)
，每条数据的格式为`{"prompt": ..,  "text": ...}`。其中prompt为生成的context，text为生成的内容。

如果你在finetune的遇到了OOM错误（一般是因为GPU数量或者显存不足导致的），可以尝试在[scripts/ds_config_2.9B_finetune.json](scripts/ds_config_2.9B_finetune.json)的`zero_optimization`部分添加`"cpu_offload": true`，来开启[ZeRO-Offload](https://www.deepspeed.ai/tutorials/zero-offload/) 以减少显存消耗。
## 引用