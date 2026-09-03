Kiểm tra tải của server K6: https://k6.io

Cài đặt

K6.io phát hành các gói cài đặt và sử dụng trên rất nhiều hệ điều hành, hệ thống như linux, mac, window, docker….

Linux (Debian/Ubuntu)
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
echo "deb https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6
```

Mac (brew)
```
brew install k6
```

Kiểm tra độ chịu tải với k6

Vì K6 sử dụng file javascript để chạy test nên khá dễ dàng tiếp cận. Chúng ta tạo một file với homepage.js đơn giản với nội dung sau:
```
import http from 'k6/http';
import { sleep } from 'k6';

export let options = {
  VUs: 10,
  duration: '30s',
};

export default function() {
   http.get('https://dbaleanh.wordpress.com');
   sleep(1);
}
```

Ở đây mình khai báo VUs = 10 và duration = 30 giây có nghĩa là trình test mô tả 10 user vào website https://dbaleanh.wordpress.com trong 30 giây (VUs viết tắt của virtual user)

Tiến hành chạy test:
```
k6 run homepage.js
```

sau khi chạy xong k6 sẽ tổng hợp số liệu như sau:
```
data_received..............: 148 MB 2.5 MB/s
data_sent..................: 1.0 MB 17 kB/s
http_req_blocked...........: avg=1.92ms   min=1µs      med=5µs      max=288.73ms p(90)=11µs     p(95)=17µs
http_req_connecting........: avg=1.01ms   min=0s       med=0s       max=166.44ms p(90)=0s       p(95)=0s
http_req_duration..........: avg=143.14ms min=112.87ms med=136.03ms max=1.18s    p(90)=164.2ms  p(95)=177.75ms
http_req_receiving.........: avg=5.53ms   min=49µs     med=2.11ms   max=1.01s    p(90)=9.25ms   p(95)=11.8ms
http_req_sending...........: avg=30.01µs  min=7µs      med=24µs     max=1.89ms   p(90)=48µs     p(95)=63µs
http_req_tls_handshaking...: avg=0s       min=0s       med=0s       max=0s       p(90)=0s       p(95)=0s
http_req_waiting...........: avg=137.57ms min=111.44ms med=132.59ms max=589.4ms  p(90)=159.95ms p(95)=169.41ms
http_reqs..................: 13491  224.848869/s
iteration_duration.........: avg=445.48ms min=413.05ms med=436.36ms max=1.48s    p(90)=464.94ms p(95)=479.66ms
iterations.................: 13410  223.498876/s
vus........................: 100    min=100 max=100
vus_max....................: 100    min=100 max=100
```

Đây là 1 case đơn giản. Trên thực tế thì phức tạp hơn trong thực tế thì user thường tương tác với Server theo một kịch bản chứ không chỉ đơn thuần là gửi request, hay số lượng user tăng lên hay giảm đi.
