# chat_vllm

[千问官方部署文档](https://github.com/QwenLM/Qwen?tab=readme-ov-file#deployment)

* 离线推理vllm_wrapper.py实现参考了[Qwen官方实现](https://github.com/QwenLM/Qwen/blob/main/examples/vllm_wrapper.py)
* 在线推理vllm_server.py和vllm_client.py实现参考了[vLLM官方实现-异步服务端](https://github.com/vllm-project/vllm/blob/main/vllm/entrypoints/api_server.py)、[vLLM官方实现-异步客户端](https://github.com/vllm-project/vllm/blob/main/examples/api_client.py)


# 核心技术原理

探索生产环境下的高并发推理服务端搭建方法，核心工作非常清晰，边角细节没有投入太多精力

* vLLM支持Continuous batching of incoming requests高并发批推理机制，其SDK实现是在1个独立线程中运行推理并且对用户提供请求排队合批机制，能够满足在线服务的高吞吐并发服务能力
* vLLM提供asyncio封装，在主线程中基于uvicorn+fastapi封装后的asyncio http框架，可以实现对外HTTP接口服务，并将请求提交到vLLM的队列进入到vLLM的推理线程进行continuous batching批量推理，主线程异步等待推理结果，并将结果返回到HTTP客户端
* vLLM天然支持流式返回next token，基于fastapi可以按chunk流式返回流式推理成果，在客户端基于requests库流式接收chunk并复写控制台展示，实现了流式响应效果


## Requirements
OS：ubuntu20.04 
python：3.10
cuda：12.1 
没有在其他版本环境测试过

pip install -r req
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


