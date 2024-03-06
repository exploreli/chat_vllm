# chat_vllm
参考qwen的vllm实现，实现了vllm本地部署并发推理大模型。
[千问官方部署文档](https://github.com/QwenLM/Qwen?tab=readme-ov-file#deployment)

* 离线推理vllm_wrapper.py实现参考了[Qwen官方实现](https://github.com/QwenLM/Qwen/blob/main/examples/vllm_wrapper.py)
* 在线推理vllm_server.py和vllm_client.py实现参考了[vLLM官方实现-异步服务端](https://github.com/vllm-project/vllm/blob/main/vllm/entrypoints/api_server.py)、[vLLM官方实现-异步客户端](https://github.com/vllm-project/vllm/blob/main/examples/api_client.py)


## Requirements
OS：ubuntu20.04 
python：3.10
cuda：12.1 
没有在其他版本环境测试过

## Quickstart
pip install -r requirements.txt

*vllm-gptq需要单独通过源码安装
```
git clone https://github.com/QwenLM/vllm-gptq
cd vllm-gptq
pip install -e .
```
## 离线推理

python程序直接拉起模型，本地推理的方式。
```
python vllm_offline.py
```
## 在线推理

启动一个远端python http服务端，通过http客户端调用的方式，并且可以流式返回推理结果。

启动HTTP服务端:
```
python vllm_server.py
```

启动HTTP客户端
```
python vllm_client.py
```

## webui

启动vllm_server后，可以再单独运行gradio_webui.py，它是基于gradio实现的聊天webui，支持多轮对话和流式应答，底层会与vllm_server实时远程调用。

```
python gradio_webui.py
```


