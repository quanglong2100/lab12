# Day 12 Lab - Mission Answers

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found
1. Vấn đề 1: API key hardcode trong code. Nếu push lên GitHub → key bị lộ ngay lập tức
2. Vấn đề 2: Không có config management
3. Vấn đề 3: Print thay vì proper logging
4. Không có health check endpoint -> Nếu agent crashes, platform không biết để restart
5. Port cố định, không đọc từ environment -> Trên Railway/Render, PORT được inject qua env var

### Exercise 1.2: Run basic version

### Exercise 1.3: Comparison table
| Feature | Basic | Advanced | Tại sao quan trọng? |
|---------|-------|----------|---------------------|
| Config | Hardcode | Env vars | Nếu hardcode thì key bị lộ |
| Health check | Không | Có | Nếu agent crashes, platform không biết để restart |
| Logging | print() | JSON | Log hẳn hoi sau còn xem lại để debug |
| Shutdown | Đột ngột | Graceful | Shutdown sai cách thì mất dữ liệu, request bị drop |

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image: Image gốc để build container (giống OS + runtime ban đầu)
2. Working directory: Thư mục mặc định trong container để chạy lệnh (WORKDIR /app) — khỏi phải cd mỗi lần.
3. Tại sao COPY requirements.txt trước: Để tận dụng cache Docker. Nếu file này không đổi thì không cần cài lại dependencies → build nhanh hơn.
4. CMD vs ENTRYPOINT khác nhau thế nào: CMD = mặc định (có thể bị override khi run container), entrypoint = command chính luôn chạy (khó override hơn)

### Exercise 2.3: Image size comparison
- Develop: 1000 MB
- Production: 200 MB
- Difference: 500%

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: https://caring-comfort-production-636d.up.railway.app/
- Screenshot: ![Part 3.1 Lab 12](part_3-1_lab_12.png)

## Part 4: API Security

### Exercise 4.1-4.3: Test results
```
@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/production (main) $ python app.py
=== Demo credentials ===
  student / demo123  (10 req/min, $1/day budget)
  teacher / teach456 (100 req/min, $1/day budget)
INFO:     Started server process [84833]
INFO:     Waiting for application startup.
INFO:__main__:Security layer initialized
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
INFO:     171.255.122.249:0 - "GET / HTTP/1.1" 200 OK
curl -X POST http://localhost:8000/token \
  -H "Content-Type: application/json" \
  -d '{"username":"student","password":"demo123"}'
curl -X POST http://localhost:8000/ask \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"question":"what is docker?"}'
INFO:     127.0.0.1:43532 - "HEAD /health HTTP/1.1" 405 Method Not Allowed
INFO:     127.0.0.1:55374 - "POST /token HTTP/1.1" 200 OK
INFO:     127.0.0.1:54646 - "POST /token HTTP/1.1" 200 OK

@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/production (main) $ curl -X POST http://localhost:8000/token \
  -H "Content-Type: application/json" \
  -d '{"username":"student","password":"demo123"}'
{"access_token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJzdHVkZW50Iiwicm9sZSI6InVzZXIiLCJpYXQiOjE3NzY0MjI2NTIsImV4cCI6MTc3NjQyNjI1Mn0.CIgEq-LbMLZFEJz5zRETFd764Cv_cUIZ73xQHfQ861Q","token_type":"bearer","expires_in_minutes":60,"hint":"Include in header: Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."}@
@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/production (main) $ 

@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/production (main) $ TOKEN="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJzdHVkZW50Iiwicm9sZSI6InVzZXIiLCJpYXQiOjE3NzY0MjI2NTIsImV4cCI6MTc3NjQyNjI1Mn0.CIgEq-LbMLZFEJz5zRETFd764Cv_cUIZ73xQHfQ861Q"
curl http://localhost:8000/ask -X POST \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"question": "Explain JWT"}'
{"question":"Explain JWT","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":9,"budget_remaining_usd":1.9e-05}}@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/production (main) $  

@quanglong2100 ➜ /workspac# Gọi liên tục 20 lầnd_va_deployment/04-api-gateway/production (main) $  
for i in {1..20}; do
  curl http://localhost:8000/ask -X POST \
    -H "Authorization: Bearer $TOKEN" \
    -H "Content-Type: application/json" \
    -d '{"question": "Test '$i'"}'
  echo ""
done
{"question":"Test 1","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":8,"budget_remaining_usd":3.5e-05}}
{"question":"Test 2","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":7,"budget_remaining_usd":5.3e-05}}
{"question":"Test 3","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":6,"budget_remaining_usd":7e-05}}
{"question":"Test 4","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":5,"budget_remaining_usd":9.1e-05}}
{"question":"Test 5","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":4,"budget_remaining_usd":0.000112}}
{"question":"Test 6","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":3,"budget_remaining_usd":0.000128}}
{"question":"Test 7","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":2,"budget_remaining_usd":0.000146}}
{"question":"Test 8","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":1,"budget_remaining_usd":0.000163}}
{"question":"Test 9","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":0,"budget_remaining_usd":0.000179}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":17}}
```


