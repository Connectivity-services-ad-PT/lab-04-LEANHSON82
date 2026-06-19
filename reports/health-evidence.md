# Lab 04 – Bằng chứng chạy container (health + logs)

Sinh ngày: 2026-06-19 — sinh viên: LEANHSON82 — team-iot

## 1. Image đã build & tag

```text
fit4110/iot-ingestion:lab04
ghcr.io/leanhson82/team-iot:v0.1.0-team-iot   (theo quy ước v0.1.0-<team>)
```

## 2. Lệnh chạy container

> Cổng 8000 trên máy này đang bị Portainer chiếm, nên ánh xạ host `8001 -> 8000`.
> Trên máy sạch (cổng 8000 trống) dùng đúng `-p 8000:8000` như trong RUN_LOCAL.md.

```bash
docker run -d --name fit4110-iot-lab04 -p 8001:8000 --env-file .env.example fit4110/iot-ingestion:lab04
```

## 3. GET /health trả 200

```json
{"status":"ok","service":"iot-ingestion","version":"0.4.0"}
```

## 4. Docker HEALTHCHECK = healthy

```json
{"Status":"healthy","FailingStreak":0,"Log":[{"Start":"2026-06-19T14:52:10.095363254+07:00","End":"2026-06-19T14:52:10.300916858+07:00","ExitCode":0,"Output":""},{"Start":"2026-06-19T14:52:40.301514309+07:00","End":"2026-06-19T14:52:40.530107434+07:00","ExitCode":0,"Output":""}]}
```

## 5. Service chạy bằng non-root user

```text
uid=100(appuser) gid=101(appgroup) groups=101(appgroup)
```

## 6. Container logs (uvicorn khởi động)

```text
INFO:     Started server process [7]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
INFO:     172.17.0.1:49998 - "GET /health HTTP/1.1" 200 OK
INFO:     172.17.0.1:50002 - "POST /readings HTTP/1.1" 401 Unauthorized
INFO:     127.0.0.1:44002 - "GET /health HTTP/1.1" 200 OK
INFO:     172.17.0.1:38824 - "GET /health HTTP/1.1" 200 OK
INFO:     172.17.0.1:38824 - "POST /readings HTTP/1.1" 201 Created
INFO:     172.17.0.1:38824 - "GET /readings/latest?device_id=ESP32-LAB-A01&limit=5 HTTP/1.1" 200 OK
INFO:     172.17.0.1:38824 - "GET /readings/R-20260619-0001 HTTP/1.1" 200 OK
INFO:     172.17.0.1:38824 - "POST /readings HTTP/1.1" 401 Unauthorized
INFO:     172.17.0.1:38824 - "POST /readings HTTP/1.1" 401 Unauthorized
INFO:     172.17.0.1:38824 - "POST /readings HTTP/1.1" 422 Unprocessable Entity
INFO:     172.17.0.1:38824 - "POST /readings HTTP/1.1" 422 Unprocessable Entity
```

## 7. Kết quả Newman trên container

```text
iterations: 1/1   requests: 11/11   test-scripts: 11/11   assertions: 19/19   failed: 0
```

Report chi tiết: reports/newman-lab04-local.html , reports/newman-lab04-local.xml
