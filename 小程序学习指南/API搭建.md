- 本地测试API环境：Python3.x+vscode  
- 服务端环境：腾讯云服务器（国内）+Flask（Python3.x）框架+core（跨域库)
本地测试代码   
```
import requests
# 定义接口的 URL，假设 Flask 应用运行在本地主机的 3000 端口（可以设置任意端口，这里以3000端口为例）
url = 'http://（此处替换为服务器公网ip）:3000/login'

# 定义测试数据
test_data = {
    'username': 'zhangsan',
    'password': 'pwd123456',
    'code': 'test_code'
}

# 发送 POST 请求到 /login 接口
try:
    response = requests.post(url, json=test_data)
    
    # 检查响应状态码
    if response.status_code == 200:
        session_id = response.json().get('sessionId')
        print('登录成功，Session ID:', session_id)
    elif response.status_code == 400:
        print('请求失败，缺少必要的参数:', response.json().get('error'))
    elif response.status_code == 401:
        print('登录失败，用户名或密码错误:', response.json().get('error'))
    else:
        print('其他错误:', response.status_code, response.text)

except requests.RequestException as e:
    print('请求失败:', e)
```
服务端代码
```
