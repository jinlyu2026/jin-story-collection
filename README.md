# 用户故事收集表

一个可以直接提交到后台的温情问卷，用于收集铁粉的使用故事。

**在线地址：** https://jinlyu2026.github.io/jin-story-collection/

---

## 如何使用

### 第一步：配置表单接收端点

打开 `story-collection.html`，找到这一行：

```javascript
const FORM_ENDPOINT = '';  // ← 填入你的 endpoint
```

把它替换成你的实际接收地址。

---

## 后端方案选择

### 方案 A：Formspree（推荐，最简单）

1. 访问 https://formspree.io/
2. 用邮箱注册（免费，无需信用卡）
3. 创建一个新 Form
4. 复制 endpoint URL，格式如：`https://formspree.io/f/xkndjypq`
5. 填入 `FORM_ENDPOINT`
6. 完成！每次提交都会出现在你的 Formspree 后台，可导出 CSV

**免费额度：** 每月 50 条提交，对于初期收集足够用了。

**优势：** 无需搭建任何服务器，5 分钟搞定，数据可导出，支持邮件通知。

---

### 方案 B：Getform

类似 Formspree，访问 https://getform.io/ 注册获取 endpoint。

---

### 方案 C：Google Sheets（完全免费，无限条数）

如果你希望数据直接进入 Google Sheets：

1. 创建一个新的 Google Sheet
2. 打开 **扩展程序 → Apps Script**
3. 粘贴以下代码：

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = JSON.parse(e.postData.contents);

  // 表头（第一次运行时创建）
  if (sheet.getLastRow() === 0) {
    sheet.appendRow([
      '提交时间', '称呼', '职业', 'Q1_起源', 'Q2_场景', 'Q3_影响',
      'Q4_一句话', 'Q5_推荐', '评分', 'Q7_其他', '同意授权'
    ]);
  }

  sheet.appendRow([
    data.submitted_at || new Date().toISOString(),
    data.nickname || '',
    data.role || '',
    data.q1_origin || '',
    data.q2_scenario || '',
    data.q3_impact || '',
    data.q4_quote || '',
    data.q5_recommend || '',
    data.rating || '',
    data.q7_extra || '',
    data.consent || ''
  ]);

  return ContentService.createTextOutput(JSON.stringify({success: true}))
    .setMimeType(ContentService.MimeType.JSON);
}
```

4. 点击 **部署 → 新部署**
5. 选择类型为 **Web 应用**
6. 执行身份选 **我**，访问权限选 **任何人**
7. 复制 Web App URL，填入 `FORM_ENDPOINT`

---

### 方案 D：自建 API

如果你有自己的服务器或 Cloud Function，直接填入 API 地址即可。表单会以 `POST` + `JSON` 格式发送数据。

---

## 表单字段说明

| 字段名 | 说明 |
|--------|------|
| `nickname` | 称呼（选填） |
| `role` | 职业/身份（选填） |
| `q1_origin` | 怎么开始使用的 |
| `q2_scenario` | 使用场景 |
| `q3_impact` | 解决了什么问题 |
| `q4_quote` | 一句话形容 |
| `q5_recommend` | 是否推荐 |
| `rating` | 星级评分（1-5） |
| `q7_extra` | 其他想说的 |
| `consent` | 是否同意授权 |
| `submitted_at` | 提交时间（自动添加） |
| `user_agent` | 浏览器信息（自动添加） |
| `page_url` | 页面地址（自动添加） |

---

## 本地预览

```bash
# 方式一：直接用浏览器打开
open story-collection.html

# 方式二：本地服务器
python3 -m http.server 8080
# 然后访问 http://localhost:8080/story-collection.html
```

---

## 自定义

- 修改信件内容：编辑 HTML 中的 `.letter-body` 部分
- 修改问题：搜索 `question-block` 区域
- 修改配色：调整 `:root` 中的 CSS 变量
- 关闭配置提示：设置 `FORM_ENDPOINT` 后，黄色的配置提示会自动消失
