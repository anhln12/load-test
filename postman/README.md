Trong Postman, cách phố bển nhất lấy access_token từ response của request login rồi lưu vào Enviroment variable, sau đó các request khác dùng biến {{access_token}}

1. Ở request Login

Tab Scripts -> Chọn Post-response

<img width="571" height="102" alt="image" src="https://github.com/user-attachments/assets/daad19dd-af3f-406a-bf5a-ad7aca175944" />
```
Params | Authorization | Headers | Body | Scripts | Settings
```

Thêm đoạn JavaScript sau:
```
const json = pm.response.json();

if (json.code === "200" && json.data?.access_token) {
    pm.environment.set("access_token", json.data.access_token);

    console.log("✅ Access token đã lấy thành công");
    console.log("Token length:", json.data.access_token.length);
} else {
    console.log("❌ Không lấy được access token");
}
```

Sau đó bấm Send request Login.
