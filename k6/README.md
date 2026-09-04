Kiểm tra tải của server K6: https://k6.io

K6 là gì?

Để kiểm thử một hệ thống CNTT, một trong số bài test cực kỳ quan trọng mang tên load testing (Kiểm thử test).

Để thực hiện load testing, ta cần phải quân tâm tới nhiều thứ, từ việc mô phỏng một số lượng user cùng lức truy cập, mô phỏng lượng use thay đổi theo thời gian tới việc theo dõi, ghi lại và tính toán các con số, cùng vô vàn những thứ phức tạp khác.

Chính vì vậy, k6 được sinh ra để giúp việc thực thi những chuyện đó trở nên dễ dàng và tối ưu nhất cho các developer, tester, sysadmin ... 

K6 tên đầy đủ là Grafana k6, là một công cụ hỗ trợ load testing được phát triển bởi Grafana Labs và cộng đồng, nó là dự án mã nguồn mở và thể mở rộng. k6 hỗ trợ tốt cho mô hình CI/CD, dễ dàng tính hợp vào các CI/CD như tools như Jenkins, Azure Pipeliné.

K6 được phát triển bằng Go tuy nhiên test script được viết bằng JavaScript giúp chúng ta dễ dàng tiếp cân và sử dụng. Công cụ này nổi bật với tính năng đơn giản và hiệu năng mà nó mang lại.

1. Cài đặt

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

2. Một vài thành phần quan trọng của test script và giải thích test output

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

Đây là 1 case load test trang web bằng k6 đơn giản. Trên thực tế thì phức tạp hơn trong thực tế thì user thường tương tác với Server theo một kịch bản chứ không chỉ đơn thuần là gửi request, hay số lượng user tăng lên hay giảm đi.

Bây giờ chúng ta sẽ tìm hiểu về cả test script và test output

Những khái niệm cơ bản

Cùng xem lại script này, nó là 1 file JavaScript thuần

Trong file script, ta đã export hai thành phần cơ bản của k6:
* Default function: mỗi test script luôn phải export một default function, nó mô tả công việc mà mỗi VUs làm và lặp đi lặp lại trong suốt quá trình test.
* Options: định nghĩa test-run behavior, cấu hình của k6 có thể nằm ở nhiều nơi, và khi có cùng một giá trị cấu hình được đặt ở nhiều nơi thì k6 sẽ lấy giá trị ở nơi có độ ưu tiên cao hơn, cụ thể bạn tham khảo tại đây https://grafana.com/docs/k6/latest/using-k6/k6-options/how-to/#order-of-precedence

Ví dụ, chung ta chạy command line:
```
k6 run --vus 20 homepage.js
```

Khi đó, options ở bên ngoài command sẽ đè lên options ở bên trong script: options 20 VUs sẽ override lại 10 VUs ở bên trong.

Có rất nhiều options cho k6, nhưng trước tiên mình muốn giới thiệu về hai options là VUs và duration.
* VUs trong k6 về cơ bản là các vòng lặp while(true) chạy song song
* Duration chính là thời gian thực thi của test này

Như vậy script trên thì công việc mô phỏng 10 users cùng truy cập liên tục trong 30s, có nghỉ 1ms giữa 2 lần liên tiếp của mỗi user nhờ câu lệnh sleep(1)

Có một options nữa cũng rất hay dùng đó là iterations, nó được hiểu là tổng số lần các VUs thực thi default function
```
export const options = {
  vus: 10,
  iterations: 100,
};
```

Ví dụ với options trên, 10 VUs sẽ chia nhau chạy sao cho đủ 100 lần công việc trong default function là kết thúc test. Chú ý là thời gian hoàn thành mỗi vòng lặp của mỗi VUs có thể khác nhau nên không có nghĩa là một VUs sẽ chạy đúng 10 vòng lặp.

Có một câu hỏi thú vị mà mình muốn chia sẻ với các bạn ở phần này là:

