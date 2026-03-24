# CEAC Visa Status Checker

自动查询美国签证 CEAC (NIV) 状态，使用智谱视觉模型识别验证码，并输出结构化 JSON。

> 默认语言：中文
>
> Language: 中文 | [English](README.en.md)

## 功能特性

- 自动访问 CEAC 状态查询页面 (NIV)
- 自动获取并识别验证码 (Zhipu Vision)
- 自动提交查询并解析结果字段
- 查询失败时自动重试
- 标准 JSON 输出，便于后续自动化接入

## 项目结构

```text
.
├─ requirements.txt
├─ scripts/
│  └─ check_ceac_status.py
└─ references/
   └─ LOCATION.md
```

## 环境要求

- Python 3.9+
- 可访问 https://ceac.state.gov
- 智谱 API Key（用于验证码识别）

## 安装依赖

```bash
python -m pip install -r requirements.txt
```

## 配置环境变量

将 .env.example.txt 复制为 .env，并填写真实信息。

```env
LOCATION=CHINA, BEIJING
NUMBER=AA0020AKAX
PASSPORT_NUMBER=E12345678
SURNAME=ZHANG
ZHIPU_API_KEY=your_zhipu_api_key

# 可选
ZHIPU_MODEL_VISION=glm-4v-flash
MAX_RETRIES=5
RETRY_DELAY_SECONDS=5
```

### 必填字段说明

- LOCATION: CEAC 页面中的地点文本（例如 CHINA, BEIJING）
- NUMBER: Application ID 或 Case Number
- PASSPORT_NUMBER: 护照号
- SURNAME: 与 CEAC 记录一致的姓氏字母
- ZHIPU_API_KEY: 智谱开放平台密钥

地点参考见 references/LOCATION.md。

## 运行

```bash
python scripts/check_ceac_status.py
```

脚本会读取 .env 并输出 JSON 到标准输出。

## 输出示例

成功示例：

```json
{
  "success": true,
  "time": "2026-03-24 10:12:30",
  "visa_type": "NON IMMIGRANT VISA",
  "status": "Approved",
  "case_created": "01-Jan-2026",
  "case_last_updated": "20-Mar-2026",
  "description": "Your visa has been approved...",
  "application_num": "AA0020AKAX",
  "application_num_origin": "AA0020AKAX",
  "location": "CHINA, BEIJING"
}
```

失败示例：

```json
{
  "success": false,
  "error": "QUERY_FAILED",
  "message": "Failed after 5 attempts.",
  "last_error": "..."
}
```

查询失败时，程序会以非 0 状态码退出，便于 CI 或调度系统识别失败。

## 常见问题

### 1) LOCATION 匹配失败

- 请确保 LOCATION 与 CEAC 下拉项一致
- 可参考 references/LOCATION.md
- 脚本会尝试模糊匹配（忽略大小写和多余空格）

### 2) 验证码识别偶发失败

- 属于正常情况，脚本会自动重试
- 可适当增大 MAX_RETRIES 和 RETRY_DELAY_SECONDS

### 3) API Key 或网络问题

- 检查 ZHIPU_API_KEY 是否正确
- 确认网络可访问 CEAC 与智谱接口

## 相关链接

- Skill 发布页: https://clawhub.ai/xavierjiezou/ceac-visa-status-checker
- 参考仓库（思路参考，非代码拷贝）: https://github.com/Andision/CEACStatusBot
- 参考仓库（思路参考，非代码拷贝）: https://github.com/dutou520/SakuraFRP_Auto_AI_check

## 安全说明

- 不要将 .env 提交到版本库
- 不要在代码中硬编码 API Key、护照号等敏感信息

## 免责声明

本项目仅用于个人自动化查询与学习用途。请遵守 CEAC 网站使用条款及当地法律法规。
