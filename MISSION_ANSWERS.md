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
@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/develop (main) $ python app.py
API Key: demo-key-change-in-production
Test: curl -H 'X-API-Key: demo-key-change-in-production' http://localhost:8000/ask?question=hello
INFO:     Will watch for changes in these directories: ['/workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/develop']
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [67574] using WatchFiles
INFO:     Started server process [67576]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     171.255.122.249:0 - "GET / HTTP/1.1" 200 OK
curl http://localhost:8000/ask -X POST \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
INFO:     127.0.0.1:60560 - "POST /ask HTTP/1.1" 401 Unauthorized
INFO:     127.0.0.1:44372 - "POST /ask HTTP/1.1" 403 Forbidden
INFO:     127.0.0.1:42452 - "POST /ask HTTP/1.1" 422 Unprocessable Entity
@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/develop (main) $ curl http://localhost:8000/ask -X POST \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
{"detail":"Missing API key. Include header: X-API-Key: <your-key>"}@quanglong2100 ➜ /workspaces/da@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/develop (main) $ curl http://localhost:8000/ask -X POST \
  -H "X-API-Key: secret-key-123" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
@quanglong2100 ➜ /workspaces/day12_ha-tang-cloud_va_deployment/04-api-gateway/develop (main) $ curl http://localhost:8000/ask -X POST \
  -H "X-API-Key: demo-key-change-in-production" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
{"detail":[{"type":"missing","loc":["query","question"],"msg":"Field required","input":null}]}@qua

### Exercise 4.4: Cost guard implementation
[Explain your approach]

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
[Your explanations and test results]