Có thể có tối đa bao nhiêu Virtual Users cho mỗi test script như thế này?

Câu trả lời là tùy thuộc vào phần cứng mà script này đang hoạt động trên đó.

Result output

Trước tiên, chúng ta xem thử các con số trong test result này sinh ra như thế nào.

k6 tạo tải cho web của bạn, sau đó nó đo lường kết quả của hệ thống theo thời gian thực (real - time) dựa vào những kết quả trả về, do đó đa số các số liệu trong report ở dạng thống kê, đồ thị sẽ thay đổi theo thời gian. Report mà chúng ta thấy ở console chính là số liệu đo ở thời điểm kết thúc thử nghiệm (end of test summary), cùng các thông số đặc trưng của bên xác suất thông kế như:
- Average (avg): giá trị trung bình
- Minimum (min): giá trị nhỏ nhất
- Maximum (max): giá trị lớn nhất
- Medium (med): trung vị, tức là giá trị ở giữa sau khi sắp xếp các kết quả lại
- Percentiles(p): bách phân vị, ví dụ như http_req_duration có thông số p(90)=384.33ms thì có ý nghĩa là có 90% các request có duration nhỏ hơn 384.33ms

Nếu muốn, các bạn cũng có thể thử cài và tìm cách xem những phiên bản report có màu mè hay đồ thị ở đây: https://grafana.com/blog/how-to-visualize-load-testing-results/

3. k6 test lifecycle

Từ test script đơn giản ở phần trước, chúng ra thấy là có một options và một default options định nghĩa hành động test. Vậy ngoài hai phần trên thì một test script có thể có những thành phần nào?
Những thành phần đó sẽ đóng vai trò gì trong quá trình chạy test? Hãy cùng nhau tìm hiểu vòng đời của một k6 test để trả lời những câu hỏi trên.

Test scripts của k6 gồm bốn thành phần: init (bắt buộc), setup, VU code (bắt buộc) và teardown.

```
// 1. init code
export function setup() {
  // 2. setup code
  const data = '';
  return data;
}

export default function (data) {
  // 3. VU code
}

export function teardown(data) {
  // 4. teardown code
}
```

|Thành phần|Mục đích|Ví dụ|Số lần gọi|
|---|---|---|---|
|init (required)|Import modules, load files, định nghĩa options, khai báo các hàm (hàm tự định nghĩa hoặc các hàm đặc biệt như handleSummary())|Import thư viện, khai báo options.|1 lần cho mỗi VUs|
|setup (optional)|Setup, cung cấp data cho tất cả VUs|Lấy dữ liệu từ một trang web khác về để cho vào body trong http request ở VUs code.|1 lần cho cả quá trình test|
|VU code (required)|Mô phỏng công việc của từng VU|Gửi http requests, validate response|Tùy thuộc vào options|
|teardown (optional)|Hậu xử lý data của setup, dừng các test environment|Validate kết quả của setup, gửi thông tin rằng test đã hoàn thành.|1 lần cho cả quá trình test|

**Khái niệm Checks?**

Checks dùng để kiểm tra một điều kiện đúng sai trong test của bạn và xuất ra trong output. 

Người ta thường dùng checks để kiểm tra xem response trả về của request có đúng như mong đợi hay không.

Ví dụ: Sử dụng checks để kiểm tra các response trả về có trả về status 200 OK hay không, vùng với 2 checks kiểm tra tính chất của body trả về.

```
import { check } from 'k6';
import http from 'k6/http';

export default function () {
  const res = http.get('http://test.k6.io/');
  check(res, {
    'is status 200': (r) => r.status === 200,
    'body size is 11,105 bytes': (r) => r.body.length == 11105,
    'is body contain k6': (r) => r.body.includes('k6')
  });
}
```

**Thresholds (Ngưỡng)**

