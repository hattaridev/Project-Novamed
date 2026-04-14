<div align="center">

# 🏥 NovaMed

### Voice-First Medical eCommerce Platform

*Website bán thuốc & vật tư y tế trực tuyến, điều khiển hoàn toàn bằng giọng nói tiếng Việt*

---

![Tech](https://img.shields.io/badge/React-TypeScript-61DAFB?style=flat-square&logo=react)
![AI](https://img.shields.io/badge/Qwen3.5--35B--A3B-Modal-10b981?style=flat-square)
![DB](https://img.shields.io/badge/ChromaDB-Vector_Search-8b5cf6?style=flat-square)
![Infra](https://img.shields.io/badge/Modal-L40S_GPU-f59e0b?style=flat-square)

</div>

---

## 💡 Giới thiệu

NovaMed là một nền tảng thương mại điện tử dược phẩm tích hợp **AI Voice Agent** — người dùng có thể điều khiển toàn bộ website chỉ bằng giọng nói tiếng Việt, từ tìm kiếm sản phẩm, thêm vào giỏ hàng, điền form thanh toán cho đến đăng nhập bằng giọng nói.

Điểm đặc biệt là AI Agent chạy trên hạ tầng GPU serverless tự host (Modal.com), không phụ thuộc vào dịch vụ AI thương mại của bên thứ ba, cho phép kiểm soát hoàn toàn về chi phí, độ trễ và dữ liệu.

---

## ✨ Tính năng chính

### 🎙️ Voice AI Agent
- Sử dụng **Wake word** — kích hoạt hands-free, không cần chạm màn hình
- Hiểu tiếng Việt tự nhiên, kể cả khi Speech-to-Text nhận dạng sai
- Hỗ trợ **multi-action**: một câu lệnh thực hiện nhiều bước liên tiếp
- **Voice Authentication** — đăng nhập bằng Voice ID hoặc OTP

### 🛒 Thương mại điện tử
- Tìm kiếm, lọc, sắp xếp sản phẩm theo danh mục & giá
- Giỏ hàng, mã giảm giá, checkout với SePay QR / COD / Chuyển khoản
- Quản lý tài khoản, lịch sử đơn hàng, địa chỉ giao hàng
- Danh sách yêu thích

### 🌐 Đa ngôn ngữ & Giao diện
- Hỗ trợ Tiếng Việt / English / Japanese
- Dark mode / Light mode (chuyển bằng giọng nói)
- Responsive — tối ưu Mobile & Desktop

---

## 🤖 AI Agent — Kiến trúc

Hệ thống Voice AI Agent được xây dựng theo luồng 2 lớp:

### Frontend Layer (TypeScript)
| Thành phần | Vai trò |
|---|---|
| **VAD Service** | Phân tích âm thanh real-time bằng Web Audio API FFT, phân biệt giọng người (85–3400 Hz) với tiếng ồn |
| **Wake Word** | Nhận dạng liên tục "Nova ơi" với fuzzy matching, tự động khởi động lại |
| **Speech Service** | STT via Web Speech API + bộ lọc nhiễu; TTS via ElevenLabs → fallback Web Speech |
| **Action Executor** | Thực thi 30+ loại lệnh lên UI: navigate, add_to_cart, fill_form, login, scroll... |

### Backend Layer (Python / Modal)
```
Giọng nói → STT → POST /chat
                      ↓
          [LLM Call #1] Qwen intent detection
                tool_choice: auto → tool_calls
                      ↓
          [Tool Execution] asyncio.gather()
                ChromaDB vector search (max 2 tools, 5s timeout)
                      ↓
          [LLM Call #2] Synthesis + SSE streaming
                Inject tool results → tạo câu trả lời + action JSON
                      ↓
          SSE events → Frontend → Action Executor → TTS
```

---

## 🧠 AI & Data Stack

| Công nghệ | Mục đích |
|---|---|
| **Qwen3.5-35B-A3B** (MoE, 3B active params) | Language model chính, chạy qua SGLang |
| **SGLang** | LLM inference server, hỗ trợ native tool calling & SSE streaming |
| **ChromaDB** | Vector database lưu catalog sản phẩm + knowledge base |
| **multilingual-e5-large** | Embedding model đa ngôn ngữ cho semantic search |
| **Modal.com** | Serverless GPU hosting (L40S 48GB), auto scale, persistent volume |

**Semantic search pipeline:** Toàn bộ catalog sản phẩm (hàng nghìn SKU) được embed bằng E5-large và lưu vào ChromaDB. Khi user tìm kiếm, query được embed real-time và tìm kiếm theo cosine similarity — không chỉ match từ khóa mà hiểu được **ngữ nghĩa**.

---

## 🏗️ Tech Stack

**Frontend**
- React 18 + TypeScript, Vite
- TailwindCSS
- React Router
- Web Audio API / Web Speech API

**Backend & Data**
- Python 3.11, FastAPI, asyncio
- Modal Serverless (GPU: NVIDIA L40S)
- SGLang, ChromaDB, SentenceTransformers

**External Services**
- WordPress + WooCommerce (CMS & Order management)
- ElevenLabs (AI TTS)
- SePay (QR payment gateway)

---

## 🗺️ Workflow

> Xem chi tiết tại **[hattaridev.github.io/Project-Novamed/](https://hattaridev.github.io/Project-Novamed/)**

Diagram mô tả đầy đủ:
1. Luồng Web App bình thường (React ↔ WordPress API)
2. Luồng Voice AI Agent end-to-end (VAD → STT → Modal/Qwen → Action → TTS)
3. Chi tiết Agentic Loop trong Modal Backend (2 LLM calls + parallel tool execution)
4. Data Ingest Pipeline (CSV → ChromaDB)
5. Full lifecycle trace của một câu lệnh thực tế

---

<div align="center">

*NovaMed — Bringing healthcare closer through voice AI*

</div>
