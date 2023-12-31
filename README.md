本仓库为FT-Data Ranker：大语言模型微调数据竞赛 -- 1B模型赛道第13名队伍的代码仓库



本仓库包含用于复现本队伍结果所需的相关代码、数据和模型，原始数据和模型请参照[比赛页面的说明](https://tianchi.aliyun.com/competition/entrance/532157/customize404)



总体思路：

1. 修正数据中的不合理的unicode字符

2. 限制训练文本的ppl最大值以排除可能存在的低质量样本

3. 约束文本长度为380~2000（分词后）以提升模型处理长文本的能力

4. 过滤含有过多无意义字符的文本

5. 去重



该仓库内文件路径：



```
├──模型文件
│  ├──added_tokens.json
│  ├──config.json
│  ├──configuration_falcon.py
│  ├──generation_config.json
│  ├──merges.txt
│  ├──modeling_falcon.py
│  ├──pytorch_model.bin *
│  ├──special_tokens_map.json
│  ├──tokenizer_config.json
│  ├──trainer_state.json
│  ├──training_args.bin
│  ├──training_log.txt
│  └──vocab.json
├──模型训练代码
│  └──lm-training
│     ├──train_scripts
│     │  ├──deepspeed_configs
│     │  ├──deepspeed_train_1b.sh
│     │  └──deepspeed_train_7b_lora.sh
│     ├──convert_to_hf.py
│     └──train.py
├──数据处理代码
│  ├──alpaca-cot-en-refine.yaml
│  └──get_train_dataset_1b.py
├──数据文件.jsonl
└──README.md
```



复现方法：

安装、配置比赛页面“提交指南”中第一节所述的环境，并下载第二节中的原始基础模和原始数据集



仓库内"模型文件/pytorch_model.bin"文件可通过[百度网盘下载](https://pan.baidu.com/s/1ImFYfxIRkidOUc_KRdrnZQ?pwd=p4ns)



一、 数据处理复现

1. 更改文件"数据处理代码/alpaca-cot-en-refine.yaml"中的dataset_path字段为下载的原始数据集competition_kit/data/raw_data/raw_data_en.jsonl的路径
2. 更改文件"数据处理代码/alpaca-cot-en-refine.yaml"中的export_path字段为保存处理后的数据集的路径，记为P1
3. 在"competition_kit/data-juicer"目录下，执行命令python tools/process_data.py --config <Absolute_Path>/数据处理代码/alpaca-cot-en-refine.yaml
4. 更改文件"数据处理代码/get_train_dataset_1b.py"中EN_DATA_DIR变量为处理后的数据集路径P1，OUTPUT_FILES变量为保存采样后的数据集的路径，记为P2
5. 在目录"数据处理代码"下，执行命令python get_train_dataset_1b.py
6. 文件P2即为处理后的数据，可用于下一阶段的微调
   
   

二、微调模型

1. 进入"competition_kit/lm-training"文件夹或者进入"模型训练代码/lm-training"文件夹，执行命令sh train_scripts/deepspeed_train_1b.sh <model_path> <data_path> <output_path>
   
   

   参数 <model_path> 为下载的原始基模的路径。



   参数 <data_path> 为您使用Data-juicer改良后的数据集， 即上述P2。



   参数 <output_path> 为保存您训练后的模型的路径。


