# Cursor长效Token获取服务

## 项目介绍

本服务提供Cursor API的长效访问令牌获取解决方案，彻底解决官方Token一小时过期的问题。无需再频繁手动注册或使用注册机，一次获取，长期有效。

## 核心优势

- ✅ **长效令牌**：通过本服务获取的Token比官方直接提供的Token使用期限更长
- ✅ **稳定可靠**：解决注册机不稳定或令牌频繁失效的问题
- ✅ **简单易用**：只需一个API调用，即可获取长效令牌
- ✅ **状态查询**：提供账号状态查询接口，实时掌握账号情况

## 使用方式

### 方式一：Web界面手动交互 (推荐)

直接访问我们的Web界面，无需编写代码即可获取长效Token：

**网址**：[https://token.cursorpro.com.cn](https://token.cursorpro.com.cn)

在Web界面上，您可以：
1. 输入您的WorkosCursorSessionToken
2. 点击获取按钮获取长效令牌
3. 查看Token详情（用户ID、过期时间、剩余天数等）
4. 检测账号状态
5. 复制获取的访问令牌和刷新令牌

**如何获取WorkosCursorSessionToken：**
1. 登录Cursor官方网站
2. 打开浏览器开发者工具(F12或右键-检查)
3. 切换到Application(应用程序)或Storage(存储)标签
4. 在左侧找到Cookies，并选择cursor.com
5. 找到名为"WorkosCursorSessionToken"的Cookie
6. 复制它的值并粘贴到输入框中

### 方式二：API接口调用

如果需要在应用中集成，可以使用我们的API接口：

## API接口说明

### 1. 获取/刷新Token接口

**URL**: `https://token.cursorpro.com.cn/reftoken`

**方法**: GET

**参数**:
- `token`: WorkosCursorSessionToken值（从Cursor网站Cookie获取）
- `force_refresh`: true（可选，强制刷新）

**返回格式**:
```json
{
  "code": 0,
  "msg": "获取成功",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",  // 用于API调用的访问令牌
    "refreshToken": "eyJhbGciOiJIUzI1NiIs...",  // 用于刷新访问令牌
    "user_id": "user_01JR58P7SG66YN9H4RX1A9EDR1",  // 用户ID
    "expire_time": "2025-06-10 14:26:52",  // 过期时间
    "days_left": 60  // 剩余有效天数
  }
}
```

### 2. 账号状态检测接口

**URL**: `https://token.cursorpro.com.cn/checkcursor/getcursorstatus`

**方法**: GET

**参数**:
- `token`: 访问令牌(accessToken)

**返回格式**(正常账号):
```json
{
  "status": "0",
  "account_type": "free_trial",  // 账号类型
  "account_status": "试用账号",  // 账号状态中文描述
  "profile_data": {
    "membershipType": "free_trial",
    "daysRemainingOnTrial": 10  // 剩余试用天数
  }
}
```

**返回格式**(被封账号):
```json
{
  "status": "blocked",
  "message": "账号已被封或授权失效",
  "profile_data": {...}
}
```

## API使用方法

### 步骤一：获取初始Token

从Cursor网站获取WorkosCursorSessionToken（浏览器Cookie中）

### 步骤二：调用刷新接口获取长效Token

使用上面获取的Token调用我们的刷新接口，获取长效Token

### 步骤三：使用长效Token进行API操作

将获取到的accessToken用于后续所有Cursor API调用

## 使用示例

### cURL示例

```bash
curl -X GET 'https://token.cursorpro.com.cn/reftoken?token=您的WorkosCursorSessionToken'
```

### JavaScript示例

```javascript
// 获取Cursor API Token
async function getCursorToken(sessionToken) {
  try {
    const response = await fetch(`https://token.cursorpro.com.cn/reftoken?token=${encodeURIComponent(sessionToken)}`);
    const data = await response.json();
    
    if (data.code === 0) {
      return {
        accessToken: data.data.accessToken,
        refreshToken: data.data.refreshToken,
        expireTime: data.data.expire_time,
        daysLeft: data.data.days_left,
        userId: data.data.user_id
      };
    } else {
      throw new Error(data.msg || '获取失败');
    }
  } catch (error) {
    console.error('获取Token失败:', error);
    throw error;
  }
}
```

### Python示例

```python
import requests

# 初始Token（从Cursor网站Cookie获取）
initial_token = "your_workos_cursor_session_token"

# 获取长效Token
response = requests.get(
    "https://token.cursorpro.com.cn/reftoken",
    params={"token": initial_token}
)

if response.status_code == 200 and response.json()["code"] == 0:
    token_data = response.json()["data"]
    access_token = token_data["accessToken"]
    expire_time = token_data["expire_time"]
    days_left = token_data["days_left"]
    
    print(f"获取成功! Token有效期至: {expire_time}，剩余天数: {days_left}")
    
    # 检查账号状态
    status_response = requests.get(
        "https://token.cursorpro.com.cn/checkcursor/getcursorstatus",
        params={"token": access_token}
    )
    
    if status_response.status_code == 200:
        account_info = status_response.json()
        print(f"账号类型: {account_info.get('account_type')}")
        print(f"账号状态: {account_info.get('account_status')}")
else:
    print("获取失败:", response.text)
```

## 重要说明

- 每个IP每天只能获取5次Token
- Token有效期通常为多天，请妥善保存
- 接口会自动缓存结果，相同的SessionToken重复请求会返回缓存结果
- 如需强制刷新，可在请求参数中添加`force_refresh=true`

## 联系方式

如需更多帮助或了解更多服务，请联系我们：

- 联系人：[您的联系方式]
- 微信/QQ：[您的社交媒体]
- 邮箱：[您的邮箱]

## 注意事项

- 本服务仅供学习研究使用
- 请勿用于非法用途
- API可能会有调整，请保持关注最新文档 
