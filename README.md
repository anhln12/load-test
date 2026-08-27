Load test (kiểm thử tải) là việc **giả lập một lượng request/người dùng lớn truy cập vào hệ thống** để xem hệ thống chịu tải được đến đâu và tìm ra điểm nghẽn.

Ví dụ kiến trúc hệ thống của bạn

```
                    Load Test
                       │
              ┌────────▼────────┐
              │  1.000 requests/s│
              └────────┬────────┘
                       │
                       ▼
                    Nginx
                       │
                       ▼
                   Keycloak
                       │
              ┌────────┼────────┐
              ▼        ▼        ▼
          PostgreSQL  Redis    Kafka
```

Load test thường kiểm tra gì?

|Chỉ số|Ý nghĩa|
|---|---|
|RPS/TPS| Hệ thống xử lý bao nhiêu request/giây|
|Latency|Một request mất bao lâu|
|P95/P99|95%/99% request hoàn thành dưới bao nhiêu ms|
|Error rate|Tỷ lệ request lỗi|
|CPU|CPU có bị full không|
|RAM|Có tăng Ram/memory leak không|
|DB connections|DB có hết connection không|
|Throughput|Lưu lượng hệ thống xử lý được|
|Concurency|Bao nhiêu request đồng thời|

Ví dụ:
```
Concurrent users    RPS       P95       Error
------------------------------------------------
100                 80        120ms     0%
500                 390       180ms     0%
1000                750       350ms     0.1%
2000                1100      1.2s      2%
5000                1300      8.5s      18%
```

Phân biệt các khái niệm
* Load test: kiểm tra hệ thống dưới tải dự kiến
* Stress test: tiếp tục tăng tải vượt mức dự kién để tìm giới hạn
* Spike test: tăng tải đột ngột, ví dụ 100 -> 10.000 users trong vài giây
* Soak test: chạy tải trong thời gian dài, để tìm memory leak hoặc degradation