Thresholds là những tiêu chí mà bạn mong muốn hệ thống của mình đạt được. Nó là ngưỡng để xem xét test của bạn là pass hay fail

Sau đây cách cấu hình thresholds
```
export const options = {
    thresholds: {
      metric_name: [
        {
          threshold: 'p(99) < 10', // string
          abortOnFail: true, // boolean
          delayAbortEval: '10s', // string
          /*...*/
        },
      ],
    },
  };
```

Trong đó:
- metric_name là thành phần những thông số trong output bạn muốn đặt thresholds.
- threshold là phần định nghĩa thresholds lên thông số. Tùy theo từng loại metric mà có cách định nghĩa khác nhau, các bạn có thể xem thêm tại đây nhé. https://grafana.com/docs/k6/latest/using-k6/thresholds
- abortOnFail xác định xem có nên ngưng test ngay tại thời điểm test không thỏa mãn thresholds hay không.
- delayAbortEval do quá trình xét thresholds là liên tục nên nếu ta muốn delay một khoảng thời gian để thu thập thêm dữ liệu giữa những lần xét thì có thể setting ở đây.

Một ví dụ
```
import http from 'k6/http';
import { check } from 'k6';

export const options = {
  vus: 50,
  duration: '10s',
  thresholds: {
    // the rate of successful checks should be higher than 90%
    checks: ['rate>0.9'],
  },
};

export default function () {
  const res = http.get('http://test.k6.io/');
  check(res, {
    'is status 200': (r) => r.status === 200,
    'body size is 11,105 bytes': (r) => r.body.length == 11105,
    'is body contain k6': (r) => r.body.includes('k6')
  });
}
```

**Tags (nhãn)**

Gán nhãn (tag) là thành phần bổ trợ rất tốt trong quá trình viết script cho k6.

Chúng ta có thể gán nhãn những thành phần như requests, checks, thresholds, custom metrics để dễ phân loại, hình dung và sử dụng trong suốt quá trình viết test script và xem output.

Sau đây là một ví dụ về cách sử dụng tags kết hợp với thresholds ở phần trên:

```
import http from 'k6/http';
import { sleep } from 'k6';

export const options = {
  thresholds: {
    'http_req_duration{type:API}': ['p(95)<1000'], // threshold on API requests only
    'http_req_duration{type:staticContent}': ['p(95)<500'], // threshold on static content only
  },
};

export default function () {
  //api request
  const apiRes1 = http.get('https://test-api.k6.io/public/crocodiles/1/', {
    tags: { type: 'API' },
  });
  const apiRes2 = http.get('https://test-api.k6.io/public/crocodiles/2/', {
    tags: { type: 'API' },
  });

  //static content request
  const contentResponses = http.batch([
    ['GET', 'https://test-api.k6.io/static/favicon.ico', null, { tags: { type: 'staticContent' } }],
    [
      'GET',
      'https://test-api.k6.io/static/css/site.css',
      null,
      { tags: { type: 'staticContent' } },
    ],
  ]);

  sleep(1);
}
```

