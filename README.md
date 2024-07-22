# 去他妈的垃圾阿里云盘限速
开了几把会员来限流量，该方法用于干掉该限制.

# 跑以下代码，然后扫码后获取refresh-token
```python
import time
from io import BytesIO

import requests
from PIL import Image

if __name__ == '__main__':
    data = requests.post('http://api.extscreen.com/aliyundrive/qrcode', data={
        'scopes': ','.join(["user:base", "file:all:read", "file:all:write"]),
        "width": 500,
        "height": 500,
    }).json()['data']
    qr_link = data['qrCodeUrl']
    sid = data['sid']
    # 两种登录方式都可以
    # 手机阿里云盘扫描登录
    Image.open(BytesIO(requests.get(qr_link).content)).show()
    while True:
        time.sleep(3)
        status_data = requests.get(f'https://openapi.alipan.com/oauth/qrcode/{sid}/status').json()
        status = status_data['status']
        if status == 'LoginSuccess':
            auth_code = status_data['authCode']
            break
    # 使用code换refresh_token
    token_data = requests.post('http://api.extscreen.com/aliyundrive/token', data={
        'code': auth_code,
    }).json()['data']
    refresh_token = token_data['refresh_token']
    print(f'refresh_token: {refresh_token}')
```

# 部署
```
# 在服务端部署
docker-compose up -d 
```

# 修改alist挂载选项
注意ip是你服务端的ip，别照抄
![QQ_1721689597932](https://github.com/user-attachments/assets/35d9cd61-339f-43ab-a708-0e5b1656aeaa)

然后把第一步获取的refresh-token 也填进去，然后alist刷新下云盘文件，即可在上面部署的服务看到对应的日志
![QQ_1721689778551](https://github.com/user-attachments/assets/1d6a4daa-7694-4133-95d9-dbabd902c84c)

# 免责声明
仅用于学习交流，请勿用于违法犯罪.

