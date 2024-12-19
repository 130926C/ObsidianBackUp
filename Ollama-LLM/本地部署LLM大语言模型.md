
参考链接：
* [用ollama在macbook 上部署LLM](https://zhuanlan.zhihu.com/p/712652401)；
* [在 Apple Silicon Mac（M1、M2 或 M3）上运行最新 LLM 模型 Meta Llama 3 的分步指南](https://www.cnblogs.com/duanweishi/p/18148158)；
* [Ollama完整教程：本地LLM管理、WebUI对话、Python/Java客户端API应用](https://blog.csdn.net/obullxl/article/details/140336838?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-140336838-blog-143400940.235%5Ev43%5Econtrol&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-140336838-blog-143400940.235%5Ev43%5Econtrol&utm_relevant_index=2)
* [【ollama】手把手教你布置本地AI语言模型 在本地运行GPT4！](https://www.bilibili.com/video/BV119vre6EiH/?vd_source=c8dbe5ab3b4bf743fae13d455b4aa039)

---

推荐使用 `LLama + Docker + OpenWebUI` 的形式在本地进行大语言模型部署。

### Step1. 在官网下载 LLama 安装包

进入 LLama [官网](https://ollama.com) 找到对应操作系统版本号的安装包点击下载；

![ollam官网-1](ollama官网-1.jpeg)

### Step2 在官网找到感兴趣的离线模型

在官网右上角的 “Model” [界面](https://ollama.com/search) 输入关键字查找自己感兴趣的模型；

![ollama搜索页面](ollama搜索页面.jpeg)

### Step3. 复制查找到的模型运行命令

在这一步通常可以先选择合适的模型参数，以 `odellama` 为例，选择 `7b` 模型后复制运行命令 `ollama run codellama:7b` 到本地终端执行，如果本地没有这个模型则会自动进行下载

![codellama下载页面](codellama下载页面.jpeg)

```shell
(base) ~ $ ollama run codellama
pulling manifest 
pulling 3a43f93b78ec... 100% ▕██████████████████████████▏ 3.8 GB                 verifying sha256 digest 
writing manifest 
success 
>>> Send a message (/? for help)
```

### Step4. 在终端向该模型提问

如果本地已经下载好该模型了，可以直接打开终端并运行命令：
```shell
(base) ~ $ ollama run codellama
>>> Send a message (/? for help)
```

----

### 【可选】Step5. 使用 Open WebUI 以网页的形式打开模型对话框

使用下面的命令下载 Open WebUI 的 docker：
```shell
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

如果本地docker 没有这个容器，那么会执行自动下载：

```shell

(base) ~ $ docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
Unable to find image 'ghcr.io/open-webui/open-webui:main' locally
main: Pulling from open-webui/open-webui
83d624c4be2d: Downloading  9.173MB/29.16MB
e9825a162d70: Download complete 
6244a9440d89: Downloading  13.64MB/16.12MB
1fdd99135f48: Download complete 
ff5b65357ed1: Download complete 
4f4fb700ef54: Download complete 
0765afc864d3: Download complete 
bd13f2f04279: Download complete 
8154ee5b0626: Downloading  9.175MB/333.8MB
288a2c0b5840: Waiting 
7c3741f4c80f: Waiting 
eb5624ad1c4d: Waiting 
c8810110e55f: Waiting 
6001e6cc1e57: Waiting 
eb4b02bece24: Waiting 
...
```

安装完成后进入 Docker Desktop 界面查看 Open WebUI 映射的端口号，通常是将 3000 映射到 8080 ，那么直接在浏览器中输入  `http://localhost:3000/` 即可进入 Web UI。

![docker容器](docker容器.jpeg)

如果弹出注册窗口随便注册一个即可，该信息并不会访问互联网，仅在本地存储；

![webui](webui.jpeg)

---

### Step6. MaxOS 中使用代码调用

参考链接：
* [linux服务器，安装ollama后](https://gitcode.csdn.net/66c6d3619a494d224f74da3b.html?dp_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6NDkxNjA2LCJleHAiOjE3MzI2MjQ4NjAsImlhdCI6MTczMjAyMDA2MCwidXNlcm5hbWUiOiJuZW5jaG91bWkzMTE5In0.jupB7rdF2t8kDt4F1orhqe8hb7ClVpYPw0_UIfADfSE&spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Eactivity-1-137452942-blog-138995214.235%5Ev43%5Econtrol&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Eactivity-1-137452942-blog-138995214.235%5Ev43%5Econtrol&utm_relevant_index=2)
* [ollama调用本地模型时出现502](https://blog.csdn.net/qq_52052771/article/details/137543187)
* [解决MacOS下502问题](https://github.com/ollama/ollama/issues/5437)
* [ollama实践之：python代码生成与执行](https://blog.csdn.net/cladel/article/details/143708779)

安装必要的库：
```shell
$ pip install ollama, requests
```

查看下 ollama 默认的端口是否被占用，说明 ollama 运行正常：
```shell
$ (qtgui) ~/Desktop/exercise/scripts $ lsof -i :11434
COMMAND   PID   USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
ollama  19097 gaohao    3u  IPv4 0xc74cc3ed18efed67      0t0  TCP localhost:11434 (LISTEN)
```

如果在 MacOS 上请求 codellama 反复遇到 502 问题，那么执行下面的语句：
```shell
$ unset http_proxy https_proxy
```

执行完上述操作后在确保 codellama 已经正在运行，如果没有运行则可以直接 `ollama run codellama` 运行 codellama ，使用下面的脚本进行一次示范，这里使用的 Curl 的 POST 模式：

```python
import requests
import json
import subprocess

# Step 1: 定义 Ollama API 的 URL 和请求的 prompt
api_url = "http://localhost:11434/api/generate"

# Step 2: 配置请求数据
data = {
    "model": "codellama",
    "prompt": "Write a Python function to calculate the factorial of a number.\ndef calculate_factorial(n):\n# Call the function and print result\n# print(calculate_factorial(5))",
    "options": {
        "temperature": 0.2,
        "max_tokens": 100
    }
}

# Step 3: 发送 POST 请求到 Ollama API
response = requests.post(api_url, headers={"Content-Type": "application/json"}, data=json.dumps(data), stream=True)

# 检查请求是否成功
if response.status_code == 200:
    # 检查 API 返回的原始内容
    print("Raw Response:", response.text)

    generated_code = ""

    # 逐行处理响应内容
    for line in response.iter_lines():
        if line:
            # 将每行内容加载为 JSON
            line_data = json.loads(line.decode('utf-8'))
            generated_code += line_data.get("response", "")
            # 检查是否生成完成
            if line_data.get("done", False):
                break

    print("Generated Code:\n", generated_code)

    # # Step 4: 将生成的代码保存到文件中 - 这里存在bug，会在开头生成一个多余行导致无法运行
    # with open("generated_code.py", "w") as code_file:
    #     code_file.write(generated_code)

    # Step 5: 执行生成的代码
    #bprint("\nExecuting Generated Code:")
    # result = subprocess.run(["python", "generated_code.py"], capture_output=True, text=True)
    # print(result.stdout)  # 输出代码执行结果
else:
    print("Error:", response.status_code, response.text)

```