(Lưu ý: trong ví dụ trên có sử dụng batch, bạn tìm hiểu thêm [tại đây](https://grafana.com/docs/k6/latest/javascript-api/k6-http/batch/) nhé)

Qua ví dụ, các bạn có thể thấy chúng ta vừa gán nhãn cho 2 requests chịu trách nhiệm về phần API, 2 requests chịu trách nhiệm về phần static content, nhờ đó mà ở phần options chúng ta có thể định nghĩa thresholds một cách rõ ràng nhờ các tags này

**Scenarios (kịch bản)**

Scenarios là một phần rất thú vị và quan trọng của k6. Được định nghĩa là những kịch bản chi tiết trong quá trình diễn ra test, scenarios có liên quan đến số lượng VUs và interations. Nó thường được dùng để mô tả những tình hình thực tế của tải.

Chúng ta sẽ định nghĩa scenarios ở phần options, cụ thể như sau:
```
export const options = {
  scenarios: {
    example_scenario: {
      // name of the executor to use
      executor: 'shared-iterations',

      // common scenario configuration
      startTime: '10s',
      gracefulStop: '5s',
      env: { EXAMPLEVAR: 'testing' },
      tags: { example_tag: 'testing' },

      // executor-specific configuration
      vus: 10,
      iterations: 200,
      maxDuration: '10s',
    },
    another_scenario: {
      /*...*/
    },
  },
};
```

Có 3 thành phần chính của một scenario option:

Executor

Executor (bộ phận thực thi) sắp xếp khối lượng công việc của VU trong k6.

Chúng ta sẽ dùng những executors được định nghĩa sẵn để đưa vào options. Các executors được định nghĩa sẵn được phân loại theo các tiêu chí:

* Dựa theo số lượng iterations cho mỗi VU
- shared-iterations: các VUs sẽ tính chung iterations và chạy đủ iterations ở phần cấu hình
- per-vu-iterations: mỗi VU sẽ chạy đúng số iterations đã định nghĩa ở phần cấu hình
* Dựa theo số lượng VUs
- constant-VUs: các VUs sau khi khởi tạo sẽ được giữ cố định trong suốt quá trình test
- ramping-VUs: số lượng VUs sẽ thay đổi theo thời gian
* Dựa theo lượng iterations trên đơn vị thời gian, gọi là iteration rate
- constant-arrival-rate: giữ số lượng iterations ở mức cho trước và ổn định theo thời gian
- ramping-arrival-rate: tăng giảm số lượng iterations theo thời gian

Common configuration

Cấu hình chung cho cả kịch bản, gồm những loại như thời gian bắt đầu (startTime), nhãn (tags),…

Specific configuration

Với mỗi loại executor thì sẽ có những cấu hình đặc trưng, cần phải được khai báo.

Mời các bạn xem kỹ hơn về các executor và specific configuration của nó tại đây.

Thử nghiệm

Để dễ dàng hình dung, chúng ta cùng tạo thử và áp dụng nhé.
```
import http from 'k6/http';

export const options = {
  discardResponseBodies: true,
  scenarios: {
    contacts: {
      executor: 'ramping-vus',
      exec: 'contacts',
      startVUs: 0,
      stages: [
        { duration: '20s', target: 50 },
        { duration: '10s', target: 0 },
      ],
      gracefulRampDown: '0s',
    },
    news: {
      executor: 'per-vu-iterations',
      exec: 'news',
      vus: 50,
      iterations: 100,
      startTime: '30s',
      maxDuration: '1m',
    },
  },
};

export function contacts() {
  http.get('https://test.k6.io/contacts.php', {
    tags: { my_custom_tag: 'contacts' },
  });
}

export function news() {
  http.get('https://test.k6.io/news.php', { tags: { my_custom_tag: 'news' } });
}
```

Nhìn vào script, chúng ta có một scenario tên là contacts dùng executor ramping-vus, cái còn lại là news dùng executor per-vu-iterations. Trong scenario contacts, ta có thể thấy các specific options:
- startVUs: chỉ định số VUs khởi đầu
- stages: miêu tả sự thay đổi của số lượng VUs
- gracefulRampDown: thời gian chờ các iterations đã start hoàn thành trước khi tắt VU đang chạy nó.

Như vậy, script này mô tả có một lượng user truy cập liên tục vào trang contacts, tăng từ 0 lên 50 trong 20s rồi giảm về lại 0 trong vòng 10s tiếp theo. Sau 30s đó, có 50 user vào trang news, mỗi người truy cập 100 lần.

Ở ví dụ này, bên cạnh giới thiệu về cách cấu hình scenarios, ta cũng thấy được:
- Cách định nghĩa hành động test nhưng không dùng default function.
- Cách sử dụng startTime để sắp xếp thứ tự chạy cho các scenarios.
