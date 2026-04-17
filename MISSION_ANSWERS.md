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
| Shutdown | Đột ngột | Graceful | ... |

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image: [Your answer]
2. Working directory: [Your answer]
...

### Exercise 2.3: Image size comparison
- Develop: [X] MB
- Production: [Y] MB
- Difference: [Z]%

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: https://your-app.railway.app
- Screenshot: [Link to screenshot in repo]

## Part 4: API Security

### Exercise 4.1-4.3: Test results
[Paste your test outputs]

### Exercise 4.4: Cost guard implementation
[Explain your approach]

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
[Your explanations and test results]