### Exercise 4.4: Cost guard implementation
```
import redis
from datetime import datetime

r = redis.Redis()

def check_budget(user_id: str, estimated_cost: float) -> bool:
    month_key = datetime.now().strftime("%Y-%m")
    key = f"budget:{user_id}:{month_key}"
    
    current = float(r.get(key) or 0)
    if current + estimated_cost > 10:
        return False
    
    r.incrbyfloat(key, estimated_cost)
    r.expire(key, 32 * 24 * 3600)  # 32 days
    return True

Explain my approach:
I'm implementing a monthly spending cap per user ($10/month) by
1. Keying usage per user + month: key = f"budget:{user_id}:{month_key}"
2. Reading current usage from Redis: current = float(r.get(key) or 0)
3. Checking limit before allowing spend: if current + estimated_cost > 10:
    return False
4. Updating usage if allowed: r.incrbyfloat(key, estimated_cost)
5. Setting expiration (~monthly reset): r.expire(key, 32 * 24 * 3600)
```

## Part 5: Scaling & Reliability

```
@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/05-scaling-reliability/production (main) $ python test_stateless.py
============================================================
Stateless Scaling Demo
============================================================

Session ID: 6a976359-c336-4731-905f-86c1f19dd971

Request 1: [instance-d1f130]
  Q: What is Docker?
  A: Container là cách đóng gói app để chạy ở mọi nơi. Build once, run anywhere!...

Request 2: [instance-5ead0f]
  Q: Why do we need containers?
  A: Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận....

Request 3: [instance-1e5bcf]
  Q: What is Kubernetes?
  A: Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ O...

Request 4: [instance-d1f130]
  Q: How does load balancing work?
  A: Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ O...

Request 5: [instance-5ead0f]
  Q: What is Redis used for?
  A: Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận....

------------------------------------------------------------
Total requests: 5
Instances used: {'instance-1e5bcf', 'instance-5ead0f', 'instance-d1f130'}
✅ All requests served despite different instances!

--- Conversation History ---
Total messages: 10
  [user]: What is Docker?...
  [assistant]: Container là cách đóng gói app để chạy ở mọi nơi. Build once...
  [user]: Why do we need containers?...
  [assistant]: Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã đư...
  [user]: What is Kubernetes?...
  [assistant]: Đây là câu trả lời từ AI agent (mock). Trong production, đây...
  [user]: How does load balancing work?...
  [assistant]: Đây là câu trả lời từ AI agent (mock). Trong production, đây...
  [user]: What is Redis used for?...
  [assistant]: Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã đư...

✅ Session history preserved across all instances via Redis!
@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/05-scaling-reliability/production (main) $
```

Cuối cùng cũng xong part 5. Ai ra đề đánh đố vậy, bug ngay từ code trong đề bài, thì người mới học họ đọc sao hiểu?
